## Running and serving a dev build

```sh
npm install
npm run start
```

Browse to [http://localhost:8080](http://localhost:8080).

## Running and serving a prod build

```sh
npm run build
```

Output files are generated into the `_site` folder.

## Project structure

```
src/
  _includes/
    All UI partials. Inside the css folder, in the global.liquid you can change the CSS variables
  _data/
    Here you can change the site info, like the title and description
  posts/
    Each individual post in markdown files
  public/
    This folder contians statics files, copied directly into the output, like the favicon, for ecample
Configuration and build files
```
