# DevSecOps Learning Journal - Docusaurus Blog

A Docusaurus-based developer diary and portfolio, configured as part of the DevSecOps course. It serves as a static website to document projects, guides, and technical knowledge.

## Quickstart

### Prerequisites
- [Node.js](https://nodejs.org/) (v22 or later)
- [pnpm](https://pnpm.io/)

### Setup
1. Clone the repository: `git clone https://github.com/NicoMeyerDev/dev-blog.git`
2. Configure environment variables - copy `example.env` to `.env` and fill in your values
3. Install dependencies: `pnpm install`
4. Start the development server: `pnpm start`

## Description

The following configuration steps are necessary to personalize the project:

### `docusaurus.config.ts`
- Adjust the title and tagline
- Set the default url to your GitHub Pages URL
- Add your GitHub repository link to the sidebar and blog edit URLs
- Add a link to the projects page in the footer
- Add a template link in the More section of the footer
- Update the copyright with your name and a reference to Developer Akademie

### `example.env`
- Set `GITHUB_ORG` to your GitHub username
- Add your repository URL as `GIT_REPOSITORY_URL`

Example configuration:

```env
DEPLOYMENT_URL=https://your-github-username.github.io
DEPLOYMENT_BRANCH=main
BASE_URL=/your-repo-name/
GITHUB_ORG=your-github-username
GITHUB_PROJECT=your-repo-name
GIT_REPOSITORY_URL=https://github.com/your-github-username/your-repo-name
```