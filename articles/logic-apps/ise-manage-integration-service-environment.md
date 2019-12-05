---
title: Gérer les environnements de service d’intégration dans Azure Logic Apps
description: Vérifiez l’intégrité du réseau et gérez les applications logiques, les connexions, les connecteurs personnalisés et les comptes d’intégration dans votre environnement de service d’intégration pour Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792623"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gérez votre environnement de service d’intégration dans Azure Logic Apps

Pour vérifier l’intégrité du réseau pour votre [environnement de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (ISE, Integration Service Environment) et gérer les applications logiques, les connexions, les comptes d’intégration et les connecteurs de votre environnement ISE, suivez les étapes décrites dans cette rubrique. Pour ajouter ces artefacts à votre environnement ISE, consultez [Ajouter des artefacts à votre environnement de service d’intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Voir votre environnement ISE

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans la zone de recherche du portail, entrez « environnements de service d’intégration », puis sélectionnez **Environnements de service d’intégration**.

   ![Rechercher les environnements de service d’intégration](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Dans la liste des résultats, sélectionnez votre environnement de service d’intégration.

   ![Sélection d’un environnement de service d’intégration](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Passez aux sections suivantes pour rechercher des applications logiques, des connexions, des connecteurs ou des comptes d’intégration dans votre environnement ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Vérifier l’intégrité du réseau

Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Intégrité du réseau**. Ce volet présente l’état d’intégrité de vos sous-réseaux et les dépendances sortantes par rapport à d’autres services.

![Vérifier l’intégrité du réseau](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gérer les applications logiques

Vous pouvez voir et gérer les applications logiques qui se trouvent dans votre environnement ISE.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Applications logiques**.

   ![Voir les applications logiques](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pour supprimer les applications logiques dont vous n’avez plus besoin dans votre environnement ISE, sélectionnez-les, puis sélectionnez **Supprimer**. Pour confirmer la suppression, sélectionnez **Oui**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gérer les connexions d’API

Vous pouvez afficher et gérer les connexions qui ont été créées par les applications logiques qui s’exécutent dans votre environnement ISE.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connexions d’API**.

   ![Voir les connexions d’API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pour supprimer les connexions dont vous n’avez plus besoin dans votre environnement ISE, sélectionnez-les, puis sélectionnez **Supprimer**. Pour confirmer la suppression, sélectionnez **Oui**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gérer les connecteurs ISE

Vous pouvez voir et gérer les connecteurs d’API qui sont déployés dans votre environnement ISE.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs managés**.

   ![Voir les connecteurs managés](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Pour supprimer les connecteurs que vous ne souhaitez pas rendre disponibles dans votre environnement ISE, sélectionnez-les, puis sélectionnez **Supprimer**. Pour confirmer la suppression, sélectionnez **Oui**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gérer les connecteurs personnalisés

Vous pouvez voir et gérer les connecteurs personnalisés que vous avez déployés dans votre environnement ISE.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs personnalisés**.

   ![Trouver des connecteurs personnalisés](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pour supprimer les connecteurs personnalisés dont vous n’avez plus besoin dans votre environnement ISE, sélectionnez-les, puis sélectionnez **Supprimer**. Pour confirmer la suppression, sélectionnez **Oui**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gérer les comptes d’intégration

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Comptes d’intégration**.

   ![Cherchez des comptes d’intégration](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Pour supprimer des comptes d’intégration de votre environnement ISE quand vous n’en avez plus besoin, sélectionnez-les, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
