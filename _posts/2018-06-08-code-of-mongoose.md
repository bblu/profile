---
title: step into code of mongoose
date: 2018-06-08
categories: blog
tags: [nodejs,mongo]
---

## 1.dependency of packages
* Mongoose is a [MongoDB](https://www.mongodb.org/) object modeling tool designed to work in an asynchronous environment.
* [MongoDB](https://www.mongodb.com/) : the official driver for Node.js
* mongodb-core the low level api not for end users.

The MongoDB Core driver is the low level part of the 2.0 or higher MongoDB driver and is meant for library developers not end users. 

### 1.1.how to use mongoose

```js
//Defines or retrieves a model.
var mongoose = require('mongoose');
var db = mongoose.createConnection(..);
db.model('Venue', new Schema(..));
var Ticket = db.model('Ticket', new Schema(..));
var Venue = db.model('Venue');
```

### 1.2.how to use MongoDB

```js
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the server
MongoClient.connect(url, function(err, client) {
  assert.equal(null, err);
  console.log("Connected successfully to server");
  const db = client.db(dbName);
  client.close();
});
```

# 2.mongoose

First, we need to define a connection. If your app uses only one database, you should use `mongoose.connect`. 
If you need to create additional connections, use `mongoose.createConnection`.

## 2.1.index.js

```js
//@return {Connection} the created Connection object. 
//Connections are thenable, so you can do `await mongoose.createConnection()`

Mongoose.prototype.createConnection = function(uri, options, callback) {
  var conn = new Connection(this);
  this.connections.push(conn);
  if (arguments.length > 0) {
    return conn.openUri(uri, options, callback);
  }
  return conn;
};

//Driver dependent APIs
var driver = global.MONGOOSE_DRIVER_PATH || './drivers/node-mongodb-native';
//Connection
var Connection = require(driver + '/connection');
```

### drivers/node-mongodb-native/connection.js

```js
var MongooseConnection = require('../../connection');
function NativeConnection() {
  MongooseConnection.apply(this, arguments);
  this._listening = false;
}
//Inherits from Connection.
NativeConnection.prototype.__proto__ = MongooseConnection.prototype;

//Switches to a different database using the same connection pool.
NativeConnection.prototype.useDb = function(name, options) {
    // Return immediately if cached
    if (options && options.useCache && this.relatedDbs[name]) 
        return this.relatedDbs[name];
}
//Closes the connection
NativeConnection.prototype.doClose = function(force, fn) {
  this.client.close(force, fn);
  return this;
};

module.exports = NativeConnection;
```

#### ./connection.js
     
```js
const EventEmitter = require('events').EventEmitter;
const driver = global.MONGOOSE_DRIVER_PATH || './drivers/node-mongodb-native';
const Schema = require('./schema');
const Collection = require(driver + '/collection');
const mongodb = require('mongodb');

//For practical reasons, a Connection equals a Db.
// @param {Mongoose} base a mongoose instance
function Connection(base) {
    this.base = base;
    this.collections = {};
    this.models = {};
    //...
}

//Inherit from EventEmitter
Connection.prototype.__proto__ = EventEmitter.prototype;

Connection.prototype.collections;
Connection.prototype.name;
Connection.prototype.db;
Connection.prototype.config;

Connection.prototype.createCollection = function(collection, options, cb) {
  this.db.createCollection(collection, options, cb);
};
Connection.prototype.dropCollection;
Connection.prototype.dropDatabase;

/**
 * Called when the connection is opened
 * @api private
 */
Connection.prototype.onOpen = function() {
  this.readyState = STATES.connected;
  // avoid having the collection subscribe to our event emitter
  // to prevent 0.3 warning
  for (var i in this.collections) {
    if (utils.object.hasOwnProperty(this.collections, i)) {
      this.collections[i].onOpen();
    }
  }
  this.emit('open');
};
Connection.prototype.openUri= function(uri, options, callback) {
    //...
    const promise = new Promise((resolve, reject) => {
    let client = new mongodb.MongoClient(uri, options);
    _this.client = client;
    client.connect(function(error) {
      if (error) {
        _this.readyState = STATES.disconnected;
        if (_this.listeners('error').length > 0) {
          _this.emit('error', error);
        }
        return reject(error);
      }

      const db = dbName != null ? client.db(dbName) : client.db();
      _this.db = db;
      //...
      _this.readyState = STATES.connected;

      for (let i in _this.collections) {
        if (utils.object.hasOwnProperty(_this.collections, i)) {
          _this.collections[i].onOpen();
        }
      }

      resolve(_this);
      _this.emit('open');
     });
    });
    return this;
}

Connection.prototype.close = function(force, callback) {
//给前置参数设置默认值🐱
  if (typeof force === 'function') {
    callback = force;
    force = false;
  }
  this.$wasForceClosed = !!force;

  return utils.promiseOrCallback(callback, cb => {
    this._close(force, cb);
  });
};


Connection.prototype.collection = function(name, options) {
  if (!(name in this.collections)) {
    this.collections[name] = new Collection(name, this, options);
  }
  return this.collections[name];
};

Connection.prototype.model = function(name, schema, collection) {
    if (utils.isObject(schema) && !schema.instanceOfSchema) {
        schema = new Schema(schema);
    }
    
    var opts = {cache: false, connection: this};
    var model;
    if (schema && schema.instanceOfSchema) {
        // compile a model
        model = this.base.model(fn || name, schema, collection, opts);
        // only the first model with this name is cached to allow
        // for one-offs with custom collection names etc.
        if (!this.models[name]) {this.models[name] = model;}
        // Errors handled internally, so safe to ignore error
        model.init(() => {});
        return model;
    }
    
     // lookup model in mongoose module
    model = this.base.models[name];
    if (!model) {
        throw new MongooseError.MissingSchemaError(name);
    }
     if (this === model.prototype.db
      && (!collection || collection === model.collection.name)) {
    // model already uses this connection.
    // only the first model with this name is cached to allow
    // for one-offs with custom collection names etc.
    if (!this.models[name]) {this.models[name] = model;}

    return model;
  }
  this.models[name] = model.__subclass(this, schema, collection);
  return this.models[name];
};

Connection.prototype.modelNames = function() {
  return Object.keys(this.models);
};
```
