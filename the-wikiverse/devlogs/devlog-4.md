# Devlog 4:  September 22-28, 2025

The Wikiverse project continues! This week was spent breaking down features for the UI/UX into individual targeted components and reviewing a previous incomplete refactor which had been started with the intention of moving this project into a mono-repo which would include both the Front and Backend. Since working on that refactoring I've had the chance to work with [p5.js](https://p5js.org/) on my portfolio site and gain a better understanding of the [React-P5-Wrapper](https://github.com/P5-wrapper) and how the two are meant to work together. 

## State:

The [React-P5-Wrapper docs](https://github.com/P5-wrapper/react) are sparse because of just how simple (and effective) this library is, not getting in the way and not including any unnecessary bells and whistles. It can be easy to miss what is arguably one of the most important pieces of this library, synchronizing and using state from React inside of your sketch. The [`.updateWithProps()`](https://github.com/P5-wrapper/react?tab=readme-ov-file#reacting-to-props) introduces a 'listener' which is both called on mount and called whenever a prop is updated. The base `ReactP5Wrapper` only requires a sketch to be passed (providing you with the centrally important `P5CanvasInstance`), but you can pass any props into the wrapper as well for use inside the sketch itself. There are a few things worth knowing about this: 

1. Using TypeScript (as this project does) it's helpful to create a `MySketchProps` interface containing the definition of the props you are going to pass, and when you do make sure this interface extends the `SketchProps` type provided by ReactP5Wrapper, if you don't you can end up with some type errors which... well they aren't super helpful. 
   
   ```typescript
      import { SketchProps } from "@p5-wrapper/react";

      interface MySketchProps extends SketchProps {
        // State Values Being Passed here...
      }
   ``` 

2. The call order for the `.updateWithProps()` does end up having this called before the `setup()` function (once) and `draw()` loop begin, so if there are any establishing variables you need for the sketch itself you should be able to initialize the sketch with the needed values. 

## Style: 

The usefulness of [TailwindCSS](https://tailwindcss.com/) has led to it being incorporated into this project for simpler styling and more consistent components. The original prototype didn't include this, but the repetitive CSS files and creating IDs for every component or classes for all the shared styling got a lot more cumbersome over time, and the schedule for completion this semester has this project crunched for time as it is. There will still be some CSS included for specific design choices, with the lion share of the spacing and other repetitive tasks being handled with the old: `className='flex'`. 

## Conclusion

Not a ton to go over this week, the focus was on the Frontend doing mostly review and outlining the scope.
Brb @horaciovelvetine