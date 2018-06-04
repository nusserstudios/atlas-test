[![Atlas, Hugo Boilerplate](https://atlas.indigotree.co.uk/images/github-banner.png)](https://github.com/indigotree/atlas)

![](http://forthebadge.com/images/badges/uses-badges.svg) ![](http://forthebadge.com/images/badges/built-by-developers.svg) ![](http://forthebadge.com/images/badges/gluten-free.svg)

# Atlas

The [Hugo](https://gohugo.io/) boilerplate we use for our projects.

**Disclaimer** - This boilerplate has been heavily integrated with [Netlify](https://www.netlify.com/), and therefore many features are specific to the Netlify platform and may not work with other hosting providers.

**Disclaimer** - Atlas is a boilerplate (starter kit) for bespoke Hugo projects. It's not a Hugo theme and cannot be placed inside the `/themes` directory. Check the [theme](#themes) docs for more information.

## Features

Atlas provides the following features out of the box:

* A set of [Gulp](/gulpfile.babel.js) tasks for SASS, Linting, ES2015, Image compression
* Environment driven `robots.txt` file (disallows robots on everything other than production)
* Base HTML templates with easy customisation/extension
* [Configuration](/netlify.toml) for Netlify deployments
* [Better defaults](#security-headers) for configuring HTTPS
* [Better redirects](#redirects) with Netlify instead of `<meta http-equiv="refresh">`

## Prerequisite

Atlas does not include a copy of the `hugo` binary. You will need to [install Hugo](https://gohugo.io/getting-started/installing/) first you can run any of the [commands](#available-commands) mentioned below.

## Getting Started

To get started, you can either clone the repository, or deploy straight to [Netlify](#deploy-to-netlify). Then run the following from the project root:

```
npm install
npm run server
```

### Available Commands

There are 3 commands available:

* `npm run build` - Builds assets (sass, js, fonts, images) and runs `hugo`
* `npm run build:preview` - The same as `build`, but runs `hugo --buildDrafts --buildFuture`
* `npm run server` - Runs BrowserSync and watches for changes, running `build` when changes are detected

## Robots.txt

A default robots.txt can be found at `/layouts/robots.txt` which is configured to disallow crawlers when the `HUGO_ENV` environment variable is **not** set to `"production"`.

The default behaviour is to disallow search engines on "branch" deployments. If you're using split testing, you will need to modify the default robots.txt template to ensure your branch deployments can be indexed.

## Functions

Atlas has `netlify-lambda` installed out of the box to make working with Netlify Functions that much easier. Functions should be made inside `src/lambda` and should end in the `.js` extension. They will be compiled into `/functions` where Netlify will recognise them and deploy them automatically.

Here is an example that you can start from:

```
exports.handler = (event, context, callback) => {
    callback(null, {
        statusCode: 200,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            message: "Hi from Lambda."
        })
    });
}
```

## Headers

Headers can be configured within `/layouts/index.headers`, which is then built to `/public/_headers`.

This is a Netlify feature. Learn more about [Headers with Netlify](https://www.netlify.com/docs/headers-and-basic-auth/).

### Security Headers

Atlas comes with some default headers to help you better protect your site. The default headers we include are: [X-Frame-Options](https://scotthelme.co.uk/hardening-your-http-response-headers/#x-frame-options), [X-XSS-Protection](https://scotthelme.co.uk/hardening-your-http-response-headers/#x-xss-protection), [X-Content-Type-Options](https://scotthelme.co.uk/hardening-your-http-response-headers/#x-content-type-options), [Referrer-Policy](https://scotthelme.co.uk/a-new-security-header-referrer-policy/).

These headers are configured with the following values:

```
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
Referrer-Policy: origin-when-cross-origin
```

## Redirects

Redirect rules can be appended to `/layouts/index.redirects`, which is then built to `/public/_redirects`.

This is a Netlify feature. Learn more about [Netlify Redirects](https://www.netlify.com/docs/redirects/).

### Aliases

Hugo [Aliases](https://gohugo.io/content-management/urls/#aliases) are usually handled by `<meta http-equiv="refresh" ...>` tags. These have been disabled within `config.toml` with `disableAliases = true`, and instead are handled by [Netlify Redirects](https://www.netlify.com/docs/redirects/). This is handled automatically and you should continue to add aliases as described in the Hugo documentation.

## Netlify CMS

Atlas provides a copy of [Netlify CMS](https://www.netlifycms.org/) which is a fantastic CMS for JAMstack sites where everything is managed by GIT.

You will need to configure Netlify CMS to point to your own repo. You can do this within `/static/admin/config.yml` by updating `backend: repo:` with your repository information. Here's an example:

```
backend:
  name: github
  repo: indigotree/atlas
```

Checkout the [quick start](https://www.netlifycms.org/docs/quick-start/) section on the Netlify CMS docs for more information.

### Remove Netlify CMS

If Netlify CMS isn't your thing, you can remove it with:

```
npm run cms:delete
```

## Themes

Atlas is a boilerplate (starter kit) designed to aid bespoke Hugo development. Using existing themes with Atlas is possible, but unsupported.

Atlas files will take priority over your theme due to the order Hugo looks for files. For this reason, you will have to remove most of the files inside `/layouts` with the exception of `_headers`, `_redirects` and `robots.txt`.

If you wish the develop your site as a theme inside Atlas, you can copy `/layouts` into your theme and update the references within the `gulpfile.babel.js`.


## File Structure

```
│
└──── /layouts               - Template files
│   │ 404.html               - 404 Template
│   │ index.headers          - Custom Netlify HTTP headers
│   │ index.redirects        - Custom Netlify redirect rules
│   │ robots.txt             - Template for robots.txt
│   │
│   └──── /_default          - Base templates for list & singular pages
│   │   │ baseof.html        - Base template
│   │   │ list.html          - List/taxonomy template
│   │   │ single.html        - Singular page/post template
│   │
│   └──── /partials          - Partials
│       │
│       └──── /site          - Site partials loaded into _default/baseof.html template
│           │ meta.html      - Site <meta> tags
│           │ header.html    - Sites primary <header>
│           │ footer.html    - Sites primary <footer>
│           │ scripts.html   - JavaScript <script> referenced before closing </body>
│           │ styles.html    - Stylesheets referenced before closing </head>
│   │
|   └──── /src               - Source files for assets (SASS, JS, Images, Fonts etc)
│   │
│   └──── /static            - Hugo static resources
│       │
│       └──── /admin         - Netlify CMS templates
│
│ .gitignore
│ .sass-lint.yml             - Linting rules for sass-lint
│ LICENSE
│ README.md
│ config.toml                - Hugo configuration
│ gulpfile.babel.js          - Gulp configuration/tasks
│ netlify.toml               - Netlify configuration
│ package.json
```

## Deploy to Netlify

You can deploy directly to Netlify using this button:

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/indigotree/atlas)

## License

MIT © [Indigo Tree](https://indigotree.co.uk)
