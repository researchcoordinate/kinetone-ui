# 現状記述：`src/ui/global.css` の横断比較

調査日: 2026-09-03
対象: `researchcoordinate/kinetone-*` の全 19 リポジトリ（`gh repo list` で列挙）。
そのうち `src/ui/global.css` を持つものを比較した。

比較したのは各リポジトリの **HEAD（作業ツリー）**。12 本すべて未コミット変更なし
（`git status --porcelain -- src/ui/global.css` が空）。
`kinetone-mahjong` だけローカルに無いため `git clone --depth 1` で取得した。

この文書は**今どうなっているか**だけを書く。良し悪しの判断・改善案は含まない。

---

## 1. 何個のリポジトリに存在するか

**12 個**（`kinetone-*` 19 リポジトリ中）。

| リポジトリ | 行数 | バイト数 | `global.css` 最終更新 | 備考 |
|---|---:|---:|---|---|
| kinetone-kikyu | 702 | 20,105 | 2026-09-02 | 最大 |
| kinetone-stepping | 379 | 10,443 | 2026-08-26 | |
| kinetone-balloon | 332 | 9,930 | 2026-08-26 | |
| kinetone-template-rec | 332 | 9,930 | 2026-09-03 | |
| kinetone-tree | 297 | 9,254 | 2026-09-02 | |
| kinetone-fruit | 214 | 6,931 | 2026-08-31 | template と**バイト単位で同一** |
| kinetone-template | 214 | 6,931 | 2026-08-31 | fruit と**バイト単位で同一** |
| kinetone-chair-stand | 213 | 4,325 | 2026-07-28 | one-leg-stand と**バイト単位で同一** |
| kinetone-one-leg-stand | 213 | 4,325 | 2026-08-05 | chair-stand と**バイト単位で同一** |
| kinetone-atodashi-janken | 188 | 6,125 | 2026-08-26 | |
| kinetone-mahjong | 161 | 4,060 | 2026-08-26 | ローカルに clone 無し |
| kinetone-block | 87 | 2,507 | 2026-08-04 | 最小 |

**持っていない 7 リポジトリ**: `kinetone-aiube` / `kinetone-camera` / `kinetone-data` /
`kinetone-flower` / `kinetone-hanabi` / `kinetone-motion` / `kinetone-voice`。
`kinetone-flower` だけは CSS 自体を持つが、`src/style.css` と `src/pet/pet.css` という別の置き方
（`src/ui/` ディレクトリが無い）。他の 6 つは CSS ファイルを 1 つも持たない
（`camera` / `motion` / `data` はライブラリ、他はアプリ以外の用途）。

---

## 2. 完全に同一か

**同一ではない。3 系統に分かれる。**

同一ペアは 2 組だけ：

- `kinetone-template` ≡ `kinetone-fruit`（sha1 `07b6e1024aaf`）
- `kinetone-chair-stand` ≡ `kinetone-one-leg-stand`（sha1 `2a299c177c89`）

### 2.0 3 系統の内訳

| 系統 | 所属 | 見分け方 |
|---|---|---|
| **A：明るい地**（現行） | template, fruit, template-rec, balloon, tree, kikyu, atodashi-janken | 冒頭コメント 18 行が md5 `26c30bd5f6` で完全一致。`--bg: #f6f2ea` |
| **B：暗い地**（旧） | mahjong / chair-stand, one-leg-stand, stepping | `--bg: #0d1726` |
| **C：木と紙** | block | `--paper` / `--ink` / `--wood`。他と共通のトークンが 1 つも無い |

B はさらに 2 つに割れる。`mahjong` は A と同じクラス構成・同じ `.screen` の
`--content-width` パディング技を持つ「A の前身」で、色だけ暗い。
`chair-stand` / `one-leg-stand` / `stepping` は別系統で、`.screen` と `.screen-scroll` を
分け、`.stack` `.lead` `.label` `.visually-hidden` `.btn--secondary` `.btn--ghost` `.btn--block`
という A に無いクラス群を持つ。`.stage` と `.brand` と `.alert` は無い。

### 2.1 CSS 変数の定義（値も含めて）

定義数：A 系 21〜22 個、mahjong 17 個、chair-stand/one-leg-stand 19 個、
stepping 32 個（+ `[data-theme='light']` と入れ子上書きで実質 3 セット）、block 9 個。

**A 系 7 本のあいだでは、`:root` の変数はすべて名前も値も完全一致**
（`kikyu` だけ `--content-width` を `.screen--play` で使うため 1 つ多い。
ただし `--content-width` は `.screen` の `var(--content-width, 900px)` として
A 系全部に登場するので、定義されていないだけで参照は全部にある）。

主な値の突き合わせ：

| 変数 | A 系 7 本 | mahjong | chair-stand / one-leg-stand | stepping（既定） | stepping（light） | block |
|---|---|---|---|---|---|---|
| `--bg` | `#f6f2ea` | `#0d1726` | `#0d1726` | `#0d1726` | `#f6f2ea` | — |
| `--bg-2` | `#ece5d8` | — | — | — | — | — |
| `--bg-soft` | — | — | `#16263d` | `#16263d` | `#ece5d8` | — |
| `--panel` | `#fffdf9` | `rgba(9,18,31,.82)` | `rgba(9,18,31,.82)` | `rgba(9,18,31,.82)` | `#fffdf9` | — |
| `--panel-solid` | `#fffdf9` | `#132339` | `#132339` | `#132339` | `#fffdf9` | — |
| `--line` | `rgba(46,38,32,.2)` | `rgba(255,255,255,.16)` | `rgba(255,255,255,.16)` | `rgba(255,255,255,.16)` | `rgba(46,38,32,.2)` | — |
| `--text` | `#2e2620` | `#ffffff` | `#ffffff` | `#ffffff` | `#2e2620` | — |
| `--text-dim` | `#6b5f52` | `#b9c9dc` | `#b9c9dc` | `#b9c9dc` | `#6b5f52` | — |
| `--accent` | `#ffb020` | `#ffb020` | `#ffb020` | `#ffb020` | （継承） | — |
| `--accent-ink` | `#2a1a00` | `#2a1a00` | `#2a1a00` | `#2a1a00` | （継承） | — |
| `--accent-line` | `#a35a12` | **無し** | **無し** | `var(--accent)` | `#a35a12` | — |
| `--btn-bg` | `#a35a12` | **無し** | **無し** | `var(--accent)` | `#a35a12` | — |
| `--btn-ink` | `#ffffff` | **無し** | **無し** | `var(--accent-ink)` | `#ffffff` | — |
| `--good` | `#0e7a52` | `#35d6a4` | `#35d6a4` | `#35d6a4` | `#0e7a52` | — |
| `--warn` | `#a8410a` | `#ff8a5b` | `#ff8a5b` | `#ff8a5b` | `#a8410a` | — |
| `--bad` | `#c02626` | `#ff6b6b` | `#ff6b6b` | `#ff6b6b` | `#c02626` | — |
| `--radius` | `20px` | `20px` | `20px` | `20px` | （継承） | — |
| `--shadow` | **無し** | **無し** | `0 18px 48px rgba(0,0,0,.45)` | 同左 | `0 12px 32px rgba(46,38,32,.14)` | — |
| `--fs-hero` 〜 `--fs-sm` | 全 5 個 | 全 5 個 | 全 5 個 | 全 5 個 | （継承） | **無し** |

**`--fs-*` の 5 個（`--fs-hero` `--fs-xl` `--fs-lg` `--fs-md` `--fs-sm`）と `--radius: 20px` は、
block を除く 11 本すべてで値まで一字一句同じ。**`--accent: #ffb020` / `--accent-ink: #2a1a00` も
11 本すべて同一。ここが最も揃っている部分。

stepping だけが特殊で、3 段構えになっている：

1. `:root` — 暗い地（`measure.html` 側）
2. `:root[data-theme='light']` — 明るい地（`index.html` 側、`<html data-theme="light">`）
3. `:root[data-theme='light'] :is(.calibration, .game, .fork, .bg-layer, .settings-backdrop)`
   — 明るい地のときでも映像・景色の上だけ暗い地の値へ戻す

さらに stepping だけが持つ変数：`--left: #59b8ff` / `--right: #ffd166`（左右の脚）、
`--btn-min-height: 84px`、`--focus-ring`、`--btn-secondary-line`、`--btn-ghost-bg`、
`--choice-line` / `--choice-bg` / `--choice-on-bg` / `--choice-on-sub`。

block は他と 1 つも共有していない（`--paper` `--paper-deep` `--ink` `--ink-soft` `--wood`
`--leaf` `--sky` `--font-scale` `--tap-min`）。`--font-scale` は
`src/app/fontScale.ts:30` が `root.style.setProperty('--font-scale', …)` で書き込む。

### 2.2 クラスの定義

セレクタの有無と、名前が同じでも中身が違うものを `!` で示す。

```
                                  te fr t-r ba tr ki at ma ch ol st bl
* / html,body,#root               X  X  X   X  X  X  X  X  X  X  X  !
body                              X  X  X   X  X  X  X  !  !  !  !  !
.screen                           X  X  X   X  X  X  X  X  !  !  !  .
.brand / .note / .alert           X  X  X   X  X  X  X  !  .  .  .  .
.title / .subtitle                X  X  X   X  X  X  X  X  X  X  X  .
.card                             X  X  X   X  X  X  X  X  X  X  !  .
.btn                              X  X  X   X  X  X  X  !  !  !  !  .
.btn--huge                        X  X  X   X  X  X  X  X  !  !  !  .
.btn:focus-visible                X  X  X   X  X  X  X  !  !  !  !  .
.btn:disabled                     X  X  X   X  X  X  X  X  X  X  X  .
.stage                            X  X  !   !  !  X  !  !  .  .  .  .
.hold / .hold__fill               X  X  .   .  .  X  .  .  .  .  .  .
.btn--quiet                       .  .  X   X  X  !  .  .  .  .  .  .
.play / .play .stage /
  .screen:has(.play) / .version   .  .  X   X  X  .  .  .  .  .  .  .
.meter                            .  .  X   X  !  !  .  .  .  .  .  .
.meter i                          .  .  X   X  X  .  .  .  .  .  .  .
.hud / .hud-score / .hud-score b /
  .hud-time                       .  .  X   X  .  .  .  .  .  .  .  .
.card--result / .result-label /
  .result-score / …span           .  .  X   X  .  .  .  .  .  .  .  .
.sound-off                        .  .  X   X  .  .  .  .  .  .  .  .
.bloom / .bloom i / .done-word    .  .  .   .  X  .  .  .  .  .  .  .
.screen-scroll / .stack / .label /
  .visually-hidden                .  .  .   .  .  .  .  .  X  X  !  .
.lead                             .  .  .   .  .  .  .  .  X  X  .  .
.btn--secondary/--ghost/--block   .  .  .   .  .  .  .  .  X  X  !  .
.btn:hover / .btn:active          .  .  .   .  .  .  .  .  X  X  X  .
input, textarea                   .  .  .   .  .  .  .  .  X  X  .  .
.choice* / .btn-sub /
  .btn:has(.btn-sub)              .  .  .   .  .  .  .  .  .  .  X  .
button / button:focus-visible     .  .  .   .  .  .  .  .  X  X  X  !
kikyu 固有 39 クラス               .  .  .   .  .  X  .  .  .  .  .  .
```

`X` = 定義あり、`!` = 定義はあるが中身が最初の repo と違う、`.` = 無し。
列は te=template, fr=fruit, t-r=template-rec, ba=balloon, tr=tree, ki=kikyu,
at=atodashi-janken, ma=mahjong, ch=chair-stand, ol=one-leg-stand, st=stepping, bl=block。

**block だけはクラス定義が 0 個**（`*` `html,body,#root` `body` `button`
`button:focus-visible` `@media (prefers-reduced-motion)` のみ）。
クラスは `src/ui/screens.css` など別ファイルに分けている。

同名で中身が違う代表例：

- **`.stage`** — `template` / `fruit` / `kikyu` は `transform: scaleX(-1)`（鏡像）を持つ。
  `template-rec` / `balloon` / `tree` / `atodashi-janken` は**持たない**（反転を canvas 側で
  やるため）。`mahjong` は `background` が `#04060f`（他は `#241d17`）。
- **`.meter`** — 3 通りある。`template-rec`/`balloon` は `bottom: clamp(14px,2.4vw,32px)`、
  `tree` は `bottom: clamp(18px,3vw,36px)`、`kikyu` は同じ名前で「気球の高度ゲージ」
  という別物（`.meter__fill` `.meter--done` を伴う）。
- **`.btn--quiet`** — `template-rec`/`balloon`/`tree` は同一。`kikyu` は別内容。
- **`.card`** — stepping だけ `backdrop-filter: blur(10px)` を持つ。
- **`.btn`** — A 系は `background: var(--btn-bg)`、mahjong / chair-stand / one-leg-stand は
  `var(--accent)` 直指定、stepping は `var(--btn-bg)`（ただし `--btn-bg` の中身が
  `var(--accent)`）。stepping だけ `min-height: var(--btn-min-height)`。

### 2.3 コメント

| リポジトリ | 全行 | コメント行 | 比率 | 冒頭コメント |
|---|---:|---:|---:|---|
| template | 215 | 61 | 28% | 18 行（md5 `26c30bd5f6`） |
| fruit | 215 | 61 | 28% | 同上 |
| atodashi-janken | 189 | 51 | 27% | 同上 |
| template-rec | 333 | 73 | 22% | 同上 |
| balloon | 333 | 73 | 22% | 同上 |
| tree | 298 | 74 | 25% | 同上 |
| kikyu | 703 | 185 | 26% | 同上 |
| mahjong | 162 | 20 | 12% | 9 行（`065136c1da`） |
| chair-stand | 214 | 14 | 7% | 5 行（`2c4db75f71`） |
| one-leg-stand | 214 | 14 | 7% | 同上 |
| stepping | 380 | 80 | 21% | 5 行（`61a8d8f8e3`） |
| block | 88 | 20 | 23% | 11 行（`8d9582e10f`） |

**A 系 7 本の冒頭 18 行コメントは 1 バイトも違わない。**
「明るい地に濃い文字」「純白は使わない」「地とアクセントの温度をそろえる」
「本文 4.5:1、大きな文字でも 3:1」の作法が全部ここに入っている。

mahjong の 9 行は A の前身にあたる版で、明るい地の話が無く
「真っ白（#fff）はタブレットで反射がきついので、地は少し暗いか少し灰みを入れる」となっている。

chair-stand / one-leg-stand の 5 行には
「トークンとクラス名は kinetone/stepping と揃えてある（見た目の流儀を共通にするため）」
と書かれているが、実際の stepping の現行 `global.css` とは既に一致していない
（stepping 側が `[data-theme='light']` 対応で大きく変わったため）。

コメントの一致は本文より広い。A 系では `.screen` の
「読みやすい幅は max-width ではなく左右の padding で作る」5 行、
`.brand` の「明るい地の上では --accent（黄）は 1.6:1 しかなく読めない」1 行、
`.btn` の「指先の精度が落ちていても押せる大きさ」1 行が全 7 本で一致。
mahjong は `.stage` の「iPad Safari では playsinline / muted / autoplay が要る」だけを A と共有。

同じ内容が言い換えで散っている例：

- 鏡像の理由（「自分の右手が画面の右に出ないと動かしにくい」）が
  `template`/`fruit`/`kikyu` の `.stage` に 5 行、`kikyu` の `.preview__video` に 1 行
  （「鏡像。**自分の右が画面の右に出ないと動かしにくい**（共通ルール）」）。
- 暗い帯の上の文字色（「`--text-dim`（濃い色）は使えない」）が
  `template-rec`/`balloon` の `.hud-time` と `.sound-off` に 2 回。
- `template-rec`/`balloon` の `.meter i` は「映像の上に載るので」、
  `tree` の `.meter i` は「絵の上に載るので」。差はこの 2 文字だけ。

---

## 3. リポジトリ固有の記述がどれくらい混ざっているか

### 3.1 A 系の境界は「行 188」にある

A 系 7 本を先頭から突き合わせると：

| 比較 | 先頭から一致する行数 |
|---|---:|
| template-rec ↔ balloon | 190 行 |
| template-rec ↔ tree | 190 行 |
| template-rec ↔ atodashi-janken | 188 行（= atodashi-janken の**全部**） |
| template-rec ↔ template / fruit / kikyu | 183 行（`.stage` の鏡像コメントで分岐） |

**`kinetone-atodashi-janken/src/ui/global.css` は共通部分そのもの**で、
ゲーム固有の記述を 1 行も含まない（ゲーム固有は `src/ui/game.css` に分けてある）。
この 188 行が A 系の「共通部分」の実体：

```
行  1– 18  冒頭コメント（作法）
行 20– 58  :root（変数 21 個）
行 60– 78  * / html,body,#root / body
行 80– 95  .screen（--content-width のパディング技）
行 97–134  .brand .title .subtitle .note .card
行 136–166  .btn .btn--huge .btn:focus-visible .btn:disabled
行 168–178  .alert
行 180–188  .stage
```

境界は明示されていない。`template-rec` / `balloon` / `tree` では、行 190 の

```
/* ────────────────────────────────────────────────────────────
   遊ぶ画面 ／ 風船わり ／ 鏡あそび
```

という罫線コメントが実質の区切りになっている。**この見出しの文言だけが
balloon と template-rec の唯一の差**（`風船わり` → `遊ぶ画面`。他 331 行は完全同一）。
template / fruit / kikyu / atodashi-janken にはこの罫線が無く、区切りが見えない。

### 3.2 ゲーム固有クラスの内訳

クラス名の実数（`.play .stage` のような組み合わせセレクタは別に数えない）。
A 系の「共通側」は 3.1 の 188 行が持つ 10 個
（`.screen` `.brand` `.title` `.subtitle` `.note` `.card` `.btn` `.btn--huge` `.alert` `.stage`）。

| リポジトリ | クラス総数 | 共通側 | 固有 | 固有クラス名 |
|---|---:|---:|---:|---|
| atodashi-janken | 10 | 10 | **0** | — |
| mahjong | 10 | 10 | 0 | —（B 系だがクラス構成は A の共通側と同じ 10 個） |
| template / fruit | 12 | 10 | 2 | `.hold` `.hold__fill` |
| tree | 16 | 10 | 6 | `.play` `.bloom` `.done-word` `.meter` `.btn--quiet` `.version` |
| template-rec / balloon | 21 | 10 | 11 | `.play` `.hud` `.hud-score` `.hud-time` `.meter` `.card--result` `.result-label` `.result-score` `.sound-off` `.btn--quiet` `.version` |
| kikyu | 51 | 12 | **39** | `.screen--play` `.preview*`(4) `.head*`(6) `.badge` `.field` `.sky*`(3) `.rider*`(10) `.balloon` `.meter*`(3) `.banner*`(4) `.btn--corner` `.btn--quiet` `.diag*`(2) `.alert--float` + `@keyframes kikyu-hover` `kikyu-sway` |
| chair-stand / one-leg-stand | 15 | 15 | 0 | 固有は `src/ui/screens/*.css` などに分離（B 系どうしの比較） |
| stepping | 21 | 13 | 8 | `.choice` `.choice-row` `.btn-sub` ＋ 変数上書きセレクタに埋まった `.calibration` `.game` `.fork` `.bg-layer` `.settings-backdrop`（`.lead` `.note` は逆に持たない） |
| block | 0 | 0 | 0 | クラスは全部 `screens.css` など別ファイル |

固有部分の割合（行数ベース、A 系）：

| | 共通 188 行 | 固有 | 固有の割合 |
|---|---:|---:|---:|
| atodashi-janken | 188 | 0 | 0% |
| template / fruit | 183 | 31 | 14% |
| tree | 188 | 109 | 37% |
| template-rec / balloon | 188 | 144 | 43% |
| kikyu | 183 | 519 | **74%** |

### 3.3 境界が曖昧になっている箇所

固有・共通の線が引きにくくなっている点：

1. **`.stage` の鏡像**（行 184–190）。共通の `.stage` の中に、
   ゲームによって要る／要らないが分かれる `transform: scaleX(-1)` が入っている。
   これが A 系を 183 行と 188 行に割っている唯一の原因。
2. **`.meter` の名前衝突**。`template-rec`/`balloon`/`tree` では「動きの量メーター」、
   `kikyu` では「気球の高度ゲージ」。同名で意味が違う。
3. **`.btn--quiet`**。`template-rec`/`balloon`/`tree` で同一、`kikyu` で別内容。
   共通に見えるが 4 本のうち 1 本がずれている。
4. **stepping の `:is(.calibration, .game, .fork, .bg-layer, .settings-backdrop)`**。
   ゲーム画面のクラス名 5 つが `:root` の変数上書きセレクタに直接埋まっている。
   ここのコメントに「**この 1 行が要る**（`color: var(--text)`）」と、
   トークン差し替えだけでは足りない理由が書かれている。
5. **`.version` / `.sound-off` / `.hud-*`**。共通か固有かが repo ごとに割れる。
   `.version` は template-rec/balloon/tree の 3 本だけ、`.sound-off` は 2 本だけ、
   `.hud-*` は 2 本だけ。

---

## 4. 同じ残骸が入っているか

### 4.1 参照されていない CSS 変数

`global.css` 内の `var()` にも、同リポジトリの `src/` 配下（`.ts` `.tsx` `.css` `.html` `.svg`）
にも一度も現れない変数：

| リポジトリ | 未参照の変数 |
|---|---|
| template | `--accent` `--accent-ink` `--fs-md` `--good` `--panel-solid` `--warn` |
| fruit | `--accent` `--accent-ink` `--fs-md` `--good` `--panel-solid` `--warn` |
| tree | `--accent` `--accent-ink` `--good` `--panel-solid` `--warn` |
| kikyu | `--accent` `--accent-ink` `--good` `--panel-solid` `--warn` |
| template-rec | `--accent-ink` `--good` `--panel-solid` `--warn` |
| balloon | `--accent-ink` `--good` `--panel-solid` `--warn` |
| mahjong | `--fs-md` `--good` `--panel-solid` `--warn` |
| atodashi-janken | `--panel-solid` |
| chair-stand | `--bg-soft` `--shadow` |
| one-leg-stand | `--bg-soft` |
| stepping | `--bad` |
| block | （なし） |

**`--panel-solid` は 11 本中 8 本で未参照**（block と、参照のある chair-stand /
one-leg-stand / stepping を除く A 系＋mahjong の全部）。値まで同じ残骸が横並びで残っている。
`--good` と `--warn` も 7 本で未参照。`--accent-ink` は 6 本、`--accent` は 4 本。

`--accent` / `--accent-ink` の未参照は A 系で意味が変わった結果として残ったもの。
A の冒頭コメントは `--accent` を「映像や景色の上（暗い面）で使う明るいアクセント」と
定義していて、暗い面を持たない画面構成の repo では出番が無くなる。
`template-rec` / `balloon` では `.hud-score b` が `--accent` を使っているので参照がある。

`--fs-md` は template / fruit / mahjong で未参照。tree / kikyu では `.btn--quiet` が使う。

### 4.2 参照されていないクラス

`global.css` で定義されているが、`className=` / `class=` / 他の CSS からの参照が
リポジトリ内に無いもの：

| リポジトリ | 未参照のクラス |
|---|---|
| kikyu | `.stage` `.hold` `.hold__fill` |
| one-leg-stand | `.screen`（`.screen-scroll` のみ使用） |
| stepping | `.visually-hidden` |
| 他 9 本 | （なし） |

**kikyu の `.stage` / `.hold` / `.hold__fill` は template から複製されたまま残っている残骸。**
kikyu は映像を `.preview__video` で出しており、`.stage` は一度も使わない。
`.hold` は template の「手をあげる溜まり具合」で、kikyu では `.head__bar` に置き換わっている。
`.hold` が「共通部分」に見えるのは template / fruit / kikyu の 3 本に居るためだが、
実際に使っているのは template / fruit の 2 本だけ。

### 4.3 CSS の値が JS 側に手で写されている箇所

`kinetone-template-rec/src/game.ts:28-54` に、`global.css` の値を canvas 用に写した定数がある。

```
canvas は CSS 変数を読めないので、global.css と同じ値をここに写している。
**global.css 側を変えたら、ここも変えること。**
```

`DOT`（`--accent` と同値）と `BACKDROP = '#241d17'`（`.stage` の background と同値）。
同コメントに「kinetone-tree で実際に起きた。ゲージが桜色のままだった」と、
過去に写しがずれた事例が記録されている。
`balloon` にはこの写しが無い（`game.ts` の作りが違う）。

その対処として `tree` は逆向きにしている。`.bloom i` は `background: var(--bloom-bar)` と書き、
**既定色を CSS に置かず**、`src/App.tsx:188` の
`style={{ '--bloom-bar': theme.petal } as CSSProperties}` で流し込む。
`.bloom` のコメントに「**var() の既定値も置かない**」理由が書かれている。
CSS 変数を JS から書き込むのは他に `block` の `--font-scale`
（`src/app/fontScale.ts:30` の `setProperty`）だけ。

---

## 5. パッケージに切り出す場合、何が障害になるか

### 5.1 現状の配り方

既に `@kinetone/*` パッケージが 3 つあり、全部 **GitHub タグ直参照**：

```
"@kinetone/camera": "github:researchcoordinate/kinetone-camera#v0.1.0"
"@kinetone/motion":  "github:researchcoordinate/kinetone-motion#v0.1.0"
"@kinetone/data":    "github:researchcoordinate/kinetone-data#v0.19.0"
```

`kinetone-camera` の中身は `camera.js` / `settings.js` / `index.js` / `index.d.ts` /
`package.json` / `README.md` / `selftest.html` のみ。**ビルド手順が無い**
（`scripts` が空、素の ES モジュールをそのままコミットしている）。
`package.json` は

```json
"exports": { ".": { "types": "./index.d.ts", "default": "./index.js" } },
"files": ["*.js", "*.d.ts"]
```

**CSS はこのパターンに一度も乗ったことがない。**`files` が `*.js` / `*.d.ts` 限定で、
`exports` はルート 1 本だけ。CSS を配るなら `files` に `*.css` を足し、
`exports` に `"./global.css": "./global.css"` のようなサブパスを足すことになる。

### 5.2 ゲーム固有クラスの扱い

現状の分かれ方は 3 通りで、揃っていない。

| 方式 | 採っている repo |
|---|---|
| `global.css` に共通も固有も全部入れる | template-rec, balloon, tree, kikyu, template, fruit, stepping |
| `global.css` は共通のみ、固有は別ファイル | atodashi-janken（`src/ui/game.css`）, block（`screens.css` ほか）, chair-stand / one-leg-stand（`src/ui/screens/*.css` ほか） |
| そもそも固有が無い | mahjong |

固有クラス数は 0（atodashi-janken）から 33（kikyu）まで開いている。
kikyu は固有が 74%（519 / 702 行）を占め、共通部分のほうが少数派になっている。

分離の具体的な引っかかり：

- **`.meter` が 2 つの意味を持つ**。パッケージ側に置くと kikyu が上書きすることになる。
- **`.btn--quiet` が 4 本にあり、うち kikyu だけ中身が違う。**
- **`.stage` の `transform: scaleX(-1)`** が repo で要／不要に割れる。
  `.stage` 自体は共通だが、この 1 プロパティだけが違う。
- **`.version` `.sound-off` `.hud-*` `.play` `.card--result`** は 2〜3 本にしかない。
  「共通」と呼ぶかどうかの線が今は引かれていない。
- **stepping の `:is(.calibration, .game, .fork, .bg-layer, .settings-backdrop)`**。
  変数の上書き範囲がゲーム画面のクラス名に依存しているため、
  `:root` の変数定義だけを切り出しても、このセレクタは切り出せない。
  同じコメントに「トークンを差し替えるだけでは足りない」と書かれている。
- **block はクラスを 1 つも共有していない**（変数も 0 個共有）。
  切り出しの対象にするかどうかで扱いが変わる。

### 5.3 CSS の配り方（import か、コピーか）

現在の読み込み方は 2 通り＋1：

| 読み込み方 | repo |
|---|---|
| `import './ui/global.css'`（`App.tsx` または `main.tsx`） | 12 本すべて |
| 2 つ目のエントリからも import | chair-stand（`src/cs30/main.tsx: '../ui/global.css'`）、stepping（`src/measure/main.tsx: '../ui/global.css'`） |
| HTML から絶対パスで `<link>` | balloon / template-rec の `harness.html:3` — `<link rel="stylesheet" href="/src/ui/global.css">` |

**この `<link>` が障害になる。**`/src/ui/global.css` という絶対パス直指定なので、
ファイルが `node_modules/@kinetone/…` に移ると 404 になる。
`harness.html` は Vite の追加エントリではなく、dev サーバから直接開くページ
（`vite.config.ts` の `rollupOptions.input` に入っているのは stepping の
`index.html` / `measure.html` / `streetview-cost.html` だけ）。

import 形式にする場合：

- Vite は `node_modules` 内の CSS の `import` を扱えるので、
  `import '@kinetone/ui/global.css'` はそのまま通る。ビルド設定の追加は要らない。
- ただし `package.json` の `files` と `exports` の追記が要る（5.1）。
- GitHub タグ直参照なので、パッケージ側を直したときは
  **12 本すべての `package.json` のタグを上げて `npm install` し直す**ことになる。
  既存の `@kinetone/camera#v0.1.0` は 3 リポジトリで同じタグに固定されている。

コピー形式（現状の複製を続ける）の場合、既に起きていることが目安になる：

- balloon と template-rec は 332 行中 331 行が同一で、差は見出しコメント 1 行のみ。
- template と fruit、chair-stand と one-leg-stand は完全に同一。
- 一方で `--panel-solid` の未参照が 8 本に、`--good` `--warn` が 7 本に同時に残っている（4.1）。
- chair-stand の冒頭コメントは「stepping と揃えてある」と書いているが、
  stepping 側が `[data-theme='light']` 対応で先へ進んだため、現在は揃っていない（2.3）。

### 5.4 ビルドの仕組みへの影響

12 本すべてが同じ形（`tsc -b && vite build`、`"type": "module"`、Vite + React）。
CSS まわりで確認した点：

- **PostCSS / Sass / CSS Modules は 1 本も使っていない。**`@import` を含む CSS も 0 件。
  素の CSS 1 ファイルを Vite が取り込むだけ。
- **Vite の設定に CSS 関連の指定は無い**（`css` オプション、`alias`、
  `rollupOptions.input` の CSS 追加のいずれも無し）。
- **PWA（vite-plugin-pwa / Workbox）を全 repo が使っている。**
  `globPatterns` に `**/*.{js,css,html,…}` が入っているので、
  パッケージ由来の CSS も Vite がバンドルした結果としてプリキャッシュ対象に入る。
  出力ファイル名が変わるのでキャッシュは切り替わる。
- **`KINETONE_BASE` で配信パスを切り替えている**（全 repo 共通）。
  `base` は Service Worker の scope / start_url にも効く。
  CSS を import 形式にしてもここは変わらない（Vite が出力に含めるため）。
  変わるのは `harness.html` の `<link href="/src/ui/global.css">` だけ（5.3）。
- **stepping だけ複数エントリ**（`rollupOptions.input` に `index.html` / `measure.html` /
  `streetview-cost.html`）。`global.css` は `src/main.tsx` と `src/measure/main.tsx` の
  2 か所から import されており、テーマは HTML 側の `data-theme` 属性で切り替わる。
  変数を 1 パッケージにまとめると、この 2 テーマ分をパッケージ側が持つことになる。
- **`kinetone-block` は `three` を依存に持ち、`--font-scale` を JS から書き込む**
  （`src/app/fontScale.ts`）。他 11 本と共有するトークンが無い。
- テストコード（`tests/`）から `global.css` を読んでいる repo は 0 件。
