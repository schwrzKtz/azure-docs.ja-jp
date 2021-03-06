---
title: "SQL Database 動的データ マスクの使用 (Azure ポータル)"
description: "Azure ポータルでの SQL Database 動的データ マスクの使用方法"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 4b36d78e-7749-4f26-9774-eed1120a9182
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: ronitr; ronmat
translationtype: Human Translation
ms.sourcegitcommit: e8513a520d4aa012dcc0ee2ee0dd53828886280d
ms.openlocfilehash: c28f444fcfc7361f02218b7866e15b77769232e5


---
# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>SQL Database 動的データ マスクの使用 (Azure ポータル)

## <a name="overview"></a>Overview
SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 Azure SQL Database の V12 バージョンでは、動的データ マスクがサポートされています。

動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。

たとえば、コール センターのサポート担当者は、社会保障番号やクレジット カード番号の一部の数字から電話の相手を特定できますが、このようなデータ項目をサポート担当者にすべて公開してはなりません。 クエリの結果セットの社会保障番号やクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを定義できます。 別の例として、開発者は、適切なデータ マスクを定義し、個人を特定できる情報 (PII) データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database 動的データ マスクの基礎
SQL Database の構成ブレードまたは設定ブレードの動的データ マスク操作を選択することで、Azure ポータルで動的データ マスク ポリシーを設定します。

### <a name="dynamic-data-masking-permissions"></a>動的データ マスクのアクセス許可
動的データ マスクを構成できるのは、Azure Database 管理者、サーバー管理者、またはセキュリティ責任者の各ロールです。

### <a name="dynamic-data-masking-policy"></a>動的データ マスク ポリシー
* **マスクから除外する SQL ユーザー** - SQL クエリの結果でデータがマスクされない SQL ユーザーまたは AAD の ID のセット。 管理者特権を持つユーザーは常にマスクから除外され、マスクのない元のデータを見ることができることに注意してください。
* **マスク ルール** - マスクされる指定のフィールドと使用されるマスク関数を定義するルールのセット。 データベースのスキーマ名、テーブル名、列名を使用し、指定のフィールドを定義できます。
* **マスク関数** - さまざまなシナリオに対応してデータの公開を制御する方法のセット。

| マスク関数 | マスク ロジック |
| --- | --- |
| **既定値** |**指定のフィールドのデータ型に応じたフル マスク**<br/><br/>• 文字列データ型 (nchar、ntext、nvarchar) のフィールドのサイズが 4 文字未満の場合は、XXXX またはそれ未満の数の X を使用します。<br/>• 数値データ型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) の場合は、値 0 を使用します。<br/>• 日付/時刻データ型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) の場合は、01-01-1900 を使用します。<br/>• SQL バリアントの場合は、現在の型の既定値が使用されます。<br/>• XML の場合は、ドキュメント <masked/> が使用されます。<br/>• 特殊なデータ型 (タイムスタンプ テーブル、hierarchyid、GUID、binary、image、varbinary 空間型) の場合は、空の値を使用します。 |
| **クレジット カード** |クレジット カードの形式でプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **社会保障番号** |米国の社会保障番号の形式でプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXX-XX-1234 |
| **電子メール** |電子メール アドレスの形式でプレフィックスとして定数文字列を使用して、**最初の文字を公開し、ドメインを XXX.com に置き換えるマスク方法**。<br/><br/>aXX@XXXX.com |
| **ランダムな数値** |選択した境界と実際のデータ型に応じて**乱数を生成するマスク方法**。 指定された境界が等しい場合、マスク関数は定数になります。<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **カスタム テキスト** |**最初と最後の文字を公開するマスク方法** 。 元の文字列が公開されたプレフィックスやサフィックスより短い場合、埋め込み文字列のみが使用されます。 <br/>prefix[padding]suffix<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>マスクが推奨されるフィールド
DDM の推奨エンジンでは、データベースの特定のフィールドに「機密データの可能性あり」の注意が付けられます。この注意を参考にマスク候補を選択できます。 ポータルの動的データ マスク ブレードには、データベースの推奨列が表示されます。 1 つまたは複数の列の **[マスクの追加]** をクリックし、**[保存]** をクリックするだけでそれらのフィールドにマスクを適用できます。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure ポータル使用によるデータベースの動的データ マスク設定
1. [https://portal.azure.com](https://portal.azure.com)で Azure ポータルを起動します。
2. マスクするデリケートなデータを含むデータベースの設定ブレードに移動します。
3. **[動的データ マスク]** タイルをクリックして、**[動的データ マスク]** 構成ブレードを起動します。
   
   * この方法に代わって、下にスクロールして **[操作]** セクションを表示し、**[動的データ マスク]** をクリックすることもできます。
     
     ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. **動的データ マスク**構成ブレードには、推奨エンジンがマスク対象として推奨したデータベース列がいくつか表示される場合があります。 推奨を受け入れるには、1 つまたは複数の列の **[マスクの追加]** をクリックします。その列の既定タイプに基づきマスクが作成されます。 マスク機能を変更できます。その場合、マスク ルールをクリックし、マスク フィールド形式を別の形式に変更します。 必ず **[保存]** をクリックして設定を保存します。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. データベースの列にマスクを追加するには、**[動的データ マスク]** 構成ブレードの一番上にある **[マスクの追加]** をクリックし、**[マスク ルールの追加]** 構成ブレードを開きます。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. **[スキーマ]**、**[テーブル]**、**[列]** を選択し、マスクする指定のフィールドを定義します。
7. 機密データのマスク カテゴリの一覧から **[マスク フィールド形式]** を選択します。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. データ マスク ルール ブレードの **[保存]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。
9. マスクから除外し、マスクされていない機密データへのアクセスを与える SQL ユーザーまたは AAD の ID を入力します。 ユーザーをセミコロンで区切った一覧にします。 管理者特権を持つユーザーは常にマスクされていない元のデータにアクセスできることに注意してください。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、アプリケーションストアでデータベースの照会に使用される SQL ユーザーまたは AAD の ID を追加します。 デリケートなデータの公開を最小限に抑えるには、この一覧に含める特権ユーザーの数を最小限にすることを強くお勧めします。
   > 
   > 
10. データ マスク構成ブレードの **[保存]** をクリックして、新しいマスク ルールまたは更新されたマスク ポリシーを保存します。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Powershell コマンドレットを使用して、データベースの動的データ マスクを設定する
「 [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)」をご覧ください。

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>REST API を使用してデータベース用の動的データ マスクを設定する
「 [Azure SQL Database の操作](https://msdn.microsoft.com/library/dn505719.aspx)」を参照してください。




<!--HONumber=Nov16_HO4-->


