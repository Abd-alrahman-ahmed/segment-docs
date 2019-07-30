# segment-docs

This is a blank template for segment-docs



# Syntax highlighting

We're using Rouge, set in the `_config.yml`. It's now default for Jekyll 3 and later, so 🎉.

A list of the cues rouge accepts can be found [here](https://github.com/rouge-ruby/rouge/wiki/list-of-supported-languages-and-lexers).


# Frontmatter

- redirect_from: takes an array of URLs, and generates a "stub" page at this URL which redirects to the file in which you're listing this.

- beta: default false. When true, show a "in beta" warning in the page layout

- hide_from_sitemap: TODO

- seo-priority: values from `1.0` to `0.1`, default: `0.5 `. Sets the `Priority` tag in the sitemap

- seo-changefreq: default: `weekly `. Use the values [in the sitemap spec](https://www.sitemaps.org/protocol.html#xmlTagDefinitions). - sets the `changefreq` tag in the sitemap.xml generator, which tells search crawlers how often to check back.
