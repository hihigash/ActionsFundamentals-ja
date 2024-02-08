# 🔨ハンズオン：再利用可能なワークフロー

この実習では、[再利用可能ワークフロー](https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow) と、それを消費するワークフローを作成します。再利用可能なワークフローにパラメータを渡し、それを消費するワークフローで出力パラメータを使用する方法を学びます。

この実習ラボは以下のステップで構成されます：
- [再利用可能なワークフローの作成](#creating-a-reusable-workflow)
- [出力パラメータの追加](#adding-an-output-parameter)
- [再利用可能ワークフローの消費](#consuming-the-reusable-workflow)

## 再利用可能ワークフローの作成

1. [新規ファイル](/../../new/main) `.github/workflows/reusable.yml` を作成する（ファイル名とパスをボックスに貼り付ける）。
2. ファイル名を `Reusable workflow` とする。

    <details>
      <summary>解決方法</summary>

    ```YAML
    name: Reusable workflow
    ```

    </details>

3. [入力パラメータ](https://docs.github.com/en/enterprise-cloud@latest/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_call) `who-to-greet` の `string` 型の `workflow_call` トリガーを追加する。デフォルト値を `World` に設定する。

    <details>
      <summary>解決方法</summary>

    ```YAML
    on:
      workflow_call:
        inputs:
          who-to-greet:
            description: 'The person to greet'
            type: string
            required: true
            default: World
    ```

    </details>

4. コンソールに "Hello <input parameter>" とエコーする `ubuntu-latest` 上で実行する `reusable-job` という名前のジョブを追加する。

    <details>
      <summary>解決方法</summary>

    ```YAML
    jobs:
      reusable-job:
        runs-on: ubuntu-latest
        steps:
          - name: Greet someone
            run: echo "Hello ${{ inputs.who-to-greet }}"
    ```

    </details>


## 出力パラメータの追加

1. ワークフローコマンドを使用して `current-time` という出力パラメータを現在の日時に設定する (そのためには `$(date)` を使用する)。
current-time` という名前の出力パラメータを現在の日付と時刻に設定する（そのためには `$(date)` を使用する）。

    <details>
      <summary>解決方法</summary>

    ```YAML
          - name: Set time
            id: time
            run: echo "time=$(date)" >> $GITHUB_OUTPUT
    ```

    </details>

2. `reusable-job` に `current-time` という出力を追加する。

    <details>
      <summary>解決方法</summary>

    ```YAML
      outputs:
          current-time: ${{ steps.time.outputs.time }}
    ```

    </details>

3. `workflow_call` に `current-time` という出力パラメータを追加し、ワークフローコマンドの出力に設定する。

    <details>
      <summary>解決方法</summary>

    ```YAML
        outputs:
          current-time:
            description: 'The time when greeting.'
            value: ${{ jobs.reusable-job.outputs.current-time }}
    ```

    </details>


    <details>
      <summary>完全な解決方法</summary>

    ```YAML
    name: Reusable workflow

    on:
      workflow_call:
        inputs:
          who-to-greet:
            description: 'The person to greet'
            type: string
            required: true
            default: World
        outputs:
          current-time:
            description: 'The time when greeting.'
            value: ${{ jobs.reusable-job.outputs.current-time }}

    jobs:
      reusable-job:
        runs-on: ubuntu-latest
        outputs:
          current-time: ${{ steps.time.outputs.time }}
        steps:
          - name: Greet someone
            run: echo "Hello ${{ inputs.who-to-greet }}"
          - name: Set time
            id: time
            run: echo "time=$(date)" >> $GITHUB_OUTPUT
    ```

    </details>

## 再利用可能なワークフローを消費する

1. [新規ファイル](/../../new/main) `.github/workflows/reuse.yml` を作成する（ファイル名とパスをボックスに貼り付ける）。
2. 名前を `Reuse other workflow` とし、手動トリガーを追加する。

    <details>
      <summary>解決方法</summary>

    ```YAML
    name: Reuse other workflow

    on: [workflow_dispatch]
    ```

    </details>

3. 再利用可能なワークフローを使用し、入力パラメータとしてユーザー名を渡すジョブ `call-workflow` を追加します。

    <details>
      <summary>解決方法</summary>

    ```YAML
    jobs:
      call-workflow:
        uses: ./.github/workflows/reusable.yml
        with:
          who-to-greet: '@octocat'
    ```

    </details>

4. 出力パラメータ `current-time` をコンソールに書き出す別のジョブ `use-output` を追加する。(ヒント: ニーズコンテキストを使って出力にアクセスする)

    <details>
      <summary>解決方法</summary>

    ```YAML
      use-output:
        runs-on: ubuntu-latest
        needs: [call-workflow]
        steps:
          - run: echo "Time was ${{ needs.call-workflow.outputs.current-time }}"
    ```

    </details>

5. ワークフローを実行し、出力を確認します。

## 要約

このラボでは、再利用可能なワークフローとそれを消費するワークフローの作成方法を学びました。また、再利用可能なワークフローにパラメータを渡し、消費するワークフローで出力パラメータを使用することを学びました。

引き続き、[README](../README.md)を参照してください。

