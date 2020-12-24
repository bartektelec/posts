---
title: 'Case study: Lameflix'
excerpt: 'After going through two Udemy courses on React I decided to start my own project from scratch...'
coverImage: 'https://github.com/bartektelec/react-netflix-clone/blob/master/screenshot.jpg?raw=true'
date: 'Thu 24 Dec, 2020'
tags: 'project, react, case study'

ogImage:
  url: 'https://github.com/bartektelec/react-netflix-clone/blob/master/screenshot.jpg?raw=true'
---

## Intro

After going through two Udemy courses on React back in February (2020) I found myself very confused after "learning" so much stuff in a short period of time. I decided to start my own project from scratch.

The source code for the project can be found [here.](https://github.com/bartektelec/react-netflix-clone)

You can view the live result [here.](https://bartektelec.github.io/react-netflix-clone/)

## Video courses are overrated

I wrapped the word "learning" in quotes for a reason - now, after some time has passed I no longer believe in online courses (like the ones found on Udemy) to be a way of "learning", at least not for myself. I've done multiple courses on the platform before and it always ends up with me just watching the videos and not being able to use the _knowledge_ later. This **passive** way of learning is not right when learning something technical, that's why from now on I decided on learning stuff on my own, by actually doing a project from start to finish, and reading the docs when I am stuck. I find this way of learning to be much more effective, because in the end it's our practical abilities that matter.

## Confusion

The thing is, working with library such as React differs a lot from the workflow we're used to when working with plain HTML and JavaScript.
From now on the whole workflow was based on reactive properties (states), props, life-cycle methods and context.
It was just a lot to process, and like always I barely could code a simple To-Do app after going through those courses.

## Project

I've spent approx. 1 month on building the project, first commit being 10th of March. Obviously if I've started today I would've probably finished it in a week, but like I said I wasn't really sure what I was doing back then.

I chose to use most of the tooling I've "learned" from the video courses, besides Redux.

Storybook was used as a UI testing library, as well as a documentation software for all the components that were styled for the project. Storybook is a tool where we can add our components, set the states these are available in and view how they look.

I used styled-components, as a CSS-in-JS styling library, which I still find great, the main thing I like about styled components is being able to pass props to the CSS values, as well as it being completely scoped from all of the other components. Styling with styled-components is actually extending a plain HTML element using `const StyledWrapper = styled.div` syntax, and adding all the styles as a template literal. It accepts SCSS, as well as media queries, and generates a JSX element with new unique classname.

## Blockers

I remember when I found out I was passing my props multiple levels down the React component tree and I knew I had to use some kind of global state. The problem was I had no idea how Redux or Context works. It obviously took some time to figure it out but I managed to solve it with the gorgeous `useContext` hook.
Yes, I used hooks for my project, even though most of the stuff I've learned from the video courses was Class-component based, there was just a short mention about hooks. I always like to learn from challenging myself, and that's also how I learned to use hooks, which I now find a much cleaner way of managing state in components.

## Improvements

I later improved some of the code, mostly using optimization techniques I've learned since.
When I coded the app in the first place I had no idea about the good and bad practices in React. I guess it's both a blessing and a curse that React gives us so much freedom to do stuff the way we want.
Wrapping my asynchronous API calls in a useEffect hook prevents the app from sending a request everytime the component rerenders.

Wrapping my event handlers in a useCallback should increase the app's performance.
I was planning on using PropTypes library for static typing of component props, but since then I've learned to use TypeScript so I might as well rewrite the whole app in TypeScript instead.
I can tell the file structure in the project is difficult to understand, I don't know what I was thinking back then.
I still see some room for improvement, and I might rewrite the whole app in the future.

## Conclusion

It's been over 9 months since I started this project, a lot has changed and I've learned a lot since, however there is still plenty I don't know. React may seem easy to start with, however there is much more to learn than just passing props and changing state. Even though I feel very comfortable using React nowadays I still cannot say I know everything.
