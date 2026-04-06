# takeshik/nuget-packages

Public prerelease NuGet feed hosted on GitHub Pages.

Packages are mirrored from [GitHub Packages](https://github.com/takeshik?tab=packages) and served as a static NuGet V3 feed that can be restored anonymously.
Stable releases continue to ship from [NuGet.org](https://www.nuget.org/).

## Feed URL

```
https://takeshik.github.io/nuget-packages/feed/index.json
```

## Adding this feed

```bash
dotnet nuget add source https://takeshik.github.io/nuget-packages/feed/index.json -n takeshik-prerelease
```

Or add to your `nuget.config`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="takeshik-prerelease"
         value="https://takeshik.github.io/nuget-packages/feed/index.json" />
  </packageSources>
</configuration>
```

## How it works

A scheduled workflow runs daily at 20:00 UTC (and can be triggered manually).
For each target defined in `.feeds/`:

1. Compares the versions available in GitHub Packages against what is already
   published in the static feed.
2. If new versions exist, downloads the corresponding `.nupkg` files from
   GitHub Packages using a scoped PAT.
3. Merges the new packages with the existing feed, applies version retention,
   and deploys the updated feed to GitHub Pages.

If no target has new versions, the workflow exits early without deploying.

## Adding a new target

Add a file at `.feeds/<id>.json`:

```json
{
  "id": "my-library",
  "packageIds": ["MyLibrary", "MyLibrary.Extensions"]
}
```

The workflow automatically discovers all `.feeds/*.json` files. `packageIds` must
match the NuGet package IDs as published to GitHub Packages.

## Repository setup

The following one-time steps are required to enable this workflow:

1. Enable GitHub Pages: **Settings → Pages → Source: GitHub Actions**.
2. Create a classic PAT with the `read:packages` scope and add it as a
   repository secret named `GH_PACKAGES_PAT`.
