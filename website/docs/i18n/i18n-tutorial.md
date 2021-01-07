---
id: tutorial
title: i18n tutorial
sidebar_label: Tutorial
slug: /i18n/tutorial
---

This tutorial will walk you through the translation of a new Docusaurus website.

Initialize a new site with `npx @docusaurus/init@latest init my-website classic` (like [this one](https://github.com/facebook/docusaurus/tree/master/examples/classic)).

We will translate this new site in english and french, assuming english is the default language.

## Configure your site

Modify `docusaurus.config.js` to add the i18n support for the french language.

### Site configuration

Declare the i18n locales and their respective configuration options:

```js title="docusaurus.config.js"
module.exports = {
  i18n: {
    defaultLocale: 'en',
    locales: ['en', 'fr'],
    localeConfigs: {
      en: {
        label: 'English',
      },
      fr: {
        label: 'Français',
      },
    },
  },
};
```

TODO link to ref api for exhaustive list of option and move underlying note

### Theme configuration

Add a navbar dropdown so that users can select the locale they want to use:

```js title="docusaurus.config.js"
module.exports = {
  themeConfig: {
    navbar: {
      items: [
        // highlight-start
        {
          type: 'localeDropdown',
          position: 'left',
        },
        // highlight-end
      ],
    },
  },
};
```

### Start your site

Start your localized site in dev mode, using the locale of your choice:

```bash npm2yarn
npm run start --locale fr
```

Your site is accessible at **`http://localhost:3000/fr/`**, but **falls back to untranslated content**.

:::caution

Each locale is a **distinct standalone single-page-application**: it is not possible to start the Docusaurus sites in all locales at the same time.

:::

## Translate your site

Let's see how to actually provide the translations.

The french translations will be put in `website/i18n/fr`.

Docusaurus is modular, and each content plugin has its own subfolder.

:::note

After copying files around, restart your site with `npm run start --locale fr`.

Hot-reload will work better when editing existing files.

:::

### Use the translation APIs

Open your homepage, and use the Docusaurus translation APIs:

```jsx title="src/index.js"
import React from 'react';
import Layout from '@theme/Layout';
// highlight-start
import Translate, {translate} from '@docusaurus/Translate';
// highlight-end

export default function Home() {
  return (
    <Layout>
      <h1>
        {/* highlight-start */}
        <Translate description="The homepage welcome message">
          Welcome to my website
        </Translate>
        {/* highlight-end */}
      </h1>

      <div>
        <input
          type="text"
          placeholder={
            // highlight-start
            translate({
              message: 'Hello',
              description: 'The homepage input placeholder',
            })
            // highlight-end
          }
        />
      </div>
    </Layout>
  );
}
```

:::caution

Docusaurus provides a **very simple and lightweight translation runtime**: documentation websites generally don't need advanced i18n features.

:::

### Translate JSON files

JSON translation files are used for everything that is not contained in a Markdown document:

- React/JSX code
- Layout navbar and footer labels
- Docs sidebar category labels
- ...

Run the following command:

```bash npm2yarn
npm run write-translations --locale fr
```

It will extract and initialize the JSON translation files that you need to translate.

The homepage translations are extract from React source code:

```json title="i18n/fr/code.json"
{
  "Welcome to my website": {
    "message": "Welcome to my website",
    "description": "The homepage welcome message"
  },
  "Hello": {
    "message": "Hello",
    "description": "The homepage input placeholder"
  }
}
```

Plugins and themes will also write JSON translation files, such as:

```json title="i18n/fr/docusaurus-theme-classic/navbar.json"
{
  "title": {
    "message": "My Site",
    "description": "The title in the navbar"
  },
  "item.label.Docs": {
    "message": "Docs",
    "description": "Navbar item with label Docs"
  },
  "item.label.Blog": {
    "message": "Blog",
    "description": "Navbar item with label Blog"
  },
  "item.label.GitHub": {
    "message": "GitHub",
    "description": "Navbar item with label GitHub"
  }
}
```

Translate the `message` attribute in the JSON files of `i18n/fr`, and your site layout and homepage should now be translated.

### Translate Markdown files

Official Docusaurus content plugins extensively use Markdown/MDX files, and allow you to translate them.

#### Translate the docs

Copy your docs Markdown files to `i18n/fr/docusaurus-plugin-content-docs/current`, and translate them:

```bash
mkdir -p i18n/fr/docusaurus-plugin-content-docs/current
cp -r docs/** i18n/fr/docusaurus-plugin-content-docs/current
```

:::info

`current` is needed for the docs versioning feature: each docs version has its own subfolder.

:::

#### Translate the blog

Copy your blog Markdown files to `i18n/fr/docusaurus-plugin-content-blog`, and translate them:

```bash
mkdir -p i18n/fr/docusaurus-plugin-content-blog
cp -r blog/** i18n/fr/docusaurus-plugin-content-blog
```

#### Translate the pages

Copy your pages Markdown files to `i18n/fr/docusaurus-plugin-content-pages`, and translate them:

```bash
mkdir -p i18n/fr/docusaurus-plugin-content-pages
cp -r pages/**.md i18n/fr/docusaurus-plugin-content-pages
cp -r pages/**.mdx i18n/fr/docusaurus-plugin-content-pages
```

:::caution

We only copy `.md` and `.mdx` files, as React components are translated through JSON translation files already.

:::

## Deploy your site

You can choose to deploy your site under a single domain, or use multiple (sub)domains.

### Single-domain deployment

Run the following command:

```bash npm2yarn
npm run build
```

It will build one single-page application per locale:

- `website/build`: for the default, english language
- `website/build/fr`: for the french language

You can now [deploy](../deployment.mdx) the `build` folder to the static hosting solution of your choice.

:::note

The Docusaurus v2 website use this strategy:

- [https://v2.docusaurus.io](https://v2.docusaurus.io)
- [https://v2.docusaurus.io/fr](https://v2.docusaurus.io/fr)

:::

### Multi-domain deployment

You can also build your site for a single locale:

```bash npm2yarn
npm run build --locale fr
```

Building for a single locale does not add the `/fr/` url prefix.

On your [static hosting provider](../deployment.mdx):

- create one deployment per locale
- configure the appropriate build command, using the `--locale` option
- configure the domain of your choice

:::caution

This strategy is not possible with Github Pages, as it is only possible to have one deployment.

:::