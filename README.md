Visualization of `https://github.com/CITF-Malaysia/citf-public/tree/main/vaccination` using SvelteJS

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

Generate the files

```
npm run-script build
```