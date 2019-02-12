---
layout: post
title: "Vue at SimpleNexus Part 1: Why We Chose Vue"
tagline: ""
categories: rails, vue.js
author: "David DeGraw"
---

Vue at SimpleNexus Part 1: Why We Chose Vue

I joined SimpleNexus in March of 2017 as the third dedicated member of the web team. At that point, our app was Ruby on Rails which drove some web functionality as well as an API for our mobile apps. The Rails stuff looked fairly typical for a Rails application: erb files with some jquery sprinkled in here and there. It worked great!

Previously to joining SimpleNexus I had been working as a front end developer (AngularJS). In contrast to a modern fancy javascript single page application, the Rails app felt slow, clunky, and outdated. More than that, the code, particularly the views, felt poorly organized and was a bit of a mess. Some erb files had random javascript in them, while other moved all the javascript to partials which were included. There were erb templates that contained logic that should have been in a controller, controllers that contained logic that should have been in a model, and models that contained logic that should have never existed in the first place!

It might sound like a critisizm of the team I joined, but it's not. Tech debt is simply a side effect of moving really fast and building a product that experiences a hocky stick line of growth. Every line of code you write will someday be tech debt.

At this point, it became clear that the development team could easily double, triple, or even quadruple in size within the year. Looking forward to that point, it was evident we needed to reign in the code style, patterns we were using, and processes we used before the team grew much bigger. I'm a big believer that simply being aware of issues and thinking about them is most of the battle.

On one hand, our product felt clunky by modern web standards. On the other hand, we had tech debt issues specifically when it came to our views.

Several solutions came to mind immediately. We could double down and refactor our Rails views and establish clear patterns and style guides on how to structure them. This is the strategy The Outline has used with their Elixr/Pheonix app and it seems to be working well for them. However, as we considered the growth of the company, we also had to take into account how we could scale the development team. How easy is it to find Rails developers or teach people Rails? Are there other technologies that might be easier to grasp or more common that developers were using?

Obvioiusly the big elephant in the room is that javascript was (and still is) the new hotness that everyone seems to simultaneously love and hate. It would be disingenuous if I tried to act like that wasn't on the top of our minds when thinking of our problem. It was also pretty clear that javascript in it's various forms was here to stay. Scaling a development team hiring qualified javascript developers would likely be easier than hiring qualified Rails developers.

But javascript is annoying/hard/changes too often/bad/good/slow/fast! I still remember the first application I worked on that used javascript beyond jquery. It was a Rails app as well. We were using AngularJS, written in coffee script, inside of Rails templates written in HAML. It was horrible and I was convinced I was just too stupid to understand javascript. That served as a pretty clear example of what we should not do.

Which javascript framework should we use? Angular 4 was new and seemed cool, React seemed destined for dominance. Was meteor still a thing? I heard about Choo.js, is that going to take off? Choosing a javascript framework felt like playing the stock market!

We decided to have a javascript framework "bake off". Each developer would just choose a framework that looked appealing to them, write a little app to consume one of our API endpionts, and display some data.

Having come from an AngularJS background, I was immediately drawn to Angular 4. I remember watching a series of YouTube tutorials on it. Each episode was maybe an hour long, and there were maybe 8 videos. By video 3, we hadn't even started building a hello world app and I gave up.

By chance a co-worker had mentioned that Vue seemed pretty cool. I was immediately put off, thinking it was simply another flash in the pan javascript framework simply adding to the cruft and confusion of front end development. After my negative experience with Angular, I decided to give the docs a read. When I was doing AngularJS, I absolutely despised their docs. There were no examples, none of the pages seemed connected in any way, it seemed impossible to grasp. I was immediately surprised and happy to see how well written Vue's docs were (and still are)! It's like some actual human wrote them for an actual human to read and use! Still to this day, I tell people to just check out the docs if they're interested in Vue. They're just that good!

Reafirmed, I decided to try and spin up a quick Vue app to see if it would hold up. Again, I was immediately inspired by the vue cli which seemed to be desperately lacking from the other framework options (and seems to now be standard practice). All I had to do was vue init and then yarn start and I had a working app with all the complicated stuff (webpack, babel, eslint, etc) built in? I was sold. I literally had my Vue app consuming our API within an hour.

Honestly, from that alone I was completely sold. The fact that I, a mere mortal, could build a Vue app with no previous experience in a matter of hours was enough. Presumably the future developers we would hire would be smarter than me and could pick it up even faster.

The other thing that became pretty apparent early on was that writing Vue code didn't feel like writing Vue code. It felt like I was just writing HTML, CSS, and javascript but in a controlled envoriment that gave me really sophisticated composition patterns. The single file .vue components felt a little bit weird at first, but very quickly I grew to totally love them. Scoped styles just made so much sense as well, I couldn't believe that wasn't standard practice.

I think that's the best way to sum up how I felt about Vue: just like the docs, Vue was written by an actual human and designed for actual humans to use. It felt very similar to Ruby, designed for humans, not machines. I'm a big fan of this quote from Yukihiro "mats" Matsumoto on his philosophy for creating Ruby:

Often people, especially computer engineers, focus on the machines. They think, "By doing this, the machine will run fast. By doing this, the machine will run more effectively. By doing this, the machine will something something something." They are focusing on machines. But in fact we need to focus on humans, on how humans care about doing programming or operating the application of the machines. We are the masters. They are the slaves.

Similarly, Evan You, creator of Vue, has expressed similar philosophies:

When you want to succeed at something, it’s really important that you feel that you’re naturally inclined to enjoy working on it.

The day of the framework "bake off" came. Various solutions were presented, and it was just a night and day difference. None of us were strong javascript developers. Even others that were presenting other frameworks were more excited to use Vue than the frameworks they presented.

Over the last two years since adopting Vue, it's clear that those inital impressions were accurate. As part of our development onboarding process, we have a goal to have every new developer commit and deploy production code on their first day. It's pretty telling when even a mobile developer will end up commiting some Vue code on their first day.

We host the Utah Vue meetup at our office, and a running joke is that we keep running out of things to talk about because Vue is so simple. Certainly there are topics that are more advanced than others, but it seems like people come accross the complex stuff naturally. In other technologies, there seems to almost be a bait and switch where the intuitive thing to do is actually a bad idea. Vue just seems to provide a set of tools and there's an intuitive right tool for the right job.

We could have doubled down on Rails views, or chosen another framework, or something else, but I think it's safe to say we're extremely happy with Vue and where it's allowed us to end up.
