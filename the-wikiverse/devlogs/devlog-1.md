# Devlog 1: September 2-8, 2025

![the Wikiverse Alpha: Wikipedia Results](https://raw.githubusercontent.com/horaciovelvetine/writing-and-resources/main/assets/wikiverse-wikipedia-result-v1.png)

The Wikiverse began as a project in 2023 with the question: “What does Wikipedia look like?” Over the following year I built out a prototype independently spare time nestled between full-time work and a professional pivot towards becoming a developer. In that time, the prototype I built became a stable Alpha application that showed enormous promise as a tool for learning and exploring topics on Wikipedia. 

Now in 2025 as a part of my coursework completing a Bachelor of Computer Science from the University of Colorado Boulder, I’m excited to be able to devote significant time towards realizing this project fully in creating a novel tool for exploring Wikipedia. 

In building the prototype it became clear the ‘Wiki’ community is exceptional. Full Stop. The resources, projects, people, and every facet of this enormous domain collectively demonstrate a dedication to the free sharing of knowledge that is tremendous, and more often these days a beacon of light in an otherwise darkening world.  This project (at the time of writing) is not affiliated with any Wikimedia entities – but aims to uphold the goals laid out by the Wikimedia Foundation of sharing knowledge freely and widely so that the world might tomorrow be a little bit brighter. 

## Structure and Goals

Through the end of this year (2025) the primary goal will be completing a Beta for public release. The application will exist in two primary pieces: a backend Java & Spring Boot API for access to data and the layout process, and a frontend TypeScript React UI/UX.

The backend relied on two major tools throughout the prototyping phase worth mentioning: the [Wikidata Toolkit](https://github.com/Wikidata-Toolkit/Wikidata-Toolkit), and [JUNG]( https://github.com/jrtom/jung). The Wikidata Toolkit is a library which provides access to the Wikidata API and a host of useful tools for searching, fetching, and handling Wikidata entities. If you are unfamiliar with Wikidata go check out my [intro writeup](https://github.com/horaciovelvetine/writing-and-resources/blob/main/the-wikiverse/topics/an-intro-to-wikidata.md) which should serve as a pretty good overview. JUNG or ‘The Java Universal Network/Graph Framework’ is a library that provides tools for working with network graphs. It served as an introduction to the idea of [Force Directed Graphs](https://en.wikipedia.org/wiki/Force-directed_graph_drawing) and contains a ton of really outstanding Java code written by people much smarter than I. The layout algorithms for this project were adapted from these and I am eternally thankful for their work serving as an introduction and structure on which my own code was modeled.

The frontend relies on the [P5.js](https://p5js.org/) library which provides a great toolset for creative ‘drawing’. It’s a little harder to capture the spirit of this library in words, but if you like creative coding and want to make some sort of interesting visual or game in the browser I highly suggest you start there. It includes access to WebGL through a handy API for 3D drawings and provides a ‘frames based’ paradigm which is accessible and easy to work with.

As of now the frontend may be integrated/developed here as opposed to a standalone repository, but this detail is still being considered… I will post links to repositories/branches as soon as there are some things to post. 

Lastly, as a part of the required coursework there will be weekly dev-log updates to track the overall progress being made, with the next post intending to layout a more detailed feature-set to define the ‘MVP’. I’m eager to get started, and looking forward to the ‘blue-sky’ planning phase coming next week where I can ideate and iterate on the lessons learned from the prototype.

Brb. @horaciovelvetine



