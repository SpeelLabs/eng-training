---
description: Windows 環境に Git と PowerShell 7 をインストールし、VS Code と連携させるための手順を説明します。
---

# Git と PowerShell のインストール

### 1. 環境構築の準備

#### 1.1 必要なツール

今回、以下のツールをインストールし、連携させます。

* **winget**: Windows パッケージマネージャー (Windows 11 に標準搭載)
* **Git**: バージョン管理システム
* **PowerShell 7**: 最新のコマンドラインシェル
* **Visual Studio Code**: コードエディター (既にインストール済み)

#### 1.2 公式ドキュメント

この手順は以下の公式ドキュメントに基づいています。

* [PowerShell のインストール - Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.5)
* [Git for Windows - ダウンロード](https://git-scm.com/download/win) - 現在の安定版は **2.49.0** (2025年4月現在)

#### 1.3 事前確認

インストールを始める前に、winget が利用可能かどうかを確認しましょう。

1. <kbd>Windows</kbd> キーを押し、検索ボックスに 「PowerShell」 と入力します
2. 検索結果に表示された **Windows PowerShell** を右クリックし、<kbd>管理者として実行</kbd> を選択します
3. 表示された Windows PowerShell で以下のコマンドを実行します。

{% code overflow="wrap" %}
```powershell
# winget が利用可能か確認
winget --version
```
{% endcode %}

<details>

<summary>実行結果</summary>

```
PS C:\Users\Username> winget --version
v1.10.340    # バージョンは異なる場合があります
```

</details>

このコマンドが正常に実行され、バージョン情報が表示されれば winget は利用可能です。エラーが表示される場合は、App Installer をインストールする必要があるかもしれません。

### 2. Git のインストール

**Git** はソースコードのバージョン管理システムです。コードの変更履歴を追跡し、チームでの共同作業を可能にします。

#### 2.1 winget でのインストール

**管理者権限の** **Windows PowerShell** で以下のコマンドを実行します。

{% code overflow="wrap" %}
```powershell
# Git をインストール
winget install --id Git.Git -e --source winget
```
{% endcode %}

> 📝 **ポイント**: `-e` オプションは `--exact` の略で、パッケージ ID の完全一致検索を行います。`--source winget` は明示的にソースを指定します。これは Git の公式ドキュメントで推奨されている方法です。

{% hint style="warning" %}
Git のインストールには管理者権限が必要です。必ず管理者として実行した PowerShell でコマンドを実行してください。
{% endhint %}

#### 2.2 Git のバージョン確認

Git が正しくインストールされたかを確認します。

{% code overflow="wrap" %}
```powershell
git --version
```
{% endcode %}

<details>

<summary>実行結果</summary>

```
PS C:\Users\Username> git --version
git version 2.49.0.windows.1
```

</details>

### 3. PowerShell 7 のインストール

**PowerShell 7** は、デフォルトの Windows PowerShell (PowerShell 5.1) より高機能で、クロスプラットフォーム対応の最新バージョンです。

#### 3.1 winget でのインストール

先ほど開いた管理者権限の PowerShell で以下のコマンドを実行します。

{% code overflow="wrap" %}
```powershell
# PowerShell 7 をインストール
winget install --id Microsoft.Powershell --source winget
```
{% endcode %}

{% hint style="warning" %}
初めて winget を使用する場合、[Microsoft Store の利用規約 (Terms of Transaction) ](https://aka.ms/microsoft-store-terms-of-transaction)への同意を求められることがあります。インストールを続行するには、`Y` キーを押して同意する必要があります。
{% endhint %}

#### 3.2 インストールの確認

PowerShell 7 のインストールが完了したら、以下の手順で確認します。

1. 管理者権限の PowerShell ウィンドウを閉じます
2. 再度 <kbd>Windows</kbd> キーを押して 「PowerShell」 と検索し、**Windows PowerShell** を開き直します
3. 開いた Windows PowerShell ウィンドウで PowerShell 7 を起動します

{% code overflow="wrap" %}
```powershell
# PowerShell 7 を起動
pwsh
```
{% endcode %}

<details>

<summary>実行結果</summary>

```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\Username> pwsh
PowerShell 7.5.0
Copyright (C) Microsoft Corporation.

https://aka.ms/powershell
Type 'help' to get help.
```

</details>

4. PowerShell 7 が起動したら、バージョン情報を確認します。

{% code overflow="wrap" %}
```powershell
# バージョン情報の確認
$PSVersionTable
```
{% endcode %}

<details>

<summary>実行結果</summary>

```
Name                           Value
----                           -----
PSVersion                      7.5.0
PSEdition                      Core
GitCommitId                    7.5.0
OS                             Microsoft Windows 10.0.26100
Platform                       Win32NT
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
WSManStackVersion              3.0
```

</details>

> 📝 **ポイント**: `pwsh` は PowerShell 7 を起動するためのコマンドです。すでに PowerShell 7 を実行中の場合は、直接 `$PSVersionTable` コマンドでバージョン情報を確認できます。PSVersion の Major バージョンが 7 であれば PowerShell 7 を使用中です。

### 4. VS Code との連携

ここでは、VS Code で PowerShell 7 と Git を使用するための設定を行います。

#### 4.1 PowerShell 7 をデフォルトターミナルに設定

1. VS Code を起動します
2. <kbd>Ctrl</kbd> + <kbd>J</kbd> を押してターミナルを開きます
3. ターミナルウィンドウ右上のドロップダウンメニュー ( <kbd>▼</kbd> ) をクリックします
4. **Select Default Profile** を選択します
5. 表示されたリストから **PowerShell** を選択します
6. ターミナルを閉じて、再度 <kbd>Ctrl</kbd> + <kbd>J</kbd> で新しいターミナルを開きます

#### 4.2 PowerShell のバージョン確認

新しく開いたターミナルで以下のコマンドを実行して、PowerShell 7 が正しく動作していることを確認します。

{% code overflow="wrap" %}
```powershell
$PSVersionTable.PSVersion
```
{% endcode %}

バージョン情報が表示され、メジャーバージョンが 7 であれば成功です。

#### 4.3 Git 機能の確認

VS Code の左側のアクティビティバーで、ソース管理アイコン (分岐アイコン) をクリックすると、Git 機能にアクセスできます。

#### 4.4 ターミナルからの Git 動作確認

VS Code のターミナルから Git コマンドが実行できることを確認します：

{% code overflow="wrap" %}
```powershell
git --version
```
{% endcode %}

<details>

<summary>実行結果</summary>

```
PS C:\Users\Username> git --version
git version 2.49.0.windows.1
```

</details>

### 5. よくあるエラーと対処法

#### 5.1 PowerShell 7 コマンドが見つからない

{% code overflow="wrap" %}
```
'pwsh' は、内部コマンドまたは外部コマンド、操作可能なプログラムまたはバッチ ファイルとして認識されていません。
```
{% endcode %}

**対処法**:

1. PowerShell を再起動する
2. それでも解決しない場合、システムの再起動を試す
3. 環境変数 PATH に PowerShell 7 のインストールディレクトリが追加されているか確認する

#### 5.2 Git コマンドが見つからない

{% code overflow="wrap" %}
```
'git' は、内部コマンドまたは外部コマンド、操作可能なプログラムまたはバッチ ファイルとして認識されていません。
```
{% endcode %}

**対処法**:

1. PowerShell を再起動する
2. Git のインストールパスが環境変数 PATH に追加されているか確認する
3. Git をインストールし直す

#### 5.3 VS Code のターミナルエラー

{% code overflow="wrap" %}
```
Cannot launch terminal. A JavaScript error occurred in the main process.
```
{% endcode %}

**対処法**:

1. VS Code を再起動する
2. PowerShell 7 が正しくインストールされているか確認する
3. VS Code を管理者として実行してみる

#### 5.4 管理者権限が必要というエラー

{% code overflow="wrap" %}
```
このアプリケーションをインストールするには管理者権限が必要です。
This application requires elevated permissions to install.
```
{% endcode %}

**対処法**:

1. インストールコマンドを実行する PowerShell を必ず 「管理者として実行」 で開く
2. Windows キーを押し、「PowerShell」 を検索して、右クリックから 「管理者として実行」 を選択する
3. UAC (ユーザーアカウント制御) のダイアログが表示されたら 「はい」 をクリックする
