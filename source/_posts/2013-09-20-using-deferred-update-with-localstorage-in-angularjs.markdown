---
layout: post
title: "Using deferred update with local storage in AngularJS"
date: 2013-09-20 10:33
comments: true
categories: 
---

I've always used client side caching (when possible and applicable) to provide a responsive UI to end users, especially on mobile applications. Lately, have been checking out the AngularJS framework but couldn't find examples on how to implement this technique. Definitely [breeze.js](http://www.breezejs.com/) works out well, but I wanted something lightweight and easy to use without all the entity framework stuff.

My main concern was that <code>$q</code> doesn't have something like a <code>notify()</code> method. Recently, I stumbled on [Ben Nadel's work on DeferredWithUpdate.js](http://www.bennadel.com/blog/2431-Resolving-An-AngularJS-Deferred-Object-Twice-With-DeferredWithUpdate-js.htm). He has nicely wrapped up the promise / deferred implementation (<code>$q</code> class) with additional <code>update()</code> and <code>mistake()</code> methods.

I have pieced in a project that uses his deferred implementation with angular-local-storage to demonstrate how powerful this can be, here is the [github link](https://github.com/santthosh/angular-cached-demo). Take a look at the [recentPostsService.js](https://github.com/santthosh/angular-cached-demo/blob/master/app/scripts/services/recentPostsService.js), which fetches the recent posts from Lithosphere (Lithium's community portal). Here we use <code>DeferredUpdateModule</code> instead of <code>$q</code> and magic happens in these lines

```javascript
      posts.get(request, function (response) {            // dispatch the api call
                                                          // when server returns with response
        localStorageService.add('recent',response);       //   add response to local storage
        deferred.resolve(response);                       //   resolve previously deferred response object
      });

                                                          // but we don't have to wait for API response now!
      var response = localStorageService.get('recent');   //    check the local storage for a cache
      if(response != undefined)  {                        //    if present
        deferred.resolve(response);                       //        just resolve it right now,
      }                                                   //        server response will come and overwrite it

      return promise;
```
You can play around with the [working prototype here](http://angular-cached-demo.herokuapp.com/#/). With all the awesomeness of AngularJS, changes to your services will be trivial and there won't be any changes to your controllers. Also here is a quick demo of the experience

{% video http://s02.savefrom.net/media/883667518/a16b4d970741351bf9119db0b26f4add/angular-cached-demo.mp4 640 320 http://errands.io/images/angular-cached-demo.png %}





