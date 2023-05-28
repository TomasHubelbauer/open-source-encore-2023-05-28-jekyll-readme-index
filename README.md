# Open Source Encore: `jekyll-readme-index` support for configurable regex

`jekyll-readme-index` is a Jekyll plugin developed by Ben Balter of GitHub.
It is used to configure Jekyll to treat `README.md` (and some other extensions)
as the index page of a Jekyll site, which usually is `index.html` or `index.md`.

<https://github.com/benbalter/jekyll-readme-index>

The motivation for this plugin is simple - GitHub renders READMEs as if they
were the "index" files of a repository and GitHub also has the GitHub Pages
feature for turning repositories into small websites, but the default Jekyll
config and the GitHub convention of using `README.md` instead of `index.md`
(this is a convention which predates GitHub) do no go together to form a smooth
experience.

Users would either need to have separate files for the repository view and the
site index or they would need to preface all READMEs with empty front matter
(because I think Jekyll will fall back to any MarkDown file if it doesn't find
a README as long as that file has front matter - but this could be wrong!) and
in general it just doesn't jive super well.

This plugin makes it jive super well and comes out of the box on GitHub Pages.

One thing the plugin doesn't do, though, is to follow and support README file
overrides the way GitHub does. GitHub has a feature where if your repository has
a README, but you don't want that README to be the one that gets rendered on
GitHub as the "index readme" and you can't change the README (because the repo
is a mirror for example, or other files depend on it being on that path - or you
just don't want to!), then you can use a file named `.github/README.md` (or
`root/README.md` or `docs/README.md`) and GitHub will render that file instead
of your README if it exists.

Ben's plugin just looks for straight READMEs and doesn't do any fallbacks or
overrides or anthing.

I suggested it be updated to support the GitHub fallback logic here:

<https://github.com/benbalter/jekyll-readme-index/issues/38>

But the more I thought about the proposal, the more convinced I became an even
more general solution would be better.
This led me to propose a change that would make the regex used to detech which
files are readmes configurable:

<https://github.com/benbalter/jekyll-readme-index/pull/39>

This solution suffers from one unfortunate downside which is that there is no
way to define the resolution order in case the regex matches multiple files.

If there was one, it would be possible to use this configuration to encode the
GitHub behavior, but there isn't.

I think the added complexity of supporting that isn't worth the effort, or at
least I don't feel like putting in that effort myself, but if it were to be
done, the way to go about it would I think be to allow configuring an array of
regexes and check the files with each in their order.

In that case, a configuration option like this would do the trick:

```yml
regex:
- "(.github|root|docs)\/README.md"
- README.md
```

This encodes the two levels of precedence - you either have the main README and
that's that or you have the override one alongside it.

To encode the precedence rules between the variants of the alternative readme,
one would have to go even deeper:

<https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes>

> If you put your README file in your repository's hidden `.github`, `root`, or
> `docs` directory, GitHub will recognize and automatically surface your README
> to repository visitors.

> If a repository contains more than one README file, then the file shown is
> chosen from locations in the following order: the `.github` directory, then
> the repository's `root` directory, and finally the `docs` directory.

```yml
regex:
- .github/README.md
- root/README.md
- docs/README.md
- README.md
```

This flexibility would be amazing and a better Rubyist than me might contribute
the support for this one day, hopefully.

I am not sure Ben's plugin is really maintained anymore, as I've bemoaned in
previous posts of mine, GitHub Pages uses outdated versions of Jekyll and Liquid
already so I am not certain there will be big hurry to update its built-in
plugins either, but that's no reason not to try and push this as far along as I
am able to from my position.
