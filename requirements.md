These requirements are for a new architecture for the www.investopedia.com. This could be drupal 8, decoupled drupal 8 or some other CMS entirely.

# Motivation
Motivations for getting off the current architecture are detailed here: https://iacpublishing.atlassian.net/wiki/spaces/INV/pages/3127742/2017-09-29+-+De-coupling+Architecture+Brainstorming

# Requirements (WIP)
High level requirements. These are the functional requirements that must be met with the new design.
At some point we will need to review this list and decide what the priority is on each as well as divide up into milestone targets.

## CMS Compatibility
- The system should be designed to be loosely-coupled to any particular CMS
  - Importance: High

## Rendering Functionality
- Template / Layout variation: Choice of layout is dependent on factors like the Content Type, taxonomy, geo location; achievable with minimal repetition of code
  - Importance: High
  - Example code: https://git.ask.com/investopedia/www/blob/master/sites/default/modules/custom/leadgen_template_switch/leadgen_template_switch.module#L89
  - Example Use Case: There was a past requirement to detect whether the page originated from a Search Engine Marketing (SEM) campaign and then rendering a different layout; the reason is that the SEM team are making advertising purchasing decisions based on a specific layout.  When we make changes to the layout on the site (which happens frequently), we may have to "freeze" the layout for pages that arrive from SEM until they have had time to adjust.
- Routing: We have two main types of pages:
  - content (eg. /news/apple-caught-uber-breaking-rules-aapl/) - This shows content stored in the CMS
  - non-content (eg. /markets/) - These are application or landing pages such as:
    - login page
    - Auto-generated pages per stock (eg /markets/stocks/aapl)
    - Landing Pages (eg /news/)
    - User-facing tools eg calculators, broker reviews, watchlist
  - Importance: High
- Ability to serve ads from the same accounts as the existing architecture - eg should work with same DFP account
  - Importance: High
- We should demonstrate building the most common UI elements on the site which are:
  - Header / Footer
  - Content Listing / Related Content blocks
    - They show things like "other content related to this one", "Latest content in a certain category".
    - eg /news/apple-caught-uber-breaking-rules-aapl/ - see "RELATED ARTICLES"
    - These are basically a list of content that has some variation of a title, url, picture, summary; exact render options should be configurable per page
    - Have different data-sources and filters, which should be configurable per page

## Development Efficiency
- Developers must be able to create new basic pages quickly (< 1 day)
  - TODO: if possible enumerate types of work and provide benchmarks for each.
  - Importance: High
- Standards based on industry or community best practices are used rather than inventing our own
  - Importance: High
- Modularity of code: when working on something like http://www.investopedia.com/trump/, it is useful to be able to work on components of the page independently, and also re-use those components in other pages.  For example, "Partner Headlines" and "News" components are likely to be re-used on many pages.
  - Importance: High
- Isolation of System components: Unlike our current system, changing something should have no chance to break an unrelated part of the site
  - Importance: High
- The system should be designed in a way that core components can have their behavior overriden for a specific use case in such a way that doesn't affect other use cases. - Example - one page wants to override a part of the header.
  - Importance: High
- Testing: Entire system, front-to-back, must be designed to be testable with a focus on supporting test-first practices. This includes very fast-running unit tests, with heavy use of mocks, as well as browser testing to verify actual user interaction in various user agents.
  - Importance: High
- Learning Curve: a more desirable choice would make it easy for developers (both back and front-end) to on-board and learn about the framework; this means that there should be good documentation, community support, training resources.
  - Importance: Medium
- The new architecture will need to support running in multiple configurations (prod, dev, staging, qa , etc.) as well as provide a mechanism for local override of any setting. This is true of both the front and back end.
  - Importance: High
- Database: The architecture must provide access to the data layer with a system that is easy to learn, powerful, and fast. It must also be possible to execute raw SQL when 
    needed (e.g. for extra performance)
- Database/config changes are automated and easily distributed to all environments (include dev) without manual steps
  - Db changes are expressed as artifacts that can be committed to source control
  - The database is versioned so the system can know which artifacts need to be applied on any given system
  - Importance: High
- Error/exception monitoring (server and client side)
  - All errors on backend must produce a useful description and full call-stack to an error log as well as an email
  - Front-end errors must be descriptive and provide detail in the console - should also log someplace where developers can access them.
  - Importance: High
- Technology Fragmentation: A more desirable choice would take advantage of the existing (as of 2017-11) processes and tools such as unit testing, mocking, security scanning, best practices.
  - Importance: Low
- Feature Flags: The system should provie a robust feature-flag system that can support deployment of new components as well as incremental updates to existing components.
  - As we all "on/off", the system should support the ability to deploy to a subset of the user population.
  - This system should also be able to be used in an A/B test scenario
  - Feature flags should be indepdent from A/B tests because they may have to occur at the same time.
  - Importance: High
- Code organization: The system should be organized in such a way that it the components for the entire system do not have to be stored in a single code repository.
  - This should extend to sections of the site - for example - all the code that corresponds to the "/personal-finance" section of our site (and all sub urls) should be able to placed in it's own repository and it should be obvious how to achieve that.
  - Importance: High
- It should be intuitive to a developer where all the things that contribute to page live.  Counter-examples today are where some stuff is happening in php templates, some is happening in various javascript files, other stuff is in css.
  - Implementation note: Isomorphic javascript can help as something is to explore

## Performance and Stability
- Asset Compilation: We must be able to perform standard build-time optimization of browser-loaded assets, such as combining javascript into a single minified file - for performance reasons.
  - Importance: High
- Performance - A better choice will perform faster, both on back-end and front-end.
  - Importance: High
- Async: Ability to easily handle multiple asynchronous calls to micro-services.
  - Importance: High
- Google AMP: For mobile sites, pages should be AMP compliant
  - Importance: ?

## Integration and Interoperability
- Must work with Optimizely
  - Example past pain: Academy Version 1 (academy.investopedia.com), learned that the Thought Industries single-page app architecture developed with EmberJS did not play well with Optimizely.  They had a hard time modifying a simple thing like a web title.
  - Importance: High

## Security
- The system should handle user uthentication by integrating either with the existing custom login (a.k.a. sitewide) system or a standards-based 3rd party system like Okta.
  - Importance: High

## Other
- Stability and support: Given new technologies that emerge, how quickly will a standard library be created to support it?  How quickly are bugs and features fixed and added to existing libraries?  How many people are available to answer questions?
  - Importance: Medium
- Community and Adoption: A better choice would be adopted by more users and major sites, as well as having an active community.  
  - Importance: Medium

# Deliverables (WIP)
The primary goal of the project is a new architecture but we will not be porting all existing code into the new architecture. The following deliverables are what is required to consider the project complete.
Ideally some content can start moving to the new architecture before all deliverables are 100% complete.
- New architecture is planned out and the new tech stack is verified to meet all of the requirements.
- Easy to use dev environment, testing frameworks, linting, code coverage tools, etc. all worked out and automated as much as possible
- Production environment ready (not necessarily service code yet but in place and load tested to verify the tech stack)
- Continuous integration and deployment
- Interoperability with the existing site so that pages can be slowly migrated over
  - Pages in both new and old architecture serve from the same domain
  - Content is available in both systems or there is a well tested process for migrating data
