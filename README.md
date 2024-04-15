# vue-demo-search

## Introduce
This project is a demo for vue3, which contains 'search' function with sdk-js. This demo will show you how to use sdk-js in vue3.

## Before setup
Clone sdk-js(https://github.com/k-ai-Documentation/sdk-js) and vue-demo-search(https://github.com/k-ai-Documentation/vue-demo-search).

And your directory should like this:
```
|-your root directory
    |-vue-demo
    |-sdk-js
```
open terminal and run
```
cd vue-demo
```
Add your 3 keys in .env.development file.
```
VUE_APP_ORGANIZATION_ID = ''
VUE_APP_INSTANCE_ID = ''
VUE_APP_API_KEY = ''
```

## Installation
```
npm install ../sdk-js

npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

# How to use sdk-js(https://github.com/k-ai-Documentation/sdk-js)

+ make sure you have installed sdk-js and react-demo.
```
npm install ../sdk-js
npm install
```
+ check in your package.json and node_modules, sdk-js should be installed.

+ check .env.development has VUE_APP_ORGANIZATION_ID, VUE_APP_INSTANCE_ID and VUE_APP_API_KEY.

+ in your vue file, import sdk-js.
```
import { KaiStudio } from "kaistudio-sdk-js";
```
if your are using typescript, you need to define searchResult.
```
import type { SearchResult } from 'kaistudio-sdk-js/modules/Search';
```
+ Create your sdk instance
````
let sdk = new KaiStudio({ organizationId: process.env.VUE_APP_ORGANIZATION_ID , instanceId: process.env.VUE_APP_INSTANCE_ID, apiKey: process.env.VUE_APP_API_KEY });
````

+ use it in your methods
```
async function searchRequest(){
    try {
        if(searchInput.value) {
            searchAnswer.value = null
            const request = await sdk.search().query(searchInput.value, "")
            searchAnswer.value = request
        }
    } catch(e) {
        console.error(e)
        return null
    }
}
```
