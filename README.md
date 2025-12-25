# Vue 3 Vis-Timeline Component

Vue 3 + TypeScript + Vite で構築された、`vis-timeline` のプレミアムなラッパーコンポーネントです。モダンなダークテーマと高いカスタマイズ性を備えています。

## 🚀 特徴

- **Vue 3 完全準拠**: リアクティブなデータ更新と `shallowRef` による最適化。
- **TypeScript サポート**: `vis-timeline` の型定義を活かした型安全な開発。
- **カスタムスロット**: `#item` スロットを使用して、バーの中に任意の Vue テンプレートを挿入可能。
- **CSS 変数によるテーマ変更**: 背景色やアイテムの色を外部から簡単にカスタマイズ。
- **イベントプロキシ**: `vis-timeline` のイベント（クリック、選択、範囲変更など）を Vue のイベントとして発火。

## 📦 インストール

```bash
npm install vis-timeline vis-data
```

## 🛠 基本的な方法

```vue
<script setup lang="ts">
import { ref } from 'vue';
import VisTimeline from './components/VisTimeline.vue';
import type { DataItem, DataGroup, TimelineOptions } from 'vis-timeline/standalone';

const items = ref<DataItem[]>([
  { id: 1, content: 'Event 1', start: '2025-01-01' },
  { id: 2, content: 'Event 2', start: '2025-01-05', end: '2025-01-20' }
]);

const options: TimelineOptions = {
  height: '400px',
  editable: true
};
</script>

<template>
  <VisTimeline :items="items" :options="options" />
</template>
```

## 🎨 カスタマイズ項目

### 1. プロパティ (Props)

| Prop | 型 | 説明 |
| :--- | :--- | :--- |
| `items` | `DataItem[]` | 表示するアイテムの配列（必須） |
| `groups` | `DataGroup[]` | タイムラインをグループ化する場合の配列 |
| `options` | `TimelineOptions` | vis-timeline 公式のオプションオブジェクト |
| `groupHeight` | `string \| number` | グループ（行）の固定高さ。例: `70` や `'50px'` |

### 2. スロット (Slots)

#### `#item` スロット
バーのコンテンツをカスタマイズするために使用します。

```vue
<VisTimeline :items="items">
  <template #item="{ item }">
    <div class="custom-content">
      <span>📌</span>
      <strong>{{ item.content }}</strong>
    </div>
  </template>
</VisTimeline>
```

### 3. CSS 変数 (Styling)
親コンポーネントから以下の CSS 変数を定義することで、外観を変更できます。

| 変数名 | デフォルト値 | 説明 |
| :--- | :--- | :--- |
| `--vt-bg` | `rgba(15, 23, 42, 0.6)` | 全体の背景色 |
| `--vt-bg-blur` | `8px` | 背景のぼかし量 |
| `--vt-border-color` | `rgba(255, 255, 255, 0.1)` | パネル境界線の色 |
| `--vt-text-color` | `#94a3b8` | タイムスケールのテキスト色 |
| `--vt-item-bg` | `rgba(30, 58, 138, 0.4)` | アイテム（バー）の背景色 |
| `--vt-item-border` | `rgba(96, 165, 250, 0.3)` | アイテムの境界線 |
| `--vt-item-selected-bg` | `rgba(59, 130, 246, 0.5)` | 選択時の背景色 |
| `--vt-current-time` | `#f43f5e` | 現在時刻線の色 |

### 4. イベント (Events)

- `@select`: アイテム選択時
- `@click`: クリック時
- `@doubleClick`: ダブルクリック時
- `@rangechange`: ズーム・移動中
- `@rangechanged`: ズーム・移動完了後

## 🔍 ライブラリの詳細
このコンポーネントは内部で [vis-timeline](https://github.com/visjs/vis-timeline) を使用しています。オプションの詳細については公式ドキュメントを参照してください。
- [vis-timeline JSDoc](https://visjs.github.io/vis-timeline/docs/timeline/)
- [vis-timeline DeepWiki](https://deepwiki.com/visjs/vis-timeline)
