---
title: Créer et supprimer des points de terminaison privés managés dans un cluster Azure Stream Analytics
description: Apprenez à gérer des points de terminaison privés dans un cluster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718401"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Créer et supprimer des points de terminaison privés managés dans un cluster Azure Stream Analytics

Vous pouvez connecter les travaux Azure Stream Analytics exécutés sur un cluster à des ressources d'entrée et de sortie situées derrière un pare-feu ou un réseau virtuel Azure. Commencez par créer un point de terminaison privé managé pour une ressource, telle qu’Azure Event Hub ou Azure SQL Database, dans votre cluster Stream Analytics. Approuvez ensuite la connexion du point de terminaison privé à partir de votre entrée ou de votre sortie.

Une fois la connexion approuvée, tout travail en cours d’exécution dans votre cluster Stream Analytics peut accéder à la ressource via le point de terminaison privé. Cet article explique comment créer et supprimer des points de terminaison privés dans un cluster Azure Stream Analytics. Vous pouvez créer des points de terminaison privés pour Azure SQL Database, Stockage Azure, Azure Data Lake Storage Gen2, Azure Event Hub et Azure Service Bus. Les points de terminaison privés pour d’autres services seront bientôt ajoutés. 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Créer un point de terminaison privé managé dans un cluster Stream Analytics

Dans cette section, vous allez apprendre à créer un point de terminaison privé dans un cluster Stream Analytics.

1. Sur le portail Azure, recherchez et sélectionnez votre cluster Stream Analytics.

1. Sous **Paramètres**, sélectionnez **Points de terminaison privés managés**.

1. Sélectionnez **Nouveau** et entrez les informations suivantes pour choisir la ressource à laquelle vous souhaitez accéder en toute sécurité via un point de terminaison privé.

   |Paramètre|Value|
   |---|---|
   |Nom|Entrez un nom pour votre point de terminaison privé. Si ce nom est utilisé, créez un nom unique.|
   |Méthode de connexion|Sélectionnez **Se connecter à une ressource Azure dans mon répertoire**.<br><br>Vous pouvez choisir parmi vos ressources celle à laquelle vous souhaitez vous connecter en toute sécurité à l'aide du point de terminaison privé, ou vous pouvez vous connecter à la ressource d'une autre personne en utilisant un ID de ressource ou un alias qu'elle a partagé avec vous.|
   |Abonnement|Sélectionnez votre abonnement.|
   |Type de ressource|Choisissez le [type de ressource qui correspond à votre ressource](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Ressource|Sélectionnez la ressource à laquelle vous souhaitez vous connecter à l'aide d'un point de terminaison privé.|
   |Sous-ressource cible|Type de sous-ressource de la ressource sélectionnée ci-dessus à laquelle votre point de terminaison privé peut accéder.|

   ![Expérience de création de point de terminaison privé](./media/private-endpoints/create-private-endpoint.png)

1. Approuvez la connexion à partir de la ressource cible. Par exemple, si vous avez créé un point de terminaison privé sur une instance d'Azure SQL Database à l'étape précédente, vous devez accéder à cette instance et voir une connexion en attente qui doit être approuvée. L'affichage de la demande de connexion peut prendre quelques minutes.

    ![Approuver un point de terminaison privé](./media/private-endpoints/approve-private-endpoint.png)

1. Vous pouvez revenir à votre cluster Stream Analytics ; en quelques minutes, vous verrez alors l'état passer de **Approbation du client en attente** à **Configuration DNS en attente** puis à **Configuration terminée**.

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Supprimer un point de terminaison privé managé dans un cluster Stream Analytics

1. Sur le portail Azure, recherchez et sélectionnez votre cluster Stream Analytics.

1. Sous **Paramètres**, sélectionnez **Points de terminaison privés managés**.

1. Choisissez le point de terminaison privé à supprimer et sélectionnez **Supprimer**.

   ![Supprimer un point de terminaison privé](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant d'une vue d'ensemble de la gestion des points de terminaison privés dans un cluster Azure Stream Analytics. Vous pouvez ensuite apprendre à mettre vos clusters à l'échelle et à exécuter des travaux dans votre cluster :

* [Mettre à l'échelle un cluster Azure Stream Analytics](scale-cluster.md)
* [Gérer des travaux Stream Analytics dans un cluster Stream Analytics](manage-jobs-cluster.md)
