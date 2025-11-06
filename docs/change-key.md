# キーマップ変更ガイド - 日本語配列キーのカスタマイズ

## 概要

このガイドでは、Kinesis Advantage 360 Pro の日本語配列キーマップをカスタマイズする方法を詳しく説明します。具体例として、**変換キー（INT4）と無変換キー（INT5）を入れ替える**手順を示します。

本ガイドで扱う内容：
- 現在のキー配置の確認方法
- キーマップファイルの編集方法
- ローカル環境でのビルド手順
- ファームウェアのインストール手順
- トラブルシューティング

## 目次

1. [前提知識](#前提知識)
2. [キー配置の確認](#キー配置の確認)
3. [編集方法の選択](#編集方法の選択)
4. [方法1: 手動編集（推奨）](#方法1-手動編集推奨)
5. [方法2: ZMK Studio使用](#方法2-zmk-studio使用)
6. [ビルド手順](#ビルド手順)
7. [ファームウェアのインストール](#ファームウェアのインストール)
8. [動作確認](#動作確認)
9. [トラブルシューティング](#トラブルシューティング)

---

## 前提知識

### 日本語配列の国際キー

Advantage 360 Pro で使用される日本語キーボード固有のキー：

| キーコード | ZMK表記 | 機能 | デフォルト位置 |
|-----------|---------|------|--------------|
| INT1 | `&kp INT1` | \_\ (バックスラッシュ/アンダースコア) | 右親指ブロック |
| INT2 | `&kp INT2` | かな/カナ (カタカナ/ひらがな/ローマ字) | 左親指ブロック |
| INT3 | `&kp INT3` | \\\| (円記号/パイプ) | 左手薬指列 |
| INT4 | `&kp INT4` | 変換 | 左親指ブロック |
| INT5 | `&kp INT5` | 無変換 | 右親指ブロック |
| LANG5 | `&kp LANG5` | 半角/全角 | (オプション) |

### キーマップファイルの種類

本プロジェクトには複数のキーマップ関連ファイルがあります：

| ファイル | 用途 | 編集方法 |
|---------|------|---------|
| `config/adv360.keymap` | メインキーマップ定義 (DTS形式) | テキストエディタ |
| `config/keymap.json` | ZMK Studio用キーマップ | ZMK Studio |
| `config/keymap_default_adv360_ja.json` | 日本語デフォルトキーマップ | テキストエディタ |
| `config/adv360_left.keymap` | 左側インクルード | 通常編集不要 |
| `config/adv360_right.keymap` | 右側インクルード | 通常編集不要 |

### 重要な注意事項

⚠️ **ZMK Studio使用時の制約**

ZMK Studio でキーマップを編集すると、設定はキーボードのフラッシュメモリに保存され、`.keymap` ファイルの内容は**無視されます**。

- ZMK Studio編集後に `.keymap` ファイルを変更しても反映されない
- 元に戻すには「Restore Stock Settings」を実行してファームウェアを再書き込み

詳細: [ZMK Studio Documentation](https://zmk.dev/docs/features/studio)

---

## キー配置の確認

### 現在のキー配置

デフォルトのBaseレイヤー（レイヤー0）の親指ブロック配置：

**左親指ブロック（左から右へ）:**
```
[BACKSPACE] [DELETE] [INT4/変換]
```

**右親指ブロック（左から右へ）:**
```
[INT5/無変換] [ENTER] [SPACE]
```

### 物理的なキー位置

`assets/key-positions.md` および `assets/key-positions.png` で確認できます。

親指ブロックのキーポジション番号：

**左親指ブロック:**
```
   35 36      (上段)
      52      (中段)
65 66 67      (下段)
```

**右親指ブロック:**
```
   37 38      (上段)
      53      (中段)
68 69 70      (下段)
```

キーマップでの配置順序（左から右、上から下）:
- キーポジション67 = INT4（変換）
- キーポジション68 = INT5（無変換）

---

## 編集方法の選択

### 方法1: 手動編集（推奨）

**メリット:**
- バージョン管理が容易（Git履歴に残る）
- 細かい制御が可能
- ZMK Studioとの競合なし
- コメントを追加して説明を残せる

**デメリット:**
- テキスト編集の知識が必要
- ビルドとインストールが必要

**推奨環境:**
- 開発者向け
- 複数の変更を一度に行いたい場合
- チーム共有や公開が必要な場合

### 方法2: ZMK Studio使用

**メリット:**
- GUI で直感的に編集可能
- USB接続で即座に反映
- ビルド不要

**デメリット:**
- `.keymap` ファイルが無視される
- バージョン管理が困難
- リセットにファームウェア再書き込みが必要

**推奨環境:**
- エンドユーザー向け
- 一時的な変更を試したい場合

---

## 方法1: 手動編集（推奨）

### ステップ1: リポジトリのクローン

既にフォークしている場合はスキップできます。

```bash
# フォークしたリポジトリをクローン
git clone https://github.com/YOUR_USERNAME/Adv360-Pro-ZMK-Japanese.git
cd Adv360-Pro-ZMK-Japanese

# ブランチを作成（推奨）
git checkout -b feature/swap-henkan-muhenkan
```

### ステップ2: キーマップファイルの編集

#### 2.1 config/adv360.keymap の編集

エディタで `config/adv360.keymap` を開きます。

**変更前 (37行目):**
```c
     &mo 2  &kp ESC  &kp INT3  &kp LEFT  &kp RIGHT                    &kp BSPC    &kp DEL  &kp INT4                  &kp INT5  &kp ENTER  &kp SPACE          &kp UP   &kp DOWN  &kp RIGHT_BRACKET  &kp NON_US_HASH             &mo 2
```

この行の構造：
```
左側キー                                                         左親指ブロック                           右親指ブロック                                         右側キー
&mo 2  &kp ESC  &kp INT3  &kp LEFT  &kp RIGHT    |    &kp BSPC  &kp DEL  &kp INT4    |    &kp INT5  &kp ENTER  &kp SPACE    |    &kp UP  &kp DOWN  &kp RIGHT_BRACKET  &kp NON_US_HASH  &mo 2
                                                                          ^^^^^^^^              ^^^^^^^^
                                                                          変換キー              無変換キー
```

**変更後 (INT4とINT5を入れ替え):**
```c
     &mo 2  &kp ESC  &kp INT3  &kp LEFT  &kp RIGHT                    &kp BSPC    &kp DEL  &kp INT5                  &kp INT4  &kp ENTER  &kp SPACE          &kp UP   &kp DOWN  &kp RIGHT_BRACKET  &kp NON_US_HASH             &mo 2
```

変更点：
- `&kp INT4` → `&kp INT5` に変更
- `&kp INT5` → `&kp INT4` に変更

#### 2.2 他のレイヤーの確認

Keypadレイヤー（レイヤー1）やFnレイヤー（レイヤー2）でも同様の位置にキーがある場合は、同様に変更します。

**Keypadレイヤー (49行目) の確認:**
```c
     &mo 2  &kp ESC  &kp EQUAL  &kp LEFT  &kp RIGHT                    &kp BSPC    &kp DEL   &kp END                  &kp PG_DN  &kp ENTER  &kp KP_N0              &kp UP   &kp DOWN     &kp KP_DOT     &kp KP_ENTER             &mo 2
```

この例では `&kp END` と `&kp PG_DN` が割り当てられているため、変更は不要です（必要に応じて変更可能）。

#### 2.3 変更内容の保存

ファイルを保存します。

**VSCode使用時の注意:**

プロジェクトルートの `.vscode/settings.json` に以下の設定があることを確認：

```json
{
    "editor.formatOnPaste": false,
    "editor.formatOnSave": false,
    "editor.formatOnType": false
}
```

これにより、自動フォーマットで意図しないスペース変更が発生することを防ぎます。

### ステップ3: keymap_default_adv360_ja.json の編集（オプション）

JSON形式のキーマップも更新する場合は、`config/keymap_default_adv360_ja.json` も編集します。

**変更箇所の特定:**

ファイルの13行目付近（Baseレイヤー最終行）:

**変更前:**
```json
"&mo 2", "&kp ESC", "&kp INT3", "&kp LEFT", "&kp RIGHT",                     "&kp BSPC",   "&kp DEL", "&kp INT4",                     "&kp INT5", "&kp ENTER", "&kp SPACE",           "&kp UP",  "&kp DOWN", "&kp RIGHT_BRACKET", "&kp NON_US_HASH",            "&mo 2"
```

**変更後:**
```json
"&mo 2", "&kp ESC", "&kp INT3", "&kp LEFT", "&kp RIGHT",                     "&kp BSPC",   "&kp DEL", "&kp INT5",                     "&kp INT4", "&kp ENTER", "&kp SPACE",           "&kp UP",  "&kp DOWN", "&kp RIGHT_BRACKET", "&kp NON_US_HASH",            "&mo 2"
```

⚠️ **注意:** JSON は厳密な構文を要求するため、カンマやクォートの位置に注意してください。

### ステップ4: 変更のコミット（推奨）

```bash
git add config/adv360.keymap config/keymap_default_adv360_ja.json
git commit -m "Swap INT4 (Henkan) and INT5 (Muhenkan) keys

- INT4 (変換) moved from left thumb to right thumb
- INT5 (無変換) moved from right thumb to left thumb
"
```

---

## ビルド手順

### 前提条件の確認

以下のいずれかが必要です：

**オプション1: Docker または Podman**
- Docker Desktop (Windows/Mac/Linux)
- Podman (Linux推奨)

**オプション2: GitHub Actions（ビルドのみ）**
- GitHubアカウント
- フォークしたリポジトリ

**オプション3: Make**
- ローカルビルドに必要
- Windows: WSL2 + Docker + Make
- Mac: brew install make docker colima
- Linux: apt-get install make docker

### ローカルビルド手順

#### Linux/Mac の場合

**1. ビルド環境の確認**

```bash
# Docker/Podman が利用可能か確認
docker --version
# または
podman --version

# Make が利用可能か確認
make --version
```

**2. ビルドの実行**

```bash
# プロジェクトルートディレクトリで実行
make
```

または左側のみビルド：
```bash
make left
```

**3. ビルドプロセス**

ビルド時に以下が実行されます：

1. バージョン情報取得 (`bin/get_version_local.sh`)
2. Dockerイメージのビルド (`Dockerfile`)
3. West ワークスペースの初期化
4. ZMK依存関係の取得
5. 左右ファームウェアのコンパイル
6. UF2ファイルの生成

**4. ビルド出力の確認**

ビルドが成功すると、`firmware/` ディレクトリに以下のファイルが生成されます：

```
firmware/
├── {YYYYMMDDHHMM}-{commit_hash}-left-clique.uf2
└── {YYYYMMDDHHMM}-{commit_hash}-right-clique.uf2
```

例：
```
firmware/
├── 202511060930-a1b2c3d-left-clique.uf2
└── 202511060930-a1b2c3d-right-clique.uf2
```

**ファイル名の構成:**
- `YYYYMMDDHHMM`: ビルド日時 (UTC)
- `commit_hash`: Gitコミットハッシュ（短縮形）
- `left-clique` / `right-clique`: 左側/右側モジュール

#### Windows (WSL2) の場合

**1. WSL2のセットアップ**

```powershell
# PowerShellで実行
wsl --install
```

**2. WSL2でUbuntuを起動**

```bash
# WSL2ターミナルで実行
sudo apt-get update
sudo apt-get install -y make docker.io
sudo service docker start
```

**3. リポジトリをWSL2内にクローン**

```bash
cd ~
git clone https://github.com/YOUR_USERNAME/Adv360-Pro-ZMK-Japanese.git
cd Adv360-Pro-ZMK-Japanese
```

**4. ビルド実行**

```bash
make
```

⚠️ **注意:** Windowsファイルシステム（`/mnt/c/`）からのビルドは非常に遅いため、WSL2のホームディレクトリ（`~`）を使用してください。

#### Mac (Apple Silicon) の場合

**1. 必要なツールのインストール**

```bash
# Homebrewがない場合はインストール
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Docker関連ツールのインストール
brew install docker colima

# Colimaの起動（x86_64アーキテクチャ）
colima start --arch x86_64
```

⚠️ **重要:** Apple Silicon (M1/M2/M3) では `--arch x86_64` が必須です。

**2. ビルド実行**

```bash
cd Adv360-Pro-ZMK-Japanese
make
```

### GitHub Actionsによるビルド

ローカル環境がない場合や、クリーンな環境でビルドしたい場合：

**1. GitHubにプッシュ**

```bash
git push origin feature/swap-henkan-muhenkan
```

**2. GitHub Actionsの確認**

1. GitHubリポジトリの「Actions」タブを開く
2. 最新のワークフロー実行を確認
3. ビルドが完了するまで待機（約5-10分）

**3. アーティファクトのダウンロード**

1. 完了したワークフローをクリック
2. 「Artifacts」セクションを探す
3. `firmware-clique` をダウンロード（ZMK Studio対応版）
   - または `firmware-no-clique` （通常版）
4. ZIPファイルを解凍

ダウンロードされるファイル：
```
{timestamp}-{commit}-left.uf2
{timestamp}-{commit}-right.uf2
```

### ビルドのクリーンアップ

ビルドエラーが発生した場合や、クリーンビルドが必要な場合：

```bash
# ファームウェアとDockerイメージを全削除
make clean

# ファームウェアのみ削除
make clean_firmware

# Dockerイメージのみ削除
make clean_image
```

---

## ファームウェアのインストール

### 必要な準備

- ビルドされたUF2ファイル（左右両方）
- USBケーブル（Type-C）
- キーボード本体

### インストール手順

#### ステップ1: 左側モジュールの書き込み

**1.1 左側をUSB接続**

左側キーボードモジュールをコンピュータにUSB接続します。

**1.2 ブートローダーモードに移行**

以下のいずれかの方法でブートローダーモードに入ります：

**方法A: キーボードショートカット**

1. `Mod` キー（レイヤー3アクセスキー）を押しながら保持
2. `macro1` キー（左上のキー - 通常は `1` の位置）を押す
3. キーボードがUSBドライブとして認識される

**方法B: 物理リセットボタン**

1. キーボードを裏返す
2. 小さなリセットボタンを見つける（位置は[ユーザーマニュアル](../doc/Advantage360-ZMK-KB360-PRO-Users-Manual-v2-5-25-Clique.pdf) セクション2.7参照）
3. ボタンを押す

**1.3 USBドライブの確認**

オペレーティングシステムに応じて、以下のように表示されます：

- **Windows**: `ADV360PRO` ドライブとして表示
- **Mac**: デスクトップに `ADV360PRO` がマウント
- **Linux**: `/media/USERNAME/ADV360PRO` にマウント

**1.4 ファームウェアのコピー**

```bash
# Linux/Mac の場合
cp firmware/202511060930-a1b2c3d-left-clique.uf2 /media/USERNAME/ADV360PRO/

# または単純にドラッグ&ドロップ
```

**1.5 自動再起動の確認**

ファイルコピーが完了すると、キーボードは自動的に再起動し、USBドライブは切断されます。

⚠️ **注意:** 一部のOSでは「ドライブが正しく取り出されませんでした」というエラーが表示されることがありますが、書き込みは正常に完了しています。

#### ステップ2: 両側の電源管理

**2.1 両側の電源をオフ**

1. 左側のUSBケーブルを抜く
2. 左側モジュールの電源スイッチをオフにする
3. 右側モジュールの電源スイッチをオフにする

**2.2 左側の電源をオン**

左側モジュール（セントラル）の電源スイッチをオンにします。

これにより、左側がBLEセントラルとして起動します。

#### ステップ3: 右側モジュールの書き込み

**3.1 右側をUSB接続**

右側キーボードモジュールをコンピュータにUSB接続し、電源をオンにします。

**3.2 ブートローダーモードに移行**

**方法A: キーボードショートカット**

1. `Mod` キーを押しながら保持
2. `macro3` キー（右上のキー - 通常は `=` または `-` の位置）を押す

**方法B: 物理リセットボタン**

左側と同様にリセットボタンを押します。

**3.3 ファームウェアのコピー**

```bash
cp firmware/202511060930-a1b2c3d-right-clique.uf2 /media/USERNAME/ADV360PRO/
```

**3.4 再起動の確認**

右側も自動的に再起動します。

#### ステップ4: 接続の確立

**4.1 右側のUSBを抜く**

右側のUSBケーブルを抜きます。

**4.2 右側の電源を入れ直す**

右側モジュールの電源スイッチを一度オフにし、再度オンにします。

**4.3 BLE接続の確認**

左右のモジュールが自動的にBluetooth接続されます。

接続確認方法：
- 右側のキーを押すと左側に伝達される
- レイヤーLEDが両側で同期する

#### ステップ5: ホスト接続

**USB接続の場合:**

左側モジュールをコンピュータにUSB接続します。

**Bluetooth接続の場合:**

1. 左側モジュールの電源をオンにする
2. コンピュータのBluetooth設定を開く
3. "Adv360 Pro" を検索してペアリング

既にペアリング済みの場合は自動接続されます。

---

## 動作確認

### 基本動作テスト

**1. キー入力テスト**

テキストエディタや以下のサイトで全キーをテスト：
- [Keyboard Tester](https://www.keyboardtester.com/)
- [Key-Test](https://en.key-test.ru/)

**2. 変換/無変換キーの確認**

日本語入力可能な環境（例: Windows の Microsoft IME、Mac の日本語入力）で：

**変更前の動作:**
- 左親指ブロックの3つ目のキー = 変換
- 右親指ブロックの1つ目のキー = 無変換

**変更後の期待動作:**
- 左親指ブロックの3つ目のキー = **無変換**
- 右親指ブロックの1つ目のキー = **変換**

**3. テスト手順**

```
1. 日本語入力モードをオンにする
2. ひらがなで何か入力（例: "へんかん"）
3. 変更した「変換キー」（右親指ブロック1番目）を押す
   → 変換候補が表示されることを確認
4. ESCで入力キャンセル
5. 再度ひらがなで入力（例: "むへんかん"）
6. 変更した「無変換キー」（左親指ブロック3番目）を押す
   → ひらがながアルファベットに変換されることを確認（"muhenkan"）
```

### レイヤー動作テスト

**1. レイヤー切り替え**

- `Mod` キー（親指ブロック下段）を押してModレイヤー（レイヤー3）に切り替え
- レイヤーLEDの色が緑色に変わることを確認

**2. Bluetooth機能**

Modレイヤーで：
- `1`, `2`, `3`, `4`, `5` キー = BLEプロファイル1-5に切り替え
- BLEペアリングと接続を確認

**3. バージョン確認**

Modレイヤーで `V` キーを押すと、バージョン情報が出力されます：

```
20251106-V3.0-a1b2c3d-CLIQUE
```

形式: `{ビルド日時}-{ブランチ}-{コミットハッシュ}-{モード}`

### バックライト・RGB テスト

**1. バックライト**

Modレイヤーで：
- `Up Arrow` = 輝度アップ
- `Down Arrow` = 輝度ダウン
- `Enter` = オン/オフ切り替え

**2. RGB アンダーグロー**

Modレイヤーで：
- `Space` = RGB オン/オフ切り替え
- レイヤー切り替え時に色が変わることを確認

---

## トラブルシューティング

### ビルドエラー

#### エラー: "west: command not found"

**原因:** Westビルドシステムが正しくインストールされていない。

**解決策:**

```bash
make clean
make
```

Dockerイメージを再構築することで解決します。

#### エラー: "Permission denied" (Linux)

**原因:** Dockerデーモンへのアクセス権限がない。

**解決策:**

```bash
# ユーザーをdockerグループに追加
sudo usermod -aG docker $USER

# ログアウトして再ログイン、または
newgrp docker

# 再度ビルド
make
```

#### エラー: ビルドは成功するがファイルが見つからない

**原因:** `firmware/` ディレクトリが存在しない、または権限問題。

**解決策:**

```bash
# firmware ディレクトリを確認
ls -la firmware/

# ディレクトリがない場合は作成
mkdir -p firmware

# 再ビルド
make
```

#### エラー: "west build failed" (Mac Apple Silicon)

**原因:** Colimaがx86_64アーキテクチャで起動していない。

**解決策:**

```bash
# Colimaを停止
colima stop

# 正しいアーキテクチャで再起動
colima start --arch x86_64

# 再ビルド
make clean
make
```

### インストールエラー

#### エラー: ブートローダーモードに入れない

**原因:** キー組み合わせが正しくない、またはファームウェアが壊れている。

**解決策:**

1. **物理リセットボタンを使用する**
   - キーボード底面のリセットボタンを探す
   - 細いピンや爪楊枝で押す

2. **電源リセット**
   - 電源をオフにして10秒待つ
   - 再度電源をオンにしてリセットボタンを押す

#### エラー: USBドライブとして認識されない

**原因:** USBケーブルの問題、またはUSBポートの問題。

**解決策:**

1. **別のUSBケーブルを試す**
   - データ転送対応のケーブルを使用（充電専用ケーブルは不可）

2. **別のUSBポートを試す**
   - USB 2.0ポートを優先的に使用

3. **コンピュータを再起動**

#### エラー: ファームウェアコピー後にエラーメッセージ

**症状:** "ドライブが正しく取り出されませんでした" 等のエラー。

**解決策:**

これは**正常な動作**です。ファームウェア書き込み後、キーボードは自動的に再起動するため、OSがドライブの切断を検出します。エラーメッセージは無視して問題ありません。

#### エラー: 左右の接続が確立しない

**原因:** ファームウェアバージョンの不一致、またはBLE接続の問題。

**解決策:**

1. **両側のファームウェアを確認**
   - 必ず同じビルドの left.uf2 と right.uf2 を使用
   - タイムスタンプとコミットハッシュが一致することを確認

2. **設定リセット**
   ```bash
   # settings-reset.uf2 を両側に書き込む
   cp settings-reset.uf2 /media/USERNAME/ADV360PRO/
   ```

3. **手順を最初からやり直す**
   - 両側の電源をオフ
   - 左側から順番に書き込む

### 動作エラー

#### 問題: キーが正しく入れ替わっていない

**原因:** 編集ミス、またはZMK Studioが有効になっている。

**解決策:**

1. **ZMK Studioの設定確認**
   - 以前にZMK Studioで編集した場合、`.keymap` の変更は無視される
   - Modレイヤーで `studio_unlock` を押す
   - ZMK Studioで「Restore Stock Settings」を実行
   - ファームウェアを再書き込み

2. **キーマップファイルの再確認**
   ```bash
   # config/adv360.keymap の37行目を確認
   grep "INT4\|INT5" config/adv360.keymap
   ```

   正しい出力例（入れ替え後）:
   ```
   ... &kp INT5 ... &kp INT4 ...
   ```

3. **再ビルドと再インストール**
   ```bash
   make clean
   make
   # ファームウェアを再度インストール
   ```

#### 問題: 他のキーが動作しない

**原因:** キーマップ編集時のシンタックスエラー。

**解決策:**

1. **元のファイルと比較**
   ```bash
   git diff config/adv360.keymap
   ```

2. **変更を元に戻す**
   ```bash
   git checkout config/adv360.keymap
   ```

3. **慎重に再編集**
   - スペース、カンマ、セミコロンの位置に注意
   - バインディング全体の構造を崩さないように

#### 問題: ビルドは成功するが起動しない

**原因:** 重大なシンタックスエラー、またはメモリ不足。

**解決策:**

1. **既知の良好なファームウェアに戻す**
   ```bash
   git checkout main
   make clean
   make
   ```

2. **ファームウェアを書き込む**

3. **変更を少しずつ適用**
   - 一度に1つの変更を行う
   - ビルドとテストを繰り返す

### その他の問題

#### 問題: 複数のキーを変更したい

**解決策:**

同じ手順で複数のキーを編集できます。

**例: INT3（\|キー）もEnterキーと入れ替える**

編集箇所（37行目）:

変更前:
```c
&mo 2  &kp ESC  &kp INT3  &kp LEFT  &kp RIGHT  ...  &kp INT5  &kp ENTER  &kp SPACE  ...
```

変更後:
```c
&mo 2  &kp ESC  &kp ENTER  &kp LEFT  &kp RIGHT  ...  &kp INT5  &kp INT3  &kp SPACE  ...
```

#### 問題: レイヤーごとに異なる配置にしたい

**解決策:**

各レイヤーは独立して編集可能です。

**例: Fnレイヤー（レイヤー2）で異なる配置**

Fnレイヤー（61行目）:
```c
&trans  &trans  &trans  &trans  &trans                    &trans  &trans  &trans                  &trans  &trans  &trans          &trans  &trans   &trans   &trans   &trans
```

`&trans` は「下のレイヤーと同じキー」を意味します。変更する場合は `&kp` に置き換えます。

---

## まとめ

### 変更・ビルド・インストールの全体フロー

```
1. キーマップファイル編集
   ↓
2. 変更のコミット（推奨）
   ↓
3. ローカルビルド or GitHub Actions
   ↓
4. UF2ファイル取得
   ↓
5. 左側モジュールに書き込み
   ↓
6. 両側の電源管理
   ↓
7. 右側モジュールに書き込み
   ↓
8. 動作確認
```

### 推奨ワークフロー

**開発サイクル:**

1. **ブランチ作成**
   ```bash
   git checkout -b feature/custom-keymap
   ```

2. **編集・ビルド・テスト**
   ```bash
   # 編集
   vim config/adv360.keymap

   # ビルド
   make

   # インストール・テスト
   # （物理キーボードで確認）
   ```

3. **問題がなければコミット**
   ```bash
   git add config/adv360.keymap
   git commit -m "Custom keymap: swap INT4 and INT5"
   ```

4. **mainブランチにマージ**
   ```bash
   git checkout main
   git merge feature/custom-keymap
   git push origin main
   ```

### 参考リソース

- **ZMKドキュメント**: https://zmk.dev/docs
- **キーコード一覧**: https://zmk.dev/docs/keymaps/list-of-keycodes
- **ZMK Studio**: https://zmk.dev/docs/features/studio
- **Kinesisサポート**: https://kinesis-ergo.com/support/kb360pro/
- **本プロジェクトのアーキテクチャドキュメント**: `docs/architecture.md`

### よくある質問

**Q: ZMK Studioと手動編集、どちらを使うべきですか？**

A: 長期的なカスタマイズには手動編集（`.keymap`ファイル）を推奨します。バージョン管理が容易で、複数の変更を一度に管理できます。一時的な試行にはZMK Studioが便利です。

**Q: 左側だけビルドして右側は古いままでも大丈夫ですか？**

A: 推奨しません。左右のファームウェアバージョンが異なると、BLE接続の問題やキー入力の不具合が発生する可能性があります。必ず両側を同じビルドで更新してください。

**Q: ビルドに失敗した場合、キーボードは壊れますか？**

A: いいえ。ビルドはコンピュータ上で行われるため、キーボード本体には影響しません。ビルドが失敗した場合は、エラーメッセージを確認して修正してください。

**Q: ファームウェア書き込みに失敗した場合、キーボードは使えなくなりますか？**

A: いいえ。書き込み中に問題が発生しても、再度ブートローダーモードに入って正しいファームウェアを書き込めば復旧できます。最悪の場合、物理リセットボタンを使用できます。

**Q: GitHub Actionsでビルドしたファームウェアとローカルビルドに違いはありますか？**

A: 基本的に同じですが、ビルド環境が異なるため、タイムスタンプやコミットハッシュが異なる場合があります。機能的には同等です。

---

**ドキュメントバージョン**: 1.0
**最終更新**: 2025年11月6日
**対象ファームウェア**: V3.0 系列 (adv360-z3.5-2)
