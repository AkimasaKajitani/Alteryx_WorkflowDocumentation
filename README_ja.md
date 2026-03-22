# Alteryx Workflow Documentation with LLM
## このプロジェクトは何ですか？

本プロジェクトは、Alteryxのワークフローのドキュメントを自動で作成するツールを作成するプロジェクトです。Alteryxのワークフローは単なるXMLファイルのため、中身を解析することで何を行っているかを理解することができます。特にそのままGeminiなどのLLMに渡すことで中身を自動的に解析することができます。ただし、LLMのコストも考慮し、本プロジェクトではXMLを解析して取得できる情報はなるべくXMLから取得し、ワークフローの解釈のみLLMに任せるという方針としています。

ワークフロー内で何が行われているかはAlteryx CopilotのUIの右上の方にある「Summarize Workflow」から取得することができますが、ドキュメントとして自動作成する向きではないため、今回のようなプロジェクトを立ち上げました。

なお、「Summarize Workflow」で本プロジェクトのメインワークフローを解説させると以下のようになります。

>This workflow processes Alteryx workflow files (.yxmd, .yxmc, .yxwz) to create detailed reports. It extracts metadata, categorizes tools, analyzes input/output, extracts Python code, and generates descriptions using an LLM. The final output is a comprehensive PDF report for each workflow, including an image of the canvas.

Google先生！（による翻訳）
>このワークフローは、Alteryxワークフローファイル（.yxmd、.yxmc、.yxwz）を処理して詳細なレポートを作成します。メタデータの抽出、ツールの分類、入出力の分析、Pythonコードの抽出、LLMを使用した説明文の生成を行います。最終出力は、キャンバスの画像を含む、各ワークフローの包括的なPDFレポートです。

これだと概要だけなので、少し物足りないかもしれません。もう少し詳細も欲しい、PDFやWordのレポートとして欲しい、といった場合に今回のプロジェクトが役に立ちます。詳細なレポートのサンプルは以下をご覧ください。

## レポートサンプル
以下のサンプルは、LLMにGoogle Geminiを使用しています。
- [Weekly Challenge 447 solution file](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Challenge_447_solution_file.yxmd.pdf)
    - Alteryx Communityの[Weekly Challenge 447](https://community.alteryx.com/t5/Weekly-Challenges/Challenge-447-Analysis-of-Survey-Results-Delivered-in-a/td-p/1331307)の解決策例のワークフロー。JupyterNotebookのアンケートを分析するようなワークフローです。
- [Advent of Code 2025 Day04](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Day04.yxmd.pdf)
    - [Advent Of Code](https://adventofcode.com/)の2025年の[4日目の問題](https://adventofcode.com/2025/day/4)を解いたワークフロー。X,Yで表現されるグリッド上の@にアクセスできる数を求める問題。
- [Project Euler Problem 003](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Problem_0003.yxmd.pdf)
    - [Project Euler](https://projecteuler.net/about)の[Problem 3](https://projecteuler.net/problem=3)を解いたワークフロー。600851475143の最大の素因数を求める問題。

## できること
- 以下の項目で構成されたレポートを作成します。
    - Workflow Basic Information(ワークフロー基本情報)
        - 基本情報
        - 著者情報
        - ワークフローメタ情報
        - ワークフロー詳細設定
    - Tool Statistics(ツール統計)
        - ツール数、ツール種類のカウント
        - カテゴリ統計
    - Input/Output
        - Input概要
        - Ouput概要
    - Workflow Canvas Image(キャンバスイメージ)
    - Workflow Overview(LLMによるワークフロー解説)
    - Python Code
- LLMによるワークフロー解説の出力は英語/日本語に対応しています

## 前提条件
- LLMにワークフローの本体を送信するため、yxmd/yxmc/yxmzファイル内に含まれている情報はLLMに送信されることに注意してください。
    - 中身のデータは送信されませんが、メタデータ（項目名など）は送信される可能性があります。また、計算式自体も送信されます。
- LLMとしては、GenAIツールもしくは専用のGemini呼び出しマクロを使用して利用します。
    - いずれかのLLMの契約が必要ということになります。
- 主にGoogle Gemini(2.5 flash)で開発を行っています。その他のLLMやモデルを使用すると予期しない動作が発生する可能性があります。
- レポート作成を行いたいワークフローは、フォルダ入力ツールに指定しているフォルダに保存します。
- ワークフローのキャンバスイメージ用にiconを展開してください。
- 以下のPythonパッケージを使います。
    - `networkx`
    - `Pillow`

## 導入方法
以下のファイルをGitHubよりダウンロードしてください。yxzpはAlteryxで展開し、zipファイルはお使いの展開ツールで解凍してください。

### 共通
- [icon.zip](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/icons.zip)
- [install_python_pkg.yxmd](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/install_python_pkg.yxmd) 
    - [日本語版](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/install_python_pkg_ja.yxmd)

### GenAI版
- [DocumentationWorkflow_GenAI.yxzp](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/DocumentationWorkflow_GenAI.yxzp)

### Gemini版
- [DocumentationWorkflow_Gemini.yxzp](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/DocumentationWorkflow_Gemini.yxzp)

## 使い方
### 追加Pythonパッケージのインストール
管理者権限がある状態でAlteryx Designerを起動し、ワークフローを実行し、Pythonの追加パッケージをインストールします。慣れていない人は、Designerを「管理者で実行」し、「install_python_pkg.yxmd」を実行してください。

### GenAI版の事前準備
1. Alteyrx OneでLLMのConnectionを作成し、LLMに接続できるように設定を行う（管理者権限必要）
    - 高速なモデルの利用を推奨します
2. LLM Overrideツールで、LLMを使えるように設定する

### Gemini版の事前準備
1. Google AI StudioからGeminiのAPIキーを取得する
2. Geminiマクロに1で取得したAPIキーを設定する

### 以下共通手順
1. ワークフローのキャンバスイメージ用のアイコンファイルをワークフローが保存しているフォルダの直下のiconフォルダ内に展開し、配置する
2. ワークフローが保存されているフォルダ直下のworkflowフォルダにレポートを作成したいファイルを保存する（もしくは、ディレクトリツールの設定を、レポートを作成したいワークフローが入ったフォルダに書き換える）
3. テキストインプットツール（ツールID 300）の言語設定を変更する。デフォルトは英語になっているので、日本語にしたい場合はjaのレコードのenabledを1にし、enのレコードは空白にする。
4. ワークフローを実行する

## その他注意事項
- 実際に実行すると、LLM処理部分でしばらく待たされます。また、ワークフローのキャンバスイメージを作成する場合にPythonツールを使用しているため少し時間がかかります。
- LLMを使用したくない場合、Detour Tool（分岐ツール）にチェックを入れて右側に分岐してください。Detour ToolのTool IDは276です。
- 以下のエラーが出ている場合、LLMの処理速度制限に達しています。同時に処理するワークフローの数を減らしてください。
    >"Warning: Prompt (279): Error occurred in LLM response generation: 429 Client Error: Too Many Requests for url: https://us1.alteryxcloud.com/aims/v1/generatedContent "
- ワークフローのサイズが大きい場合、LLMやモデルによっては処理ができないことがあります（出力が途中でストップすることもあります）。使用するモデルを見直してください。

## 各レポートのセクションと実現方法について
| No | 項目 | 作成方法 |
|:---|:----|:-------|
| 1 | Workflow Basic Information | ワークフローXMLのパース |
| 2 | Tool Statistics | ワークフローXMLのパース |
| 3 | Input/Output | ワークフローXMLのパース |
| 4 | Workflow Canvas Image | ワークフローXMLのパースしたデータを元にPythonでイメージを作成 |
| 5 | Workflow Detail Description | ほぼファイル丸ごとLLMに送信 |
| 6 | Python Code | ワークフローXMLのパース |

## 未実装機能
- LLMのコスト対策は一部のみ行っています。
    - コメントツールの背景画像を削除
    - マクロのアイコンは削除
- Tool Statistics
    - 各ツールの詳細設定は抽出していません。
    - 円グラフの色はカテゴリカラーで塗っていません（Interactive Chart toolの仕様）
- Workflow Canvas Image
    - コメントツールのコメントは表示していません。
    - マクロアイコンは表示していません
- Code抽出
    - Rツールのコードは抽出していません。

ここに記載している内容は「気になっているけど対応していない」ため、気が向いたら対応される可能性があります。ここに記載されていない内容は、全く気になっていないので実現されることはないかと思います。

## LLMの使い方（参考）
- ワークフローのXMLをほぼそのままLLMに渡しています
    - 現在と同様の内容が出せるように今後送信するデータを圧縮したい
- XMLの解析でできる部分はXMLを解析して対応しています
    - LLMコストの節約
    - アウトプットの安定化のため
- アウトプットはJSONフォーマットとしています
- Geminiでインストラクションと指示の言語が異なると、出力が二重に出てしまったため、インストラクションは使わず指示の本文にすべて記載しています
- LLMは指示をしないと基本的にmarkdown形式でレポートを作成しますが、Alteryx内ではmarkdown形式は扱いにくいため、JSONでアウトプットさせています。その後、レポーティングツールで体裁を整えるようにしています。

## 履歴
| Date | Version | Description |
|:-----|:--------|:-------|
| 2026/03/23 | 1.01 | LLMインストラクションをアップデート |
| 2026/03/21 | 1.00 | 初期リリース |

## 作者連絡先
Alteryx Community ([AkimasaKajitani](https://community.alteryx.com/t5/user/viewprofilepage/user-id/10936))

