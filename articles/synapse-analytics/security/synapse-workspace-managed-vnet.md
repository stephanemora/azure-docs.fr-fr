---
title: Réseau virtuel managé
description: Article qui explique ce qu’est un réseau virtuel managé dans Azure Synapse Analytics.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/18/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f55251932c8aa8f632bd3b498943ac722f006dee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569903"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Réseau virtuel managé Azure Synapse Analytics

Cet article explique ce qu’est un réseau virtuel managé dans Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Réseau virtuel d’espace de travail managé

Quand vous créez votre espace de travail Azure Synapse, vous pouvez choisir de l’associer à un réseau virtuel Microsoft Azure. Le réseau virtuel associé à votre espace de travail est managé par Azure Synapse. Ce réseau virtuel est appelé *réseau virtuel d’espace de travail managé*.

Le réseau virtuel d’espace de travail managé offre de la valeur de quatre manières :

- Avec un réseau virtuel d’espace de travail managé, vous pouvez déplacer la charge liée à la gestion du réseau virtuel vers Azure Synapse.
- Vous n’êtes pas obligé de configurer des règles de groupe de sécurité réseau entrantes sur vos propres réseaux virtuels pour autoriser le trafic de gestion Azure Synapse à pénétrer sur votre réseau virtuel. Une configuration incorrecte des règles de groupe de sécurité réseau entraîne une interruption du service pour les clients
- Vous n’avez pas besoin de créer de sous-réseau pour vos clusters Spark en fonction de la charge maximale
- Le réseau virtuel d’espace de travail managé, ainsi que les points de terminaison privés managés, assurent une protection contre l’exfiltration des données. Vous pouvez créer des points de terminaison privés managés uniquement dans un espace de travail associé à un réseau virtuel d’espace de travail managé.

La création d’un espace de travail avec un réseau virtuel d’espace de travail managé associé permet de garantir que le réseau de votre espace de travail est isolé de celui des autres espaces de travail. Azure Synapse fournit diverses fonctionnalités d’analytique dans un espace de travail : Intégration des données, pool Apache Spark serverless, pool SQL dédié et pool SQL serverless.

Si votre espace de travail a un réseau virtuel d’espace de travail managé, les ressources Spark et l’intégration de données sont déployées dans celui-ci. Un réseau virtuel d’espace de travail managé fournit également une isolation au niveau de l’utilisateur pour les activités Spark, car chaque cluster Spark est sur son propre sous-réseau.

Le pool SQL dédie et le pool SQL serverless sont des fonctionnalités multilocataires ; elles sont donc en dehors du réseau virtuel d’espace de travail managé. La communication intra-espace de travail avec le pool SQL dédié et le pool SQL serverless utilise des liaisons privées Azure. Ces liaisons privées sont créées automatiquement quand vous créez un espace de travail associé à un réseau virtuel d’espace de travail managé.

>[!IMPORTANT]
>Vous ne pouvez pas modifier cette configuration d’espace de travail après la création de l’espace de travail. Par exemple, vous ne pouvez pas reconfigurer un espace de travail qui n’est pas associé à un réseau virtuel d’espace de travail managé et lui associer un réseau virtuel. De même, vous ne pouvez pas reconfigurer un espace de travail qui est associé à un réseau virtuel d’espace de travail managé et le dissocier d’un réseau virtuel.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Créer un espace de travail Azure Synapse avec un réseau virtuel d’espace de travail managé

Si vous ne l’avez pas encore fait, inscrivez le fournisseur de ressources réseau. L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. Choisissez *Microsoft.Network* dans la liste des fournisseurs de ressources lors de votre [inscription](../../azure-resource-manager/management/resource-providers-and-types.md).

Pour créer un espace de travail Azure Synapse associé à un réseau virtuel d’espace de travail managé, sélectionnez l’onglet **Réseau** dans le portail Azure et cochez la case **Activer le réseau virtuel managé**.

Si vous ne cochez pas cette case, aucun réseau virtuel n’est associé à votre espace de travail.

>[!IMPORTANT]
>Vous pouvez utiliser des liaisons privées uniquement dans un espace de travail qui a un réseau virtuel d’espace de travail managé.

![Activer le réseau virtuel d’espace de travail managé](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

Après avoir choisi d’associer un réseau virtuel d’espace de travail managé à votre espace de travail, vous pouvez vous protéger contre l’exfiltration de données en autorisant la connectivité sortante allant uniquement du réseau virtuel de l’espace de travail managé vers les cibles approuvées, à l’aide de [points de terminaison privés managés](./synapse-workspace-managed-private-endpoints.md). Sélectionnez **Oui** pour limiter le trafic sortant en provenance du réseau virtuel d’espace de travail managé aux cibles uniquement, via des points de terminaison privés. 


>[!IMPORTANT]
>Le metastore est désactivé dans les espaces de travail Synapse disposant d’un réseau virtuel managé où la protection contre l’exfiltration des données est activée. Vous ne pourrez pas utiliser Spark SQL dans ces espaces de travail.

![Trafic sortant utilisant des points de terminaison privés managés](./media/synapse-workspace-managed-vnet/select-outbound-connectivity.png)

Sélectionnez **Non** pour autoriser le trafic sortant entre l’espace de travail et n’importe quelle cible.

Vous pouvez également contrôler les cibles sur lesquelles les points de terminaison privés managés sont créés à partir de votre espace de travail Azure Synapse. Par défaut, les points de terminaison privés managés des ressources qui se trouvent dans le même locataire AAD que votre abonnement sont autorisés. Si vous souhaitez créer un point de terminaison privé managé sur une ressource située dans un locataire AAD différent de celui auquel votre abonnement appartient, vous pouvez ajouter ce locataire AAD en sélectionnant **+ Ajouter**. Vous pouvez sélectionner le locataire AAD dans la liste déroulante ou entrer manuellement l’ID du locataire AAD.

![Ajouter des locataires AAD](./media/synapse-workspace-managed-vnet/add-additional-azure-active-directory-tenants.png)

Une fois l’espace de travail créé, vous pouvez vérifier si votre espace de travail Azure Synapse est associé à un réseau virtuel d’espace de travail managé en sélectionnant **Vue d’ensemble** dans le portail Azure.

![Vue d’ensemble d’espace de travail dans le portail Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Étapes suivantes

Créer un [espace de travail Azure Synapse](../quickstart-create-workspace.md)

En savoir plus sur les [points de terminaison privés managés](./synapse-workspace-managed-private-endpoints.md)

[Créer des points de terminaison privés managés à vos sources de données](./how-to-create-managed-private-endpoints.md)
