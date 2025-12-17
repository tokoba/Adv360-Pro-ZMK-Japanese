# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

これは**Kinesis Advantage 360 Pro**日本語キーボード用の**ZMK (Zephyr Keyboard Firmware)**設定リポジトリです。現在のブランチは`V3.0-Japanese`で、日本語レイアウトに特化した設定が含まれています。

### 主な特徴
- ZMKベースのオープンソースファームウェア
- 左右分割型スプリットキーボード（Bluetooth対応）
- 日本語JISキーボードレイアウト対応（国際キーINT1-INT5、LANG5）
- Dockerコンテナベースのビルドシステム
- RGBアンダーグローとレイヤーインジケーター
- ホームローモディファイア対応

## 開発環境セットアップ

### 必須ソフトウェア
- **Docker** または **Podman**（両方ある場合Podmanが優先）
- **Make**
- **Git**

### プラットフォーム別設定

#### Windows
```powershell
# WSL2とDocker Desktopが必要
wsl --install
# WSL2内で
sudo apt-get update
sudo apt-get install make
```

#### macOS
```bash
# Homebrewでインストール
brew install docker colima
# Apple Siliconの場合
colima start --arch x86_64
# Intel Macの場合
colima start
```

#### Ubuntu/Debian
```bash
sudo apt-get update
sudo apt-get install docker make
sudo usermod -aG docker $USER  # Dockerユーザーグループに追加
```

## ビルドコマンド

### 基本ビルド
```bash
# 両方のキーボード（左右）をビルド
make

# 左側のみビルド
make left
```

### クリーンアップ
```bash
# ファームウェアファイルのみ削除
make clean_firmware

# Dockerイメージのみ削除
make clean_image

# すべてクリーンアップ
make clean
```

### GitHub Actionsでのビルド
1. リポジトリをフォーク
2. GitHub Actionsを有効化
3. コミットをプッシュしてビルドをトリガー
4. ビルド成果物をダウンロード

出力ファイル: `firmware/{YYYYMMDDHHMM}-{commit_id}-{left/right}-clique.uf2`

## 設定ファイル構造

### 主要設定ファイル
- **`config/adv360.keymap`** - メインキーマップ定義（日本語レイアウト）
- **`config/adv360_left.keymap`** - 左側キーボード用キーマップ
- **`config/adv360_right.keymap`** - 右側キーボード用キーマップ
- **`config/west.yml`** - Westマニフェスト（カスタムZMKフォークを指定）
- **`config/boards/arm/adv360/Kconfig.defconfig`** - ボード固有の設定

### インクルードファイル
- **`config/macros.dtsi`** - カスタムマクロ定義
- **`config/version.dtsi`** - バージョン情報（自動生成）

## 日本語レイアウト対応

### 国際キー割り当て
| キー | ZMKコード | 物理位置 | 機能 |
|------|-----------|----------|------|
| INT1 | `INT1` | 右親指ブロック | バックスラッシュ (\) |
| INT2 | `INT2` | 左親指ブロック | かな/ひらがな/ローマ字 |
| INT3 | `INT3` | 左薬指 | パイプ (|) |
| INT4 | `INT4` | 左親指ブロック | 変換 |
| INT5 | `INT5` | 右親指ブロック | 無変換 |
| LANG5 | `LANG5` | 未使用 | 半角/全角 |

## レイヤー構造

### 定義済みレイヤー
1. **Base (レイヤー0)** - メインレイヤー
2. **Keypad (レイヤー1)** - テンキーと記号
3. **Function (レイヤー2)** - F1-F12ファンクションキー
4. **Mod (レイヤー3)** - Bluetooth、RGB、設定
5. **Red (extra1)** - 予約済み
6. **Purple (extra2)** - 予約済み
7. **Cyan (extra3)** - 予約済み
8. **Yellow (extra4)** - 予約済み

### レイヤーカラー
ZMKは最大32レイヤーをサポートし、各レイヤーに固有のRGBカラーが設定されます。

## ファームウェア書き込み

### ブートローダーモード
1. **左側**: `Mod` + `macro1` を押す
2. **右側**: `Mod` + `macro3` を押す

### 書き込み手順
1. キーボードをUSBに接続
2. ブートローダーモードにする（キーボードがUSBドライブとして認識される）
3. 対応するUF2ファイルをドラッグ＆ドロップ
4. 自動的に再起動

### 物理リセットボタン
ユーザーマニュアルのセクション2.7（9ページ）に位置の記載があります。

## 開発に関する重要事項

### VSCode設定
`.vscode/settings.json` でJSONの自動フォーマットを無効化：
```json
{
    "editor.formatOnPaste": false,
    "editor.formatOnSave": false,
    "editor.formatOnType": false
}
```

### ZMK Studioの互換性
- ZMK Studioでキーマップを編集した場合、`.keymap`ファイルの変更は無効になる
- 元に戻すには「Restore Stock Settings」を実行

### バージョン管理
- ビルド時にタイムスタンプとGitコミットハッシュが自動記録
- `Mod` + `V` でバージョン情報を表示

### 設定オプション
- **NKRO有効化**: `CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=y`（adv360_left_defconfig）
- **バッテリーレポート**: `CONFIG_BT_BAS=y`（デフォルトは無効）
- **修飾キーインジケーター色**: `CONFIG_ZMK_RGB_UNDERGLOW_MOD_COLOR=0xFF0000`（赤の場合）

## ZMK固有のパターン

### Devicetree構文
キーマップはDevicetree (.dts) 構文で記述：
```c
/ {
    keymap {
        compatible = "zmk,keymap";

        default_layer {
            bindings = <
                &kp A    &kp S    &kp D
                &kp Q    &kp W    &kp E
            >;
        };
    };
};
```

### カスタムビヘイビア
- **homerow_mods**: ホームポジションでの修飾キー機能
- **tap-preferred**: ホールドよりタップを優先する設定

### キーコード一覧
- 基本キー: `&kp KEY_NAME`
- 修飾キー: `&lc KEY_NAME`（左コントロールなど）
- レイヤー切り替え: `&mo LAYER_NUM`
- トグル: `&tog LAYER_NUM`
- マクロ: `&macro_name`

## トラブルシューティング

### V2からV3へのアップグレード
`UPGRADE_JA.md` に詳細な手順が記載されています。主な変更点：
- 設定ファイル構造の変更
- 新しいZMKフォークへの移行

### よくあるビルドエラー
1. **Dockerコンテナのビルドに失敗**
   - `make clean` でクリーンアップして再ビルド

2. **メモリ不足**
   - Dockerに十分なメモリを割り当て（推奨: 4GB以上）

3. **パーミッションエラー**
   - Dockerユーザーグループに追加済みか確認

### ベータテスト参加
`config/west.yml` の `revision` をベータブランチ名に変更します。

## 特殊な機能

### Bluetoothペアリング
Modレイヤーから:
- `bt BT_SEL 0-4`: デバイス選択
- `bt BT_CLR`: ペアリング情報クリア

### RGB制御
- `rgb_ug RGB_TOG`: RGB ON/OFF
- `rgb_ug RGB_MEFS_CMD 5`: エフェクト切り替え

### マクロ機能
- `macro_ver`: バージョン情報をタイプ
- カスタムマクロは `macros.dtsi` に定義

## 参考リソース

- [ZMK公式ドキュメント](https://zmk.dev/docs)
- [Kinesis日本語ユーザーガイド](https://manuals.plus/ja/kinesis/kb360-pro-advantage360-pro-keyboard-manual)
- [キーポジションマップ](assets/key-positions.md)
- [ZMK Studio](https://zmk.dev/docs/features/studio)

## 注意事項

- このプロジェクトはカスタムZMKフォーク（adv360-z3.5-2）を使用
- 基本ZMKとの互換性はあるが、高度な機能（RGBインジケーターLEDなど）は動作しない場合あり
- 設定変更後は必ずテストしてから書き込むこと
- 日本語環境特有の問題（IMEの挙動など）はOS側の設定も確認すること