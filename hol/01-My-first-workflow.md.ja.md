# 🔨ハンズオン：初めてのワークフロー

このハンズオンでは、初めての GitHub Actions ワークフローを作成し、Actions を使ってソフトウェア開発ライフサイクルのタスクを自動化する方法を学びます。より詳しい情報が必要な場合は、GitHub Docs の [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions) ページを参照してください。幸運を祈ります！👍

このハンズオンラボは以下のステップで構成されています：
- [リポジトリの作成](#creating-a-repository)
- [ワークフローの作成](#creating-the-workflow)
- [ワークフローの結果を見る](#viewing-your-workflow-results)

## リポジトリの作成

このリポジトリの[Code](/../../)タブに移動し、`Use this template`をクリックします：

<img width="400" alt="2022-09-18_11-24-58" src="https://user-images.githubusercontent.com/5276337/190895393-6fa0fad9-e05c-4fea-8126-a291b087d663.png">

GitHub ユーザーをオーナーとして選択し、リポジトリに名前をつけます。Actions の利用時間 (分数) を無制限にするために、リポジトリは公開のままにしておきます：

<img width="400" alt="2022-09-18_11-25-57" src="https://user-images.githubusercontent.com/5276337/190895398-751a1ec9-c1cf-497f-beb7-a6b53d4d911e.png">

新しいリポジトリで作業を続けます。

## ワークフローの作成

**Actions** | [New Workflow](/../../actions/new) に移動し、[set up a workflow yourself](/../../new/main?filename=.github%2Fworkflows%2Fmain.yml&workflow_template=blank) をクリックします。

1. `.github/workflows` ディレクトリの `main.yml` ファイルを `github-actions-demo.yml` にリネームします。
<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174096754-4e2d7219-9caf-42e8-bfd9-c190762886d3.png">

2.テンプレートの内容を削除します - (演習として、ゼロからワークフローを作成するため)
3.<kbd>Ctrl</kbd>+<kbd>Space</kbd>をクリックし、最初の要素として name を選択します：

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174097468-8be92e37-7948-4895-b5ed-20a22c5773bc.png">

4.ワークフロー名を `GitHub Actions Demo` に設定します：

```YAML
name: GitHub Actions Demo
```

5.<kbd>Ctrl</kbd>+<kbd>Space</kbd> とドキュメントを参考に、ワークフローにトリガーを追加します。ワークフローにトリガーを追加すします：
  - `main` ブランチへのプッシュがあったとき。ただし、`.github` フォルダのファイルが変更されたときでは除外。
  - main`ブランチをベースブランチとしたプルリクエストがあるとき
  - 毎週日曜日 6:15 UTC
  - 手動で

<details>
  <summary>解決方法</summary>

```YAML
on:
  push:
    branches: [ main ]
    paths-ignore: [.github/**]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '15 6 * * 0'
  workflow_dispatch:
```

</details>


6.GitHub hosted runners上の最新のUbuntuイメージで実行するジョブ`Build`を作成する。[仮想環境](https://github.com/actions/virtual-environments/) のドキュメントをチェックして、どのラベルを使うか、どのバージョンかを確認してください。ジョブは以下のことを行う必要があります：
  - ワークフローのトリガーとなったイベントの名前を出力する
  - リポジトリが現在参照しているブランチの名前を出力する
  - リポジトリ内のすべてのファイルをリストアップする

<details>
  <summary>解決方法</summary>

```YAML
jobs:
  Build:
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "🎉 The job was triggered by event: ${{ github.event_name }}"
          echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ."

      - uses: actions/checkout@v3.3.0

      - name: List files in the repository
        run: |
          echo "The repository ${{ github.repository }} contains the following files:"
          tree
```

</details>

7.ワークフローファイルをコミットし、ワークフローを手動で起動する。パスフィルターが機能していれば、自動的に実行されることはありません。[Action](/../../Actions) に移動し、[GitHub Actions Demo](/../../actions/workflows/github-actions-demo.yml) を選択して `Run workflow` を実行します：

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174105162-19f33fd1-8533-4860-9279-88fabec84451.png">


## ワークフローの結果を見る

1.ワークフローの実行をクリックします。：

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174105747-0e205e0d-37cc-464c-905b-5b29be74fc75.png">

2.ジョブの「ビルド」をクリックします：

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174105990-a1c204c6-fb7d-44a4-9343-6982899edb25.png">

3.`Set up job`を展開し、行番号のログを記録します（行番号はリンクです）。仮想環境と付属ソフトウェアの情報を確認します：

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174106759-c2a8f933-74cf-42a4-899b-b29dc67eccd7.png">

4.ジョブを展開し、出力が正しいことを確認します。:

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174107136-af9187c1-dbee-4109-9ddc-f2abd4830282.png">

5.README.md](/README.md)ファイルを修正して、他のトリガーを確認します：
  - トリガーをビルドする (`push`)
  - 修正し、`[skip ci]` を追加する（ワークフローをトリガーしない）：
  <img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174110845-93d4a38a-9c8a-4336-9b6a-9089ea9a1cfd.png">

  - プルリクエストの修正と作成 (トリガー: `pull_request`)

## まとめ

このハンズオンでは、トリガー、ジョブ、ステップ、式を使って最初のワークフローを作成する方法を学びました。次に、[最初の GitHub アクション](02-My-first-action.md) を書きます。

