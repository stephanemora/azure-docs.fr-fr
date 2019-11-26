---
title: Archiver des journaux de ressource Azure dans un compte de stockage | Microsoft Docs
description: Découvrez comment archiver vos journaux de ressource Azure pour une conservation à long terme dans un compte de stockage.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 306f6cb0b50b7befcbf51e6164a5da887d35616e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030888"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiver des journaux de ressource Azure dans un compte de stockage
Les [journaux de ressource](resource-logs-overview.md) dans Azure fournissent des informations complètes et fréquentes sur le fonctionnement interne d’une ressource Azure. Cet article décrit la collecte de journaux de ressource dans un compte de stockage Azure en vue de conserver les données pour archivage.

## <a name="prerequisites"></a>Prérequis
Vous devez [créer un compte de stockage Azure](../../storage/common/storage-quickstart-create-account.md) si vous n’en avez pas encore un. Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que la ressource qui envoie des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.


> [!IMPORTANT]
> Actuellement, les comptes ADLS Gen2 ne sont pas pris en charge en tant que destination pour les paramètres de diagnostic, même s’ils peuvent être répertoriés comme une option valide dans le portail Azure.


Nous vous déconseillons d'utiliser un compte de stockage existant sur lequel sont stockées d’autres données de non-analyse, afin de pouvoir mieux contrôler l’accès aux données d’analyse. Si vous archivez également le [journal d’activité](activity-logs-overview.md) sur un compte de stockage cependant, vous pouvez choisir d’utiliser ce même compte pour regrouper toutes vos données de surveillance au même emplacement.

## <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic
Les journaux de ressource ne sont pas collectés par défaut. Collectez-les dans un compte de stockage Azure et dans d’autres destinations en créant un paramètres de diagnostic pour une ressource Azure. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter des journaux et des métriques dans Azure](diagnostic-settings.md).


## <a name="data-retention"></a>Conservation des données
La stratégie de rétention définit le nombre de jours de conservation des données de chaque catégorie de journal et des données métriques stockées dans un compte de stockage. Une stratégie de conservation peut être définie sur n’importe quel nombre de jours entre 0 et 365. Une stratégie de rétention définie sur zéro indique que les événements de cette catégorie de journal sont stockés indéfiniment.

Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Schéma des journaux de ressource dans un compte de stockage

Une fois que vous avez créé le paramètre de diagnostic, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement se produit dans les catégories de journaux activées. Les objets blob présents dans le conteneur utilisent la convention d’affectation de noms suivante :

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Par exemple, l’objet blob d’un groupe de sécurité réseau peut être nommé de la façon suivante :

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Chaque objet blob PT1H.json contient un objet blob d’événements JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h=12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m=00) est toujours 00, car les événements du journal de ressource sont divisés en objets blob par heure.

Dans le fichier PT1H.json, chaque événement est stocké au format suivant. Il utilise un schéma de niveau supérieur courant mais est unique pour chaque service Azure comme décrit dans [Schéma des journaux de ressource](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Les journaux de plateforme sont écrits dans le stockage d’objets blob à l’aide de [lignes JSON](http://jsonlines.org/), où chaque événement correspond à une ligne et le caractère de nouvelle ligne indique un nouvel événement. Ce format a été implémenté en novembre 2018. Avant cette date, les journaux étaient écrits dans le stockage d’objets blob sous forme de tableau d’enregistrements json, comme décrit dans l’article [Préparer le changement de format des journaux de plateforme Azure Monitor archivés dans un compte de stockage](resource-logs-blob-format.md).

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des objets blob pour analyse](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Archiver des journaux d’activité Azure Active Directory avec Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Découvrez plus d’informations sur les journaux de ressource](../../azure-monitor/platform/resource-logs-overview.md)

