# 🔨ハンズオン：初めてのアクション

このハンズオンラボでは、dockerアクションを作成し、パラメータを渡してワークフローに値を返す方法を学びます。そして、CIビルドを使ってローカルでアクションをテストする方法を学びます。

このハンズオンラボは以下のステップで構成されています：
- [アクションの作成](#creating-the-action)
- [アクションのテスト](#testing-the-action)
- オプション: [アクションのリリースと使用](#optional-release-and-use-the-action)

## アクションの作成

1.新しいファイル[`hello-world-docker-action/action.yml`](/../../new/main?filename=hello-world-docker-action%2Faction.yml) を作成します:
<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174234628-14f58066-3188-42a6-9204-99c577558c08.png">

2. `action.yml`ファイルに内容を追加する（[template](https://github.com/actions/hello-world-docker-action)と
  [help](https://github.com/actions/hello-world-docker-action)を参照) アクションに `Dockerfile` を実行させ、パラメータに
  パラメータ `who-to-greet` に既定値 `world` を渡します。また、現在の時刻を返す出力も返してください。

<details>
  <summary>解決方法</summary>

```YAML
name: 'Hello World Docker Action'
description: 'Say hello to a user or the world.'
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
    default: 'world'
outputs:
  time:
    description: 'The time we said hello.'
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

</details>


3.ファイルをコミットします (`[skip ci]`でまだビルドを実行しない)。
4. `hello-world-docker-action` フォルダ内に [`Dockerfile`](/../../new/main?filename=hello-world-docker-action%2FDockerfile) を作成します。コンテナは `FROM alpine:3.10` を継承し、ファイル `entrypoint.sh` をコピーして実行する。スクリプトは `RUN chmod +x entrypoint.sh` で実行可能にすることを忘れずに。

<details>
  <summary>解決方法</summary>

```dockerfile
FROM alpine:3.10

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

</details>

5.ファイルをコミットします（`[skip ci]`でビルドの実行をスキップします）。  
6.[`entrypoint.sh`](/../../new/main?filename=hello-world-docker-action%2Fentrypoint.sh) ファイルをフォルダ内に作成します。これはコンソールにメッセージを書き、出力パラメータを設定するシンプルな bash スクリプトです。

<details>
  <summary>解決方法</summary>

```dockerfile
FROM alpine:3.10

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

</details>

7.ファイルをコミットする(`[skip ci]`でまだビルドを実行しない)。

## アクションのテスト

1. アクションをテストするために、新しいワークフローファイル[`.github/workflows/hello-world-docker-ci.yml`](/../../new/main?filename=.github%2Fworkflows%2Fhello-world-docker-ci.yml&workflow_template=blank) を作成します。
2. ワークフローは、アクションが変更された場合、全てのプッシュで実行されるべきです。また、手動でビルドを開始する手動トリガーも追加してください。
   リポジトリをチェックアウトして、ローカルでgit参照なしでアクションを参照します。

<details>
  <summary>解決方法</summary>

```YAML
name: CI Build for Docker Action
on:
  push:
    branches: [ main ]
    paths: [ hello-world-docker-action/** ]
  workflow_dispatch:

jobs:
  test-action:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3.3.0

      - name: Run my own container action
        id: hello-action
        uses: ./hello-world-docker-action
        with:
          who-to-greet: '@wulfland'

      - name: Output time set in the container
        run: echo "The time was ${{ steps.hello-action.outputs.time }} when the action said hello"

```

</details>

3.ワークフローを実行し、パラメータがどのようにアクションに渡され、ワークフローに戻るかを確認します。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174239255-262a8014-4b66-40df-aa17-6f043f948342.png">

## オプション：アクションを解放して使用する

時間が許せば、リリースを作成し、別のリポジトリのワークフローでアクションを使用することができます。

> **注意**
> GitHub アクションを公開できるのは、リポジトリのルートに存在するアクションだけです。

1. 新しいパブリックリポジトリ `hello-world-docker-action` を作成し、[hello-world-docker-action](../hello-world-docker-action) からすべてのファイルをコピーします。

2. [新しいリリース](/../..releases/new)をタグ `v1` とタイトル `v1` で作成します。Generate release notes`をクリックし、リリースを公開します。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174241482-6d3d0c34-9d55-4e3d-86fa-8ac28055cea8.png">

3. 新しいリポジトリを作成するか、既存のリポジトリを使用し、バージョン `v1` で作成したアクションを呼び出すシンプルなワークフローを作成します。

<details>
  <summary>解決方法</summary>

```YAML
name: Test
on: [workflow_dispatch]

jobs:
  test-action:
    runs-on: ubuntu-latest
    steps:
      - name: Say hello
        uses: <your-github-username>/hello-world-docker-action@v1
        with:
          who-to-greet: '@octocat'
```

</details>

## 要約

このハンズオンラボでは、dockerアクションを作成する方法、パラメータを渡す方法、ワークフローに値を返す方法、そしてCIビルドでローカルにアクションをテストする方法を学びました。

この続きは [Staged deployments](03-Staged-deployments.md) を参照してください。
