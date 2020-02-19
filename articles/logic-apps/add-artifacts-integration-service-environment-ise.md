---
title: Ajouter des artefacts à des environnements de service d’intégration
description: Ajoutez des applications logiques, des comptes d’intégration, des connecteurs personnalisés et des connecteurs managés à votre environnement de service d’intégration.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e2505d8ee8b8539f158c0a549bedfcd69a954e24
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191650"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Ajoutez des artefacts à votre environnement de service d’intégration dans Azure Logic Apps

Après avoir créé un [environnement de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (ISE, Integration Service Environment), ajoutez des artefacts tels que des applications logiques, des comptes d’intégration et des connecteurs pour qu’ils puissent accéder aux ressources de votre réseau virtuel Azure. Par exemple, les connecteurs ISE managés qui deviennent disponibles une fois que vous avez créé votre ISE n’apparaissent pas automatiquement dans le concepteur d’applications logiques. Avant de pouvoir utiliser ces connecteurs ISE, vous devez manuellement [ajouter et déployer ces connecteurs dans votre ISE](#add-ise-connectors-environment) afin qu’ils apparaissent dans le concepteur d’applications logiques.

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’environnement ISE que vous avez créé pour exécuter vos applications logiques. Si vous ne disposez pas d’un environnement ISE, commencez par [créer cet environnement](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Créer des applications logiques

Pour créer des applications logiques qui s’exécutent dans votre environnement ISE, effectuez les étapes suivantes :

1. Recherchez et ouvrez votre environnement ISE, s’il n’est pas encore ouvert. Dans le menu de l’environnement ISE, sous **Paramètres**, sélectionnez **Applications logiques** > **Ajouter**.

   ![Ajouter une nouvelle application logique à l’environnement ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Fournissez des informations sur l’application logique que vous souhaitez créer, par exemple :

   ![Sélection d’un environnement de service d’intégration](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom** | Oui | Nom de l’application logique à créer |
   | **Abonnement** | Oui | Nom de l’abonnement Azure à utiliser. |
   | **Groupe de ressources** | Oui | Nom du groupe de ressources Azure (nouveau ou existant) à utiliser |
   | **Lieu** | Oui | Sous **Environnements de service d’intégration**, sélectionnez l’ISE à utiliser, s’il n’est pas déjà sélectionné. <p><p> **Important !** Pour utiliser vos applications logiques avec un compte d’intégration, tous doivent utiliser le même ISE. |
   ||||

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. Continuez à [créer votre application logique de la façon habituelle](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Pour connaître les différences dans le fonctionnement des déclencheurs et des actions et dans la façon dont ils sont étiquetés lorsque vous utilisez un ISE par rapport au service global Logic Apps, consultez [Isolé ou global dans la vue d’ensemble de l’ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Pour gérer les applications logiques et les connexions d’API dans votre environnement ISE, consultez [Gérer votre environnement de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Créer des comptes d’intégration

En fonction de la [référence SKU d’environnement ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) sélectionnée lors de la création, votre environnement ISE comprend une utilisation de compte d’intégration spécifique sans coût supplémentaire. Les applications logiques qui existent dans un environnement ISE peuvent référencer uniquement des comptes d’intégration qui existent dans le même environnement ISE. Ainsi, pour qu’un compte d’intégration fonctionne avec des applications logiques dans un environnement ISE, le compte d’intégration et les applications logiques doivent utiliser le *même environnement* comme emplacement. Pour plus d’informations sur les comptes d’intégration et les environnements ISE, consultez [Comptes d’intégration et environnement de service d’intégration](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Pour créer un compte d’intégration qui utilise un environnement ISE, effectuez les étapes suivantes :

1. Recherchez et ouvrez votre environnement ISE, s’il n’est pas encore ouvert. Dans le menu de l’environnement ISE, sous **Paramètres**, sélectionnez **Comptes d’intégration** > **Ajouter**.

   ![Ajouter un nouveau compte d’intégration à un environnement ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Fournissez des informations sur l’application logique que vous souhaitez créer, par exemple :

   ![Sélection d’un environnement de service d’intégration](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom** | Oui | Nom du compte d’intégration que vous souhaitez créer |
   | **Abonnement** | Oui | Nom de l’abonnement Azure que vous souhaitez utiliser |
   | **Groupe de ressources** | Oui | Nom du groupe de ressources Azure (nouveau ou existant) à utiliser |
   | **Niveau tarifaire** | Oui | Niveau tarifaire à utiliser pour le compte d’intégration |
   | **Lieu** | Oui | Sous **Environnements de service d’intégration**, sélectionnez le même ISE que celui utilisé par vos applications logiques, s’il n’est pas déjà sélectionné. <p><p> **Important !** Pour utiliser votre compte d’intégration avec des applications logiques, tous doivent utiliser le même ISE. |
   ||||

1. Sélectionnez **Créer** lorsque vous avez terminé.

1. [Liez votre application logique à votre compte d’intégration de la façon habituelle](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continuez en ajoutant des artefacts à votre compte d’intégration, tels que des [parties](../logic-apps/logic-apps-enterprise-integration-partners.md) et des [contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Pour gérer les comptes d’intégration dans votre environnement ISE, consultez [Gérer votre environnement de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Ajouter des connecteurs ISE

Les connecteurs gérés par Microsoft qui deviennent disponibles une fois que vous avez créé votre ISE n’apparaissent pas automatiquement dans le sélecteur de connecteur du concepteur d’applications logiques. Avant de pouvoir utiliser ces connecteurs ISE, vous devez manuellement ajouter et déployer ces connecteurs dans votre ISE afin qu’ils apparaissent dans le concepteur d’applications logiques.

1. Dans le menu de votre environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs managés**. Dans la barre d’outils, sélectionnez **Ajouter**.

   ![Voir les connecteurs managés](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Dans le volet **Ajouter un nouveau connecteur managé**, ouvrez la liste **Rechercher un connecteur**. Sélectionnez le connecteur ISE que vous souhaitez utiliser, mais qui n’est pas encore déployé dans votre ISE. Sélectionnez **Create** (Créer).

   ![Sélectionnez le connecteur ISE que vous souhaitez déployer dans votre ISE.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Seuls les connecteurs ISE qui sont admissibles, mais qui n’ont pas encore été déployés dans votre ISE apparaissent comme disponibles. Les connecteurs déjà déployés dans votre environnement ISE apparaissent comme étant indisponibles pour la sélection.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Créer des connecteurs personnalisés

Pour utiliser des connecteurs personnalisés dans votre environnement ISE, créez-les directement dans l’environnement.

1. Recherchez et ouvrez votre environnement ISE, s’il n’est pas encore ouvert. Dans le menu de l’environnement ISE, sous **Paramètres**, sélectionnez **Connecteurs personnalisés** > **Ajouter**.

   ![Créer un connecteur personnalisé](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Indiquez le nom, l’abonnement Azure et le groupe de ressources Azure (nouveau ou existant) à utiliser pour votre connecteur personnalisé.

1. Dans la liste **Emplacement**, sous la section **Environnements de service d’intégration**, sélectionnez le même environnement ISE que celui utilisé par vos applications logiques et sélectionnez **Créer**, par exemple :

   ![Sélection d’un environnement de service d’intégration](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Sélectionnez votre nouveau connecteur personnalisé, puis sélectionnez **Modifier**, par exemple :

   ![Sélectionner et modifier un connecteur personnalisé](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continuez en créant le connecteur de la manière habituelle à partir d’une [définition OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou du [protocole SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Pour gérer les connecteurs personnalisés dans votre environnement ISE, consultez [Gérer votre environnement de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les environnements de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md)
