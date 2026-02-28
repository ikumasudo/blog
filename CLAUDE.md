# CLAUDE.md

## パッケージマネージャー

- **pnpm** を使用（`packageManager: pnpm@10.6.3`）
- コマンド: `pnpm dev` / `pnpm build` / `pnpm preview`
- デプロイ: `pnpm deploy`（build → wrangler deploy）

## 技術スタック

- Astro + Tailwind CSS v4
- `src/styles/global.css` にグローバルテーマ変数（`@theme` ブロック + `@variant dark`）
- コンポーネント固有スタイルは `.astro` ファイル内 `<style is:global>` ブロック

## ダーク/ライトモード

- `<html>` 要素の `data-theme` 属性で制御（値: `light` / `dark` / `auto`）
- テーマ切替: `<atmb-theme-select>` Web Component（`src/components/theme-select.astro`）
- FOUC 防止: `src/components/theme-provider.astro`（インラインスクリプト）
- localStorage キー: `atmb-theme`

## CSS 詳細度の注意点（重要）

`global.css` の `@variant dark` は `:where()` で詳細度ゼロに設定されている:

```css
@custom-variant dark (&:where([data-theme=dark], [data-theme=dark] *));
```

**コンポーネントの `<style is:global>` で独自 CSS 変数を定義する場合**:

- `:root` で light 値を定義 → 詳細度 (0,0,1)
- `:where([data-theme="dark"])` は詳細度 (0,0,0) → `:root` に負けてダークモードが効かない
- **正しいパターン**: `[data-theme="dark"]` を `:where()` なしで使う → 詳細度 (0,1,0) で `:root` に勝つ

```css
/* NG: ダークモードが効かない */
:root { --my-color: white; }
:where([data-theme="dark"]) { --my-color: black; }

/* OK: 詳細度で :root に勝つ */
:root { --my-color: white; }
[data-theme="dark"] { --my-color: black; }
```
