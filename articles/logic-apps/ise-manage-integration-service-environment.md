---
title: Gérer les environnements de service d’intégration dans Azure Logic Apps
description: Vérifiez l’intégrité du réseau et gérez les applications logiques, les connexions, les connecteurs personnalisés et les comptes d’intégration dans votre environnement de service d’intégration pour Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517358"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gérez votre environnement de service d’intégration dans Azure Logic Apps

Pour vérifier l’intégrité du réseau pour votre [environnement d’intégration de service](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (ISE, integration service environment) et gérer les applications logiques, les connexions, les comptes d’intégration et les connecteurs personnalisés de votre environnement ISE, suivez les étapes décrites dans cette rubrique.

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

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Applications logiques**.

   ![Rechercher les applications logiques](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pour supprimer des applications logiques de votre environnement ISE quand vous n’en avez plus besoin, sélectionnez-les, puis sélectionnez **Supprimer**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gérer les connexions d’API

1. Pour voir les connexions d’API qui ont été créées par les applications logiques exécutées dans votre environnement ISE, dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connexions d’API**.

   ![Rechercher les connexions d’API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pour supprimer des connexions de votre environnement ISE quand vous n’en avez plus besoin, sélectionnez-les, puis sélectionnez **Supprimer**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gérer les connecteurs personnalisés

1. Pour voir les connecteurs personnalisés qui ont été créés dans votre environnement ISE, dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs personnalisés**.

   ![Trouver des connecteurs personnalisés](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pour supprimer des connecteurs personnalisés de votre environnement ISE quand vous n’en avez plus besoin, sélectionnez-les, puis sélectionnez **Supprimer**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gérer les comptes d’intégration

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Comptes d’intégration**.

   ![Cherchez des comptes d’intégration](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Pour supprimer des comptes d’intégration de votre environnement ISE quand vous n’en avez plus besoin, sélectionnez-les, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [se connecter à des réseaux virtuels à partir d’applications logiques isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
