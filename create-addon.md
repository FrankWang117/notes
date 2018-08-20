1. `ember addon pharbers-tm-name`;
2. `$ cd pharbers-tm-name`;
3. `$ yarn install`;
4. `$ ember s`
5. can see"Welcome to Ember" in page
6. `$ ember g component accordion-hospital`;
7. `$ ember s`  can see error.
8.
```
// package.json
    "dependencies": {
        "bootstrap": "3.3.7",
        "bootstrap-sass": "3.3.7",
        "ember-bootstrap": "^2.0.0",
        "ember-cli-babel": "^6.6.0",
        "ember-cli-htmlbars": "^2.0.1",
        "ember-cli-sass": "^7.2.0",
        "ember-css-modules": "^0.7.10",
        "ember-css-modules-sass": "^1.0.0",
        "ember-sass-bootstrap": "^0.2.0"
    },
```
9. `$ yarn install`
10. `$ ember s`
11. if terminal show ![newaddon12.jpg](https://i.loli.net/2018/08/20/5b7a4bfa7aee8.jpg)  
should change `ember-cli-build.js` file like this:
```
'use strict';

const EmberAddon = require('ember-cli/lib/broccoli/ember-addon');

module.exports = function(defaults) {
    let app = new EmberAddon(defaults, {
        'ember-bootstrap': {
            'bootstrapVersion': 3,
            'importBootstrapFont': true,
            'importBootstrapCSS': true
        },
        sassOptions: {
            includePaths: [
                'node_modules/bootstrap-sass/assets/stylesheets'
            ]
        }
    });

    return app.toTree();
};
```

12. `$ ember s`
13. if you want to use bootstrap ,you should import bootstrap styles file into your css/scss file
```
//  addon/styles/app.scss
@import "bootstrap";
//  or
//  app/styles/app.scss
@import "ember-bootstrap/bootstrap";
```
that's mean the file direction should right.
