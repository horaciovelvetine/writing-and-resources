# Devlog 3:  September 14-20, 2025

It's another week at the Wikiverse and details are being ironed out in the planning components, and some code has been written as `v1.0.1` of the API is beginning to take shape. [Last week's](https://github.com/horaciovelvetine/writing-and-resources/blob/main/the-wikiverse/devlogs/devlog-2.md) update was about pairing down the blue-sky ideation phase into a list of actual named features which will make up the `Beta` release of the Wikiverse from a high level, however this week is much more about digging into the details and beginning to name attributes and create a data model to begin development and iterate on as the project matures. 

## A Datamodel:

![Wikiverse Data model Overview](https://raw.githubusercontent.com/horaciovelvetine/writing-and-resources/main/assets/wikiverse-api-datamodel-overview-v1.png)

Big thanks to [draw.io](https://app.diagrams.net/) for providing an excellent free tool for creating diagrams, it's a tremendous help for organizing and planning.

After starting the [new repo](https://github.com/horaciovelvetine/the-wikiverse-api) the focus was on implementing the three initial requests used in by the frontend to begin a session: `GET /status`, `GET /search-results`, and `GET /graphset/initialize`. These requests do a good job utilizing the core models and provide a good starting place for beginning to implement the needed logic for the application as a whole. 

### Core

- `Edge.java` - Basic `Vertex` connector with a sourceID, targetID, and propertyID. Inherently directional to represent the Wikidata model, ex: 'Kevin Bacon is an Actor' which is a true statement, but Kevin Bacon is not the only actor. 
- `Graphset.java` - Container data model for `vertices`, `edges`, and `properties`. Provides the primary interfacing for searching and interacting with the primary data records.
- `Metadata.java` - Provides the context information about the `Graphset` and stores needed constants for the to be implemented layout. Will be deeply integrated in the frontend's settings.
- `Point3D.java` - Extends the `java.awt` implementation of the `Point2D` by adding the z access.
- `Property.java` - Stores the context information of an `Edge.java`, in essence describing the relationship between two vertices. 
- `SearchResult.java` - Stores the smaller data related needed to fulfill the `GET /search-results`, very similar to the `Vertex` without a few of the additional details
- `Vertex.java` - The primary data of the application, used to represent the `ItemDocument` model from Wikidata Toolkit, analogous to a Wikipedia page itself. 
- `WikiverseError` - The main Error container for application, a sealed interface which contains records for all the known Errors the application can encounter providing some meaningful feedback for issues occuring during development.

### Requests

- `Request.java` - Generic parent class representing a request from the client, handles some timing data and the `Wikiverse Error` in case something goes wrong while completing a request
- `GraphsetRequest.java` - Handles requests related to modifying the underlying Graphset data. This will include fetching new data and filtering/removing data requested by the client. 
- `LayoutRequest.java` - Handles requests related to modifying the layout or positions of the vertices themselves, including the layout refresh.
- `SearchRequest.java` - Handles the initial search requests from the client, allowing them to get an abridged version of the `Graphset` so users can pick the target they want to build the Graph visualization around.
- `StatusRequest.java` - Sent on client startup to check that the requests can be carried out and make sure the Wikidata is available and the API itself is online. Includes some timing data after making a request to the Wikidata API for some test data.

### Services

**Logging:**

- `Logfile.java` - For retaining details from the application into .log files for debugging and evaluation, used by the `ProcessLogger` and the `WikidataDocumentLogger` to handle all the file writing details.
- `ProcessLogger.java` - Includes wrappers for executing methods to log timing details and execution details for any process/method in development.
- `WikidataDocumentLogger.java` - Allows writing of Wikidata Documents to .json and other files so that the data and results of queries can be examined for structure and sanity checks.

**Wikidata:**

- `DefaultWikidataFilter.java` - Reads from a resource .json file containing details about a bunch of Entities from Wikidata which are either mislabeled or otherwise have shown up in testing and will never be data the user needs to see. 
- `DocumentProcessor.java` - Transitions the results from Wikidata fetches into the core data models used in this application. Handles a lot of the 'decision' making looking for bad, incomplete, or irrelevant data and omitting that from results. 
- `FetchBroker.java` - Handles all the fetching from the Wikidata API, provides the search and fetch methods using the `WikibaseDataFetcher` from the Wikidata Toolkit. Provides handling in case the API is offline/unavailable and having no matching results for a given request.
- `IDFilterRecord.java` - Used to represent an ID which has been deliberately filtered out of the results we want from the Wikidata API. 
- `WikidataService.java` - Wrapper class to provide access to the `Bean` instances of the Wikidata Service classes (`Fetch Broker`, `Document Processor`, and `Default Wikidata Filter`). Also provides a place to create re-useable methods for repeatable logic which might be consistent across different requests.
- `WikidataSnak.java` - An intermediary which implements a visitor pattern to wrap information from the Wikidata API about claims made about `ItemDocuments`. This class exists to wrap values before being written into the `Graphset` as some part of the core data model and helps filter out data which cannot be used in this application.
- `WikidataValue.java` - Used by the `Wikidata Snak` to represent one of the finite types of data which can be recieved from the Wikidata API. Also implements the visitor pattern to help filter out irrelevant and unuseable data for this application.

## Wikidata Structures

Anyone who has some familiarity with the domain of network visualization models will be familiar with two core problems: hairballs and the diminishing returns of usefulness as they increase in size. These issues are not 'solved' by any means with ongoing research effort in the domain and novel approaches to solving these problems constantly being explored. There is one enormously clear strategy for mitigating these issues - filtering. Providing the user tools to remove irrelevant data and filtering the data which is included in the Graph are crucial to making sure these visualizations are helpful and engaging to use. In order to best integrate these features it is crucial to understand the [Wikidata Toolking](https://github.com/Wikidata-Toolkit/Wikidata-Toolkit), the data it returns, and the tools it provides for filtering and specifying the data it returns. 

The [WikibaseDataFetcher](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/wikibaseapi/WikibaseDataFetcher.html) is the entry point for getting data from the Wikidata API, it provides a means of sending requests directly to the API providing conventional search access including: gets by ID, gets by Title, and gets by List of ID's and Titles. Perhaps most important for this application is the interfacing which allows pre-emptive filtering of data for requests, specifically the feature allowing requests to target a target language ([see](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/datamodel/interfaces/DocumentDataFilter.html)). In the Wikiverse code base this is an option the user will specify which defaults to the English language: `en` which is stored as a part of the `Metadata` class under the var name `wikiLangTarget`. This should provide an initial means of reducing the data returned but only serves as an initial piece of the puzzle. The next step in creating useable Graphs will be integrating classes like the `DefaultWikidataFilter` and `IDFilterRecord` with the `DocumentProcessor` to omit irrelevant pieces of information that would be otherwise unimportant to the user of the application. To do so it's crucial to understand some of the structure behind the Wikidata Toolkit: 

![Wikidata Document Structure](https://raw.githubusercontent.com/horaciovelvetine/writing-and-resources/main/assets/wikidata-document-structure-diagram-v1.png)

This is just an overview of the Document data structure with the relevant methods pulled out: `getLabels()`, `getDescriptions()`, and `getSiteLinks()` each provide crucial pieces of information for the `DocumentProcessor`, so checking that those attributes are populated (not null) will act as a first line of filtering for fetched information. After this stage the `getAllStatements()` method will be invoked on each document to continue on to the next stage of the ingest. [Statements](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/datamodel/interfaces/Statement.html) are a crucial part of the overall structure of the Wikidata model, providing a means of connecting and describing the connection between any [Entity](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/datamodel/interfaces/EntityDocument.html) and a piece of data or information which might describe it.

![Wikidata Document Ingest Process](https://raw.githubusercontent.com/horaciovelvetine/writing-and-resources/main/assets/document-ingest-process-diagram-v1.png)

Each Statement from the list can describe literally *ANYTHING*, which requires this part of the ingest process to be relatively complex and flexible so that it can omit the things we don't need without accidentally excluding otherwise important or useful data. Statements have X crucial things to look at: `getClaim()`, `getMainSnak()`, `getValue()`, and even potentially `getQualifiers()`. For now the focus will be on the first three, with `getQualifiers()` being a stretch goal to come back and revisit at a later date. A [Snak](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/datamodel/interfaces/Snak.html), serves as the basic information structure used to describe Entities in the Wikibase - and for the most part consist of property-value pairs. There is some flexibility to this in the `No Value` and `Some Value` Snak's which are only really a property, but conceptually it's easiest to thing about them as property-value pairs. Both properties and values are stored using the same [Value](https://wikidata-toolkit.github.io/Wikidata-Toolkit/org/wikidata/wdtk/datamodel/interfaces/Value.html) interface, which is split neatly into a variety of sub-interfaces outlining the various datatypes a value can be. This is another means by which data can be filtered out, the below diagram details the structure of these with the relevant values being in blue. 

![Wikidata Value Structure](https://raw.githubusercontent.com/horaciovelvetine/writing-and-resources/main/assets/wikidata-value-structure-diagram-v1.png)

If you aren't familiar with the `.accept()` pattern I suggest you take a look at my write up on [the visitor pattern](https://github.com/horaciovelvetine/writing-and-resources/blob/main/the-wikiverse/topics/the-visitor-pattern.md), as it details why both the `Snak` and `Value` interfaces are implemented the way they are. For the Wikiverse codebase the `Wikidata Snak` and `Wikidata Value` classes will handle the underlying 'visiting', to retrieve data from these results. 

## Conclusion

This week has been about beginning to iron-out implementation details for the API's data model, and while there are still a lot of details to sort out, should serve as a starting place to begin to build. Next week the intention will be to have the code running for the `GET api/graphset/initialize` request, and document any changes or pivots made in the development process itself. 

Brb @horaciovelvetine