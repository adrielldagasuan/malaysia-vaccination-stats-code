Visualization of `https://github.com/CITF-Malaysia/citf-public/tree/main/vaccination` using [SvelteJS](https://svelte.dev/).

Application can be seen in https://vaccination-stats-my.netlify.app/.

## Installation

This requires [Node.js](https://nodejs.org/) to run.

Install the dependencies and devDependencies and start the server.

```sh
cd malaysia-vaccination-stats-code
npm i
```

You can also modify to fetch the data/csv straight from the github URL. But since the data doesn't change that often, downloading it locally prevents having to do on-demand requests.

Generate the files

```
npm run prep
npm run build
```

To run locally

```
npm run dev
```
