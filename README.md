# react-native-typed-sass-transformer

[![NPM version](http://img.shields.io/npm/v/react-native-typed-sass-transformer.svg)](https://www.npmjs.org/package/react-native-typed-sass-transformer)
[![Downloads per month](https://img.shields.io/npm/dm/react-native-typed-sass-transformer.svg)](http://npmcharts.com/compare/react-native-typed-sass-transformer?periodLength=30)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github)

Load Sass files to [react native style objects](https://facebook.github.io/react-native/docs/style.html).

This transformer also generates `.d.ts` Typescript typings for the Sass files. Notice that platform specific extensions are not supported in the Typescript typings.

> This transformer can be used together with [React Native CSS modules](https://github.com/kristerkari/react-native-css-modules).

## Usage

Please use the `.scss` file extension for SCSS syntax and the `.sass` file extension for indented Sass syntax.

### Step 1: Install

```sh
yarn add --dev react-native-typed-sass-transformer node-sass
```

### Step 2: Configure the react native packager

#### For React Native v0.57 or newer / Expo SDK v31.0.0 or newer

Add this to `metro.config.js` in your project's root (create the file if it does not exist already):

```js
const { getDefaultConfig } = require("metro-config");

module.exports = (async () => {
  const {
    resolver: { sourceExts }
  } = await getDefaultConfig();
  return {
    transformer: {
      babelTransformerPath: require.resolve(
        "react-native-typed-sass-transformer"
      )
    },
    resolver: {
      sourceExts: [...sourceExts, "scss", "sass"]
    }
  };
})();
```

If you are using [Expo](https://expo.io/), you also need to add this to `app.json`:

```json
{
  "expo": {
    "packagerOpts": {
      "config": "metro.config.js"
    }
  }
}
```

---

#### For React Native v0.56 or older

If you are using React Native without Expo, add this to `rn-cli.config.js` in your project's root (create the file if you don't have one already):

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-typed-sass-transformer");
  },
  getSourceExts() {
    return ["ts", "tsx", "scss", "sass"];
  }
};
```

---

#### For Expo SDK v30.0.0 or older

If you are using [Expo](https://expo.io/), instead of adding the `rn-cli.config.js` file, you need to add this to `app.json`:

```json
{
  "expo": {
    "packagerOpts": {
      "sourceExts": ["ts", "tsx", "scss", "sass"],
      "transformer": "node_modules/react-native-typed-sass-transformer/index.js"
    }
  }
}
```

## How does it work?

Your `App.scss` file might look like this:

```scss
%blue {
  color: blue;
}

.myClass {
  @extend %blue;
}
.myOtherClass {
  color: red;
}
```

When you import your stylesheet:

```js
import styles from "./App.scss";
```

Your imported styles will look like this:

```js
var styles = {
  myClass: {
    color: "blue"
  },
  myOtherClass: {
    color: "red"
  }
};
```

The generated `App.scss.d.ts` file looks like this:

```ts
export const myClass: string;
export const myOtherClass: string;
```

You can then use that style object with an element:

```jsx
<MyElement style={styles.myClass} />
```
