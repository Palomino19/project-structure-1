# A Baseline Front-End Project Structure

## Introduction

The purpose of this repo is to provide a strongly predictable and intuitive
structure for organizing a project. Although this example uses the main
combination of **React + Redux** (more on this later), this structure can be
adapted to other technologies that follow along with similar ideas of organizing
various parts of a project.

The overall structure assumes the following:

-   A project is being tested (automated)
-   A project is being documented
-   A project is using a CI/CD strategy
-   A project is being organized by features

### The primary goals of the structure are

-   **1**) Initial comprehension of a project for a new developer should be
    near painless. The structure of features should be intuitive, and
    finding anything in even a larger codebase should be very easy.

-   **2**) A project should be organized by features and what these feature
    _do_, instead of being organized by grouping together similar "types" of
    code structures (there are exceptions to this rule where grouping by "type"
    makes sense).

-   **3**) Provide a clear maintainability and testing strategy (even if
    ultimately, testing is minimal) by having clear separation between the
    following:

    -   Global State
    -   Local State
    -   Side Effects

-   **4**) Provide a clear documentation strategy (even if ultimately,
    documentation is minimal). The benefit of organizing a codebase by features
    is that each feature and any technical decisions can be documented alongside
    a features related code.

-   **5**) Differentiate between two distinct types of features: user-facing
    features (visual features), and non user-facing features.

-   **6**) Project files should be easy to find and differentiate between (it
    should be obvious _what_ a file is **and** _what_ it does).

## The Structure

-   The root directory of a project should contain the following:

    -   The main `src` directory of the project,

    -   Any auxiliary directories (for example, the E2E testing framework cypress
        uses a root directory to store tests and related configuration/setup).

    -   Any project-level configurations related to static analysis tools, CI/CI,
        testing, or related (this includes the `package.json`).

    -   A `README.md` file containing general project information and development
        environment setup instructions.

    -   A `CHANGELOG` file containing a list of notable changes and updates (for
        reference on what a changelog should look like, [click
        here](https://keepachangelog.com/)),

-   The `src` directory should contain:

    -   An `assets` directory containing any relevant project assets (e.g.: fonts,
        images, vector graphics, etc).

    -   An `app` directory that contains:

        -   The main entry point for the application (the `index.js`).

        -   The very top-level app component (the `App.js`). This component should
            only do 2 things: Wrap the entire app with the global state [store
            provider](https://react-redux.js.org/api/provider), and render the
            main app router component.

        -   A `store` directory that contains the setup for the main global state
            store, any setup files related to this, and a directory containing any
            middleware-specific setup files.

    -   A `shared` directory for granular artifacts that are relevant to the
        entire project. Files should be grouped by type (e.g.: components,
        hooks). This is where very generic components like `Button` and `Card`
        would be.

    -   A `utils` directory that contains any utility modules. If several
        utilities are related, (for example, having multiple `testing` utils)
        are related, they should be placed in an organizational directory to
        keep them grouped.

    -   A `screens` directory that should contain:

        -   Top level screen component directories (more on this below).

        -   A top level `AppRouter` component that handles the routing setup of
            switching between the different top-level screens. (This component
            _only_ cares about the top level of screens. If screens have
            sub-screens, the top-level of screens should be concerned with the
            rendering of sub-level screens, not the main app router).

    -   Directories that contain non user-facing features. Examples of these
        types of features might be `authentication` or `caching`. These
        directories should be organized the same as feature directories (more on
        feature directories below), with the exception that similar code
        entities should be grouped, just like the `shared` directory (an example
        of this would be grouping `components` together in a directory).

### Screen directories

Screen directories should only contain 3 things:

-   Sub-screen directories (these have the same rules that apply to top-level
    screen directories).

-   Feature directories.

-   A component for rendering this screen (this component is only concerned with
    rendering/routing it's related features or sub-screens, mixing both should
    be avoided if possible).

### Feature directories

The contents of feature directories are the most vague, as features range in
complexity. If possible, complex features should be broken down into
sub-features. The organization of a feature directory should be the most
flexible, but in general the following guidelines should be followed:

-   Common code entities should be grouped together if there are multiple entities
    (for example, if there is only 1 redux store action, then it doesn't need
    it's own directory. If there are more than one, then they should be grouped
    together). The exception to this rule are components, unless a particular
    feature has an unusually large amount of related components.

-   As state above, components don't need to be grouped, except each component
    having it's own directory (more on component directories below).

-   There should be a feature-specific component that gets rendered by the
    parent screen (or feature) component.

-   Complex features that cannot be quickly comprehended just by looking at the
    source code should also have a `README.md` to explain any complex behaviors.
    This doc might also convey explanations as to why certain things are the way
    they are, and explain technical decisions.

In cases where features are so simple, they can be abstracted to be a
screen-level feature, it is preferable to do so. The line between screens and
features can be blurry, and this is ok; screens are themselves features.

### Component directories

Low-level and granular components should have their own directories (keep in
mind that the app itself, screens and features themselves are technically
components). The reasoning is so relevant styling (whether styling actually is
separate might depend, styling might be apart of the component source code in
some situations), tests, documentation can grouped together with the component
source code itself.

The directory structure of a component might look like this:

```
Timeline
├── Timeline.jsx
├── Timeline.module.css
├── Timeline.story.js
├── Timeline.test.jsx
└── index.js
```

(The file naming strategy is explained in depth below.)

An important part of this structure is the `index.js` and the `Timeline.jsx`
being separate files. Technically speaking, these are actually _two_ components,
but the separation is important: The `Timeline.jsx` should **not** have any\*
connection to the global state store. It's state should be completely isolated a
separate.

This makes testing the component in isolation **extremely** easy. The `index.js`
should then wrap that component with a connection to the global state store,
injecting the main `Timeline.jsx` component with that global state via props.
Testing the `index.js` connection should be done by the integration test in the
feature-level test.

NOTE: In this contrived example, the `Timeline.story.js` is actually the file
containing documentation. This is due to the preferred use of the documentation
tool called [Storyboard](https://storybook.js.org/), which organizes
documentation into "stories".

## Testing strategy

On a source-code level, integration and unit tests are mainly concerned with
directly testing single or multiple units of code together. For these tests, a
`MyFeature.test.js` integration test should be alongside every screen and
feature, and a `MyComponent.test.jsx` should be alongside every component for
unit testing. (Both can be written using the very popular
[Jest](https://jestjs.io/) with your preferred testing helping library such as
[react-testing-library](https://testing-library.com/docs/react-testing-library/intro)
or [Enzyme](https://airbnb.io/enzyme/))

For End-to-End testing, it will depend on the tool being used. These tests may
be completely separate from the root all together, but for a tool like
[Cypress](https://www.cypress.io/), E2E tests are placed in an auxiliary
directory in the project root. The same goes for any visual regression testing.

Leveraging static analysis tools like ESLint and Prettier, and directly
integrating them into the projects CI/CD pipeline is another important part of
testing. The following tools can also making automatic running of these tools a
lot easier:

-   [Husky](https://github.com/typicode/husky)
-   [Lint-Staged](https://github.com/okonet/lint-staged)
-   [Npm-Run-All](https://github.com/mysticatea/npm-run-all)

NOTE: Integration tests may also be written alongside E2E tests in some
situations.

## Documentation strategy

Documentation is a sometimes neglected part of a project, but it is important.
Using tools like [Storyboard](https://storybook.js.org/) or
[MDX](https://mdxjs.com/) can make writing documentation a lot easier though.
Such tools can also make communicating with Management and Design a lot easier
as well.

When onboarding a new developer, setting up their development environment should
be outlined as well. For complicated setups that require a very specific
environment setup can be offloaded to containerization using Docker,
but only when it makes sense to do so.

## File naming

File naming should make it obvious what the file/module is doing, and what
"entity" it is. When the entity is obvious, then it can be excluded from the
file name.

Example file name:

`addTodo.action.js`

By default, all file names should follow camelCase, the only exception being
components which follow PascalCase (remember that screens and features are also
technically components).

React specific:

-   Low-level and granular components should all have a `.jsx` extension,
    while top-level, screen, and feature components should only have the `.js`
    extension. This makes differentiation between the two very obvious and easy.

-   Hooks do not need to have a `.hook.jsx` extension, just a `.jsx` extension.
    Hooks name should follow the general convention of prefixing the hook with
    "use", making spotting hooks easy. Anything that's not a hook should avoid
    the "use" prefix to avoid confusion. Example: `useFuzzyFilter.jsx`

## Managing State and Side Effects

The separation of global and local state is very important. In general, state
should be kept as local as possible. Making use of state sharing using APIs like
[React Context](https://reactjs.org/docs/context.html) at a feature-level will
make managing state as painless as it's able to be.

Other global state management solutions like
[Unstated](https://github.com/jamiebuilds/unstated) or
[mobx-state-tree](https://mobx-state-tree.js.org/intro/philosophy) can also be
used as an alternative to Redux, but should still be able to follow the same
general structure.

For Redux specifically, following the [Official Redux Style
Guide](https://redux.js.org/style-guide/style-guide) will also help keep code
organized. Side Effects themselves also need be managing separately, as to keep
reducers pure.

Depending on the complexity of the app, side effects can be contained to actions
using one or a combination of:

-   [Redux Thunk](https://github.com/reduxjs/redux-thunk) - This allows you to
    dispatch functions as actions. It's the simplest of solutions, and works
    perfectly for containing small side-effects.
-   [Redux Saga](https://github.com/redux-saga/redux-saga) - This is the middle
    ground, it makes dealing with asynchronous effects straightforward and to
    the point in managing.
-   [Redux Observable](https://github.com/redux-observable/redux-observable) -
    For side effects that are very asynchronous and complex to deal with,
    isolating actions into event streams that can be manipulated with the full
    power of [RxJS](https://rxjs.dev/api) works amazingly.

In React specifically, making strong usage of hooks to manage non
render-specific code dependencies will also greatly help keep a project cohesively
contained, while still allowing strong abstractions and code sharing.

## Custom Module Resolution

The advantage of being able to find anything with relative ease due to structure
of a project can be doubly useful when combined with custom module resolution.

Using a custom module resolver plugin like
[babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver)
means that import statements can be drastically cleaned up.

Example:

```JavaScript
import { someUtil } from "../../../../utils/someUtil";
// turns into
import { someUtil } from "utils/someUtil";
```

## Utility libraries

Pulling in utility libraries such as [Lodash](https://lodash.com/),
[Moment](https://momentjs.com/), [RxJS](https://rxjs.dev/api), and/or [Ramda](https://ramdajs.com/) will also
help rapidly speed up development. These libraries can help take care of generic
and arbitrarily complex actions. Just be careful to watch the bundle size and
use tree shaking with these libraries to keep up performance.

## TypeScript

For large scale projects, using Typescript, or at [Flow](https://flow.org/) is
strongly recommended. TypeErrors are the number 1 JavaScript error, and the main
culprit of a majority of JavaScript crashes. Avoiding this altogether by using
type checking will help to bring down the error count and number of user issues.

Typescript usage should be identical to JavaScript usage, and the same naming
rules apply. File name extensions are converted from `.js` to `.ts`, and `.jsx`
turns into `.tsx`.

Typedef-only files can be organized at the discretion of the developer. A
majority of the time, it will make sense to treat typedefs as their own "code
entity", and organize them accordingly.
