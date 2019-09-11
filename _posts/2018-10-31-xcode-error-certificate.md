---
title: xcode - ios development certificate revoked
date: 2018-10-31 22:40
categories: blog
tags: [xcode, ios]
---

xcode调试程序的时候iPhone无法验证开发者应用，虽然点击了很多次切换流量尝试在设置里验证也没有效果。

### so how to Refresh provisioning profile devices?

I tried a solution in stackoverflow which is

#### This is what you need to do.

* Go to ~/Library/MobileDevice/Provisioning\ Profiles/ and delete all the provisioning profiles from there.
* Go to XCode > Preferences > Accounts and select the Apple Id.
* Click Download All Profiles. And it will download all the provisioning profiles again.

BUT it did not work for me!

so i went to Library/mobileDevice/provisioningProfiles,rm every provisioning profile in the dir.
download again in xcode.

the file in the dir above wiil show you it revoked n days. that's ok mybe it's just not flushed upto date.


the another wired thing is 
### dyld: Library not loaded: @rpath/libswiftCore.dylib
and this time the solusion on stackoverflow is worked:

For me none of the previous solutions worked. We discovered that there is an "Always Embed Swift Standard Libraries" flag in the Build Settings that needs to be set to YES. It was NO by default!

Build Settings > Always Embed Swift Standard Libraries

After setting this, clean the project before building again.

Now the app can run on iphone😊


