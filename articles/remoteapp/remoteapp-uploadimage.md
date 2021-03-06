
---
title: Azure RemoteApp のカスタム イメージをアップロードする | Microsoft Docs
description: Azure RemoteApp のカスタム イメージをアップロードする方法について説明します。
services: remoteapp
documentationcenter: ''
author: ericorman
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: ericor

---
# Azure RemoteApp のカスタム イメージをアップロードする
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。詳細については、[お知らせ](https://go.microsoft.com/fwlink/?linkid=821148)をご覧ください。
> 
> 

カスタム テンプレート イメージの作成、または変更と更新ができたら、そのイメージを Azure RemoteApp イメージ ライブラリにアップロードする必要があります。次の手順を使用します。

## 開始する前に
1. カスタム イメージが[イメージの要件](remoteapp-imagereqs.md)と[アプリケーションの要件](remoteapp-appreqs.md)を満たしていることを確認します。
2. [Azure PowerShell モジュール](../powershell-install-configure.md)をインストールします。

## カスタム イメージのアップロード方法のステップ バイ ステップ ガイド
1. Azure 管理ポータルを開いて、[RemoteApp] ページに移動します。
2. **[テンプレート イメージ]** タブで、ページの下部にある **[アップロード]** をクリックします。
3. イメージのフレンドリ名を入力し、ストレージ アカウントの場所を指定します。この場所が、RemoteApp コレクションの場所、またはコレクションを作成しようとしている場所と同じであることを確認します。
4. メッセージが表示されたら、ローカル PC にスクリプトをダウンロードします。
5. テキスト ボックスのコマンド パラメーターをクリップボードにコピーします。
6. 管理者特権の Windows PowerShell ウィンドウを開きます。
7. 管理者特権での Windows PowerShell ウィンドウから、スクリプトをダウンロードしたディレクトリに移動します。
8. コピーしたコマンドを貼り付けて、**Enter** キーを押します。
   
   アップロード プロセスが開始されます。所要時間は、ネットワークの速度やイメージのサイズなど、さまざまな要因によって異なります。
9. ネットワーク接続の中断などの理由でアップロードが成功しなかった場合は、開始したアップロード プロセスをいつでも再開できます。アップロードを再開するには、同じコマンド ラインを使用して、スクリプトを再度実行します。

> [!WARNING]
> アップロード スクリプトを変更しないでください。アップロードするイメージがイメージの要件とアプリケーションの要件を満たしていることを確認するための特定のチェックが実装されています。
> 
> 

## 一般的な問題
* Azure PowerShell ではなく、Windows PowerShell を使用してください。Azure PowerShell モジュールをインストールする必要があります。アップロード プロセス中に特定のモジュールが必要になるためです。
* スクリプトを変更しないでください。スクリプトには検証が含まれています。
* アップロード中に vhd ファイルがロックアウトされる場合は、ファイルを新しい場所にコピーまたは移動して、再度アップロードを実行してください。一部の Windows プロセスがアップロードを阻んでいる可能性があります。

<!---HONumber=AcomDC_0817_2016-->