# チートシート

このドキュメントは、基本的な概念と用語の概要が記載されています。途中で用語に迷ったときの参考書として使用することができます。

## Github Actions 高階層コンポーネント図

<img width="671" alt="image" src="https://user-images.githubusercontent.com/10853833/216319332-3d5529e8-abfc-4dab-b9b7-e55c4cba99e5.png">

## YAML 関数

| 機能 | 説明 |
|----|----|
| success() | 前のステップがどれも失敗しなかったりキャンセルされなかった場合に true を返す。|
| always() | 前のステップがキャンセルされた場合でも true を返し、そのステップは常に実行される。|
| cancelled() | ワークフローがキャンセルされた場合のみ true を返す。|
| failure() | ジョブの前のステップが失敗した場合に true を返す。|
| contains(search、item) | search が item を含む場合に true を返す。|
| startsWith(search, item) | 検索がアイテムから始まる場合に true を返す。|
| endsWith(search, item) | 検索がアイテムで終わる場合に true を返す。|
| format('{0} {1}', item1, item2) | 文字列中のプレースホルダーを置き換える。|
| join(array, separator) | 配列内の全ての値が、指定されたセパレータを使って文字列に連結される。
| toJSON(value) | 値の JSON 表現を pretty-print で返す。|
| fromJSON(value) | 値の JSON オブジェクトまたは JSON データ型を返す。|

## ワークフロー コマンド

こちらも参照：
https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions

アクションはランナーマシンと通信し、環境変数の設定、他のアクションで使用される値の出力、出力ログへのデバッグメッセージの追加、その他のタスクを実行できます。  
ほとんどのワークフローコマンドは、特定のフォーマットで echo コマンドを使用します。

echo ワークフロー コマンドの例
```bash
echo "::workflow-command parameter1={data},parameter2={data}::{command value}"
```
ファイルに書き込むワークフロー コマンドの例
```bash
echo "SELECTED_COLOR=green" >> $GITHUB_OUTPUT
```
