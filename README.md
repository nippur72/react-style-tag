# react-style-tag

Write styles declaratively in React

#### Table of contents
* [Installation](#installation)
* [Usage](#usage)
* [Implementation](#implementation)
* [Summary](#summary)
* [Scoped styles](#scoped-styles)
* [Props](#props)
* [Development](#development)
* [Todo](#todo)

#### Installation

```
$ npm i react-style-tag --save
```

#### Usage

```javascript
// ES2015
import Style from 'react-style-tag';

// CommonJS
const Style = require('react-style-tag').default;
```

#### Implementation

```javascript
import React, {
  Component
} from 'react';

import Style from 'react-style-tag';

class App extends Component {
  render() [
    return (
      <div>
        <h1 className="foo">
          Bar
        </h1>
        
        <Style>{`
          .foo {
            color: red;
          }
        `}</Style>
      </div>
    );
  }
}
```

#### Summary

`react-style-tag` creates a React component that will inject a `<style>` tag into the document's head with the styles that you pass as the text content of the tag. Notice above that the styles are wrapped in ``{` ``and`` `}``, which create a template literal string. Internally, `react-style-tag` parses this text and applies all necessary prefixes via [`autoprefixer`](https://github.com/postcss/autoprefixer). All valid CSS is able to be used (`@media`, `@font-face`, you name it).

The style tag that is injected into the head will be automatically mounted whenever the component it is rendered in is mounted, and will be automatically unmounted whenever the component it is rendered in is unmounted.

#### Scoped styles

There is an additional utility provided that can help to scope your styles in the vein of [CSS Modules](https://github.com/css-modules/css-modules), and this is `hashKeys`. This function accepts an array of keys to hash, and returns a map of the keys to their hashed values.

```javascript
import Style, {
  hashKeys
} from 'react-style-tag';

const classNamesToHash = ['foo', 'bar'];
const {
  foo,
  bar
} = hashKeys(classNamesToHash);

class App extends Component {
  render() {
    return (
        <div>
          <div className={foo}>
            My text is red due to the scoped style of foo.
          </div>
          
          <div className={bar}>
            My text is green due to the scoped style of bar.
          </div>
          
          <div className="baz">
            My text is blue due to the global style of baz.
          </div>
          
          <Style>{`
            .${foo} {
              color: red;
            }
            
            .${bar} {
              color: green;
            }
            
            .baz {
              color: blue;
            }
          `}</Style>
        </div>
    );
  }
}
```

Notice you can easily mix both scoped and global styles, and for mental mapping the scoped styles all follow the format `scoped__{key}__{hash}`, for example `scoped__test__3769397038`. The hashes are uniquely based on each execution of `hashKeys`, so the implementation can either be Component-specific (if defined outside the class) or instance-specific (if defined inside the class, on `componentDidMount` for example).

#### Props

Naturally you can pass all standard attributes (`id`, `name`, etc.) and they will be passed to the `<style>` tag, but there are a couple of additional props that are specific to the component.

**doNotPrefix** *boolean, defaults to false*

If set to `true`, it will prevent autoprefixer from processing the CSS and just render whatever text you pass it.

**isMinified** *boolean, defaults to false*

If set to `true`, it will minify the rendered CSS text. A possible implementation for this would be something like:

```javascript
const IS_PRODUCTION = process.env.NODE_ENV === 'production';

<Style isMinified={IS_PRODUCTION}>
  .test {
    display: block;
  }
</Style>
```

This would result in:

```javascript
<style>.test{display:block}</style>
```

### Development

Standard stuff, clone the repo and `npm i` to get the dependencies. npm scripts available:
* `compile-for-publish` => runs the `lint` and `transpile` scripts
* `dev` => runs the webpack dev server for the playground
* `lint` => runs ESLint against files in the `src` folder
* `prepublish` => if in publish, runs `compile-for-publish`
* `transpile` => runs Babel against files in `src` to files in `lib`

#### Todo
* Add tests with Enzyme / Tape
