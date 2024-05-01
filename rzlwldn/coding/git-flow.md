# ⚓ Git Flow

Git flow are set of extensions to provide high-level repository operations for Vincent Driessen's branching model. [more](https://nvie.com/posts/a-successful-git-branching-model/)

### Setup

**Arch Linux**

```bash
$ yay install gitflow-avh
```

### Get Started

Git flow need to be initialized in order to customize project setup.

#### 1. Initialize

Start using git-flow by initializing it inside an existing git repository:

```bash
$ git flow init
```

You'll have to answer a few questions regarding the naming conventions for your branches. It's recommended to use the default values.

#### 2. Features

* Develop new feature
* Typically exists in developer repos only

**Start new feature:**

Development of new features starting from the 'develop' branch. Start developing a new feature with

```bash
$ git flow feature start MYFEATURE
```

This action creates a new feature branch based on 'develop' and switches to it

**Finish up a feature:**

Finish the development of a feature. This action performs the following:

* Merges MYFEATURE into `develop` Branch
* Removes the feature branch
* Switch back to `develop` branch

```bash
$ git flow feature finish MYFEATURE 
```

**Publish a feature**

Are you developing a feature in collaboration? Publish a feature to the remote server so it can be used by other users.

```bash
$ git flow feature publish MYFEATURE
```

**Getting a published feature**

Get a feature published by another user

```bash
$ git flow feature pull origin MYFEATURE
```

You can track a feature on origin by using

```bash
$ git flow feature track MYFEATURE
```

#### 3. Make a release

* Support preparation of a new production release
* Allow for minor bug fixes and preparing meta-data for a release

**Start a release**

To start a release, use the git flow release command. It creates a release branch created from the `develop` branch.

```bash
$ git flow release start RELEASE [BASE]
```

You can optionally supply a `[BASE]` commit sha-1 hash to start the release from. The commit must be on the 'develop' branch.

It's wise to publish the release branch after creating it to allow release commits by other developers. Do it similar to feature publishing with the command:

```bash
$ git flow release publish RELEASE
```

You can track a remote release with the command

```bash
$ git flow release track RELEASE
```
