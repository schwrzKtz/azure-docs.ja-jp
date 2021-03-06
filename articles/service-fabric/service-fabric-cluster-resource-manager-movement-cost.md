---
title: "Service Fabric クラスター リソース マネージャー：移動コスト | Microsoft Docs"
description: "Service Fabric サービスの移動コストの概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 54cdfee64360543e0d1011f04c9b79400e912582


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>クラスター リソース マネージャーの選定に影響を与えるサービス移動コスト
クラスターに対して行う変更とソリューションのスコアを決定するときに考慮すべき重要な要因は、そのソリューションを実現するための全体的なコストです。

サービス インスタンスやレプリカの移動では、最低でも CPU 時間とネットワーク帯域幅のコストがかかります。 ステートフル サービスの場合は、古いレプリカのシャット ダウン前に状態のコピーを作成するために必要なディスク領域のコストもかかります。 Azure Service Fabric クラスター リソース マネージャーによって構成されるソリューションのコストが最小限であることが望ましいのは明らかです。 一方でクラスター内のリソースの割り当てを大幅に改善するソリューションを無視することは避けたいものです。

クラスター リソース マネージャーには、その他の目的に応じて、クラスターの管理時にもコストを計算して制限する方法が 2 つあります。 第一に、クラスター リソース マネージャーはクラスターの新しいレイアウトを計画するときに、実行するすべての移動を個別にカウントします。 2 つのソリューションの全体的なバランス (スコア) がほぼ同じになる単純な例では、片方を最低コスト (移動の合計数) にします。

この方法は成功します。 ただし、既定の負荷や静的な負荷の場合と同様に、すべての移動が等しい複雑なシステムではそうはいきません。 一部のコストがはるかに高くなる可能性があります。

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>レプリカの移動コストと考慮事項を変更する
負荷の報告機能 (クラスター リソース マネージャーの別の機能) と同様に、サービスに、特定の時点での移動にどれだけのコストがかかるかを自己報告させることができます。

コード:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost には、Zero、Low、Medium、High の 4 つのレベルがあります。 Zero を除き、これらのレベルは相対的なものです。 Zero の場合、レプリカの移動にコストがかからず、ソリューションのスコアに対してカウントしません。 移動コストを High に設定しても、レプリカが移動されないことが保証されるわけでは*ありません*。相応の理由がない限り移動されないだけです。

![移動するレプリカを選択する際の要因としての移動コスト][Image1]

移動コストは、同等のバランスを最も簡単に維持したまま全体的な中断が最小限になるソリューションを見つけるのに役立ちます。 サービスのコストの概念はさまざまなものに関係する可能性があります。 最も一般的なものは次のとおりです。

* サービスが移動する必要のある状態またはデータの量。
* クライアント切断のコスト。 通常、プライマリ レプリカを移動するコストは、セカンダリ レプリカの移動コストより高くなります。
* 実行中の操作を中断するコスト。 一部のデータ ストア レベル操作またはクライアント コールの応答で実行される操作にはコストがかかります。 特定の時点以降では、必要でなければ処理を中止しない方が適切です。 そのため、操作の実行中はコストを高くして、サービスのレプリカまたはインスタンスが移動される可能性を低くします。 操作が完了したら元に戻します。

## <a name="next-steps"></a>次のステップ
* Service Fabric クラスター リソース マネージャーは、メトリックを使用して、クラスターの利用量と容量を管理します。 メトリックの詳細とその構成方法については、「 [Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」を参照してください。
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、「 [Service Fabric クラスターの均衡をとる](service-fabric-cluster-resource-manager-balancing.md)」を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Nov16_HO3-->


