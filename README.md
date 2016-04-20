# react-prod-bundler
Reference for bundling prod ready react builds using webpack

# Steps to minimize prod builds
## webpack.config.js ->

module.exports = {
  // devtool: 'eval' // for development - bundles a lot of dev code info
  devtool: 'cheap-module-source-map' // results in ~75% decrease in build output file size by shoving off dev info
},

plugins: [
    new webpack.optimize.UglifyJsPlugin(),  // minifies/uglifies your code
    new webpack.optimize.DedupePlugin(),    // replaces duplicate code to minimize file-size
    new webpack.DefinePlugin({              // tells webpack to omit some things it uses for node environment builds
        'process.env': {                    // results ~30% decrease in size
            'NODE_ENV': JSON.stringify('production')
        }
    })
],

## Library way imports (ES6 de-structing syntax) Vs Component way imports

Always use Component way of imorting.
e.g.
### Library way (ES6 de-structing syntax)

import { Button } from 'react-bootstrap';
import { FlatButton } from 'material-ui';

Above code not only bundles Button and FlatButton (and their dependencies) but the whole libraries!!

### Component way

import Button from 'react-bootstrap/lib/Button';
import FlatButton from 'material-ui/lib/flat-button';

This will only bundle Button, FlatButton and their respective dependencies. But not the whole library. So I would try to get rid of all your library imports and use the component way instead.

If you are not using lot of components then it should reduce considerably the size of your bundled file.
