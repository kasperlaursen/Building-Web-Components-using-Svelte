# Building Web Components using Svelte

So... I own a few 3D Printers, one of which is hooked up to a Raspberry PI Zero running [Octoprint](https://github.com/OctoPrint/OctoPrint).

I also run the open-source home automation platform [Home Assistant](https://www.home-assistant.io/) on a Raspberry PI 4 to control and automate all the smart sensors and lighting etc. in my house.

In this post, I will go through how I created a custom UI element for Home Assistant that transform my 3D Printer dashboard as shown below!

| Before      | After                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------------- |
| ![image (9)](https://user-images.githubusercontent.com/8472619/114595424-aad05080-9c8e-11eb-9f25-455ed8be0def.png) | ![Octoprint card](https://user-images.githubusercontent.com/8472619/114276747-50ce5180-9a28-11eb-99d5-2f921a8aba67.png) |

The code for my Home Assistant component is available on GitHub: https://github.com/kasperlaursen/octoprint-card

And the code for this tutorial can also be found on GitHub: https://github.com/kasperlaursen/Building-Web-Components-using-Svelte

If you're using [Visual Studio Code](https://code.visualstudio.com/) it's recommend installing the official extension [Svelte for VS Code](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode).

Let's just get into it, no one wants to read a multi-page introduction 😀

### Project Setup

To get started we need to generate a new Svelte project:

```
npx degit sveltejs/template my-svelte-project
```

Go to the folder this creates:

```
cd my-svelte-project
```

Let's enable Typescript:

```
node scripts/setupTypeScript.js
```

Install dependencies and run the local dev server:

```
npm install
npm run dev
```

### Compiling to a Web Component

Now let's compile the code to a Web Component rather than a full website!  
First, off we need to add the `svelte:options` tag to the top of our `App.svelte` component:

```
<svelte:options tag="my-web-component" />
```

Then we need to let the svelte compiler know that we want to create web components.  
Go to `rollup.config.js` and add the line `customElement: true,` as shown below:

```diffs
svelte({
 preprocess: sveltePreprocess({ sourceMap: !production }),
 compilerOptions: {
 // enable run-time checks when not in production
 dev: !production,
 customElement: true,
 },
}),

```

While we are in the rollup file, let's have svelte compile the CSS to the main bundle.js instead of a separate CSS file.  
This is done by removing the following code from the rollup file;

```
import css from "rollup-plugin-css-only";

....

// we'll extract any component CSS out into
// a separate file - better for performance
css({ output: "bundle.css" }),

```

At this point, Svelte is already creating a Web Component/Custom Element for us, but we are still rendering the component to the body element using svelte.

Go to the `main.ts` file and let's stop svelte from rendering the component, by removing the following code:

```
const app = new App({
 target: document.body,
 props: {
 name: 'world'
 }
});

export default app;
```

If you do **not** plan to integrate your web component with Home Assistant, you can simply delete `main.ts` and make the following change in `rollup.config.js`:

```
export default {
 // input: "src/main.ts",
 input: "src/App.svelte",
 output: {
```

Now your local dev server should show a blank page.  
So let's add the Web Component to the page.  
Go to `public/index.html` and update the file to look like below:

```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="utf-8" />
 <meta name="viewport" content="width=device-width,initial-scale=1" />

 <title>Svelte app</title>

 <link rel="icon" type="image/png" href="/favicon.png" />
 <link rel="stylesheet" href="/global.css" />

 <script defer src="/build/bundle.js"></script>
 </head>

 <body>
 <my-web-component />
 </body>
</html>

```

After Reloading your page you should now see `HELLO UNDEFINED!` on your page.  
To pass the name prop to your component simply add the property to the custom element in your index.html file as below:

```
<my-web-component name="world" />
```

So... This was the basics of generating a Web Component/Custom Element with Svelte. You can now add your bundle.js to any HTML page and the component will be available as an HTML custom element.

The next post will focus on integration with Home Assistant, and when available the link will be found {{Here}}!

The code for this project is available on GitHub: https://github.com/kasperlaursen/Building-Web-Components-using-Svelte  
Where there can be a commit for each section (header) on these posts!
