---
title: Surveiller votre compte Azure Cosmos DB pour les mises à jour et la régénération de clés
description: Utilisez la métrique Clés de compte mises à jour pour surveiller les mises à jour des clés de votre compte. Cette métrique indique le nombre de fois où les clés primaires et secondaires sont mises à jour pour un compte et l’heure à laquelle elles ont été modifiées.
ms.service: cosmos-db
ms.topic: how-to
ms.author: sngun
author: SnehaGunda
ms.date: 09/16/2021
ms.openlocfilehash: fe344bdfbd9d4c88fd5cdf4024052ac122cf0e5d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602448"
---
# <a name="monitor-your-azure-cosmos-db-account-for-key-updates-and-key-regeneration"></a>Surveiller votre compte Azure Cosmos DB pour les mises à jour et la régénération de clés

Azure Monitor pour Azure Cosmos DB fournit des métriques, des alertes et des journaux pour surveiller votre compte. Vous pouvez créer des tableaux de bord et les personnaliser selon vos besoins. Vous n’avez pas besoin d’activer ni de configurer quoi que ce soit de manière explicite, car les métriques d’Azure Cosmos DB sont collectées par défaut. Pour surveiller la mise à jour des clés de votre compte, utilisez la métrique **Clés de compte mises à jour**. Cette métrique indique le nombre de fois où les clés primaires et secondaires sont mises à jour pour un compte et l’heure à laquelle elles ont été modifiées. Vous pouvez également configurer des alertes pour recevoir des notifications lors de la mise à jour d’une clé.

## <a name="monitor-key-updates-with-metrics"></a>Surveiller les mises à jour de clés à l’aide de métriques

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Surveillance** dans la barre de navigation gauche, puis sélectionnez **Métriques**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Volet Métriques dans Azure Monitor" border="true":::

1. Dans le volet **Métriques**, sélectionnez l’étendue de la ressource dont vous souhaitez afficher les métriques.

   1. Tout d’abord, choisissez l’**abonnement** requis et, pour le champ **Type de ressource**, sélectionnez **Comptes Azure Cosmos DB**. La liste des groupes de ressources où se trouvent les comptes Azure Cosmos DB s’affiche.

   1. Choisissez un **groupe de ressources** et sélectionnez l’un de vos comptes Azure Cosmos existants. Sélectionnez Appliquer.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="Sélectionner l’étendue du compte pour afficher les métriques" border="true":::

1. Pour le champ **Métrique**, choisissez la métrique **Clés de compte mises à jour**. Laissez le champ **Agrégation** définit sur la valeur par défaut **Nombre**. Cet affichage indique le nombre total de mises à jour de la clé primaire et secondaire pour le compte sélectionné. Vous pouvez également sélectionner une chronologie dans le graphique et afficher la date et l’heure de mise à jour de la clé.

1. Pour voir plus précisément quelle clé a été modifiée, sélectionnez l’option **Appliquer le fractionnement**. Sélectionnez **KeyType** et définissez les propriétés **Limite** et **Tri**. Le graphique est maintenant fractionné par les mises à jour de la clé primaire et secondaire, comme illustré dans l’image suivante :

   :::image type="content" source="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png" alt-text="Graphique métrique lorsque les clés primaire et secondaire sont mises à jour" border="true" lightbox="./media/monitor-account-key-updates/account-keys-updated-metric-chart.png":::

## <a name="configure-alerts-for-a-key-update"></a>Configurer des alertes pour une mise à jour de clé

Vous souhaiterez peut-être surveiller les mises à jour de clé d’un compte. Lorsqu’une clé est renouvelée ou mise à jour, il est nécessaire de mettre à jour les applications clientes dépendantes pour qu’elles puissent continuer à fonctionner. En configurant des alertes, vous pouvez recevoir des notifications lors de la mise à jour d’une clé.

Suivez les instructions de l’article [Créer une alerte](create-alerts.md) avec quelques modifications. Lorsque vous sélectionnez la condition d’alerte, choisissez le signal **Clés de compte mises à jour**. Sélectionnez la dimension **KeyType** et choisissez la clé **Primaire** ou **Secondaire**. En fonction du type de clé que vous sélectionnez, une alerte est déclenchée lors de la mise à jour de la clé.

La capture d’écran suivante montre comment configurer une alerte de type critique lorsque les clés de compte sont mises à jour :

:::image type="content" source="./media/monitor-account-key-updates/configure-key-update-alert.png" alt-text="Configurer une alerte pour recevoir une notification par e-mail lors de la mise à jour de clés de compte":::

## <a name="next-steps"></a>Étapes suivantes

* Superviser les données d’Azure Cosmos DB en utilisant des [paramètres de diagnostic](cosmosdb-monitor-resource-logs.md) dans Azure.
* [Guide pratique pour auditer les opérations de plan de contrôle Azure Cosmos DB](audit-control-plane-logs.md)