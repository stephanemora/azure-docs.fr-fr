---
title: Fournisseurs de stockage Durable Functions - Azure
description: En savoir plus sur les différents fournisseurs de stockage pour Durable Functions et leur comparaison
author: cgillum
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: azfuncdf
ms.openlocfilehash: 090db9c84786735071ef82e60ab2234fb01d5d6e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385751"
---
# <a name="durable-functions-storage-providers"></a>Fournisseurs de stockage Durable Functions

Durable Functions conserve automatiquement les paramètres des fonctions, les valeurs renvoyées et autres états dans un stockage durable afin d’assurer une exécution fiable. La configuration par défaut pour Durable Functions stocke cet état d’exécution dans un compte Stockage Azure (classique). Toutefois, il est possible de configurer Durable Functions v2.0 et versions ultérieures pour utiliser un autre fournisseur de stockage durable.

Durable Functions est un ensemble de déclencheurs et de liaisons Azure Functions qui sont alimentés en interne par [Durable Task Framework](https://github.com/Azure/durabletask) (DTFx). DTFx prend en charge différents fournisseurs de stockage principal, y compris le fournisseur Stockage Azure utilisé par Durable Functions. À partir de Durable Functions **v2.4.3**, les utilisateurs peuvent configurer leurs applications de fonction pour utiliser des fournisseurs de stockage DTFx autres que le fournisseur Stockage Azure.

> [!NOTE]
> La décision d’utiliser des fournisseurs de stockage autres que Stockage Azure doit être prise avec précaution. La plupart des applications de fonction s’exécutant dans Azure doivent utiliser le fournisseur Stockage Azure par défaut pour Durable Functions. Toutefois, il existe des compromis importants en matière de coûts, d’évolutivité et de gestion des données qui doivent être pris en compte lorsque vous décidez d’utiliser un autre fournisseur de stockage. Cet article décrit en détail un grand nombre de ces compromis.

Deux fournisseurs de stockage DTFx de remplacement ont été développés pour être utilisés avec Durable Functions, le fournisseur de stockage _Netherite_ et le fournisseur de stockage _Microsoft SQL Server (MSSQL)_ . Cet article décrit les trois fournisseurs pris en charge, les compare les uns avec les autres et fournit des informations de base sur leur prise en main.

## <a name="azure-storage"></a>Stockage Azure

Stockage Azure est le fournisseur de stockage par défaut pour Durable Functions. Il utilise des files d’attente, des tables et des objets blob pour conserver l’état de l’orchestration et de l’entité. Il utilise également des objets blob et des baux d’objets blob pour gérer les partitions. Dans de nombreux cas, le compte de stockage utilisé pour stocker l’état d’exécution de Durable Functions est le même que le compte de stockage par défaut utilisé par Azure Functions (`AzureWebJobsStorage`). Toutefois, il est également possible de configurer Durable Functions avec un compte de stockage distinct. Le fournisseur Stockage Azure est intégré à l’extension Durable Functions et n’a pas d’autres dépendances.

Les principaux avantages du fournisseur Stockage Azure sont les suivants :

* Aucune configuration requise : vous pouvez utiliser le compte de stockage qui a été créé pour vous par l’expérience d’installation d’application de fonction.
* Modèle de facturation sans serveur à faible coût : Stockage Azure dispose d’un modèle de tarification basé sur la consommation reposant entièrement sur l’utilisation ([plus d’informations](durable-functions-billing.md#azure-storage-transactions)).
* Meilleure prise en charge des outils : Stockage Azure offre une émulation locale interplateforme et s’intègre à Visual Studio, Visual Studio Code et le Azure Functions Core Tools.
* Plus mature : Stockage Azure est le stockage principal d’origine et le plus éprouvé pour Durable Functions.

Le code source des composants DTFx du fournisseur Stockage Azure se trouve dans le référentiel GitHub [Azure/durabletask](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage).

> [!NOTE]
> Des comptes Stockage Azure standard à usage général sont requis lors de l’utilisation du fournisseur Stockage Azure. Tous les autres types de compte de stockage ne sont pas pris en charge. Nous vous recommandons vivement d’utiliser des comptes de stockage à usage général v1 hérités, car les comptes de stockage v2, plus récents, peuvent être beaucoup plus onéreux pour les charges de travail de Durable Functions. Pour plus d’informations sur les types de comptes de stockage Azure, consultez l’article [Vue d’ensemble du compte de stockage](../../storage/common/storage-account-overview.md).

## <a name=""></a><a name="netherite">Netherite (préversion)</a>

Le stockage principal Netherite a été conçu et développé par [Microsoft Research](https://www.microsoft.com/research). Il utilise [Azure Event Hubs](../../event-hubs/event-hubs-about.md) et la technologie de base de données [FASTER](https://www.microsoft.com/research/project/faster/) en plus d’[objets blob de pages Azure](../../storage/blobs/storage-blob-pageblob-overview.md). La conception de Netherite permet un traitement à débit beaucoup plus élevé des orchestrations et des entités par rapport aux autres fournisseurs. Dans certains scénarios de test, le débit a augmenté d’un ordre de grandeur supérieur à celui du fournisseur Stockage Azure par défaut.

Les principaux avantages du fournisseur de stockage Netherite comprennent les suivants :

* Débit nettement plus élevé à moindre coût par rapport aux autres fournisseurs de stockage.
* Prend en charge l’optimisation des performances, ce qui vous permet de mettre à l’échelle les performances en fonction des besoins.
* Prend en charge jusqu’à 32 partitions de données avec les références SKU De base et Standard d’Event Hubs.
* Plus rentable que les autres fournisseurs pour les charges de travail à débit élevé.

Vous pouvez en apprendre plus sur les détails techniques du fournisseur de stockage Netherite, notamment sa prise en main, dans la [documentation de Netherite](https://microsoft.github.io/durabletask-netherite). Le code source du fournisseur de stockage Netherite se trouve dans le référentiel GitHub [Microsoft/durabletask-netherite](https://github.com/microsoft/durabletask-netherite). Une évaluation plus approfondie du fournisseur de stockage Netherite est également disponible dans le document de recherche suivant : [Workflows sans serveur avec Durable Functions et Netherite](https://arxiv.org/abs/2103.00033).

> [!NOTE]
> Le nom _Netherite_ provient du monde de [Minecraft](https://minecraft.fandom.com/wiki/Netherite).

## <a name=""></a><a name="mssql">Microsoft SQL Server (MSSQL) (préversion)</a>

Le fournisseur de stockage Microsoft SQL Server (MSSQL) conserve tout l’état dans une base de données Microsoft SQL Server. Il est compatible avec les déploiements locaux et hébergés sur le cloud de SQL Server, y compris les [bases de données Azure SQL](../../azure-sql/database/sql-database-paas-overview.md).

Les principaux avantages du fournisseur de stockage MSSQL sont les suivants :

* Prend en charge les environnements déconnectés : aucune connectivité Azure n’est requise lors de l’utilisation d’une installation SQL Server.
* Portable dans plusieurs environnements et clouds, y compris Azure et en local.
* Forte cohérence des données, permettant la sauvegarde/restauration et le basculement sans perte de données.
* Prise en charge native du chiffrement de données personnalisé (une fonctionnalité de SQL Server).
* S’intègre avec les applications de base de données existantes via des procédures stockées intégrées.

Vous pouvez en apprendre plus sur les détails techniques du fournisseur de stockage MSSQL, notamment sa prise en main, dans la [documentation du fournisseur Microsoft SQL](https://microsoft.github.io/durabletask-mssql). Le code source du fournisseur de stockage MSSQL se trouve dans le référentiel GitHub [Microsoft/durabletask-mssql](https://github.com/microsoft/durabletask-mssql).

## <a name="configuring-alternate-storage-providers"></a>Configuration de fournisseurs de stockage alternatifs

La configuration de fournisseurs de stockage alternatifs est généralement un processus en deux étapes :

1. Ajoutez le package NuGet approprié à votre application de fonction (cette exigence est temporaire pour les applications qui utilisent des packs d’extension).
1. Mettez à jour le fichier **host.json** pour spécifier le fournisseur de stockage que vous souhaitez utiliser.

Si aucun fournisseur de stockage n’est explicitement configuré dans host.json, le fournisseur Stockage Azure est activé par défaut.

### <a name="configuring-the-azure-storage-provider"></a>Configurer le fournisseur Stockage Azure

Le fournisseur Stockage Azure est le fournisseur de stockage par défaut et n’a besoin d’aucune configuration explicite, de références de package NuGet ou de références de pack d’extensions. Vous pouvez trouver l’ensemble complet d’options de configuration de **host.json** [ici](durable-functions-bindings.md#hostjson-settings), sous le chemin d’accès `extensions/durableTask/storageProvider`.

### <a name="configuring-the-netherite-storage-provider"></a>Configuration du fournisseur de stockage Netherite

Pour utiliser le fournisseur de stockage Netherite, vous devez d’abord ajouter une référence au package NuGet [Microsoft.Azure.DurableTask.Netherite.AzureFunctions](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Netherite.AzureFunctions) dans votre fichier **csproj** (applications .NET) ou votre fichier **extensions.proj** (applications JavaScript, Python et PowerShell).

> [!NOTE]
> Le fournisseur de stockage Netherite n’est pas encore pris en charge dans les applications qui utilisent des [packs d’extension](../functions-bindings-register.md#extension-bundles).

L’exemple de host.json ci-dessous montre la configuration minimale requise pour activer le fournisseur de stockage Netherite.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "Netherite",
        "storageConnectionName": "AzureWebJobsStorage",
        "eventHubsConnectionName": "EventHubsConnection"
      }
    }
  }
}
```

Pour obtenir des instructions d’installation plus détaillées, consultez la [documentation de prise en main de Netherite](https://microsoft.github.io/durabletask-netherite/#/?id=getting-started).

### <a name="configuring-the-mssql-storage-provider"></a>Configuration du fournisseur de stockage MSSQL

Pour utiliser le fournisseur de stockage MSSQL, vous devez d’abord ajouter une référence au package NuGet [Microsoft.DurableTask.SqlServer.AzureFunctions](https://www.nuget.org/packages/Microsoft.DurableTask.SqlServer.AzureFunctions) dans votre fichier **csproj** (applications .NET) ou votre fichier **extensions.proj** (applications JavaScript, Python et PowerShell).

> [!NOTE]
> Le fournisseur de stockage MSSQL n’est pas encore pris en charge dans les applications qui utilisent des [packs d’extension](../functions-bindings-register.md#extension-bundles).

L’exemple ci-dessous montre la configuration minimale requise pour activer le fournisseur de stockage MSSQL.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "mssql",
        "connectionStringName": "SQLDB_Connection"
      }
    }
  }
}
```

Pour obtenir des instructions d’installation plus détaillées, consultez la [documentation de prise en main du fournisseur MSSQL](https://microsoft.github.io/durabletask-mssql/#/quickstart).

## <a name="comparing-storage-providers"></a>Comparaison des fournisseurs de stockage

Il existe de nombreux compromis significatifs entre les différents fournisseurs de stockage pris en charge. Le tableau suivant peut être utilisé pour vous aider à comprendre ces compromis et déterminer quel fournisseur de stockage est le mieux adapté à vos besoins.

| Fournisseur de stockage | Stockage Azure | Netherite | MSSQL |
|- |-              |-          |-      |
| État du support officiel | ✅ Disponibilité générale (GA) | ⚠ Préversion publique | ⚠ Préversion publique |
| Dépendances externes | Un compte Stockage Azure (usage général v1) | Hubs d'événements Azure<br/>Un compte Stockage Azure (usage général) | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) ou Azure SQL Database |
| Options de développement et d’émulation locales | [Azurite v3.12+](../../storage/common/storage-use-azurite.md) (multiplateforme)<br/>[Émulateur de stockage Azure](../../storage/common/storage-use-emulator.md) (Windows uniquement) | Émulation en mémoire ([plus d’informations](https://microsoft.github.io/durabletask-netherite/#/emulation)) | SQL Server Développeur (prend en charge les conteneurs [Windows](/sql/database-engine/install-windows/install-sql-server), [Linux](/sql/linux/sql-server-linux-setup) et [Docker](/sql/linux/sql-server-linux-docker-container-deployment)) |
| Configuration du hub de tâches | Explicit (Explicite) | Explicit (Explicite) | Implicite par défaut ([plus d’informations](https://microsoft.github.io/durabletask-mssql/#/taskhubs)) |
| Débit maximal | Modéré | Très élevée | Modéré |
| Taille maximale de l’orchestration/de l’entité (nœuds) | 16 | 32 | N/A |
| Taille maximale de l’activité (nœuds) | N/A | 32 | N/A |
| Prise en charge du plan de consommation | ✅ Entièrement pris en charge | ❌ Non pris en charge | ❌ Non pris en charge |
| Prise en charge du plan Élastique Premium | ✅ Entièrement pris en charge | ⚠ Requiert la [supervision de l’échelle de runtime](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) | ⚠ Requiert la [supervision de l’échelle de runtime](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) |
| Prise en charge de la mise à l’échelle de [KEDA 2.0](https://keda.sh/)<br/>([plus d’informations](../functions-kubernetes-keda.md)) | ❌ Non pris en charge | ❌ Non pris en charge | ✅ Pris en charge avec l’[outil de mise à l’échelle MSSQL](https://keda.sh/docs/scalers/mssql/) ([plus d’informations](https://microsoft.github.io/durabletask-mssql/#/scaling)) |
| Prise en charge des [packs d’extensions](../functions-bindings-register.md#extension-bundles) (recommandé pour les applications hors .NET) | ✅ Entièrement pris en charge | ❌ Non pris en charge | ❌ Non pris en charge |
| Prix-performances configurables ? | ❌ Non | ✅ Oui (TU et CU Event Hubs) | ✅ Oui (processeurs virtuels SQL) |
| Prise en charge des environnements déconnectés | ❌ Connectivité Azure requise | ❌ Connectivité Azure requise | ✅ Entièrement pris en charge |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les performances et la mise à l’échelle de Durable Functions](durable-functions-perf-and-scale.md)
