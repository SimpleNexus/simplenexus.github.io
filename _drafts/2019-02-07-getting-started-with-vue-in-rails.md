---
layout: post
title: "Getting Started with Vue.js in Rails"
tagline: "This post demonstrates how you can get started using Vue.js in Rails without a build process."
categories: rails, vue.js
author: "Rich Corbridge"
---

# Getting Started with Vue.js in Rails

TLDR; You can use Vue.js in Rails similarly to how you would use jQuery.

In this post we will explore how to get started using Vue.js in Rails without a build process. If you are coming into this without much javascript experience then this post will be enough to get you started with Vue in Rails.

Once you fall in love with Vue (and you will) you'll want to use more of Vue's features like re-usable components, routing, and state management. At that point you will want to create a formal build process for your Vue app(s).

I write "app(s)" because you do not have to go "all in" with Vue and convert your entire Rails-based web app into a Single Page Application (SPA) all at once. You can incrementally convert chunks of your web app to Vue. We are still in the process of converting our web app to Vue chunk by chunk. We have Rails pages along side our stand-alone Vue apps. Some times we've even thrown in some Vue on a Rails page. To do this correctly requires a build process. Setting up a build process is beyond the scope of this introductory post.

## Two Simple Steps

There are two simple steps to get started using Vue inside of your Rails views:

1. Get Vue into your Rails view.

    1a. EASY: Include a script tag in your Rails view file. You can download and host the `vue.js` file yourself or link to it from a CDN.

    ```
    <script src="https://unpkg.com/vue@2.5.9/dist/vue.js"></script>
    ```

    1b. LESS EASY: Download the minified Vue source from the link above (or another) and add it to your existing assets infrastructure. Don't worry if you don't know what that is you can use method 1a above.

2. Connect your Vue app to an element in your Rails view.

    ```
    <!-- this selector (id="app") needs to match "el: "#app"" below in the script tag -->
    <div id="app">
        <h1>{{ capitalize(title) }}</h1>
        <h2>{{ currentDate }}</h2>
        <h3>{{ amount | formatMoney }}</h3>
    </div>

    <script src="https://unpkg.com/vue@2.5.9/dist/vue.js"></script>

    <script>
    new Vue({
        el: "#app",
        created () {
            // Do something when the app is created like retrieve some data.
            // Maybe the response.data object from the server includes an `amount`.
            this.amount = response.data.amount
        },
        filters: {
            formatMoney (text) {
                return '$ ' + text + '.00'
            }
        },
        data () {
            return {
                title: 'My First View App',
                amount: ''
            }
        },
        computed: {
            currentDate () {
                return new Date()
            }
        },
        methods: {
            capitalize (text) {
                if (!text) return ''
                return text.toUpperCase()
            }
        }
    })
    </script>
    ```
Not all of that code is needed to get started. I included some little bits of Vue to show how they could be used. Let's go through them briefly:

- **`new Vue({})`**: This creates a new instance of Vue on the page so that you can use it.
- **`el: "#app"`**: This tells Vue where to mount itself on the page.
- **`created`**: This is a life-cycle hook and runs when the Vue app is instantiated on the page. You have access to the entire Vue instance within the `created` hook. This is usually used to retrieve data for the app. We use [axios](https://github.com/axios/axios) for all of our async javascript request needs. You can include it on your page in the same way you included Vue via script tag above. The other most commonly used life-cycle hook is the `mounted` hook. For purposes of this introduction they are about the same.
- **`filters`: These are methods that can be used in the template/HTML part of the app and are generally used to format data.
- **`data`: This is where the app's data should be declared. Even if you plan to populate the data in a life-cycle hook you should declare the variables in the data element so that Vue knows to watch them for reactivity purposes. If you do not declare your data here and then try to add it later you app might not behave like you think it should. It is important to note that this is a function that returns an object.
- **`computed`: Computed properties are methods that take no input arguments and return a value. These methods have access to the Vue data properties via direct reference like `this.amount` or `this.title` from our example.
- **`methods`: Methods are functions that can take input arguments and do something. Methods do not have to return anything.

The `created` life-cycle hook/method isn't required but you should use it to load your page with data. The `created` and/or `mounted` life-cycle hooks/methods are probably the best way to get data into your stand-alone Vue app.

Another less-desirable way to get data from Rails into your Vue app involves ERB in javascript and is presented below. It is less desirable because it makes your Vue code coupled with your Rails view code and can be a mess to maintain. Here's how you might do it but remember it is better to use a `created` or `mounted` life-cycle hook to retrieve the data directly from the server. It is only presented as an option to use if you are unable to get your data from the server. We used this pattern initially in one of our apps and we recommend moving away from it as soon as you can if you have to use it.

```
<script>
// NOTICE THE ERB INSIDE THE SCRIPT TAG AND OUTSIDE THE VUE INITIALIZATION
 var title = <%= @title %>
 var user  = <%= @user.to_json %>

 new Vue({
     el: "#app",
     data () {
         return {
             title: title
         }
     }
 })
 </script>
 ```

 This was a brief introduction to getting started using Vue.js in a Rails app. For more details on how to use Vue.js their [documentation](https://vuejs.org/v2/guide/) is fabulous.