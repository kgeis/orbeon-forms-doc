# Product roadmap

## Past releases

See the [Orbeon Forms release history](release-history.md).

## Release strategy

- __How often do we release?__ Since 2013, starting with version 4.0, we're on a "fast release cycle", with a new major release done about every 4 months, for about 3 major releases every year. In the 2 years from February 2013 to February 2015, we had 9 major releases, and 8 additional point releases. Since 2017 we have switched to a more sustainable 2 major releases a year, with point releases as needed.
- __Why do we release often?__ We think more frequent releases are better because features and bug-fixes reach users faster. They also keep us almost always ready to ship, rather than having very large work in progress which can destabilize the product and then take weeks or months to fix. In short, we think that incremental development and releases are much better for everyone! Based on our experience since early 2013 when we introduced the new release schedule, we are so far (early 2015) very happy with the change.
- __What do releases contain?__ Releases address both new features and bug-fixes. Point-releases (2017.2.1, 2017.2.2, etc.) essentially contain bug-fixes and sometimes very small features. They are done only when necessary, and only for the PE version of the product. Bigger features are left to the major releases (2017.1, 2017.2, 2018.1, etc.), and we strive to do those on a regular schedule.

## Planning strategy

- __How far ahead do we plan?__ Once we've published a major release, we do the planning for the following one. You can see the future releases listed on the [open projects page](https://github.com/orbeon/orbeon-forms/projects), and from there, see what has been planned for the next release. We don't plan feature or bugs to be included in releases beyond the very next release, so we can react faster to our customers needs, deciding for every release what will best serve our customers.
- __How do we decide what improvements to include in a release?__ A fair amount of the new features we develop are designed in collaboration with our customers, and sponsored by customers through a [Dev Support plan](https://www.orbeon.com/services). Other features are added to better serve existing and future users of the product. And last, but not least, for every release we strive to do work in each one of the development tracks outlined below.

## Development tracks

- __Mobile__ – Better support [responsive design](https://github.com/orbeon/orbeon-forms/issues/1181), and the ability to [run our form engine on the client](https://github.com/orbeon/orbeon-forms/issues/1221) to enable offline support and native apps.
- __Performance__ – While Orbeon Forms has been proven to be able to sustain a [fair amount of load](faq/form-builder-runner.md#how-much-load-can-orbeon-forms-handle), we 're always striving to improve the product in that regard, including [reducing memory usage](https://github.com/orbeon/orbeon-forms/issues/1606), [reducing load time](https://github.com/orbeon/orbeon-forms/issues/1239), and [improving performance on MySQL](https://github.com/orbeon/orbeon-forms/issues/649).
- __Architecture and maintenance__ – Like any project, Orbeon Forms has some technical debt. It is not possible to remove all of it at once, but, like garbage collection, we want improve the code base incrementally. In addition, we want to look to the future to use the best possible languages and frameworks. This includes: [modularizing of the code](https://github.com/orbeon/orbeon-forms/issues/1585), taking care of [issues flagged as "maintainance"](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3AMaintenance), drop old frameworks and adopt new ones ([get rid of YUI](https://github.com/orbeon/orbeon-forms/issues/1599), [use Scala.js](https://github.com/orbeon/orbeon-forms/issues/1600)), and do more automated testing (we have almost 800 tests, but need more).
- __Integration and workflow__ – We want to make it easier to [embed forms](https://github.com/orbeon/orbeon-forms/issues/1235), make it easier to use [HTTP/DB services for validation](https://github.com/orbeon/orbeon-forms/issues/1304), allow [forms to be imported/exported with dependencies](https://github.com/orbeon/orbeon-forms/issues/779), better document [standard use cases / workflows](https://github.com/orbeon/orbeon-forms/issues/228), make [integration with external systems/workflows simpler](http://wiki.orbeon.com/forms/projects/form-runner-builder/form-runner-data-envelope), include built-in [Basic workflow](https://github.com/orbeon/orbeon-forms/issues/2256), and more.
- __Form Builder modernization__ – We want to push the envelope and keep improving the [usability of Form Builder](https://github.com/orbeon/orbeon-forms/issues/1675).

## What's in a release

For each release we would like to:

- [ ] have bug-fixes
- [ ] have at least one new feature (which can be small)
- [ ] book one day of work on [documentation tasks](https://github.com/orbeon/orbeon-forms/issues?direction=desc&labels=Doc&milestone=&page=1&sort=updated&state=open)
- [ ] pick one [maintenance task](https://github.com/orbeon/orbeon-forms/issues?direction=desc&labels=Refactoring&milestone=&page=1&sort=updated&state=open) (build system, refactoring, …)
- [ ] pick one [performance task](https://github.com/orbeon/orbeon-forms/issues?direction=desc&labels=Performance&milestone=&page=1&sort=updated&state=open)
- [ ] pick one mobile item if possible

The idea is, as some of those tasks are large, to do it incrementally when possible, so that some progress is made.

## Upcoming releases

### Orbeon Forms 2019.1

Consider:

- [low-hanging fruits](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3A%22Low-Hanging+Fruit%22)
- [dogfood items](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3ADogfood)
- [maintenance items](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3AMaintenance)
- [spare time items](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3A%22Spare+Time+Feature%22)
- [top issues](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3A%22Top+issue%22)
- [top RFEs](https://github.com/orbeon/orbeon-forms/issues?q=is%3Aopen+is%3Aissue+label%3A%22Top+RFE%22)
- [2019.1 items](https://github.com/orbeon/orbeon-forms/projects/11)

Focus:

- TBD
- Progress towards [Basic Workflow #2256](https://github.com/orbeon/orbeon-forms/issues/2256)

__Please remember that the following schedule is a plan, not a promise:__

- 2019-06: tentative release date

### Orbeon Forms 2019.2

TBD
