# cocos-psd-prefab-2x

**English** · [中文](./README.zh-CN.md)

A standalone **Cocos Creator 2.4.x** editor extension that converts a Photoshop `.psd` into a prefab **with one click** — it automatically exports PNGs, generates `.meta` files with spriteFrames, builds a node tree following a layer-naming convention, and applies a series of **code-friendly** transformations. It can also be used as a command-line tool, independently of the editor.

> Built for Cocos Creator 2.4.x (developed/verified on 2.4.15).

> **Companion tool**: [ps-cocos-psd-namer](https://github.com/shiliyu1991-lang/ps-cocos-psd-namer)
> — a Photoshop panel that adds this convention's prefixes (`btn_` / `!`, etc.) to selected layers with one click, so artists can annotate layers per the naming convention.

## Features

- **One-click conversion**: PSD layer tree → prefab node tree, pixel-perfect alignment (anchor 0.5/0.5, positioned by the real bounding box).
- **Type detection**: bitmap → `cc.Sprite`, text layer → `cc.Label` (text/font size/color picked up automatically, no prefix needed), `btn_` → `cc.Button`, `lay_` → `cc.Layout`, `sp_ #..` → nine-slice.
- **MD5 dedup**: identical images are exported only once, shared across prefabs and across runs.
- **Transparent-edge trimming**: automatically trims fully transparent regions around a layer for more accurate size/position (can be turned off; `@nocrop` exempts a single layer).
- **Code-friendly naming**: node names are all-English (Chinese converted to pinyin) + type prefix (`Img_`/`Label_`/`Node_`) + suffix ≤ 8 characters (keeping distinguishing digits) + **unique within the same parent** (so `getChildByName` works).
- **Structure cleanup**: flattens empty containers that hold only a single child (keeping the outer name), with optional merging of overlapping decorative images.
- **Self-healing on deletion**: optionally checks whether cached images still exist and automatically re-exports any that were deleted.

## Installation (drop into a project)

```bash
git clone https://github.com/shiliyu1991-lang/cocos-psd-prefab-2x.git
cd cocos-psd-prefab-2x
npm install        # install deps ag-psd / pngjs / pinyin-pro
```

Place the entire `cocos-psd-prefab-2x` folder into the target project's `packages/` directory, for example:

```
<your-project>/packages/cocos-psd-prefab-2x/
```

After restarting / refreshing Cocos Creator, the **`PSD Tools / PSD to Prefab`** menu item appears.
(You can also place it in the global `~/.CocosCreator/packages/` to share it across all projects.)

> Tip: `node_modules/` is not committed to the repo. Just run `npm install` once after cloning; alternatively, you can ship a full zip with dependencies bundled in a Release, so it works out of the box on download without installing.

## Usage (editor panel)

1. Open the panel via the `PSD Tools / PSD to Prefab` menu.
2. Click **Select PSD**.
3. Options (checkboxes): MD5 dedup, transparent-edge trimming, flatten empty containers (on by default); merge decorative images, check cache (off by default).
4. Click **Convert to Prefab**. The result shows `nodes / new images / reused / trimmed / flattened / merged / re-exported / ignored`, the output lands in `assets/<output-dir>/`, and the asset library refreshes automatically.

## Usage (command line)

```bash
node psd2prefab.js <input.psd> --project <Cocos-project-root> [--out PSD] [--name X] [--no-dedup] [--dry-run]
```

## Documentation

- `docs/PSD命名规范.md` — the complete layer-naming convention + output naming rules + naming recommendations for artists.
- `docs/操作指南.html` — an illustrated operation guide (with panel mockups, flowcharts, and FAQ).

## Directory structure

```
cocos-psd-prefab-2x/
├── package.json        Extension manifest (menu + panel)
├── main.js             Main process: menu / IPC / conversion / asset-db refresh
├── panel/index.js      Panel UI
├── psd2prefab.js       Command-line entry
├── lib/convert.js      Conversion core (parse / align / trim / filter / dedup / naming / serialize)
├── docs/               Naming convention + HTML operation guide
└── test/               make_test_psd.js (generate samples) + verify.js (assertions)
```

## License

MIT © liyu
