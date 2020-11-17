# Personal Web (Powered by Hugo)

> "One way to start documenting is to give yourself permission to stop working on the thing, and grant yourself the space and time for reflection." -[Kamdyn Moore, Lead Program Manager (Spotify Design)](https://spotify.design/article/ask-spotify-design-01).

### Step by step to upload

- Change the baseurl in your config with `https://username.github.io`.
- Build the static pages with command `hugo -D`.
- Git init in your folder site.
- Remove the public folder with command `rm -rf public`.
- Use this command `git submodule add -b master https://github.com/username/username.github.io.git public`.
- And then do the usual git workflow (git status, git add, git commit, git push) in public folder.

> username is your github username, please don't make that mistake.

### References

- [Hugo github pages hosting and deployment tutorial](https://gohugo.io/hosting-and-deployment/hosting-on-github/#step-by-step-instructions).
- [Hugo quick start](https://gohugo.io/getting-started/quick-start/).
- [Search feature on pulp theme](https://koirand.github.io/blog/2018/pulp-search/)
