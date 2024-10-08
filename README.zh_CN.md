# @tomjs/vscode-extension-webview

[![npm](https://img.shields.io/npm/v/@tomjs/vscode-extension-webview)](https://www.npmjs.com/package/@tomjs/vscode-extension-webview) ![node-current (scoped)](https://img.shields.io/node/v/@tomjs/vscode-extension-webview) ![NPM](https://img.shields.io/npm/l/@tomjs/vscode-extension-webview)

[English](./README.md) | **中文**

> 在 `vscode` 扩展开发使用 `webview.html` 时，支持 `vue`/`react` 的 `HMR`。

由于 `webview.html` 限制，无法直接使用 `vue`/`react` 的 `HMR`，所以需要做一些特殊处理来实现。在返回 `html` 的内容中添加 `<iframe>` 标签，并将 `src` 设置为 `http://localhost:8080`，这样就可以实现 `HMR`。客户端通过 `postMessage` 向父级 `webview` 发送消息，来实现 [acquireVsCodeApi](https://code.visualstudio.com/api/references/vscode-api#Webview) 的 API。

其他也可以通过监听前端代码变化，通过 `commands.executeCommand('workbench.action.webview.reloadWebviewAction')` 刷新前端代码。

如果使用 `vite` 开发，推荐 [@tomjs/vite-plugin-vscode](https://www.npmjs.com/package/@tomjs/vite-plugin-vscode)

## 安装

```bash
# pnpm
pnpm add @tomjs/vscode-extension-webview

# yarn
yarn add @tomjs/vscode-extension-webview

# npm
npm i @tomjs/vscode-extension-webview
```

## 使用

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

将客户端代码放到 `vue`/`react` 的 `main.ts` 放在顶部。

```ts
// eslint-disable-next-line simple-import-sort/imports
import '@tomjs/vscode-extension-webview/client';
```

## 文档

- [unpkg.com](https://www.unpkg.com/) 提供的 [index.d.ts](https://www.unpkg.com/browse/@tomjs/vscode-extension-webview/dist/index.d.ts).

## 示例

先执行以下命令安装依赖，并生成库文件：

```bash
pnpm install
pnpm build
```

打开 [examples](./examples) 目录，有 `vue` 和 `react` 示例。

- [react](./examples/react)
- [vue](./examples/vue)

## 重要说明

### v2.0.0

**破坏性更新：**

- 模拟的 `acquireVsCodeApi` 与 [@types/vscode-webview](https://www.npmjs.com/package/@types/vscode-webview) 的 `acquireVsCodeApi` 保持一致，改用 `sessionStorage.getItem` 和 `sessionStorage.setItem` 来实现 `getState` 和 `setState`。
