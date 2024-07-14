# Chemical
Easily create your own web proxy with no experience required.

## Setup

A simple example can be found in [`/example/`](https://github.com/chemicaljs/chemical/tree/main/examples/example).

A example with the vite plugin can be found in [`/example-vite/`](https://github.com/chemicaljs/chemical/tree/main/examples/example-vite).

A example with all the components can be found in [`/example-components/`](https://github.com/chemicaljs/chemical/tree/main/examples/example-components).

A real word styled example can be found in [`/example-styled/`](https://github.com/chemicaljs/chemical/tree/main/examples/example-styled).

### Server

Create a new Node.js project and create a script file for the server.

1. Install Chemical `npm install chemicaljs`.

2. Import `ChemicalServer` and create a new server.

```js
import { ChemicalServer } from "chemicaljs";

const chemical = new ChemicalServer();
```

You can pass options to disable proxy services and set the default service.

```js
const chemical = new ChemicalServer({
    default: "rammerhead",
    uv: true,
    scramjet: false,
    rammerhead: true,
    hostname_blacklist: [ /google\.com/, /reddit\.com/ ],
    hostname_whitelist: [ /example\.com/ ]
});
```

3. Use `chemical.app` which is an express app. You may need to import express for certain APIs.

```js
chemical.app.get("/", function(req, res){
    res.send("Hello World");
});
```

4. Use `chemical.server` and listen on a port of your choosing.

```js
chemical.server.listen(3000);
```

Below is an example of a simple backend. This example will setup Chemical and serve the "public" folder along with the `index.html` file as `/` and `.html` files without the extension.

```js
import { ChemicalServer } from "chemicaljs";
import express from "express";

const chemical = new ChemicalServer();
const port = process.env.PORT || 3000;

chemical.app.use(express.static("public", {
    index: "index.html",
    extensions: ["html"]
}));

chemical.server.listen(port, () => {
    console.log(`Chemical demo listening on port ${port}`);
});
```

### Client

In your project create a folder to store your static assets. Create an index.html file which will be the homepage of your website.

1. Add the Chemical script to the top of your page. This will load all needed scripts for Chemical and other packages.

```html
<script src="/chemical.js"></script>
```

If you want to set the wisp server to an external server just change the `wisp` attribute.

```html
<script wisp="wss://wisp.mercurywork.shop/" src="/chemical.js"></script>
```

If you want to set the transport just change the `transport` attribute. Choose `libcurl` (default becuase it supports Firefox) or `epoxy`.

```html
<script transport="epoxy" src="/chemical.js"></script>
```

2. In a inline script or javascript file, encode a URL with Chemical using the async function `window.chemicalEncode`.

```js
await window.chemicalEncode("https://example.com")
```

Optional: Change service to `uv`, `scramjet`, or `rammerhead`. Defaults to `uv` or server option.

```js
await window.chemicalEncode("https://example.com", "rammerhead")
```

3. You may want to check if Chemical has loaded before encoding a URL.

```js
if (window.chemicalLoaded) {
    //Chemical is loaded
}
```

```js
window.addEventListener("chemicalLoaded", function(e) {
    //Chemical has loaded
});
```

4. Change the transport and Wisp URL with `chemicalTransport`

```js
await chemicalTransport("libcurl", "wss://wisp.mercurywork.shop/")
```

Below is a simple example of a simple input that redirects to the encoded URL when the user presses enter. It checks if there is any input and if Chemical has loaded before loading.

```html
<h1>Chemical Example</h1>
<input id="search" placeholder="Enter URL">

<script src="/chemical.js"></script>
<script>
    const search = document.getElementById("search");

    search.addEventListener("keydown", async function (e) {
        if (e.key == "Enter" && window.chemicalLoaded && e.target.value) {
            window.location = await window.chemicalEncode(e.target.value)
        }
    })
</script>
```

## Vite Plugin

1. Create a new vite app and open `vite.config.js` or `vite.config.ts`

2. Import `ChemicalVitePlugin` and add it to plugins.

```js
import { defineConfig } from "vite"
import { ChemicalVitePlugin } from "chemicaljs"

export default defineConfig({
    plugins: [/*Other plugins*/ChemicalVitePlugin()],
})
```

You can pass options to just like on the main server.

```js
export default defineConfig({
    plugins: [
        ChemicalVitePlugin({
            default: "rammerhead",
            uv: true,
            scramjet: false,
            rammerhead: true,
            hostname_blacklist: [ /google\.com/, /reddit\.com/ ],
            hostname_whitelist: [ /example\.com/ ]
        })
    ],
})
```

## Components
Setup a proxy site with easy HTML components.

1. Add the Chemical components along with the main script to the top of your page.

```html
<script src="/chemical.js"></script>
<script src="/chemical.components.js"></script>
```

2. Now add any component to your site!

### Basic Input

Opens in the current tab when the enter key is pressed.

```html
<input target="_self" placeholder="Enter URL" is="chemical-input">
```

Opens in current tab when the enter key is pressed.

```html
<input target="_blank" placeholder="Enter URL" is="chemical-input">
```

Custom action when the enter key is pressed. Change `action` to your function name. The first parameter of the action will be the encoded URL.

```html
<input action="logURL" placeholder="Enter URL" is="chemical-input">
<script>
    function logURL(url) {
        console.log(url)
    }
</script>
```

### Input with Button

Opens when the enter key is pressed or button is clicked. Set the `for` attribute to the `id` of the button.

```html
<input id="my-input" target="_blank" placeholder="Enter URL" is="chemical-input">
<button for="my-input" is="chemical-button">Go!</button>
```

### With iframe

A hidden iframe that is shown when the enter key is pressed. Set the `frame` attribute to the `id` of the iframe.

```html
<input frame="my-iframe" placeholder="Enter URL" is="chemical-input">
<iframe id="my-iframe" is="chemical-iframe"></iframe>
```

A hidden iframe that is shown when the enter key is pressed. Includes controls the are hidden when the iframe is hidden and can control websites in the iframe as well as hiding the iframe and controls.

Set the `frame` attribute to the `id` of the iframe.
Set the `controls` attribute to the `id` of the controls.

Set the second parameter of `chemicalAction` to the `id` of the iframe.

```html
<input frame="my-iframe-2" placeholder="Enter URL" is="chemical-input">
<section id="my-controls-2" is="chemical-controls">
    <button onclick="chemicalAction('back', 'my-iframe-2')">←</button>
    <button onclick="chemicalAction('forward', 'my-iframe-2')">→</button>
    <button onclick="chemicalAction('reload', 'my-iframe-2')">⟳</button>
    <button onclick="chemicalAction('close', 'my-iframe-2')">🗙</button>
</section>
<iframe controls="my-controls-2" id="my-iframe-2" is="chemical-iframe"></iframe>
```

## License
Chemical Uses the MIT license.
