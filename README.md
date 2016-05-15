<p align="center">
  <img alt="Lerna" src="https://i.imgur.com/yT7Skxn.png" width="480">
</p>

<p align="center">
  A tool for managing JavaScript projects with multiple packages.
</p>

<p align="center">
  <a href="https://travis-ci.org/kittens/lerna"><img alt="Travis Status" src="https://img.shields.io/travis/kittens/lerna/master.svg?style=flat&label=travis"></a>
</p>

## About

Splitting up large codebases into separate independently versioned packages is
extremely useful for code sharing. However, making changes across many
repositories is messy and difficult to track, and testing across repostories
gets complicated really fast.

To solve these (and many other) problems, some projects will organize their
codebases into multi-package repostories. Projects like Babel, React, Angular,
Ember, Meteor, Jest, and many others develop all of their packages within a
single repository.

Lerna is a tool that optimizes the workflow around managing multi-package
repositories with git and npm.

### What does a Lerna repo look like?

There's actually very little to it. You have a file system that looks like this:

```
my-repo/
  package.json
  packages/
    package-1/
      package.json
    package-2/
      package.json
```

### Do what can Lerna do?

The two primary commands in Lerna are `lerna bootstrap` and `lerna publish`.

Bootstrap will link dependencies in the monorepo together.

## Usage

To start, let's install Lerna from [npm](https://www.npmjs.com/).

```sh
$ npm install --global lerna
```

Now create a new repository:

```sh
$ git init my-cool-project
$ cd my-cool-project
```

And then to turn it into a lerna repo run the following:

```sh
$ lerna init
```

This will create a `lerna.json` file as well as a `packages` folder.

> **Note:** Depending on the project you might want to run this in
> `--independent` mode.







## About

While developing [Babel](https://github.com/babel/babel) I followed a
[monorepo](https://github.com/babel/babel/blob/master/doc/design/monorepo.md) approach where the entire project was split into individual packages but everything lived in the same repo. This was great. It allowed super easy modularisation which meant the core was easier to approach and meant others could use the useful parts of Babel in their own projects.

This tool was abstracted out of that and deals with bootstrapping packages by linking them together as well as publishing them to npm. You can see the
[Babel repo](https://github.com/babel/babel/tree/master/packages) for an example of a large Lerna project.

> Lerna will log to a `lerna-debug.log` file (same as `npm-debug.log`) when lerna encounters an error running a command.

### Init

```sh
$ lerna init
```

> Lerna assumes you have already created a git repo with `git init`.

1. Add lerna as a `devDependency` in `package.json` if it isn't already there.
2. Create a `lerna.json` config file to store the `version` number and other info.
3. Create a `packages` folder if it's not created already.

Example output on a new git repo:

```sh
> lerna init
$ Lerna v2.0.0-beta.9
$ Creating packages folder.
$ Updating package.json.
$ Creating lerna.json.
$ Successfully created Lerna files
```

### Bootstrap

```sh
$ lerna bootstrap
```

1. Link together all `packages` that depend on each other.
2. `npm install` all other dependencies of each package.

### Updated

```sh
$ lerna updated
```

1. Check which `packages` have changed since the last release, and log it.

### Diff

```sh
$ lerna diff
# diff a specific package
$ lerna diff package-name
```

1. Diff all packages or a single package since the last release. Similar to `lerna updated`. This command basically runs `git diff`.

### Publishing

```sh
$ lerna publish
```

1. Publish each module in `packages` that has been updated since the last version to npm with the tag `prerelease`.
2. Once all packages have been published, remove the `prerelease` tags and add the tags `latest` and `stable`.

#### --npm-tag [tagname]

```sh
$ lerna publish --npm-tag=next
```

> This will add the tag you specify instead of the default: `latest`.

If you need to publish to a different tag (maybe for prerelease versions), use the `--npm-tag` flag.

#### --canary, -c

```sh
$ lerna publish --canary
```

The `canary` flag will publish packages after every successful merge using the sha as part of the tag.

It will take the current `version` and append the sha as the new `version`. ex: `1.0.0-canary.81e3b443`.

## How it works

Lerna projects operate on a single version line. The version is kept in the `lerna.json` file at the root of your project under the `version` key. When you run `lerna publish`, if a module has been updated since the last time a release was made, it will be updated to the new version you're releasing. This means that you only publish a new version of a package when you need to.
