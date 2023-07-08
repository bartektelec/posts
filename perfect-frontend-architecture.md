---
title: "On a quest to build a perfect frontend architecture"
excerpt: "Working on a project for years forces us to take a deep breath and rethink our architecture once in a while, doing that in smaller steps helps us adding more features..."
coverImage: "/assets/builder.jpg"
date: "Fri 7 Jul, 2023"
tags: "frontend, architecture, project management"

ogImage:
  url: "/assets/builder.jpg"
---

## Intro

Working on a project for years forces us to take a deep breath and rethink our architecture once in a while, doing that in smaller steps helps us adding more features to the project without putting the whole development on hold for weeks or even months.

## Problem
![Collapsing building](https://media.giphy.com/media/geDYq73orMiMLQ1bH5/giphy.gif)

I have been working at my curent job for 2,5 years now and I have seen the project since it's initial commits.
The decision has been made to make the stack rather exotic - Svelte as a renderer, Tailwind as a CSS abstraction and TypeScript to let us sleep better at night. Later we also decided on adding some Rust WASM logic in.

Just like most frontend projects we started tiny with a very well known folder structure to most frontend developers, splitting the source files into:
- components - the most common pattern you can spot throughout frontend projects, a singular directory where you'd put all your custom components
- constants - we do have hardcoded values in our code so you will want to place them somewhere to keep it clean
- icons - well these are SVGs that we have converted to .svelte files so we can change different properties on them, these could just be placed in the components directory if one prefers it that way
- services - the portal to another dimension, here lay all the API related queries and commands
- stores - at some point you will need a global state in your application and that's where we would put it, in React world you'd probably use something like Redux (is it still a thing?)
- styles - if you go on Twitter and read people fighting about wether using Tailwind is a good idea or not, you will see many people are against it because it makes the markup cluttered, well - we have noticed that it is true and sometimes it makes sense, but as you develop custom styles for your elements you can either encapsulate them in a custom component, create a separate style for them using the Tailwind config or just create a CSS class with @apply - we found the latter to be the easiest to maintain in the long run
- utils - the famous util folder, he lays pretty much EVERYTHING that doesn't fit anywhere else, let's face it - it is a pile of garbage
- views - often called pages, is a directory where only the page related components are placed, and in the beginning we would only put the components that could be called 'a page' in here
- types - a set of d.ts files that simply model our data

This structure worked fine as long as the app was poor in features, but now as the app has grown to be over 30 pages, each handling a different use case and context; the structure has started to evolve by itself.
Continuously working on the project we could notice where the some of the patterns started failing, applying changes as we go, we would find some way to allow the app to scale.

In the beginning the app would consist of a couple of tightly coupled features, but after a while the business needs increased and the app became a set of features which were either tightly coupled or not related to each other at all.

One of the biggest mind shifts was to allow ourselves to include other feature-related files in the pages folders, from now a page would simply mean a feature and most of it's logic and components would be stored there unless they can be reused.
This allowed us to get rid of clutter from components, utils and stores directories where we used to mix different features before.

Another crucial shift was to get used to inferred types instead of relying on type definitions we annotated ourselves. 
A tool that helped us a lot with that is `zod` - a TypeScript schema validator and parser. 
From now on we would be parsing our API responses to make sure they match the contract instead of assuming that the data we receive is structured correctly. 
This would also allow us to handle invalid API responses in the runtime and handle them as errors.

## Moving forward

I have spent last week on reading about different architecture approaches - Layered, Hexagonal, Onion etc.
Most of these architectures have a lot in common, but they are usually well described in context of building a backend and some parts simply can't be transcribed to frontend directly.

No matter which architecture mentioned above you pick, you will find they usually focus on having a Domain layer as a base, this layer usually consists of entity definitions and code that describes the business rules using the same language as the domain experts in your business would use.
Sometimes this layer is wrapped in some kind of repository pattern or splits the domain into model and services.

Next, usually you will either have one or more layers related to Application services or use cases, that are simply pieces of logic that we need to apply on top of the domain layer to be able to structure your procedures. 

Other layers will usually be some kind of ports and adapters that form a Infrastructure layer that can be split up into Gateways, Controllers and Presenters that are supposed to serve the UI, Devices, DB and generally the outside world.

## Frontend duties

What tasks do we perform on the frontend?
Well, obviously frontend and backend do completely different jobs, and even if in theory the backend should take care of most domain related logic it sometimes can't.

Most frontend applications would do just fine as an interface to the backend - we create a form, maybe validate it and then send it to the backend to do the rest for us.

The truth is that it's almost never as simple as that, because if it was we would not need all these JavaScript frameworks that we rely on nowadays.
Our goal is to serve the end user and provide them with a stable, snappy user experience - and there is nothing as annoying as a poor frontend that crashes without a reason. Say what you want, but if the API takes 2 seconds to respond or it throws a 500 in my face I will be fine. However if my frontend crashes and I lose all my unsaved data because somewhere in the code there was an unhandled null and I can't do my job because of that? Oh boy that grinds my gears.

Looking at the folder structure I added above, a basic frontend app would take care of multiple state values for improving the user experience - is the page loading, are we seeing an error, light mode, dark mode, keyboard navigations, modals, dropdown, tooltips, preferences, quick access menus. Additionally an app is usually a stitch of commonly used components like dropdowns and modals, as well as feature and page specific components like fligh seat selection component. 

If an application consists of many features it should be split up using these, if it doesn't then maybe it shouldn't.

We also have some form of connection to the outer world - REST API, WebSockets and also storage like cookies and localStorage.
And in case we need to program some business related logic we should be speaking the domain language.



// draft
// design oriented on features and domain needs
// dep incjection for maintainability
// ....
