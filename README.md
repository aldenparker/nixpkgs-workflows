# nixpkgs-workflows
A set of workflows for running reviews and keeping my packages up to date. `review.yml` and `build.yml` were taken from [nixpkgs-review-gha](https://github.com/Defelo/nixpkgs-review-gha) and modified to suit my needs, while the `upkeep.yml` was heavily inspired by [nixpkgs-upkeep](https://github.com/niklaskorz/nixpkgs-upkeep). These are mostly to make my life easier and take advantage of the easy cross compile available in Github Actions.

## review.yml
Runs [nixpkgs-review](https://github.com/Mic92/nixpkgs-review) in GitHub Actions across Linux and Mac runners. Many options including posting results to the pull request of origin and cacheing the build.

### Setup
1. Fork the original [nixpkgs-review-gha](https://github.com/Defelo/nixpkgs-review-gha) repository (Or mine if you want the other workflows as well).
2. In your fork, go to the [Actions](../../actions) tab and enable GitHub Actions workflows.

That's it! Below are how to get some of the more advanced features working:

**Auto Post / Approve Nixpkgs PR**: You need to generate a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens):
    1. Go to <https://github.com/settings/tokens> and generate a new **classic** token with the `public_repo` scope.
    2. In your fork, go to "Settings" > "Secrets and variables" > "actions" and [add a new repository secret](../../settings/secrets/actions/new) with the name `GH_TOKEN` and set its value to the personal access token you generated before.
    
**Cache Build (Attic)**: Replace `$CACHE` with the name of your cache (e.g. `nixpkgs`) and `$SERVER` with the url of your Attic server (e.g. `https://attic.example.com/`):
    1. Generate a token with `push` and `pull` permissions: `atticadm make-token --sub nixpkgs-review-gha --validity 1y --pull $CACHE --push $CACHE`
    2. [Create a new variable](../../settings/variables/actions/new) with the name `ATTIC_SERVER` and set it to the value of `$SERVER`
    3. [Create a new variable](../../settings/variables/actions/new) with the name `ATTIC_CACHE` and set it to the value of `$CACHE`
    4. [Create a new secret](../../settings/secrets/actions/new) with the name `ATTIC_TOKEN` and set its value to the token you generated before.
    
**Cache Build (Cachix)**: Note: If both an Attic cache and a Cachix cache is configured, the Attic cache is preferred and the Cachix configuration is ignored.
    1. Go to https://app.cachix.org/ and set up your binary cache.
    2. [Create a new variable](../../settings/variables/actions/new) with the name `CACHIX_CACHE` and set it to the name of your Cachix cache.
    3. [Create a new secret](../../settings/secrets/actions/new) with the name `CACHIX_AUTH_TOKEN` and set its value to your auth token. If you are using a self-signed cache, you also need to create a `CACHIX_SIGNING_KEY` secret and set its value to your private signing key.
    
**Review and PR Track Button in Nixpkg PRs**: Add [`review-button.js`](review-button.js) as a user script in your browser using [Tampermonkey](https://www.tampermonkey.net/). Be sure to change the `const repo = "aldenparker/nixpkgs-workflows";` part to reflect the url of your fork.

### Usage
#### With Shortcut
1. Open the pull request you want to review in the [nixpkgs](https://github.com/NixOS/nixpkgs) repo.
2. Click the `Run nixpkgs-review` button at the top right of the page. (You will also see a `PR Tracker` button to easily track if your changes have propegated to all the other branches)
3. It will bring you to the `Run Workflow` screen on your fork and fill out the PR number for you. Fill out the rest and run the workflow.
4. Reload the page if necessary and click on the review run to see the logs.

#### Without Shortcut
1. Open the [review workflow in the "Actions" tab](../../actions/workflows/review.yml).
2. Click on "Run workflow".
3. Enter the number of the pull request in nixpkgs you would like to review and click on "Run workflow".
4. Reload the page if necessary and click on the review run to see the logs.

## build.yml
Builds specific package from registered flakes. Remember that the package is not just the package name, but `flake#package`. So, the `hello` package is actually `nixpkgs#hello`.

### Usage
1. Open the [build workflow in the "Actions" tab](../../actions/workflows/build.yml).
2. Click on "Run workflow".
3. Enter the packages you want to build and click on "Run workflow".
4. Reload the page if necessary and click on the review run to see the logs.

## upkeep.yml
Checks specific packages for updates each day and makes a pull request if one needs updating. This is mainly to keep my packages as up to date as possible, but you could add your own packages when you fork.
