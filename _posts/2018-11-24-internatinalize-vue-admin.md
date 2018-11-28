---
title: internatinalize vue-admin-template
date: 2018-11-24 17:00
categories: blog
tags: [VUE, web]
---

An admin framework will be ok for a flying control project. I found the [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin) by PanJiaChen on github, and it's a too much for the SPA. according to the Readme a simple [vue-admin-template](https://github.com/PanJiaChen/vue-admin-template) of the same coder came up. It fits the project and the international languages support is important but missing. so that's what i did to the template.

### steps to add the vue-i18njs

* 1 install vue-i18n & vue.use(i18n) & enable this.$i18n
* 2 link to vuex.store.getters & store language to Cookies.language
* 3 translate files zh.js|en.js & initialize VueI18n
* 4 make object symbols fit for translate {{ $t(objTitle) }}
* 5 find the function translatedTitle(title) for titles in router views

### work flow handleSetLanguage->setLanguage->SET_LANGUAGE
* src/main.js: Vue.use(ElementUi,{i18n: (key, value) => i18n.t(key, value)})
* src/main.js: new Vue({el: '#app',  router,  store,  i18n,  render: h => h(App)})
* src/views/layout/components/Navbar.vue: LangSelect
* src/components/LangSelect/index.vue: methods:{ handleSetLanguage(lan) }
* src/store/modules/app.js: app.state.language: Cookies.get('language')||'en' app.actions:{ setLanguage(){commit('SET_LANGUAGE',lang)} }
* src/store/getters.js language: state => state.app.language,


### user components LangSelect is the core
```html
<template>
  <el-dropdown trigger="click" class="international" @command="handleSetLanguage">
    <div>
      <svg-icon class-name="international-icon" icon-class="language" />
    </div>
    <el-dropdown-menu slot="dropdown">
      <el-dropdown-item :disabled="language==='zh'" command="zh">中文</el-dropdown-item>
      <el-dropdown-item :disabled="language==='en'" command="en">English</el-dropdown-item>
    </el-dropdown-menu>
  </el-dropdown>
</template>

<script>
export default {
  computed: {
    language() {
      return this.$store.getters.language
    }
  },
  methods: {
    handleSetLanguage(lang) {
      this.$i18n.locale = lang
      this.$store.dispatch('SetLanguage', lang)
      this.$message({
        message: 'Switch Language Success',
        type: 'success'
      })
    }
  }
}
</script>
```
the most importand three lines of this file is
* @command="handleSetLanguage"
* this.$i18n.locale = lang
* this.$store.dispatch('SetLanguage', lang)

### what's i18n?
```js
//  Adds data that is used to translate
i18n.translator.add({
  values:{
    "Hello": "こんにちは"
  }
})

//  Then translate something
i18n("Hello");  // -> こんにちは
```
