---
title: Modifications pour mettre en cluster d’accès de configuration - Azure HDInsight
description: En savoir plus sur les modifications apportées à l’accès aux champs de configuration de cluster sensibles.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610213"
---
# <a name="changes-to-cluster-configuration-access"></a>Modifications apportées à l’accès de configuration de cluster

La dernière version du SDK Azure HDInsight apporte d’importantes modifications pour prendre en charge plus précise en fonction du rôle de l’accès pour obtenir des informations sensibles. Comme partie de ces modifications, certaines **action peut être nécessaire** si vous utilisez l’une des méthodes affectées.

## <a name="sdk-for-net-500"></a>Kit de développement logiciel pour .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) sera **retournent plus de paramètres sensibles** comme clés de stockage (core-site) ou les informations d’identification HTTP (passerelle).
    - Pour récupérer toutes les configurations, notamment les paramètres sensibles, utilisez `ConfigurationOperationsExtensions.List` à l’avenir.  Notez que les utilisateurs dotés du rôle « Lecteur » ne sera pas en mesure d’utiliser cette méthode. Cela permet un contrôle granulaire quels utilisateurs peuvent accéder à des informations sensibles pour un cluster. 
    - Pour récupérer des données d’identification de passerelle HTTP, utilisez `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) est maintenant déconseillé et a été remplacé par `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) et [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) sont désormais déconseillés. HTTP est désormais toujours activée, donc ces méthodes ne sont plus nécessaires.