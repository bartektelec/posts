---
title: "Shuuz - my first Vue project"
excerpt: "Being given a task of creating a small e-commerce website I wanted to raise the bar a little..."
coverImage: "https://github.com/bartektelec/shuuz-client/raw/master/screenshot.jpg"
date: "Fri 25 Dec, 2020"
tags: "project, vue, strapi, case study, e-commerce"

ogImage:
  url: "https://github.com/bartektelec/shuuz-client/raw/master/screenshot.jpg"
---

## Intro

This project was an assignment for Noroff's Semester project. Being given a task of creating a small e-commerce website I wanted to raise the bar, I felt like a small e-commerce website was something I could easily create without using any libraries or frameworks. I have been reading about Vue lately and wanted to learn it, this project was perfect fit for it to be my first ever Vue project - it was complicated enough to make good use of all the tools the Vue ecosystem is built on.
![man jumping out of a car and chasing it](https://media.giphy.com/media/XKSa6XxpmHh1NEBvvl/giphy.gif)
I decided to go for Vue 3 (which is still in beta now), Vue Router, Vuex and Tailwind on the front-end. We were told to use Strapi as the backend, which I find a great tool for this task.

The source code for the project can be found [here.](https://github.com/bartektelec/shuuz-client)

You can view the live result [here.](https://bartektelec.github.io/shuuz-client/) Please keep in mind the server needs about 30 sec to start when you visit the website.

## UI Design

I started out with drawing wireframes of different pages in figma, the website was going to be a shoe e-commerce site, so websites like zalando.no, nike.com etc were a huge inspiration for the way I structured my pages.
I ended up drawing wireframes for the homepage, product/category page, product detail page, checkout page and the admin dashboard both on desktop and mobile.
![Figma wireframes](/assets/shuuz-wireframe.png)

After I have decided of what main, secondary and accent colors, as well as fonts I am willing on using for the project I started drawing simple components like cards in figma. It took me approx. 1 week to be finished with the whole design. Sticking to my predefined colors and fonts, as well as spacings made it easier to design, but also more appealing to the user.
![Full website design](/assets/shuuz-design.png)

## Coding

I found Vue to be very easy to start with, I believe anyone jumping straight out of HTML + Vanilla JS into the Vue train will enjoy their first ride.
Creating components in Vue is straight forward, these are included in their separate files, spread in to 3 sections that are template, script and style. This is just as easy as writing plain HTML and JS, or maybe easier? Why?
Vue does many things for you, and for most of this stuff it uses its own directives that look like custom HTML attributes.
`v-if, v-else, v-on:click, v-bind, v-model` are just examples of the Vue directives, and these are so easy to learn that there is no explanation needed.

Vue has it's own way of handling state, lifecycle methods and functions, which prevents the user from making a mistake and declaring a event callback function inside of a component on each re-render like many newcomers do when using React.

![Boom so easy](https://media.giphy.com/media/3o7btNa0RUYa5E7iiQ/giphy-downsized.gif)

Building components with Vue has been very easy and enjoyable from day one.

## Managing state and logic

The fun thing when using Vue compared to React is that Vue is a fully functional framework, not just a view library.
Vue has its own router and store module, which are just great and as easy to learn as Vue itself.

Vuex follows the same data-layer architecture as Redux - called "Flux". It is based on a store - being a wrapper around all the global states, mutations - which are exacly what they're; synchronous functions that mutate the state, actions - which perform asynchronous tasks, and getters - which are just a gorgous way of exposing our state in different ways.
Vuex was easy to understand, and easy to use, mostly because it is accessible from every Vue component in the app. It doesn't need to be imported or initiated in any way, it can be referred to from `this.$store` which is just great.
The only difference is when we want to use Vue 3 Composition API, which is the new API for managing the script part of the component. It is similar to React hooks and functional components in some way, and makes writing the logic inside of components much clearer.

![No groaning in my store animation](https://media.giphy.com/media/l2Je6Ae3mKzobqwbS/giphy-downsized.gif)

I used Vuex to store all the global state in the app, like user's JWT token, products in basket, favourites list and all the available products.
Using actions and mutations helped me a lot when doing features like storing current states in the localStorage.

## Boo-hoo TypeScript

I've been a huge fan of TypeScript recently, it made me code faster and more secure for the past few months, that's why I wanted to try out Vue 3 with TypeScript. Based on how great TypeScript works with React my expectations were really high, and Vue let me down in some way. Vue doesn't handle TypeScript as well as React does, it let me code a loosely typed code that I am no longer a fan of. For some reason it acted like it didn't really care. However I can't be too mad about that, they announced Vue 3 will support much more TypeScript but I used a beta build of Vue 3 so all I can do is wait and hope they will improve it later.
![Boo-hoo animation](https://media.giphy.com/media/WDelIGXnGdGdq/giphy-downsized.gif)

## Conclusion

The final product ended to be a simple e-commerce website, with admin dashboard for adding, editing products, and customizing the hero banner. There is still some room for improvement and I have already added a feature where you can zoom in the product picture when pointing your cursor on the image itself. I enjoyed working with Vue, even though I know there are multiple things I could've done better, it was really fun - and easy! Vue is a great tool that I am most likely going to use more in the future, because of it being so easy-going and all the modules being so well-fitted in the framework.
