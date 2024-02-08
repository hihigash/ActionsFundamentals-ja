# 🔨ハンズオン：段階的デプロイメント

このハンズオンラボでは、環境と承認付きの段階的デプロイメント ワークフローを作成します。
> このハンズオン・ラボでは、環境と承認付きの段階的デプロイ ワークフローを作成します。プライベート リポジトリに環境を追加できるのは、Team アカウント（またはそれ以上）を持つ組織と GitHub Pro アカウントを持つユーザーのみです。

このハンズオンラボは、[My first workflow](01-My-first-workflow.md) をベースに以下のステップを追加したものです：
- [環境の作成と保護](#creating-and-protecting-environments)
- [手動ワークフロートリガーへの環境選択入力の追加](#adding-a-input-for-picking-environments-to-manual-workflow-trigger)
- [ワークフローのステップと条件実行の連鎖](#chaining-workflow-steps-and-conditional-execution)
- [デプロイのレビューと承認](#reviewing-and-approving-deployments)

## 環境の作成と保護

1. [設定](/../../settings) | [環境](/../../settings/environments)に移動し、[新しい環境](/../../settings/environments/new)をクリックします。
2. `Production` という名前を入力し、`Configure environment` をクリックします。
3. この環境の `Required reviewer` として自分自身を追加します：

<img width="349" alt="image" src="https://user-images.githubusercontent.com/5276337/174113475-967127de-45a7-4dc9-8477-4de4df62c7e6.png">

4. この環境には `main` ブランチのみをデプロイできるようにします：

<img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174113782-70a1b18a-0ab9-49fd-a53e-cb2ea78916e1.png">

5. さらに2つの環境 (`Test` と `Load-Test`) を作成します。

## 手動ワークフローのトリガーに環境を選択する入力を追加する

`workflow_dispatch`トリガーに environment 型の入力を追加する。

<details>
  <summary>解決方法</summary>

```YAML
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        type: environment
        required: true
```

</details>

## ワークフローのステップの連結と条件実行

1.ワークフローファイルに3つのジョブを追加する：
  - Test: `Build` の後に `ubuntu-latest` で実行される。ワークフローが手動でトリガーされた時のみ実行される。`Test` 環境で実行する。ジョブは `Testing...` をワークフローのログに書き込む。  
  - Load-Test: `Build` の後に `ubuntu-latest` 環境で実行される。ワークフローが手動でトリガーされた場合のみ実行される。`Load-Test` 環境で実行される。ジョブは `Testing...` をワークフローログに書き込み、15 秒間スリープする。  
  - Production: `Test` と `Load-Test` の後に `ubuntu-latest` 上で実行される。入力パラメータとして `Production` が選択された場合のみ、`Production` 環境にデプロイされる。この環境の URL は `https://writeabout.net` です。デプロイをシミュレートするために、ジョブは 5 つのステップを実行します。各ステップは `Step x deploying... ` をワークフロー ログに書き込み、10 秒間スリープします。

    <details>
      <summary>解決方法</summary>

    ```YAML
      Test:
        runs-on: ubuntu-latest
        if: github.event_name == 'workflow_dispatch'
        needs: Build
        environment: Test
        steps:
          - run: echo "🧪 Testing..."

      Load-Test:
        runs-on: ubuntu-latest
        if: github.event_name == 'workflow_dispatch'
        needs: Build
        environment: Load-Test
        steps:
          - run: |
              echo "🧪 Testing..."
              sleep 15

      Production:
        runs-on: ubuntu-latest
        needs: [Test, Load-Test]
        environment:
          name: Production
          url: https://writeabout.net
        if: github.event.inputs.environment == 'Production'
        steps:
          - run: |
              echo "🚀 Step 1..."
              sleep 10
          - run: |
              echo "🚀 Step 2..."
              sleep 10
          - run: |
              echo "🚀 Step 3..."
              sleep 10
          - run: |
              echo "🚀 Step 4..."
              sleep 10
          - run: |
              echo "🚀 Step 5..."
              sleep 10
    ```

    </details>

2. ワークフローをトリガーし、環境として `Production` を選択します：

<img width="212" alt="image" src="https://user-images.githubusercontent.com/5276337/174119722-9b76d479-e355-414b-a534-03d8634536ef.png">

## デプロイのレビューと承認

1. ワークフローを開き、レビューを開始します。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174120029-f395e8ec-5e6e-4350-94c5-130caefaafc2.png">

2. そしてコメントを添えて承認します：

<img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174120086-fed98feb-2d7f-476b-a997-1aa099de7d0e.png">

3. デプロイ中のプログレスバーに注目します :

<img width="200" alt="image" src="https://user-images.githubusercontent.com/5276337/174120314-c900585c-6b94-4fc2-8fe9-92452b0cf187.png">

4. 結果はこのようになり、承認と本番環境の URL が含まれます：

<img width="800" alt="image" src="https://user-images.githubusercontent.com/5276337/174120381-cef48594-6663-481a-aadd-1ef0dbd50b0a.png">

## 要約

このラボでは、GitHub で環境を作成して保護し、ワークフローで使用する方法を学びました。また
キーワードを使ってジョブを連鎖させることも学びました。

引き続き、[再利用可能なワークフロー](04-Reusable-workflows.md) をご覧ください。

