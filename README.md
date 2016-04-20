# react-prod-bundler
Reference for bundling prod ready react builds using webpack

# Steps to minimize prod builds

## Use Webpack Bundle Size Analyzer
A tool for finding out what contributes to the size of Webpack bundles

https://github.com/robertknight/webpack-bundle-size-analyzer

## Running webpack CLI with production command

```
webpack --production
or
webapck -p
```
## Use dedicated ```webpack.production.config.js``` file for production build
```
webpack -p --config ./webpack.production.config.js
```
The -p tells webpack to generate a production build. You have to change the build script in ```package.json``` to include the production flag.

## webpack.config.js
```
module.exports = {
  // devtool: 'eval' // for development - bundles a lot of dev code info
  devtool: 'cheap-module-source-map' // results in ~75% decrease in build output file size by shoving off dev info
},

plugins: [
    // CommonsChunkPlugin extracts the common code from all your chunks and put it in a separate file common.js
    new webpack.optimize.CommonsChunkPlugin('common.js'), 
    new webpack.optimize.UglifyJsPlugin(),  // minifies/uglifies your code
    new webpack.optimize.DedupePlugin(),    // replaces duplicate code to minimize file-size
    new webpack.optimize.AggressiveMergingPlugin(),
    new webpack.DefinePlugin({              // tells webpack to omit some things it uses for node environment builds
        'process.env': {                    // results ~30% decrease in size
            'NODE_ENV': JSON.stringify('production')
        }
    }),
    // Automatically loaded modules. Module (value) is loaded when the identifier (key) is used as free variable in a     // module. The identifier is filled with the exports of the loaded module.
    new webpack.ProvidePlugin({
        $: "jquery"
    })
],
```

## Webpack advanced configuration for React

Refer to http://www.pro-react.com/materials/appendixA/ for detailed explaination.

## Library way imports (ES6 de-structing syntax) Vs Component way imports

Always use Component way of imorting.
e.g.
### Library way (ES6 de-structing syntax)
```
import { Button } from 'react-bootstrap';
import { FlatButton } from 'material-ui';
```
Above code not only bundles Button and FlatButton (and their dependencies) but the whole libraries!!

### Component way
```
import Button from 'react-bootstrap/lib/Button';
import FlatButton from 'material-ui/lib/flat-button';
```
This will only bundle Button, FlatButton and their respective dependencies. But not the whole library. So I would try to get rid of all your library imports and use the component way instead.

If you are not using lot of components then it should reduce considerably the size of your bundled file.

--------------------------------------------------------------------------------------

### Tools (Dev): Redux action dispatch time profiling middleware

https://www.npmjs.com/package/redux-perf-middleware

Measure the time that the actions need to change the state.
#### Usage:
```
import perflogger from 'redux-perf-middleware';
 
const createStoreWithMiddleware = applyMiddleware( perflogger )(createStore);
const store = createStoreWithMiddleware(reducer);
```
This middleware will log something like below in console:
```
Dispatching Object { type: "REQUEST_DATA" }
Action with type "REQUEST_DATA" took 108.28 milliseconds.
```

### React Performance
Refer to https://reactjsnews.com/how-to-make-your-react-apps-10x-faster for more info.



