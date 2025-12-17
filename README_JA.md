
# Kinesis Advantage 360 Pro ZMK設定

## 日本語キーボードレイアウト

本ブランチは日本語用キーボードレイアウトです。

### 日本語マニュアル

Advantage 360 Proの[日本語ユーザーガイド](https://manuals.plus/ja/kinesis/kb360-pro-advantage360-pro-keyboard-manual)
Advantage 360 Proの[旧日本語マニュアル](https://manuals.plus/ja/kinesis/kb360-pro-advantage360-professional-zmk-keyboard-manual)

## キーマップの変更

[ZMKのドキュメント](https://zmk.dev/docs)には、基本的な機能から高度な機能までが網羅されており、キーコードのOS互換性に関する表も掲載されています。RGBアンダーグロー、バックライト、および電源管理のセクションは、Advantage 360 ProのカスタムZMKフォークには関連しないことにご注意ください。詳細については、[こちらの注記](#注記)を参照してください。

キーマップを編集するためのWebベースのGUIは[ZMK Studio](https://zmk.dev/docs/features/studio) を使用します。このリポジトリは、他の特定のWebベースZMKキーマップエディタとも互換性がありますが、それらのエディタには360 Proに実装されていないキーコードやビヘイビアが含まれている可能性があり、異常な動作やビルドの失敗を引き起こす可能性があります。さらに、他のキーマップエディタで行った変更は、KinesisのGUIに戻して使用する際に互換性がない場合があります。

ZMK Studioのドキュメントに記載されているようにZMK Studioでキーマップを編集した場合は, `.keymap` ファイルの変更は無効となります。元に戻すには「Restore Stock Settigs」アクションを行う必要があります。詳細は[ZMK Studioドキュメント](https://zmk.dev/docs/features/studio)をご覧ください。

特定のZMK機能（例：コンボ）を使用するには、マトリックス内の正確なキー位置を知る必要があります。キー位置は、画像とテキスト形式の両方で[こちら](assets/key-positions.md)にあります。

### キーマップ編集

#### VSCodeの準備

VSCodeを使用する際にjsonを自動でフォーマットしないようにする必要があります。
ワークスペースの.vscode/settings.jsonに以下の設定を追加してください：

```json
{
    "editor.formatOnPaste": false,
    "editor.formatOnSave": false,
    "editor.formatOnType": false,
    "[json]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnPaste": false,
        "editor.formatOnSave": false,
        "editor.formatOnType": false
    }
}
```

### 日本語用のキーマップ編集

JIS109キーボードなどinternationalキーボード用に[国際的に割り当てられているキー](https://zmk.dev/docs/keymaps/list-of-keycodes)は，ZMKでは以下のように定義されています。
Advantage 360の日本国内モデルではこれらのインターナショナルキーが最初から設定されています。

#### INT1

Advantage 360には標準で搭載されています。右手親指ブロックにあります。

```c
_\
```

#### INT2

Advantage 360には標準で搭載されています。左手親指ブロックにあります。

```c
カタカナ/ひらがな/ローマ字
```

#### INT3

Advantage 360には標準で搭載されています。左手薬指の位置にあります。

```c
\|
```

#### INT4

Advantage 360には標準で搭載されています。左手親指ブロックにあります。

```c
変換
```

#### INT5

Advantage 360には標準で搭載されています。右手親指ブロックにあります。

```c
無変換
```

#### LANG5

Advantage 360には標準で搭載されています。左手小指ブロックにあります。通常の日本語配列キーボードと同じ位置です。

```c
半角/全角
```

## GitHub Actionsでのファームウェアビルド

### セットアップ(Github Actions)

1. このリポジトリをフォークします。
2. フォークしたリポジトリでGitHub Actionsを有効にします。

### ファームウェアのビルド(Github Actions)

1. コミットをプッシュしてビルドをトリガーします。
2. ビルド成果物（artifact）をダウンロードします。

## ローカルコンテナでのファームウェアビルド

### セットアップ(Docker/Podman等のローカルコンテナ)

#### ソフトウェア

* PodmanまたはDockerのいずれかが必要です。両方がインストールされている場合はPodmanが選択されます。
* Makeも必要です。

#### Windows固有

* Windowsでコンパイルする場合は、WSL2とDockerを使用してください。[Dockerセットアップガイド](https://docs.docker.com/desktop/windows/wsl/)。
* WSL2インスタンス内で`sudo apt-get install make`を使用してmakeをインストールします。
* リポジトリはWSL2インスタンスに直接クローンするか、WSLがデフォルトで提供するC:ドライブのマウントポイント（`/mnt/c/path-to-repo`）経由でアクセスできます。

#### macOS固有

macOSでは[brew](https://brew.sh)を使用して必要なコンポーネントをインストールできます。

* docker
* Dockerエンジンとして[colima](https://github.com/abiosoft/colima)を使用できます。

```shell
brew install docker colima
colima start
```

##### 注：Apple Silicon（ARMベース）システムでは、使用するコンテナに適したアーキテクチャでcolimaを起動する必要があります

```sh
colima start --arch x86_64
```

#### Ubuntu/Debian固有

```shell
sudo apt-get install docker make
```

### ファームウェアのビルド(Docker/Podman等のローカルコンテナ)

1. `make`を実行して両方のキーボードのファームウェアをビルドするか、`make left`を実行して左側のみのファームウェアをビルドします。
2. `firmware`ディレクトリで最新のファームウェアビルドを確認します。ファイル名の最初の部分はファームウェアがビルドされたタイムスタンプです。

#### ファームウェアビルド例

Ubuntuでは以下のようにビルドを行います。(Docker CLI, makeインストール済みと仮定)

```sh
user: ~/Adv360-Pro-ZMK-Japanese$ make

... build logs ...

git checkout config/version.dtsi
Updated 1 path from the index
user: ~/Adv-Pro-ZMK-Japanese$ cd firmware
user: ~/Adv-Pro-ZMK-Japanese/firmware$ ls -al
total 896
drwxrwxr-x 2 user user   4096 Jun  7 17:09 .
drwxrwxr-x 9 user user   4096 Jun  7 17:08 ..
-rw-rw-r-- 1 user user      0 Jun  7 15:19 .gitkeep
-rw-r--r-- 1 root root 541696 Jun  7 17:09 202506070805-9b337cd-left-clique.uf2
-rw-r--r-- 1 root root 362496 Jun  7 17:09 202506070805-9b337cd-right-clique.uf2
```

出力されるファイル名は自動生成されます。
ファイル名の命名規則は次のとおりです。

```sh
LEFT : {YYYYMMDDHHMM}-{github_commit_id}-left-clique.uf2
RIGHT: {YYYYMMDDHHMM}-{github_commit_id}-right-clique.uf2

{YYYYMMDDHHMM} = 202506070805
{github_commit_id}= 9b337cd
```

### クリーンアップ

ビルドされたDockerコンテナとコンパイル済みのファームウェアファイルは`make clean`で削除できます。これは、フォークをV2.0からV3.0に更新してビルドエラーが発生した場合に必要になることがあります。

Dockerコンテナの作成には時間がかかります。そのため、`make clean_firmware`を使用してDockerコンテナを削除せずにファームウェアのみをクリーンアップできます。同様に、`make clean_image`を使用してコンパイル済みのファームウェアファイルを削除せずにDockerコンテナを削除できます。

## ファームウェアの書き込み

[クイックスタートガイド](https://kinesis-ergo.com/wp-content/uploads/Advantage360-Professional-QSG-v8-25-22.pdf)の8ページにあるプログラミング手順に従ってファームウェアを書き込みます。

### 概要

1. GitHubのビルドジョブからダウンロードしたアーカイブからファームウェアを解凍します（クラウドビルダーを使用している場合）。または、`firmware`フォルダから取り出します（ローカルでビルドしている場合）。
1. 左側のキーボードをUSBに接続します。
1. `Mod`+`macro1`を押して左側をブートローダーモードにします。コンピュータにUSBドライブとして認識されるはずです。
1. `left.uf2`をUSBドライブにコピーすると、ドライブは自動的に切断されます。
1. 両方のキーボードの電源を切ります（USBを抜き、スイッチがオフになっていることを確認します）。
1. 左側のキーボードのスイッチをオンにします。
1. 右側のキーボードをUSBに接続して電源を入れます。
1. `Mod`+`macro3`を押して右側をブートローダーモードにし、USBドライブとしてマウントします。
1. `right.uf2`をマウントされたドライブにコピーします。
1. 右側のキーボードのUSBを抜き、再度電源を入れます。
1. お楽しみください！

> 注：両方のキーボードには物理的なリセットボタンもあり、これを使ってブートローダーモードの開始と終了ができます。ボタンの位置は[ユーザーマニュアル](https://kinesis-ergo.com/wp-content/uploads/Advantage360-ZMK-KB360-PRO-Users-Manual-v3-10-23.pdf)のセクション2.7（9ページ）に、使用方法はセクション5.9（14ページ）に記載されています。

> 注：一部のオペレーティングシステムでは、settings-resetファイルを書き込んだ後、ドライブが正常に取り出されたと扱われなかったり、誤ったエラーが表示されたりすることがありますが、これは書き込みプロセスが失敗したことを意味するものではありません。

### V2からV3へのアップグレード

リポジトリをV3.0に更新する際にgitのコンフリクトが発生した場合は、[こちら](UPGRADE.md)の解決手順に従ってください。

V2.0ベースのファームウェアからV3.0ベースのファームウェアへの更新は、かなり複雑なプロセスになる可能性があります。主要なファームウェアリビジョンごとにリセットファイルがあり、更新プロセスに関するドキュメントは[こちら](https://kinesis-ergo.com/support/kb360pro/#firmware-updates)で入手できます。

## バージョン管理

2023年11月15日より、Advantage 360 Proはコンパイル日時、ブランチ、Gitコミットハッシュを自動的に記録し、`Mod`+`V`でアクセスできるマクロに保存するようになりました。これにより、`YYYYMMDD-XXXX-YYYYYY`という文字列がタイプされます。ここで`XXXX`はGitブランチの最初の4文字、`YYYYYY`はGitコミットハッシュです。これに加えて、GitHub Actionsでコンパイルされたビルドにはタイムスタンプが付与され、ファイル名にもコミットハッシュが記録されるようになりました。

## Nキーロールオーバー

デフォルトではこのキーボードはNKRO（Nキーロールオーバー）が有効ですが、互換性の理由から、より広い範囲は有効になっていません。F13-F24キーやINTL1-9キーをNKRO有効で使いたい場合は、[adv360_left_defconfig](/config/boards/arm/adv360/adv360_left_defconfig#L65)内の`CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=n`を`CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=y`に変更してください。

## バッテリーレポート

デフォルトでは、BLE経由でのバッテリーレベルの報告は無効になっています。これは、一部のコンピュータが繰り返し意図せずスリープから復帰する原因となるためです。この機能を有効にしたい場合は、[adv360_left_defconfig](/config/boards/arm/adv360/adv360_left_defconfig#L58)内の`CONFIG_BT_BAS=n`を`CONFIG_BT_BAS=y`に変更してください。

## 修飾キーインジケーターの色

CAPS/NUM/SCROLL LOCKインジケーターLEDの色は、16進数のRGBカラーコードを指定することで設定できます。例えば、`CONFIG_ZMK_RGB_UNDERGLOW_MOD_COLOR=0xFF0000`とすると、インジケーターの色が赤になります。両方のモジュールでインジケーターの色を設定するには、[adv360_left_defconfig](/config/boards/arm/adv360/adv360_left_defconfig)と[adv360_right_defconfig](/config/boards/arm/adv360/adv360_right_defconfig)の両方が更新されていることを確認してください。

## レイヤーカラー

ZMKは合計32レイヤーをサポートしており、現在アクティブな最も高いレイヤーが左右両モジュールのレイヤーLEDで表示されます。すべての可能な色を以下に示します。最初の8レイヤーでは、両モジュールで同じ色が表示されます。その後は、右モジュールの色だけが循環し、「ロールオーバー」すると左モジュールの色も変わります（これが繰り返されます）。混乱を避けるため、黒/オフのLEDカラーはレイヤー0でのみ使用されます。

| レイヤー # | 左/右 | レイヤー # | 左/右 | レイヤー # | 左/右 | レイヤー # | 左/右 |
| ---: | :---: | ---: | :---: | ---: | :---: | ---: | :---: |
| 0 | <img valign='middle' src='assets/swatches/000000.svg'/> <img valign='middle' src='assets/swatches/000000.svg'/> | 8 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/0000FF.svg'/> | 16 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/FF0000.svg'/> | 24 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/00FFFF.svg'/> |
| 1 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/FFFFFF.svg'/> | 9 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/00FF00.svg'/> | 17 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/FF00FF.svg'/> | 25 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/FFFF00.svg'/> |
| 2 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/0000FF.svg'/> | 10 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/FF0000.svg'/> | 18 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/00FFFF.svg'/> | 26 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/FFFFFF.svg'/> |
| 3 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/00FF00.svg'/> | 11 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/FF00FF.svg'/> | 19 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/FFFF00.svg'/> | 27 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/0000FF.svg'/> |
| 4 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/FF0000.svg'/> | 12 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/00FFFF.svg'/> | 20 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/FFFFFF.svg'/> | 28 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/00FF00.svg'/> |
| 5 | <img valign='middle' src='assets/swatches/FF00FF.svg'/> <img valign='middle' src='assets/swatches/FF00FF.svg'/> | 13 | <img valign='middle' src='assets/swatches/FFFFFF.svg'/> <img valign='middle' src='assets/swatches/FFFF00.svg'/> | 21 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/0000FF.svg'/> | 29 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/FF00FF.svg'/> |
| 6 | <img valign='middle' src='assets/swatches/00FFFF.svg'/> <img valign='middle' src='assets/swatches/00FFFF.svg'/> | 14 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/FFFFFF.svg'/> | 22 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/FF0000.svg'/> | 30 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/00FFFF.svg'/> |
| 7 | <img valign='middle' src'assets/swatches/FFFF00.svg'/> <img valign='middle' src='assets/swatches/FFFF00.svg'/> | 15 | <img valign='middle' src='assets/swatches/0000FF.svg'/> <img valign='middle' src='assets/swatches/00FF00.svg'/> | 23 | <img valign='middle' src='assets/swatches/00FF00.svg'/> <img valign='middle' src='assets/swatches/FF00FF.svg'/> | 31 | <img valign='middle' src='assets/swatches/FF0000.svg'/> <img valign='middle' src='assets/swatches/FFFF00.svg'/> |

## 変更履歴

設定リポジトリと、それがビルドする基盤となるZMKフォークの両方の変更履歴は、[こちら](CHANGELOG.md)で確認できます。

## ベータテスト

Advantage 360 Proは常にアップデートと改良が行われています。ベータテストに参加したい場合は、[ZMKのこのガイド](https://zmk.dev/docs/features/beta-testing#testing-features)に従って、設定リポジトリがどのブランチを指すかを変更してください。言及されている`west.yml`ファイルは`config/`にあります。[このリンク](config/west.yml)からファイルに移動できます。通常は、`revision:`をベータブランチ名に合わせて変更するだけです。現在、テスト可能なベータブランチはありません。

ベータブランチに関するフィードバックは、この設定リポジトリではなく、ベースのZMKリポジトリにGitHub issueとして提出してください。

メジャーアップデートがあった場合、ベータブランチは現在のメインラインバージョンの設定リポジトリと互換性がない可能性があります。その場合は、アップデート方法とともにここに詳細が記載されます。

## 注記

デフォルトでは、この設定リポジトリは、[ベースのZMK](https://github.com/zmkfirmware/zmk)にAdvantage 360 Pro固有の機能や変更を加えた[カスタムバージョンのZMK](https://github.com/ReFil/zmk/tree/adv360-z3.5)を参照しています。Kinesisのフォークは定期的に更新され、ベースZMKからの最新のアップデートや変更が取り込まれますが、常に完全に最新であるとは限りません。新しいキーコードなどの一部の機能は、ベースZMKに実装された後、すぐに360 Proで利用可能になるわけではありません。

Advantage 360 ProはベースZMKと互換性がありますが（マージのためのプルリクエストは[こちら](https://github.com/zmkfirmware/zmk/pull/1454)で確認でき、実装方法もご覧いただけます）、一部の高度な機能（インジケーターRGB LEDなど）は動作しません。また、KinesisはベースZMKの使用に関するカスタマーサービスを提供できません。同様に、ZMKコミュニティはKinesisのキーマップエディタやKinesisのカスタムフォークの使用に関するサポートを提供できません。

## その他のサポート

さらなるサポートリソースはKinesis.comでご覧いただけます：

* <https://kinesis-ergo.com/support/kb360pro/#firmware-updates>
* <https://kinesis-ergo.com/support/kb360pro/#manuals>

ハードウェアの問題が発生した場合は、このリポジトリにGitHub issueを立てるのではなく、Kinesisに直接サポートチケットを申請する必要がある場合があります。

* <https://kinesis-ergo.com/support/kb360pro/#ticket>
