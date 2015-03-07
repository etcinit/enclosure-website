---
layout: page
title: Roadmap
hidden: true
permalink: /docs/roadmap/
---

This library is still a work in progress. Specific features are being tracked on GitHub issues. Main project features are listed below:

- **The Container Component:** A complete service/IOC container
    - [X] Singleton services
    - [X] Shared services (cached services)
    - [X] Factory functions
    - [X] Service providers
    - [ ] Deferred service providers
    - [X] Load providers defined in `package.json`
- **The Loader Component:** An alternative to Node's `require` function
    - [X] Abstract the process of requiring modules from the filesystem (`use()`)
    - [X] Introduce something loosely similar to namespaces from other languages. Namespaces would be defined based on the file path in the project: `src/Chromabits/Mailer/MandrillMailer.js` should be accessible by doing something like `var MandrilMailer = use('Chromabits/Mailer/MandrillMailer')`.
    - [X] Introduce a new `autoload` key to `package.json` which would be parsed by the Enclosure loader in order to figure out namespace to filesystem mappings.
    - [ ] Add a mapper capable of exploring libraries in `node_modules` and discover namespaces provided by each library
    - [ ] Cache class maps (a lot like PHP Composer's dump-autoload)
    
### Near-term improvements:

- [ ] Better test coverage (>80%)
- [X] `package.json` extensions
- [X] Better bootstrap experience
