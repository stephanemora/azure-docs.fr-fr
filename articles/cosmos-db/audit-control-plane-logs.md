---
title: Guide pratique pour auditer les opérations de plan de contrôle Azure Cosmos DB
description: Découvrez comment auditer les opérations de plan de contrôle telles que l’ajout d’une région, la mise à jour du débit, le basculement de région, l’ajout d’un réseau virtuel, etc., dans Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420295"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Guide pratique pour auditer les opérations de plan de contrôle Azure Cosmos DB

Les opérations de plan de contrôle incluent les modifications apportées au compte ou au conteneur Azure Cosmos. Par exemple, la création d’un compte Azure Cosmos, l’ajout d’une région, la mise à jour du débit, le basculement de région, l’ajout d’un réseau virtuel, etc., sont des opérations de plan de contrôle. Cet article explique comment auditer les opérations de plan de contrôle dans Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Désactiver l’accès en écriture aux métadonnées basé sur les clés
 
Avant d’auditer les opérations de plan de contrôle dans Azure Cosmos DB, désactivez l’accès en écriture aux métadonnées basé sur les clés sur votre compte. Quand l’accès en écriture aux métadonnées basé sur les clés est désactivé, les clients qui se connectent au compte Azure Cosmos par le biais de clés de compte ne peuvent pas accéder au compte. Vous pouvez désactiver l’accès en écriture en affectant à la propriété `disableKeyBasedMetadataWriteAccess` la valeur true. Une fois cette propriété définie, les modifications apportées à une ressource ne peuvent être effectuées que par un utilisateur qui a le rôle de contrôle d’accès en fonction du rôle (RBAC) et les informations d’identification appropriés. Pour en savoir plus sur la définition de cette propriété, consultez l’article [Prévention des modifications à partir des SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk).

 Tenez compte des points suivants lors de la désactivation de l’accès en écriture aux métadonnées :

* Vérifiez que vos applications n’effectuent pas d’appels de métadonnées qui changent les ressources mentionnées plus haut (par exemple, création d’une collection, mise à jour du débit, etc.) à l’aide du SDK ou de clés de compte.

* Comme le portail Azure utilise des clés de compte pour les opérations de métadonnées, ces opérations sont bloquées. Vous pouvez également utiliser Azure CLI, les kits SDK ou les déploiements de modèle Resource Manager pour effectuer ces opérations.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Activer les journaux de diagnostic pour les opérations de plan de contrôle

Vous pouvez activer les journaux de diagnostic pour les opérations de plan de contrôle à l’aide du portail Azure. Effectuez les étapes suivantes pour activer la journalisation sur les opérations du plan de contrôle :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte Azure Cosmos.

1. Ouvrez le volet **Paramètres de diagnostic**, puis fournissez un **Nom** pour les journaux à créer.

1. Sélectionnez **ControlPlaneRequests** comme type de journal et sélectionnez l’option **Envoyer à Log Analytics**.

Vous pouvez également stocker les journaux dans un compte de stockage ou les transmettre à un hub d’événements. Cet article explique comment envoyer des journaux à Log Analytics, puis les interroger. Une fois l’activation effectuée, la prise en compte des journaux de diagnostic prend quelques minutes. Toutes les opérations de plan de contrôle effectuées par la suite peuvent faire l’objet d’un suivi. La capture d’écran suivante montre comment activer les journaux du plan de contrôle :

![Activer la journalisation des demandes du plan de contrôle](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Afficher les opérations de plan de contrôle

Après avoir activé la journalisation, suivez les étapes ci-dessous pour effectuer le suivi des opérations d’un compte spécifique :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez l’onglet **Superviser** à partir de la barre de navigation de gauche, puis sélectionnez le volet **Journaux**. Une interface utilisateur s’ouvre, dans laquelle vous pouvez facilement exécuter des requêtes avec ce compte spécifique dans l’étendue. Exécutez la requête suivante pour voir les journaux du plan de contrôle :

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

La capture d’écran suivante montre les journaux générés lors de l’ajout d’un réseau virtuel à un compte Azure Cosmos :

![Journaux du plan de contrôle lors de l’ajout d’un réseau virtuel](./media/audit-control-plane-logs/add-ip-filter-logs.png)

La capture d’écran suivante montre les journaux générés lors de la mise à jour du débit d’une table Cassandra :

![Journaux du plan de contrôle lors de la mise à jour du débit](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifier l’identité associée à une opération spécifique

Si vous souhaitez effectuer un débogage supplémentaire, vous pouvez identifier une opération spécifique dans le **journal d’activité** à l’aide de l’ID d’activité ou de l’horodatage de l’opération. L’horodatage est utilisé pour certains clients Resource Manager dans les cas où l’ID d’activité n’est pas explicitement passée. Le journal d’activité fournit des détails sur l’identité sous laquelle l’opération a été lancée. La capture d’écran suivante montre comment utiliser l’ID d’activité et rechercher les opérations qui lui sont associées dans le journal d’activité :

![Utiliser l’ID d’activité et rechercher les opérations](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Étapes suivantes

* [Explorer Azure Monitor pour Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Superviser et déboguer à l’aide de métriques dans Azure Cosmos DB](use-metrics.md)