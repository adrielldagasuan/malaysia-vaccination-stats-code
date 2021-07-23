Visualization of `https://github.com/CITF-Malaysia/citf-public/tree/main/vaccination` using [SvelteJS](https://svelte.dev/).

Application can be seen in https://init.dppxm197rqtn5.amplifyapp.com/.

## Installation

This requires [Node.js](https://nodejs.org/) to run.

Install the dependencies and devDependencies and start the server.

```sh
cd malaysia-vaccination-stats-code
npm i
```

Download the raw version`https://github.com/CITF-Malaysia/citf-public/tree/main/vaccination/vax_state.csv` and save inside `src`

```
curl https://raw.githubusercontent.com/CITF-Malaysia/citf-public/main/vaccination/vax_state.csv -o src/vax_state.csv
```
You can also fetch straight from the URL. But since the data doesn't change that often, downloading it locally prevents having to do on-demand requests.

Generate the files

```
npm run build
```

To run locally

```
npm run dev
```
