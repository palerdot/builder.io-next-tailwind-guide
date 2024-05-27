# Builder.io + Next + Tailwind guide

Builder.io lets you build websites with a simple drag and drop interface. It also allows you to integrate seamlessly with existing components  built by your developers. Let us explore how to bootstrap a `Builder.io` drag and drop interface within a `Next.js` app.


Before configuring a nextjs powered builder.io project, you can also quickly bootstrap this setup in two simple commands.

1. Configure Nextjs with tailwind

```javascript
npx create-next@latest
```

Select `tailwind` in the configuration screen.

2. Configure `builder.io` automatically

```javascript
npm init builder.io --latest
```

After you run this command, you can open the webapp at `localhost:3000` and everything will be automatically configured for you. 

Now let us check out how to configure a `Builder.io` project with `Next.js/tailwind`.

### Setup Nextjs app (`app` directory)

```javascript
npx create-next
```

You can bootstrap a nextjs app with your preferred configuration. You can select `tailwind` as your preferred styling library in the configuration screen.

# Configuring `Tailwind` manually

Let us see how to configure `Tailwind` manually in your existing nextjs project

- Install tailwind dependencies

```javascript
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

- Configure `tailwind.config.js` with the following content

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
 
    // Or if using `src` directory:
    "./src/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

- Add tailwind directives to your `globals.css`

```javascript
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Congrats! You have configured tailwind for your `Next` app.


# Configure `Builder`

To get started with `builder.io`, add `Builder` dependencies.

```javascript
npm install --save @builder.io/sdk @builder.io/react
```

### Getting `Builder` API Key

You need a valid api key to configure a `Builder` project. You can get this easily by logging in/signing up for a `builder.io` account. If you have a github account, you can easily create an account with one single click. 

After logging into your `builder.io` account, you can get your api keys by pressing `Cmd + k` and typing `api key`. Select, `Copy API` key option from the command palette.

Alternatively, you can go to the account settings screen and get your API Key.

### Create `Builder` component

- Create a `components/builder.tsx` file and add the following contents

```javascript
// components/builder.tsx
"use client";
import { BuilderComponent, useIsPreviewing } from "@builder.io/react"; 
import { BuilderContent, builder } from '@builder.io/sdk';
import DefaultErrorPage from "next/error";

interface BuilderPageProps { 
  content?: BuilderContent;
}

// Replace with your Public API Key
builder.init(YOUR_API_KEY);

export function RenderBuilderContent({ content }: BuilderPageProps) { 
  // Call the useIsPreviewing hook to determine if 
  // the page is being previewed in Builder
  const isPreviewing = useIsPreviewing(); 
  // If `content` has a value or the page is being previewed in Builder,
  // render the BuilderComponent with the specified content and model props.
  if (content || isPreviewing) {
    return <BuilderComponent content={content} model="page" />;
  }
  // If the `content` is falsy and the page is 
  // not being previewed in Builder, render the 
  // DefaultErrorPage with a 404.
  return <DefaultErrorPage statusCode={404} />; 
}
```

## Configure App Page

- Create a `app/[...page]/page.tsx` file with following contents

```javascript
// Example file structure, app/[...page]/page.tsx
// You could alternatively use src/app/[...page]/page.tsx
import { builder } from "@builder.io/sdk";
import { RenderBuilderContent } from "../../components/builder";

// Replace with your Public API Key
builder.init(YOUR_API_KEY);

interface PageProps {
  params: {
    page: string[];
  };
}

export default async function Page(props: PageProps) {
  const content = await builder
    // Get the page content from Builder with the specified options
    .get("page", {
      userAttributes: {
        // Use the page path specified in the URL to fetch the content
        urlPath: "/" + (props?.params?.page?.join("/") || ""),
      },
      // Set prerender to false to return JSON instead of HTML
      prerender: false,
    })
    // Convert the result to a promise
    .toPromise();

  return (
    <>
      {/* Render the Builder page */}
      <RenderBuilderContent content={content} />
    </>
  );
}
```


### Configure model preview URL

Most common way to use `Builder` with your existing `nextjs` app is to configure a page model. This enables `Builder` to open your app in the browser visual editor.

To enable preview URL

- Go to the [Models](https://builder.io/models) section and choose the page model.
- Set the preview URL to `http://localhost:3000`. Make sure to configure your port correctly if your nextjs app is using a different port.

### Creating Builder Pages

Most powerful feature of `Builder` is an intuitive drag-and-drop page builder that integrates seamlessly with your existing codebase. You can use this drag-and-drop page builder without any coding knowledge.

To create `Builder` Pages
- Go to [Content](https://builder.io/content) and click `+New`. 
- Create a new page by giving an appropriate title, e.g `Contact`. `Builder` automatically generates an URL (`/contact`) for you.
- In the newly created page, drag and drop any elements from the visual editor. For example, you can drag a `Contact us` text block.
- Click `Publish` to publish your changes. Now visit the `http://localhost:3000/contact` page. You have created a `Contact us` page without any coding.

The best part about `Builder` is your drag and drop pages will seamlessly integrate with custom components created by your developers. 

For a more detailed guide, visit https://www.builder.io/c/docs/integrating-builder-pages. For an example repo, checkout https://github.com/palerdot/builder.io-next-tailwind-guide.
