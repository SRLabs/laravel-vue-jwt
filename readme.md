# JWT with Laravel and Vue.js

This repo is a "proof of concept" for using [Json Web Tokens](https://jwt.io/) to secure commincations between a Vue.js SPA and a Laravel PHP api.  We are extending the native Laravel Auth tools to support JWT via the "[tymon/jwt-auth](https://packagist.org/packages/tymon/jwt-auth)" package.


## Installation

### Laravel


```bash
~/project/laravel$ composer install
~/project/laravel$ touch database/database.sqlite
~/project/laravel$ cp .env.example .env
~/project/laravel$ php artisan key:generate
```

Make sure to update your `.env` ``"DB_DATABASE"`` value to be the absolute path to your newly created `database.sqlite` file, and the ``"FRONTEND_URL"`` value to the url for your vue js application instance.

The [tymon/jwt-auth](https://packagist.org/packages/tymon/jwt-auth) package uses its own "JWT_SECRET" env var as its encryption seed value.  Make sure you generate it by running:

```bash
~/project/laravel$ php artisan jwt:secret
```

FYI:  In this project we are using a develomment version of this package.  The setup instructions for the currently stable release are slightly different.  Also, for reasons I don't yet understand, using the variable name "JWT_SECRET" for the secret token was somehow not working.  Changing the name of the variable allowed it to work as expected.

### Vue.js

```bash
~/project/vue$ npm install
```

If you are not going to use the default PHP server domain (http://127.0.0.1:8000) as your API url, you can specify your own by creating a file in the root of the Vue application directory called `.env.js` with this format:

```javascript
'use strict'
module.exports = {
  API_URL: '"http://api.url"'
}
```

This is optional.

## Serving

### Laravel

The laravel application can be served using any of the reccomended methods: [Valet](https://laravel.com/docs/5.5/valet), [Homestead](https://laravel.com/docs/5.5/homestead), [Docker](http://laradock.io/).  Be sure to update the values in your `.env` file appropriately.

You can also use the native php development server like so:

```bash
~/project/laravel$ php artisan serve
```

### Vue.js

These instructions have been borrowed from the [Vue CLI Webpack Template](https://github.com/vuejs-templates/webpack) Repo:

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

# run unit tests
npm run unit

# run e2e tests
npm run e2e

# run all tests
npm test
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

## Tests

### Laravel

The PHP unit tests can be run like so:

```bash
~/project/laravel$ vendor/bin/phpunit
```

### Vue.js

This project does not yet have any unit tests for the Vue.js components, but hopefully I will add those down the road.

## Notes

### CORS and CSRF Protection

As you [can see here](https://github.com/SRLabs/laravel-vue-jwt/blob/master/laravel/app/Http/Middleware/VerifyCsrfToken.php#L15), we are explicitly disabling Laravel's built-in CSRF protection for the API endpoints.  To compensate for that, we use the [barryvdh/laravel-cors](https://packagist.org/packages/barryvdh/laravel-cors) package to set up a Cross Origin Resource Sharing whitelist.  In this case we use the "FRONTEND_URL" value from the `.env` settings as the whitelisted domain.


### Routing

The vue router instance in this project is set up to use HTML5 history mode by default, which removes the "#!" hashbangs from the front-end urls.  The vue router documentation reccomends setting up a catch-all route, which has been done here, but you may need to [adjust your server settings](https://router.vuejs.org/en/essentials/history-mode.html) to ensure that pages are loaded correctly.