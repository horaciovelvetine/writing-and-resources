# Devlog 5:  September 29 - (October) 5, 2025

Having set out and planned the data model last week, this week was spent initializing the data model as planned inside of the Spring Boot API. The original data model contains just enough infrastructure to be able to send requests locally from the Client to the API, and the first official step into integration testing. The first two requests are the `GET api/status` and the `GET api/search-results`, these will be a sanity check to make sure the user isn't accidentally sending requests to an offline or unusable API, and the first fetch of data from the Wikidata API. With these requests available the journey to begin the landing page is well within reach!

## Frontend Tooling: 

Like a lot of projects having some (enforced) consistency is will help ensure that the Frontend doesn't end up a mess of tabs and spaces. This job is normally relegated to your favorite Linter and Formatter, in this case [ESLint](https://eslint.org/), and [Prettier](https://prettier.io/) will do the trick. Having no strong opinions for the configuration (or use of these tools), were sticking with the defaults and setting each up as instructed. Try these docs if you want some guidance or more details on those:

- [Getting Started with ESLint](https://eslint.org/docs/latest/use/getting-started)
- [Prettier Installation](https://prettier.io/docs/install)

Since this project is starting with the remanent of an incomplete refactor already in the works, there is also a significant amount of cleanup and maintenance which will be done to save but remove old code. 

## API:

The Backend will receive the same tooling, with Prettier being used (again) to enforce some consistency. After implementing the core of the data model, the first chance for updates to the API is creating the: `LayoutSettings` class. This class will be responsible for keeping track of the settings from the Client that the backend needs to know about to complete the layout process. 

```java
public class LayoutSettings {

  private Number attractionMultiplier;

  private Number repulsionMultiplier;

  private final Number vertexDensity;

  private final Number maxLayoutIterations;

  private final Number maxIterationMovement;

  private final Number temperatureCurveMultiplier;

  private final boolean prefers3D;
  
  //  ... implementation details omitted for brevity...
}
```

- `attractionMultiplier` - the strength/force used to pull related `Vertex` towards one another, the higher this number the more likely Vertices are to clump up.
- `repulsionMultiplier` - the strength/force used to push all `Vertex` away from each other (even if they are related!), the higher this number the more likely Vertices are to spread out. 
- `vertexDensity` - the allotted space provided for the entire `Graphset`, the lower this value the smaller the layout.
- `maxLayoutIterations` - how many iterations of the layout algorithm are run at most. This number isn't always reached but is a hard cap on the number of times the layout process will run and allow each `Vertex` position to be 'optimized'. If you reach the base temperature before this the layout will exit earlier, but this serves as the 'hard stop' ending point to prevent runaway layouts. 
- `maxIterationMovement` - the largest distance (in a non-specific type of units of distance) that an individual `Vertex` can move in a single iteration. 
- `temperatureCurveMultiplier` - how severe the annealing calculations are, the larger this value the slower the temperature will cool across the overall system (allowing a `Vertex` to move further longer, possibly preventing settling in a 'bad' solution).
- `prefers3D` - whether or not the layout should be completed in 2- or 3-dimensional space. 

## The 2D vs. 3D Debate:

In the world of Visualization, the presence of 3D is often seen as an unnecessary or unhelpful addition to a tool. Arguing that since you are inevitably using a device which uses a flat screen, our ability to perceive depth (which is poor to start with) is almost a useless when synthesized on a 2D screen. This project is as much about learning as it is building something cool, and this is an excellent chance to learn this rule for myself. Having a single tool which can alternate between 2D and 3D layout's (hopefully) on the fly, should allow a single dataset to be examined easily in both domains. The original conception of this project was entirely focused on the idea of 3D, and with VR and glasses tech continuing to advance, and 3D rendering capable with the `WebGL` mode of [P3.js](https://p5js.org/reference/p5/WEBGL/), there’s plenty of reason to explore this space and learn for ourselves why so many visualization experts consider 3D a bad fit for network graph visualizations. 

## Conclusion: 

With an implementation of the base data model outlined in the previous 2 weeks and the added wrench of being 2D and 3D compatible, this week is all about building the first iteration of the foundation. Back next week to start filling in more details. 

brb,
@horaciovelvetine

