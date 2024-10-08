# @tomjs/vscode-extension-webview

[![npm](https://img.shields.io/npm/v/@tomjs/vscode-extension-webview)](https://www.npmjs.com/package/@tomjs/vscode-extension-webview) ![node-current (scoped)](https://img.shields.io/node/v/@tomjs/vscode-extension-webview) ![NPM](https://img.shields.io/npm/l/@tomjs/vscode-extension-webview) **English** | [中文](./README.zh_CN.md)

> When using `webview.html` in `vscode` extension development, `HMR` of `vue`/`react` is supported.

Due to the limitations of `webview.html`, `HMR` of `vue`/`react` cannot be used directly, so some special processing is required to achieve it. You can implement `HMR` by adding an `<iframe>` tag to the content that returns `html` and setting the `src` to `http://localhost:5173`. The client sends a message to the parent `webview` through `postMessage` to implement the API of [acquireVsCodeApi](https://code.visualstudio.com/api/references/vscode-api#Webview).

Others can also monitor front-end code changes and refresh the front-end code through `commands.executeCommand('workbench.action.webview.reloadWebviewAction')`.

If you use `vite` for development, [@tomjs/vite-plugin-vscode](https://www.npmjs.com/package/@tomjs/vite-plugin-vscode) is recommended

## Install

```bash
# pnpm
pnpm add @tomjs/vscode-extension-webview

# yarn
yarn add @tomjs/vscode-extension-webview

# npm
npm i @tomjs/vscode-extension-webview
```

## Usage

`extension`

```ts
import getHtml from '@tomjs/vscode-extension-webview';

const panel = window.createWebviewPanel('showHelloWorld', 'Hello World', ViewColumn.One, {
  enableScripts: true,
  localResourceRoots: [Uri.joinPath(extensionUri, 'dist/webview')],
});

if (process.env.NODE_ENV === 'development') {
  panel.webview.html = getHtml({
    serverUrl: `http://localhost:5173`,
  });
} else {
  panel.webview.html = /* html */ `
<!doctype html>
  <html lang="en">
</html>`;
}
```

Place the client code in `main.ts` of `vue`/`react` at the top.

```ts
// eslint-disable-next-line simple-import-sort/imports
import '@tomjs/vscode-extension-webview/client';
```

## Documentation

- [index.d.ts](https://www.unpkg.com/browse/@tomjs/vscode-extension-webview/dist/index.d.ts) provided by [unpkg.com](https://www.unpkg.com).

## Examples

First execute the following command to install dependencies and generate library files:

```bash
pnpm install
pnpm build
```

Open the [examples](./examples) directory, there are `vue` and `react` examples.

- [react](./examples/react)
- [vue](./examples/vue)

## Important Notes

### v2.0.0

**Breaking Updates:**

- The simulated `acquireVsCodeApi` is consistent with the `acquireVsCodeApi` of [@types/vscode-webview](https://www.npmjs.com/package/@types/vscode-webview), and `sessionStorage.getItem` and `sessionStorage.setItem` are used to implement `getState` and `setState`.
