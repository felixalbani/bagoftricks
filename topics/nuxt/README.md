# NuxtJS

https://nuxtjs.org/

## Creating a nuxt project

```bash
# npx
npx create-nuxt-app <project-name>

# npm
npm init nuxt-app <project-name>

# yarn
yarn create nuxt-app <project-name>
```

## Minimal Installation

Create an empty directory with the name of your project and navigate into it:

```bash
mkdir <project-name>
cd <project-name>
Replace <project-name> with the name of your project.
```

Create the `package.json` file:

```bash
touch package.json
```

Fill the content of your package.json with:

```bash
package.json
{
  "name": "my-app",
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "generate": "nuxt generate",
    "start": "nuxt start"
  }
}
```

`scripts` define Nuxt.js commands that will be launched with the command `npm run <command>` or `yarn <command>`.

### Install Nuxt

Once the `package.json` has been created, add nuxt to your project via npm or yarn like so below:

```bash
# npm
npm install nuxt

# yarn
yarn add nuxt
```

### Create your first page

Nuxt.js transforms every `*.vue` file inside the `pages` directory as a route for the application.

```bash
mkdir pages
touch pages/index.vue
```

Open the `index.vue` file in your editor and add the following content:

```html
<template>
  <h1>Hello world!</h1>
</template>
```

## Starting in dev

```bash
# npm
npm run dev

# yarn
yarn dev
```

## Visual Studio Code

[Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur)

## Folder Structure

- _layouts_: Contains your layouts
- _pages_: Contains the top level views (in .ue files)
- _components_: Contains your reusable Vue components
- _store_: Your Vuex store files
- _static_: Files served from /
- _assets_: Uncompiled assets. Ex: Stylus, SASS, images or fonts
- _plugins_: JavaScript plugins to load before starting the Vue app
- _middleware_: Custom functions to run before rendering a layout or page
- _nuxt.config.js_: Used to modify the default nuxt configuration
- _.nuxt_: The .nuxt directory should not be committed to your version control system and should be ignored through your .gitignore as it will be generated automatically when executing nuxt dev or nuxt build.

## Single Page Application `SPA` vs Server Side Rendering`SSR`

### Universal Deployment

HTML is generated once on every request

### Static Site Deployment

HTML generated once and deployed to server

```bash
# To generate a static site run:
npm run generate

# or
yarn generate
```


## Adding SEO

Nuxt uses [vue-meta](https://vue-meta.nuxtjs.org/). List of all `metaInfo` properties [here](https://vue-meta.nuxtjs.org/api/#metainfo-properties)

### /layout/default.vue

```javascript
  export default {
      head: {
        titleTemplate: '%s - Real World Events', // <-- title template
        meta: [
          {
            hid: 'description',
            name: 'description',  // <-- moved this over from index.vue
            content:
              'Where you can find all the events taking place in your neighborhood'
          }
        ]
      },
```

### views

```javascript
<script>
        <script>
    export default {
      head() {
        return {
          title: 'Event Listing'
        }
      }
    }
    </script>
```

## Lifecycle

![lifecycle hooks](https://nuxtjs.org/docs/2.x/nuxt-lifecycle.svg)

## Routing

All that is needed is to create files under the `./pages` directory. To naviate between internal pages you use `NuxtLink`.

```javascript
<template>
  <NuxtLink to="/">Home page</NuxtLink>
  <a href="https://nuxtjs.org">External Link to another page</a>
</template>
```

Nuxt automatically generates `./nuxt/router.js` based on the files in our pages directory.

## Layout

The only thing you need to include in the layout is the <Nuxt /> component which renders the page component.

`layouts/default.vue`
```javascript
<template>
  <Nuxt />
</template>
```

For custom layouts you need to use the `layout` property in the page like this:

```javascript
<script>
  export default {
    layout: 'blog'
    // page component definitions
  }
</script>
```

If you don't add a layout property to your page, e.g. layout: 'blog', then the default.vue layout will be used. Layout is special, since you should not include the <Nuxt/>  component inside its template

![views](https://nuxtjs.org/docs/2.x/views.png)

## Error page

The error page is a page component which is always displayed when an error occurs (that does not happen while server-side rendering). Created under layouts folder even when its really a page and not a layout.

## Context

```javascript
function (context) { // Could be asyncData, nuxtServerInit, ...
  // Always available
  const {
    app,
    store,
    route,
    params,
    query,
    env,
    isDev,
    isHMR,
    redirect,
    error,
   $config
  } = context

  // Only available on the Server-side
  if (process.server) {
    const { req, res, beforeNuxtRender } = context
  }

  // Only available on the Client-side
  if (process.client) {
    const { from, nuxtState } = context
  }
}
```


```javascript
export default {
  async asyncData({ params, $http, error }) {
    const id = params.id

    try {
      // Using the nuxtjs/http module here exposed via context.app
      const post = await $http.$get(`https://api.nuxtjs.dev/posts/${id}`)
      return { post }
    } catch (e) {
      error(e) // Show the nuxt error page with the thrown error
    }
  }
}
```

## Process Helpers

* `process.client` and `process.server`: help determine whether your app was rendered on the server or fully on the client

## `@nuxtjs/axios`

Nuxt uses [nuxtjs/axios](https://axios.nuxtjs.org/) for varios reasons:

- Automatically set base URL for client & server side
- Exposes `setToken` function to `$axios` so we can easily and globally set authentication tokens
- Automatically enables `withCredentials` when requesting to base URL
- Proxy request headers in SSR
- Fetch Style requests
- Integrated with Nuxt progress bar
- Integrated with Proxy Module
- Auto retry requests with axios-retry

### `asyncData(context)` method

Called each time before the page component is loaded. Nuxt will wait until API call is finished to render the component. The object retured will be **merged** with component data. We can use `async/await` or `Promises`.

### `fetch` hook

Hook that works on client and server-side to fill the store _before_ rendering the page. Unlike `asyncData` it does not return a value that merges with component data. 

## Testing
Why testing?

- Improve code quality
- Boosted confidence
- Better documentation

### Unit Testing

What to test?
- Components

Inputs:

- Component data
- props
- user interaction
- lifecycle methods
- vuex store
- route params

Outputs:
- What is rendered to DOM
- external function calls
- events emitted by component
- updates to vuex store
- changes in child components

### Snapshot Testing
### E2E Testing


## Resources

- [1] - Nuxt slides from Sebastien Chopin - Available: https://nuxtjs.slides.com/atinux/
- [2] - Tutorial de Nuxt - https://github.com/i62navpm/Tutorial-Nuxt
- [3] -
