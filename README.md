# DevSecOps Journal

This website is built using [Docusaurus](https://docusaurus.io/), a modern static website generator.

## Repository Description

This repository hosts a developer blog built with Docusaurus. It includes tools and scripts for creating, managing, and deploying static web content. The software supports rapid local development, customizable theming, and seamless deployment to GitHub Pages.

## Table of Contents

- [DevSecOps Journal](#devsecops-journal)
  - [Repository Description](#repository-description)
  - [Table of Contents](#table-of-contents)
  - [Quickstart](#quickstart)
    - [Prerequisites](#prerequisites)
  - [Repository Structure](#repository-structure)
  - [Deployment](#deployment)

## Quickstart

### Prerequisites

- [Node.js](https://nodejs.org/) (v22 or later recommended)
- [pnpm](https://pnpm.io/) (package manager for faster and more efficient dependency handling)

1. Installation

```
   $ pnpm install
```

2. Local Development

```
   $ pnpm start
```

   This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

3. Build

```
   $ pnpm build
```

   This command generates static content into the `build` directory and can be served using any static contents hosting service.

## Repository Structure

The repository is organized as follows:

- `blog/`: Contains markdown files for blog posts.
- `docs/`: Contains markdown files for documentation.
- `src/`: Contains custom React components, CSS, and JavaScript.
- `static/`: Stores static assets (e.g., images, icons).
- `sidebars.ts`: Configures the structure of sidebars in the documentation section.
- `docusaurus.config.ts`: Main configuration file for customizing and managing Docusaurus behavior.
- `example.env`: Contains example environment variables needed to configure and deploy the project.

## Deployment

This website is automatically deployed to GitHub Pages using a prepared GitHub Actions workflow. The deployment is triggered whenever a commit is pushed to the main branch.