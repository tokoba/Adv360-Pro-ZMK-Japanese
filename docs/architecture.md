# Kinesis Advantage 360 Pro ZMK ファームウェア アーキテクチャドキュメント

## 概要

本プロジェクトは、Kinesis Advantage 360 Pro キーボードの日本語配列用ファームウェアです。ZMK (Zephyr Mechanical Keyboard) ファームウェアをベースとし、分離型エルゴノミックキーボードの左右両側のモジュールを制御します。

### プロジェクトの特徴

- **ハードウェア**: Nordic nRF52840 SoC (ARM Cortex-M4)
- **ファームウェア**: ZMK (Zephyr RTOS ベース)
- **接続**: Bluetooth Low Energy (BLE) およびUSB
- **キーボード構成**: 分離型左右モジュール (左側がセントラル、右側がペリフェラル)
- **日本語対応**: JIS配列に対応した国際キー (INT1-INT5, LANG5) をサポート

## プロジェクト構造

```
Adv360-Pro-ZMK-Japanese/
├── .github/
│   └── workflows/
│       └── build.yml              # GitHub Actions ビルドワークフロー
├── .vscode/
│   └── settings.json              # VSCode エディタ設定
├── assets/                        # ドキュメント用アセット (画像等)
├── bin/                           # ビルドスクリプト
│   ├── build.sh                   # Docker コンテナ内ビルドスクリプト
│   ├── get_version.sh             # CI用バージョン取得スクリプト
│   └── get_version_local.sh       # ローカルビルド用バージョン取得スクリプト
├── config/                        # ZMK 設定ファイル
│   ├── boards/arm/adv360/         # ボード定義ファイル
│   ├── adv360.keymap              # キーマップ定義
│   ├── adv360_left.keymap         # 左側キーボード設定
│   ├── adv360_right.keymap        # 右側キーボード設定
│   ├── macros.dtsi                # マクロ定義
│   ├── version.dtsi               # バージョン情報
│   ├── keymap.json                # キーマップJSON (ZMK Studio用)
│   ├── keymap_default_adv360_ja.json  # 日本語デフォルトキーマップ
│   ├── info.json                  # キーボードレイアウト情報
│   └── west.yml                   # West マニフェスト (依存関係管理)
├── doc/                           # ユーザーマニュアル (PDF)
├── firmware/                      # ビルド済みファームウェア出力先
├── Dockerfile                     # ビルド環境定義
├── Makefile                       # ローカルビルド用Makefile
└── settings-reset.uf2             # 設定リセットファームウェア
```

## アーキテクチャコンポーネント

### 1. ハードウェア層 (Hardware Layer)

#### 1.1 マイクロコントローラ

**Nordic nRF52840 (ARM Cortex-M4F @ 64MHz)**
- フラッシュメモリ: 1MB
- RAM: 256KB
- Bluetooth 5.0 対応
- USB 2.0 デバイス対応

ファイル: `config/boards/arm/adv360/adv360.dtsi`

主要な設定:
```c
compatible = "nordic/nrf52840_qiaa.dtsi"
```

#### 1.2 フラッシュメモリパーティション

メモリレイアウト (`config/boards/arm/adv360/adv360.dtsi:100-134`):

| パーティション | アドレス | サイズ | 用途 |
|--------------|---------|--------|------|
| sd_partition | 0x00000000 | 0x00026000 (152KB) | SoftDevice (BLEスタック) |
| code_partition | 0x00026000 | 0x000c6000 (792KB) | アプリケーションコード |
| storage_partition | 0x000ec000 | 0x00008000 (32KB) | 設定保存 (NVS) |
| boot_partition | 0x000f4000 | 0x0000c000 (48KB) | ブートローダー |

#### 1.3 キーマトリクス

**左側モジュール** (`config/boards/arm/adv360/adv360_left.dts:11-36`):
- 行 (rows): 5本 (GPIO P1.11, P1.15, P0.3, P1.14, P1.12)
- 列 (columns): 10本 (GPIO P0.25, P0.11, P0.2, P0.28, P0.29, P0.30, P0.31, P1.9, P0.12, P0.7)
- ダイオード方向: col2row
- マトリクスサイズ: 5行 × 20列 (左側は列0-9を使用)

**右側モジュール** (`config/boards/arm/adv360/adv360_right.dts:14-44`):
- 行 (rows): 5本 (GPIO P0.19, P0.5, P0.31, P0.30, P0.29)
- 列 (columns): 10本 (GPIO P0.12, P1.9, P0.7, P1.11, P1.10, P1.13, P1.15, P0.3, P0.2, P0.28)
- ダイオード方向: col2row
- マトリクスオフセット: 列10からスタート (`col-offset = <10>`)

#### 1.4 LED制御

**RGB アンダーグロー** (`config/boards/arm/adv360/adv360.dtsi:136-159`):
- コントローラ: WS2812 (SPI経由)
- LED数: 3個 (レイヤーインジケーター用)
- SPI周波数: 4MHz
- カラーマッピング: GRB

**バックライト** (`config/boards/arm/adv360/adv360.dtsi:63-68`):
- PWM制御 (PWM0チャンネル)
- GPIO: P0.17
- 周波数: 10kHz

### 2. ファームウェア層 (Firmware Layer)

#### 2.1 ZMKコア

本プロジェクトは、Kinesisがカスタマイズした ZMK フォークを使用:

**リポジトリ情報** (`config/west.yml`):
```yaml
remote: refil (https://github.com/refil)
revision: adv360-z3.5-2
```

主な機能:
- ZMK Studio サポート (USB/UART経由のRPC)
- ポインティングデバイスサポート
- 拡張HIDレポート (F13-F24, 国際キー対応)
- Bluetooth プロファイル管理
- 設定永続化 (NVS)

#### 2.2 ボード設定

**左側モジュール設定** (`config/boards/arm/adv360/adv360_left_defconfig`):

主要機能:
```kconfig
CONFIG_ZMK_SPLIT_ROLE_CENTRAL=y     # セントラル役割
CONFIG_ZMK_BLE=y                     # BLE有効
CONFIG_ZMK_USB=y                     # USB有効
CONFIG_ZMK_STUDIO=y                  # ZMK Studio有効
CONFIG_ZMK_RGB_UNDERGLOW=y           # RGB LED有効
CONFIG_ZMK_BACKLIGHT=y               # バックライト有効
CONFIG_ZMK_HID_REPORT_TYPE_NKRO=y   # NKRO有効
CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=y  # 拡張キー有効
CONFIG_ZMK_POINTING=y                # ポインティングデバイス有効
CONFIG_BT_BAS=n                      # バッテリーサービス無効 (デフォルト)
```

電源設定:
- Bluetooth送信電力: +8dBm
- バックライト起動輝度: 20%
- アイドル時自動オフ: 有効

**右側モジュール設定** (`config/boards/arm/adv360/adv360_right_defconfig`):

左側との相違点:
- セントラル役割なし (ペリフェラル)
- ZMK Studio なし
- HIDインジケーターなし
- ポインティングデバイスなし

USB/Bluetooth設定:
```kconfig
CONFIG_USB_DEVICE_VID=0x29EA        # Kinesis Corporation
CONFIG_USB_DEVICE_PID=0x0362        # Advantage 360 Pro
CONFIG_USB_DEVICE_MANUFACTURER="Kinesis Corporation"
```

#### 2.3 キーマップ定義

**デフォルトレイヤー構成** (`config/adv360.keymap`):

| レイヤー番号 | 名称 | 用途 | LED色 (左/右) |
|------------|------|------|--------------|
| 0 | Base | 標準入力レイヤー | 黒/黒 (OFF) |
| 1 | Kp (Keypad) | テンキーレイヤー | 白/白 |
| 2 | Fn | ファンクションキー | 青/青 |
| 3 | Mod | モディファイア (設定/BT) | 緑/緑 |
| 4-7 | extra1-4 | 予約レイヤー | 赤〜黄 |

**日本語配列専用キー** (`config/adv360.keymap:32-38`):

| キーコード | 位置 | 機能 |
|----------|------|------|
| INT1 | 右親指ブロック | \_\ (バックスラッシュ/アンダースコア) |
| INT2 | 左親指ブロック | カタカナ/ひらがな/ローマ字 |
| INT3 | 左手薬指列 | \\\| (円記号/パイプ) |
| INT4 | 左親指ブロック | 変換 |
| INT5 | 右親指ブロック | 無変換 |
| LANG5 | (オプション) | 半角/全角 |

**ビヘイビア (Behaviors)** (`config/adv360.keymap:10-23`):

- **homerow_mods**: ホームローモディファイア実装
  - タッピング時間: 200ms
  - クイックタップ: 175ms
  - フレーバー: tap-preferred

#### 2.4 マクロ定義

**プリセットマクロ** (`config/macros.dtsi`):

カテゴリ別マクロ:

1. **テキスト編集マクロ**:
   - `macro_quotes`: シングルクォートペア入力
   - `macro_dquotes`: ダブルクォートペア入力
   - `macro_braces`: 波括弧ペア入力
   - `macro_parens`: 丸括弧ペア入力
   - `macro_brackets`: 角括弧ペア入力

2. **Windowsショートカットマクロ**:
   - `Win_Cut`, `Win_Copy`, `Win_Paste`: クリップボード操作
   - `Win_Undo`: 元に戻す
   - `Win_Select_All`: すべて選択
   - `Win_Desktop`: デスクトップ表示
   - `Win_File_Explorer`: エクスプローラー起動
   - `Win_Snip_Tool`: スクリーンショット
   - `Win_Show_All_Windows`: ウィンドウ一覧
   - `Win_Lock_PC`: PC ロック
   - `Win_Tile_Left/Right/Up/Down`: ウィンドウタイリング

3. **macOSショートカットマクロ**:
   - `Mac_Cut`, `Mac_Copy`, `Mac_Paste`: クリップボード操作
   - `Mac_Undo`: 元に戻す
   - `Mac_Select_All`: すべて選択
   - `Mac_Mission_Control`: Mission Control
   - `Mac_Snip_Tool`: スクリーンショット
   - `Mac_Spotlight_Search`: Spotlight検索
   - `Mac_Close_Program`: プログラム終了
   - `Mac_Strike_Through_Text`: 取り消し線

4. **システムマクロ**:
   - `macro_kinesis`: "Kinesis" 文字列入力
   - `macro_ver`: バージョン情報入力 (自動生成)
   - `Double_Click`: ダブルクリック

**バージョンマクロ** (`config/version.dtsi`):

ビルド時に自動生成され、以下の情報を含む:
- ビルド日時 (YYYYMMDD形式)
- ブランチ名 (最初の4文字)
- コミットハッシュ
- Clique モード識別子

例: `20250607-V3.0-f65e8a8-CLIQUE`

### 3. ビルドシステム (Build System)

#### 3.1 ローカルビルド (Docker/Podman)

**Makefile ターゲット**:

```makefile
make          # 左右両方をビルド
make left     # 左側のみビルド
make clean    # ファームウェアとDockerイメージを削除
make clean_firmware  # ファームウェアのみ削除
make clean_image     # Dockerイメージのみ削除
```

**ビルドプロセス** (`Makefile`):

1. バージョン情報取得 (`bin/get_version_local.sh`)
2. Dockerイメージビルド (`Dockerfile`)
3. Westビルドシステムによるコンパイル
4. UF2ファイル生成

**Dockerfile** の処理:
```dockerfile
FROM zmkfirmware/zmk-build-arm:stable
WORKDIR /app
COPY config/west.yml config/west.yml
RUN west init -l config       # Westワークスペース初期化
RUN west update               # 依存関係取得
RUN west zephyr-export        # Zephyr環境変数設定
CMD ["./build.sh"]            # ビルドスクリプト実行
```

**出力ファイル名形式**:
```
{YYYYMMDDHHMM}-{commit_hash}-{left|right}-clique.uf2
例: 202506070805-9b337cd-left-clique.uf2
```

#### 3.2 CI/CD (GitHub Actions)

**ワークフロー** (`.github/workflows/build.yml`):

2つのビルドジョブを並列実行:

1. **build (Legacy)**: 通常ビルド
   - ZMK Studioなし
   - 左右両方をビルド
   - アーティファクト名: `firmware-no-clique`

2. **build-clique (Clique)**: ZMK Studio対応ビルド
   - 左側のみZMK Studio有効 (`-S studio-rpc-usb-uart`)
   - 右側は通常ビルド
   - アーティファクト名: `firmware-clique`

**ビルドコマンド例**:
```bash
# 左側 (ZMK Studio対応)
west build -s zmk/app -d build/left -b adv360_left \
  -S studio-rpc-usb-uart \
  -- -DZMK_CONFIG="${GITHUB_WORKSPACE}/config" \
     -DCONFIG_ZMK_STUDIO=y

# 右側
west build -s zmk/app -d build/right -b adv360_right \
  -- -DZMK_CONFIG="${GITHUB_WORKSPACE}/config"
```

**キャッシング**:
- West modules (zephyr, zmk, tools 等) をキャッシュ
- キャッシュキー: `west.yml` のハッシュ値

### 4. 物理レイアウト (Physical Layout)

#### 4.1 キー配置

**レイアウト定義** (`config/boards/arm/adv360/adv360-layouts.dtsi`):

- 総キー数: 85キー (左右合計)
- マトリクス変換: 5行 × 20列
- 物理レイアウト: エルゴノミック分離型

**キー物理属性**:
- 標準キー: 100×100単位
- 1.25U キー: 外側列 (Shift, Ctrl など)
- 2.0U キー: 親指ブロック (Space, Enter, Backspace, Delete)

**親指ブロックの回転配置**:
- 左親指ブロック: 15度回転 (回転中心: x=525, y=400)
- 右親指ブロック: -15度回転 (回転中心: x=1275, y=400)

#### 4.2 キーポジション

キーポジションマップ: `assets/key-positions.md` および `assets/key-positions.png`

マトリクス座標は以下の形式:
```
RC(row, col)
例: RC(0,0) = 左上端キー
```

### 5. 通信プロトコル (Communication)

#### 5.1 分離通信 (Split Communication)

**役割**:
- **左側 (Central)**: BLEセントラル、ホストとUSB接続
- **右側 (Peripheral)**: BLEペリフェラル、左側に接続

**プロトコル**:
- ZMK Split Protocol (独自BLEプロトコル)
- キー状態の同期
- LED状態の同期 (レイヤーインジケーター)

#### 5.2 ホスト通信

**USB接続**:
- HIDキーボード (NKRO対応)
- HIDコンシューマー (メディアキー)
- ZMK Studio RPC (左側のみ)

**Bluetooth接続**:
- BLE HIDプロファイル
- 最大5デバイスペアリング
- プロファイル切り替え対応

**HIDレポート設定**:
```kconfig
CONFIG_ZMK_HID_REPORT_TYPE_NKRO=y              # NKRO有効
CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=y      # F13-F24対応
CONFIG_ZMK_HID_CONSUMER_REPORT_USAGES_BASIC=y  # 基本メディアキー
```

### 6. 電源管理 (Power Management)

#### 6.1 バッテリー監視

**電圧分圧器** (`config/boards/arm/adv360/adv360.dtsi:56-61`):
- ADC入力: チャンネル2
- 出力抵抗: 100kΩ
- 分圧比: 1:2 (100kΩ + 100kΩ)

#### 6.2 省電力機能

**アイドルスリープ**:
- RGB アンダーグロー: アイドル時自動オフ
- バックライト: アイドル時自動オフ

**Bluetooth省電力**:
```kconfig
CONFIG_BT_PERIPHERAL_PREF_TIMEOUT=600  # 接続タイムアウト (オプション)
CONFIG_BT_PERIPHERAL_PREF_MIN_INT=12   # 最小接続間隔 (オプション)
CONFIG_BT_PERIPHERAL_PREF_MAX_INT=24   # 最大接続間隔 (オプション)
CONFIG_BT_PERIPHERAL_PREF_LATENCY=30   # 接続レイテンシ (オプション)
```

**外部電源制御** (`config/boards/arm/adv360/adv360.dtsi:51-54`):
- 制御GPIO: P0.13
- RGB LEDとバックライトの電源制御

### 7. ZMK Studio統合

#### 7.1 ZMK Studioサポート

**設定ファイル** (`config/boards/arm/adv360/adv360.zmk.yml`):
```yaml
file_format: "1"
id: adv360
name: Adv360 Pro
type: board
arch: arm
```

**機能**:
- リアルタイムキーマップ編集
- レイヤー管理
- マクロ設定
- RGB/バックライト設定
- Bluetooth管理

**通信方式**:
- USB経由のRPCプロトコル
- UARTバックアップ通信

**studio_unlock動作** (`config/adv360.keymap:71`):
- ZMK Studioロック解除
- キーマップ編集を有効化

#### 7.2 キーマップJSON

**構造** (`config/keymap.json`, `config/keymap_default_adv360_ja.json`):

```json
{
  "keyboard": "adv360",
  "keymap": "default",
  "layout": "LAYOUT",
  "layer_names": ["base", "keypad", "fn", "mod"],
  "layers": [ ... ]
}
```

ZMK Studioとの互換性を維持:
- レイヤー名の定義
- キーバインディングの表現
- 日本語配列専用設定 (keymap_default_adv360_ja.json)

### 8. LED制御とビジュアルフィードバック

#### 8.1 RGB アンダーグロー

**設定** (`config/boards/arm/adv360/adv360_left_defconfig:34-40`):

```kconfig
CONFIG_ZMK_RGB_UNDERGLOW=y                  # RGB有効
CONFIG_ZMK_RGB_UNDERGLOW_EXT_POWER=y        # 外部電源制御
CONFIG_ZMK_RGB_UNDERGLOW_ON_START=y         # 起動時点灯
CONFIG_ZMK_RGB_UNDERGLOW_EFF_START=4        # 起動エフェクト番号
CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE=y    # アイドル自動オフ
```

**レイヤーインジケーター色**:

最大32レイヤーをサポート。各レイヤーに固有の色を割り当て:

| レイヤー範囲 | 左LED | 右LED | 例 |
|------------|-------|-------|-----|
| 0-7 | 単色 | 同色 | Layer 0: 黒/黒, Layer 1: 白/白 |
| 8-15 | 白 | 7色サイクル | Layer 8: 白/青 |
| 16-23 | 青 | 7色サイクル | Layer 16: 青/赤 |
| 24-31 | 緑〜赤 | 7色サイクル | Layer 24: 緑/シアン |

カラーコード: RGB 24bit (例: 0xFF0000 = 赤)

**モディファイアインジケーター色設定**:

Caps Lock/Num Lock/Scroll Lock時のLED色をカスタマイズ可能:
```kconfig
CONFIG_ZMK_RGB_UNDERGLOW_MOD_COLOR=0xFF0000  # 例: 赤色
```

左右両方のdefconfigで設定する必要あり。

#### 8.2 バックライト

**PWM設定** (`config/boards/arm/adv360/adv360_left_defconfig:42-47`):

```kconfig
CONFIG_ZMK_BACKLIGHT=y                      # バックライト有効
CONFIG_ZMK_BACKLIGHT_BRT_START=20           # 起動時輝度 (0-100%)
CONFIG_ZMK_BACKLIGHT_AUTO_OFF_IDLE=y        # アイドル自動オフ
```

**制御キー** (Modレイヤー):
- `&bl BL_TOG`: オン/オフ切り替え
- `&bl BL_INC`: 輝度アップ
- `&bl BL_DEC`: 輝度ダウン

### 9. ファームウェア書き込み

#### 9.1 ブートローダーモード

**エントリー方法**:

1. **キーボードショートカット**:
   - 左側: `Mod` + `macro1` キー
   - 右側: `Mod` + `macro3` キー
   - Modレイヤーの`&bootloader`バインディング

2. **物理リセットボタン**:
   - キーボード底面のリセットボタンを押す
   - 位置: ユーザーマニュアル セクション2.7参照

#### 9.2 書き込み手順

標準的な書き込みプロセス:

1. 左側をUSB接続
2. 左側をブートローダーモードに (USBドライブとしてマウント)
3. `{timestamp}-{commit}-left-clique.uf2` をコピー
4. 自動的に再起動
5. 両側の電源をオフ
6. 左側の電源をオン (Bluetooth セントラル起動)
7. 右側をUSB接続
8. 右側をブートローダーモードに
9. `{timestamp}-{commit}-right-clique.uf2` をコピー
10. 右側の電源をオン

**注意事項**:
- 必ず左側から書き込む
- 両側の電源管理を適切に行う
- ファームウェアバージョンの整合性を保つ

#### 9.3 設定リセット

**リセットファイル**: `settings-reset.uf2`

用途:
- キーマップ設定のリセット
- Bluetoothペアリング情報の削除
- 工場出荷状態への復元

手順:
1. ブートローダーモードでマウント
2. `settings-reset.uf2` をコピー
3. 再起動後、新しいファームウェアを書き込む

### 10. 開発とカスタマイズ

#### 10.1 キーマップのカスタマイズ

**編集方法**:

1. **ZMK Studio使用** (推奨):
   - リアルタイムGUI編集
   - USB接続で即座に反映
   - 設定はフラッシュに保存

2. **手動編集**:
   - `config/adv360.keymap` を編集
   - 再ビルドと書き込みが必要
   - ZMK Studioで編集後は`.keymap`の変更が無効化される

**ZMK Studio使用時の注意**:
- Studio で編集すると `.keymap` ファイルは無視される
- 元に戻すには「Restore Stock Settings」を実行
- 詳細: [ZMK Studio Documentation](https://zmk.dev/docs/features/studio)

#### 10.2 コンボ (Combos)

キー位置の正確な指定が必要:
- キーポジションマップ: `assets/key-positions.md`
- マトリクス座標: RC(row, col) 形式

#### 10.3 デバッグとログ

**Kconfig 出力**:
- GitHub Actions: ビルドログに`.config`ファイル出力
- ローカル: `build/{left|right}/zephyr/.config` 参照

**バージョン確認**:
- `Mod` + `V` でバージョン情報を出力
- 形式: `YYYYMMDD-XXXX-YYYYYY`
  - XXXX: ブランチ名 (最初の4文字)
  - YYYYYY: コミットハッシュ

### 11. プロジェクト固有の考慮事項

#### 11.1 日本語配列対応

**国際キー配置**:

本プロジェクトでは、JIS配列に必要な国際キーを以下のように配置:

- **INT3** (`\|`): 左手薬指列 (US配列のバックスラッシュ位置)
- **INT2** (かな): 左親指ブロック
- **INT4** (変換): 左親指ブロック
- **INT5** (無変換): 右親指ブロック
- **INT1** (`\_`): 右親指ブロック
- **LANG5** (半角/全角): オプション位置

**ZMK でのキーコード**:
```c
&kp INT1    // International 1
&kp INT2    // International 2
&kp INT3    // International 3
&kp INT4    // International 4
&kp INT5    // International 5
&kp LANG5   // Language 5
```

詳細: [ZMK Keycodes - International](https://zmk.dev/docs/keymaps/list-of-keycodes)

#### 11.2 NKRO (N-Key Rollover)

**デフォルト設定**:
```kconfig
CONFIG_ZMK_HID_REPORT_TYPE_NKRO=y           # NKRO有効
CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=y   # 拡張レポート有効
```

**互換性**:
- NKRO有効で通常のキーは全て同時押し可能
- 拡張レポートでF13-F24およびINT1-9キーもNKRO対応
- 一部のBIOSやレガシーシステムでは非互換の可能性

拡張レポートが不要な場合:
```kconfig
CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=n   # 無効化
```

#### 11.3 バッテリーレポート

**デフォルト設定**: 無効

理由:
- 一部のコンピュータで意図しないスリープ解除が発生

有効化方法:
```kconfig
# config/boards/arm/adv360/adv360_left_defconfig
CONFIG_BT_BAS=y   # n から y に変更
```

#### 11.4 カスタムZMKフォーク

本プロジェクトは Kinesis のカスタムZMKフォークを使用:

**利点**:
- Advantage 360 Pro 専用機能
- RGB レイヤーインジケーター
- ZMK Studio 統合
- 定期的なアップストリーム同期

**注意事項**:
- ベースZMKの最新機能は即座に利用できない場合がある
- ベースZMKとの互換性は保たれている (PR: [#1454](https://github.com/zmkfirmware/zmk/pull/1454))
- カスタム機能のサポートはKinesisが提供
- ベースZMKのサポートはZMKコミュニティ

#### 11.5 V2.0 から V3.0 へのアップグレード

**移行手順**: `UPGRADE.md` / `UPGRADE_JA.md` 参照

**主な変更点**:
- ファームウェア構造の大幅な変更
- 設定リセットが必要
- メジャーバージョンごとに専用リセットファイルあり

詳細情報: [Kinesis Firmware Updates](https://kinesis-ergo.com/support/kb360pro/#firmware-updates)

### 12. ベータテストとアップデート

#### 12.1 ベータブランチの使用

**設定方法** (`config/west.yml`):

```yaml
projects:
  - name: zmk
    remote: refil
    revision: {beta-branch-name}  # ブランチ名を変更
    import: app/west.yml
```

**注意事項**:
- 現在テスト可能なベータブランチはなし
- フィードバックはベースZMKリポジトリにissueを作成
- メジャーアップデート時は設定リポジトリの互換性に注意

#### 12.2 変更履歴

**ドキュメント**: `CHANGELOG.md` / `CHANGELOG_JA.md`

記録内容:
- 設定リポジトリの変更
- ZMKフォークの変更
- 機能追加・削除
- バグフィックス
- 既知の問題

### 13. サポートとリソース

#### 13.1 公式ドキュメント

**ZMKドキュメント**:
- 公式サイト: https://zmk.dev/docs
- キーコード一覧: https://zmk.dev/docs/keymaps/list-of-keycodes
- ビヘイビア: https://zmk.dev/docs/behaviors
- ZMK Studio: https://zmk.dev/docs/features/studio

**Kinesisリソース**:
- サポートページ: https://kinesis-ergo.com/support/kb360pro/
- ファームウェアアップデート: https://kinesis-ergo.com/support/kb360pro/#firmware-updates
- マニュアル: https://kinesis-ergo.com/support/kb360pro/#manuals
- サポートチケット: https://kinesis-ergo.com/support/kb360pro/#ticket

#### 13.2 プロジェクトリポジトリ

**フォーク元**: Kinesis Advantage 360 Pro ZMK Config

**カスタムZMKフォーク**: https://github.com/ReFil/zmk/tree/adv360-z3.5

**本プロジェクトの特徴**:
- 日本語配列対応
- 日本語ドキュメント完備
- デフォルトで日本語キーマップ設定

#### 13.3 トラブルシューティング

**一般的な問題**:

1. **ビルドエラー**:
   - `make clean` でクリーンビルド
   - `west.yml` の内容を確認
   - Dockerイメージの再構築

2. **キーが反応しない**:
   - ファームウェアバージョンの確認
   - 左右両側のファームウェア整合性
   - マトリクススキャン設定の確認

3. **Bluetooth接続問題**:
   - ペアリング情報のクリア (settings-reset)
   - デバイス側のペアリング削除
   - 電源の再投入

4. **ZMK Studio接続失敗**:
   - USB接続の確認
   - 左側モジュールに接続
   - Clique ビルドの使用を確認

**ハードウェア問題**:
- Kinesis に直接サポートチケットを申請
- GitHub issueではなく公式サポート窓口へ

### 14. 今後の拡張性

#### 14.1 追加可能な機能

**現在のZMK機能**:
- マウスキー (Pointing Device)
- トラックポイント/トラックボール統合
- ディスプレイ統合 (OLED等)
- エンコーダー (ロータリーエンコーダ)
- 追加センサー (加速度計等)

**カスタマイズポイント**:
- レイヤー数の増減 (最大32)
- マクロの追加
- コンボ定義
- タップダンス
- モードベース入力

#### 14.2 コミュニティ貢献

**貢献方法**:
- GitHub issues: バグレポート、機能リクエスト
- Pull requests: コード改善、ドキュメント追加
- フォーク: 個人用カスタマイズ版の作成

**ガイドライン**:
- Kinesisカスタムフォーク関連: 本リポジトリ
- ベースZMK関連: ZMK本家リポジトリ
- ハードウェア問題: Kinesis公式サポート

## まとめ

Kinesis Advantage 360 Pro ZMK ファームウェアは、高度にカスタマイズ可能な分離型エルゴノミックキーボードのための包括的なソリューションです。本プロジェクトは日本語配列に特化し、ZMKの強力な機能とKinesisのハードウェア最適化を組み合わせています。

**主要な特徴**:
- Nordic nRF52840 ベースのBLE/USB対応
- 分離型左右モジュール (左:セントラル、右:ペリフェラル)
- ZMK Studio によるGUIキーマップ編集
- 日本語JIS配列完全サポート
- RGB レイヤーインジケーター
- NKRO および拡張HIDレポート
- Docker/Podman によるクロスプラットフォームビルド
- GitHub Actions 自動ビルド

本ドキュメントは、プロジェクトの全ファイルを詳細に解析し、ハードウェア、ファームウェア、ビルドシステム、カスタマイズ方法の全体像を網羅的に説明しました。

---

**ドキュメントバージョン**: 1.0
**最終更新**: 2025年11月6日
**対象ファームウェアバージョン**: V3.0 系列 (adv360-z3.5-2)
