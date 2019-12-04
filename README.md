# Blazor Pages

[![Azure Status](https://dev.azure.com/fernandreu-public/BlazorPages/_apis/build/status/fernandreu.blazor-pages?branchName=master)](https://dev.azure.com/fernandreu-public/BlazorPages/_build/latest?definitionId=6?branchName=master)
[![Actions Status](https://github.com/fernandreu/blazor-pages/workflows/gh-pages/badge.svg)](https://github.com/fernandreu/blazor-pages/actions)


This project is an example of using Azure Pipelines / GitHub Actions to automatically deploy a client-side
Blazor app to Github Pages. For a live demo, check the following link:

https://fernando.andreu.info/blazor-pages/

Microsoft Docs already contains a [general overview](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/blazor/client-side?view=aspnetcore-3.0#github-pages)
of the steps needed for a successful deploy, including an example of the final result 
([repository](https://github.com/blazor-demo/blazor-demo.github.io) / [live site](https://blazor-demo.github.io/)).


This project goes one step ahead by:

- providing the full solution from where the pages are built;
- showing the use of an auxiliary [Shared](src/Shared) project which could be re-used in
  the ASP.NET Core server (similarly to how the combined client- and server-side Blazor
  template does); and
- automating the entire build and deployment to GitHub Pages.


## How it works

The CI pipelines first perform a normal `dotnet publish` of the app, which will generate
a `dist` bundle ready to be deployed. This bundle is then pushed differently depending on
the CI environment:

- Azure Pipelines: the bundle is force pushed to `gh-pages` by using raw Git
commands
- GitHub Actions: an already existing [action](https://github.com/marketplace/actions/deploy-to-github-pages)
is used to push the bundle to `gh-pages-from-actions` (this will need to be renamed to
`gh-pages` to host your website from this branch)


## How to use this for your own project

The `<base>` url in [`index.html`](src/Client/wwwroot/index.html) will need to be modified 
depending on where the project is deployed. If deploying on the root level, set 
`segmentCount = 0` in [`404.html`](src/Client/wwwroot/404.html) as well.

When testing on localhost, the `applicationUrl` for IIS Express in 
[`launchSettings.json`](src/Client/Properties/launchSettings.json) will need to be updated to 
reflect the same base url as in [`index.html`](src/Client/wwwroot/index.html).

Paths in the [Azure Pipelines yaml file](azure-pipelines.yml) / [GitHub Actions workflow](.github/workflows/gh-pages.yml)
may need to be updated accordingly.

*The presence of the [`.nojekyll`](src/Client/wwwroot/.nojekyll) file in `wwwroot` can be 
[quite important](https://help.github.com/en/articles/files-that-start-with-an-underscore-are-missing).*


## CI / CD

The Azure pipeline is expecting access to one variable group named `GitHubPATGroup`, which
should contain the following three variables:

- `GitHubPAT`: A Personal Access Token with sufficient permission to (force) push to the `gh-pages` branch
- `GitHubName`: The name of the user committing to the `gh-pages` branch
- `GitHubEmail`: The email of the user committing to the `gh-pages` branch

The `gh-pages` branch **must** exist already for the deployment to be successful (this
is a temporary limitation in the pipeline configuration).

In the case of GitHub Actions, only a single secret is needed: `ACCESS_TOKEN`, equivalent to `GitHubPAT` above. 
