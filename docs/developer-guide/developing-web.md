# Developing the Web UI

Developing the Web UI is largely the same as the other components, however the development server is run locally (i.e. not in Minikube).

The `skaffold run` command deploys a Pod to Minikube that proxies traffic back to your local machine.
This allows the VKP API and UI to run on the same URL, avoid CORS problems while still taking advantage of hot-reloads.

## Getting started

```bash
cd web/
```

Install NodeJS LTS and NPM by following the instructions [here](https://nodejs.org/en/download/).

Install dependencies:

!!! note
	We use `npm ci` rather than `npm install` to make sure that we install the exact same dependencies as everyone else.

```bash
npm ci
```

Start the development server:

!!! info
	NPM will probably tell you that the app is available on `http://localhost:3000`, however we will be accessing it via `https://vkp.192-168-49-2.nip.io`.

```bash
npm run start
```

## Making changes

As you make changes, the browser will automatically reload to show the new changes.
