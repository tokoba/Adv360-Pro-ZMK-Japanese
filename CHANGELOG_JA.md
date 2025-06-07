
# 変更履歴

ここでは、設定リポジトリと、そのリポジトリがビルドするベースZMKの両方に対するすべての注目すべき変更点とコミットを記載します。

このファームウェアをより良くするためにissueやプルリクエストを提出してくださった皆様に心から感謝申し上げます！

## 設定リポジトリ

2025/2/9 - ローカルビルドプロセスにマイナーな変更を加え、ローカルビルド体験を向上。キーマップファイルからプリプロセッサコマンドを削除 [#643](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/643)

2025/2/6 - ベースZMKを更新。Kinesis Cliqueのサポートを追加。RGBパラメータを更新し、ポインティングサポートを有効化 [#630](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/630)

2024/11/27 - 変更履歴内の誤ったPRリンクを修正 [#590](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/590)

2024/5/29 - 日本語デフォルトレイアウト

2024/4/16 - 変更履歴の日付を修正 [#448](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/448)

2024/4/15 - ドキュメントから冗長な情報を削除 [#445](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/445)

2024/4/7 - 新しいレイヤーカラーと設定可能な修飾キーインジケーターカラーに関するドキュメントを追加 [#431](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/431)

2024/4/5 - ベースZMKを更新。非推奨の属性を削除。書き込み用cmakeを変更 [#426](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/426)

2024/3/14 - macOSでのビルドを妨げるMakefileのエラーを修正 [#409](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/409)

2024/2/18 - ローカルビルド実行時にビルド後に`version.dtsi`がリセットされる問題を修正 [#385](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/385)

2024/2/12 - GitHubビルドワークフローを最新のアクションを使用するように更新 [#376](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/376)

2024/2/2 - Makefileの機能強化（左側ファームウェアのみのビルド、ファームウェアとDockerのクリーンターゲットを分離、ビルド後に`version.dtsi`をリセット）[#363](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/363)

2024/1/16 - Makefileを変更し、WSL2との互換性を修正 [#335](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/335)

2024/1/14 - ベースZMKを更新。KConfig属性をサポートするように変更。安定性向上のため実験的なBLE機能を有効化 [#326](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/326)

2023/12/27 - macOSでの体験を向上させるため、バージョン管理スクリプトで使用する文字を変更 [#303](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/303)

2022/12/15 - 新しいバージョン管理マクロを反映するように`keymap.json`を更新 [#300](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/300)

2023/12/15 - PRテンプレートを追加 [#293](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/293)

2023/12/6 - バージョン管理スクリプトが`$PATH`からbashを使用するように更新 [#287](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/287)

2023/12/5 - ユーザーフィードバックに基づきBluetooth設定を更新 [#289](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/289)

2023/11/16 - ベースZMKの更新内容を変更履歴に追加 [#268](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/268)

2023/11/15 - 新しい自動バージョン管理システムを追加し、ドキュメント化 [#267](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/267)

2023/11/7 - 拡張NKRO範囲のための新しい設定オプションを追加し、ドキュメント化 [#264](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/264)

2023/11/2 - 新しい設定オプションに関する注記をドキュメントに更新。その他、フィードバックに基づく雑多な改善 [#260](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/260)

2023/10/30 - [settings_reset.uf2](/settings-reset.uf2)ファイルを更新し、新しいアップデートでのリセット挙動を改善

2023/10/20 - 競合のためBLEプライバシーを無効化。BLEバッテリーレポートを無効化。マイナーアップデートを含む新しいZMKブランチを指すように変更。マイナーアップデートのため、更新前に各サイドに[settings_reset.uf2](/settings-reset.uf2)ファイルを書き込む必要があることに注意 [#248](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/248) (adv360-z3.2-2)

2023/10/9 - ドキュメントをさらに洗練。ベータテストに関するセクションを追加。BLEプライバシーをドキュメント化 [#241](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/241)

2023/10/9 - shellcheckからの警告を修正するためビルドアクションを変更 [#242](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/242)

2023/9/7 - ドキュメントに変更履歴を追加。コンボ用のキー位置をドキュメント化し、その他雑多なドキュメントを改善 [#221](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/221) [#222](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/222)

2023/7/28 - READMEにアップデート後の接続問題を解決する方法に関するセクションを追加 [#197](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/197)

### 2023/7/6 - V3.0 - 上流PRに合わせ、ZMK pre-commit要件に準拠するためのボード定義の大規模リファクタリング。将来のgitコンフリクトを避けるためのキーマトリックスの最終変更。zephyr Pinctrl APIへの切り替え。settings-resetファイルを更新。ベースZMKリポジトリのzephyr 3.2ブランチへ切り替え。更新時のコンフリクト解決手順を追加 (adv360-z3.2)

2023/4/7 - READMEの改善。書き込み手順、GUIエディタへのリンクを追加し、フォーマットをクリーンアップ [#128](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/128)

2023/3/4 - マトリックスにどこにも接続されていない余分なキーを追加し、USB3.1ケーブル使用時の偽キープレス問題を修正 [#114](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/114) [#116](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/116)

2023/2/14 - 消費電力を改善するため、ZMKロギングをデフォルトで無効化 [#101](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/101)

2023/1/25 - OS-Xでローカルビルダー使用時に自動OS検出が正しくビルドされるように修正 [#91](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/91)

2023/1/16 - キーマップGUIファイルのフォーマットを変更 [#92](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/92)

2022/11/21 - 新しいMakefile構造を考慮してREADMEを書き直し [#57](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/57)

2022/11/18 - MakefileビルドシーケンスにSELinuxサポートを追加 [#58](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/58)

2022/11/14 - Makefileをクリーンアップし、クリーン時にDockerイメージを削除し、よりシームレスに実行されるように [#42](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/42)

2022/10/30 - `make clean`を改善し、ビルドファームウェアなしで実行してもエラーにならないように [#36](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/36)

2022/10/26 - Dockerの代わりにPodmanを通じたビルドをサポート [#10](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/10)

2022/10/23 - 非推奨のアクションを避けるためGitHub Actionsを更新 [#33](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/33)

2022/10/23 - Dockerイメージを毎回再利用するためのMakefileを追加 [#29](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/29)

2022/10/20 - セントラルからペリフェラルデータを完全に消去するようにsettings resetファイルを更新

2022/10/12 - V2.0アップデート後のローカルDockerビルドを修正 [#25](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/25)

2022/10/11 - BLE経由で製造者情報を設定 [#28](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/28)

2022/10/9 - キーマップのクリーンアップ [#24](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/)

2022/10/7 - 設定ファイルにUSB VID、PID、製造者情報を追加

2022/9/26 - V2.0アップデート後のエラーを避けるため、ローカルビルドスクリプトを修正

### 2022/9/17 - V2.0 - Zephyr 3をサポートするための変更 (adv360-z3)

2022/8/9 - GUIから余分なキーを削除 [#5](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/5)

2022/6/13 - デフォルトキーマップを変更、デフォルトマクロを追加

2022/5/23 - Dockerでのローカルビルドを追加、READMEを追加 [#4](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/4)

2022/5/6 - 信頼性向上のためLFCLK精度を変更 [#2](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/2)

### 2022/3/3 - V1.0 - 初期設定リポジトリリリース [#1](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/pull/1) (adv360-beta)

2022/3/1 - ライセンスの初期公開

## ベースZMK

これまでに360 Pro用として5つのZMKブランチが使用されてきました。ベータブランチは頻繁な変更や調整が行われるため、変更履歴には記載されていません。

| ブランチ | 開始日 | 終了日 | 設定ブランチ |
| -------- | ------- |-------|-----|
| [adv360-beta](https://github.com/ReFil/zmk/tree/adv360-beta) | 2022/3/1 | 2022/9/17 | V1.0 (削除済) |
| [adv360-z3](https://github.com/ReFil/zmk/tree/adv360-z3) | 2022/9/17 | 2023/7/6 | V2.0 (削除済) |
| [adv360-z3.2](https://github.com/ReFil/zmk/tree/adv360-z3.2) | 2023/7/6 | 2023/10/20 | [V3.0](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/tree/V3.0) (コミット 82494e7 まで) |
| [adv360-z3.2-2](https://github.com/ReFil/zmk/tree/adv360-z3.2-2) | 2023/10/20 | 2024/1/14 | [V3.0](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/tree/V3.0) (コミット 4a5003a まで) |
| [adv360-z3.2-3](https://github.com/ReFil/zmk/tree/adv360-z3.2-3) | 2024/1/14 | 2024/4/5 | [V3.0](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/tree/V3.0) (コミット 742d19e まで) |
| [adv360-z3.5](https://github.com/ReFil/zmk/tree/adv360-z3.5) | 2024/4/5 | 2025/2/6 | [V3.0](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/tree/V3.0) (コミット 8988c99 まで) |
| [adv360-z3.5-2](https://github.com/ReFil/zmk/tree/adv360-z3.5-2) | 2025/2/6 | 現在 | [V3.0](https://github.com/KinesisCorporation/Adv360-Pro-ZMK/tree/V3.0) (最新) |

### adv360-z3.5-2

2025/1/26 - cliqueに合わせてマウスキープレスのメタデータを変更

2025/1/25 - 新しいバッテリー表示ビヘイビアを追加

2025/1/22 - RGBエフェクトをペリフェラル側に送信

2025/1/14 - 外部電源の状態がフラッシュに保存されないように変更

2025/1/14 - RGBの「オン」状態をBluetooth接続経由で送信

2025/1/13 - マウスボタンのビヘイビアにメタデータを追加

2025/1/11 - RGBハンドラがフラッシュに保存しないように変更

2025/1/11 - リベース後のビルドを修正するための変更

2025/1/4 - 最新のZMKにリベース (コミット 3377ed02)

2024/11/21 - 接続ロックアップを修正するための追加チェックを追加

2024/11/21 - デフォルトのデバウンスを15msに変更

### adv360-z3.5

2024/4/15 - 利用可能なBTプロファイルカラーのリストを明確化 [#20](https://github.com/ReFil/zmk/pull/20)、プルリクエストのCIを修正 [#21](https://github.com/ReFil/zmk/pull/21) (コミット b0c91d3)

2024/4/10 - 全32レイヤーの色を追加 [#18](https://github.com/ReFil/zmk/pull/18)、修飾キーインジケーターの色を設定可能に [#19](https://github.com/ReFil/zmk/pull/19)、バッテリーレベル表示を修正 [#17](https://github.com/ReFil/zmk/pull/17) (コミット 2fcd15d)

2024/4/8 - CIをリファクタリングし、360 Pro専用にターゲット

2024/4/8 - CIの失敗を修正

2024/3/27 - ZMKイベントを新しいフォーマットに更新

2024/3/27 - 最新の上流ZMKからリベース (コミット 94c3b9a)

### adv360-z3.2-3

2023/1/8 - 最新の上流ZMKをマージ (コミット 7652fbeb)

2023/12/17 - 以前の拡張NKROレポートとの互換性を維持するためのKConfig行を追加

2023/12/17 - スプリット通信のための上流HIDインジケーターコードとのコンフリクトを修正

2023/12/17 - ライティングコードとの互換性を維持するため、HIDインジケーターLEDの定義を追加

2023/12/17 - 最新の上流ZMKをマージ (コミット 78fa1e77)

注：以前はこのブランチにカスタムで実装されていたいくつかの機能（BTバッテリーレポート無効化、拡張NKRO、HIDインジケーター）は、現在では上流にマージされています。

### adv360-z3.2-2

2023/11/16 - Bluetoothコードの競合状態を修正し、スプリット接続の問題を改善

2023/11/7 - 互換性のため、HIDの最大NKRO使用をコンフィグオプション（`CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT`）で設定可能に

2023/11/1 - より長いマクロシーケンスを実行できるように、ビヘイビアキューのサイズを増加

2023/11/1 - ライティングがバッテリーレポートモードで固まる状況を防ぐため、RGB初期化の順序を変更

2023/10/27 - NKRO有効時にF13-F24やその他の稀に使用されるキーコードを使用できるように、HIDの最大NKRO使用を変更

2023/10/18 - フラッシュの消耗を軽減するため、特定のRGB要素のフラッシュメモリへの保存を無効化

2023/10/18 - コード内でBLEバッテリーレポートを再有効化（現在は設定リポジトリで`CONFIG_BT_BAS` KConfigオプションを使用して無効化）

2023/10/18 - 最新の上流ZMKをマージ (コミット 7fe9ecd8)

### adv360-z3.2

2023/7/31 - 壊れたCIビルドを修正 [#6](https://github.com/ReFil/zmk/pull/6)

2023/5/30 - プルリクエストからの最新の変更に合わせ、RGBインジケーターコードを更新

2023/5/30 - 最新のHIDインジケータープルリクエストとその依存関係をマージ [#999](https://github.com/zmkfirmware/zmk/pull/999) [#1803](https://github.com/zmkfirmware/zmk/pull/1803)

2023/5/30 - 信頼性の問題によりBLEバッテリーレポートを無効化

2023/5/30 - zephyr 3.2でのコンパイルを可能にするための様々な修正

2023/5/29 - 最新の上流ZMKをマージ (コミット b276a3b)

### adv360-z3

2023/3/27 - RGBライティングの電源オン時の挙動を修正

2023/2/24 - BLEプロファイル5のインジケーターLEDを修正

2023/1/12 - 最新の上流ZMKをマージ (コミット a82a0ec)

2023/1/12 - pre-commitフォーマットを修正

2023/1/12 - ヘッダーで構造体を定義することにより、コンパイル時の警告を修正 [#4](https://github.com/ReFil/zmk/pull/4)

2022/11/25 - macOSでのHIDライトレポートを修正するため、追加のBLE特性を追加

2022/10/19 - 多くのBLEデバイスがある環境でのパフォーマンスを向上させるため、BLEホワイトリストスキャンを追加

2022/10/19 - 最新の上流ZMKをマージ (コミット c9eb631)

2022/10/19 - 初回電源オン時のライティングを修正

2022/10/2 - バッテリー寿命を改善するため、RGBとバックライトの輝度をスケーリングする機能を追加

2022/9/13 - ベースZMKの上にカスタムライティング機能を追加

2022/9/12 - HIDインジケータープルリクエスト[#999](https://github.com/zmkfirmware/zmk/pull/999)をベースZMKの上にマージ

2022/9/11 - ベースZMKから分岐 (コミット 6124d25)

### adv360-beta

このリポジトリは1年間非推奨となっており、ドキュメント化されません。より多くの機能と信頼性が向上しているV3.0へのアップグレードをお勧めします
