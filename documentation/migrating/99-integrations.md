---
title: Integrations
---

インテグレーションに関する詳細情報については [FAQ](/faq#integrations) をご参照ください。

### HTML minifier

Sapper はデフォルトで `html-minifier` を含んでいました。SvelteKit はこれを含まないのですが、[hook](/docs#hooks-handle) としてこれを追加することができます:

```js
import { minify } from 'html-minifier';
import { prerendering } from '$app/env';

const minification_options = {
	collapseBooleanAttributes: true,
	collapseWhitespace: true,
	conservativeCollapse: true,
	decodeEntities: true,
	html5: true,
	ignoreCustomComments: [/^#/],
	minifyCSS: true,
	minifyJS: false,
	removeAttributeQuotes: true,
	removeComments: true,
	removeOptionalTags: true,
	removeRedundantAttributes: true,
	removeScriptTypeAttributes: true,
	removeStyleLinkTypeAttributes: true,
	sortAttributes: true,
	sortClassName: true
};

export async function handle({ event, resolve }) {
  const response = await resolve(event);

  if (prerendering && response.headers.get('content-type') === 'text/html') {
    return new Response(minify(await response.text(), minification_options), {
    	status: response.status,
    	headers: response.headers
    });
  }

  return response;
}
```

サイトのプロダクションビルドをテストするのに `svelte-kit preview` を使用しているとき、`prerendering` が `false` となることにご注意ください。そのため、minify の結果を検証するには、ビルド済の HTML を直接確認する必要があります。
