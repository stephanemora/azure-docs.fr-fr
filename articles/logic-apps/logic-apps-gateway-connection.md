---
title: Accéder à des sources de données locales à partir d’Azure Logic Apps
description: Se connecter à des sources de données locales à partir d’applications logiques en créant une passerelle de données locale
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 09/01/2019
ms.openlocfilehash: dcb0fe4da968408a261e387c636cc548fa757a09
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036656"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Se connecter à des sources de données locales à partir d’Azure Logic Apps

Pour accéder à des sources de données locales à partir de vos applications logiques, créez une ressource de passerelle de données locales dans le portail Azure. Vos applications logiques peuvent ensuite utiliser les [connecteurs locaux](../connectors/apis-list.md#on-premises-connectors). Cet article explique comment créer votre ressource de passerelle Azure *après* avoir [téléchargé et installé la passerelle sur un ordinateur local](../logic-apps/logic-apps-gateway-install.md). Pour obtenir des informations détaillées sur la passerelle, consultez [Fonctionnement de la passerelle](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Pour vous connecter à des réseaux virtuels Azure, envisagez de créer un [*environnement de service d’intégration*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) à la place. 

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

* [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Pour Azure Logic Apps, la passerelle de données locale prend en charge des [connecteurs locaux](../connectors/apis-list.md#on-premises-connectors) pour les sources de données suivantes :

* BizTalk Server 2016
* Système de fichiers
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Bien que la passerelle proprement dite n’entraîne pas de frais supplémentaires, le [modèle tarifaire de Logic Apps](../logic-apps/logic-apps-pricing.md) s’applique à ces connecteurs et à d’autres opérations dans Azure Logic Apps.

## <a name="prerequisites"></a>Prérequis

* La passerelle de données locale doit être déjà [installée sur un ordinateur local](../logic-apps/logic-apps-gateway-install.md).

* Vous avez le [même compte Azure et le même abonnement Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que ceux que vous avez utilisés lors de l’installation de la passerelle de données locale.

* Vous n’avez pas encore lié l’installation de votre passerelle à une autre ressource de passerelle dans Azure.

  Lorsque vous créez une ressource de passerelle, vous sélectionnez une installation de passerelle à associer à votre ressource de passerelle. Vous ne pouvez pas sélectionner une installation de passerelle déjà liée lorsque vous créez des ressources de passerelle.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Créer une ressource de passerelle Azure

Après avoir installé la passerelle sur un ordinateur local, créez une ressource Azure pour votre passerelle. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec le même compte Azure que celui utilisé pour installer la passerelle.

1. Dans la fenêtre de recherche du portail Azure, entrez « passerelles de données locales », puis choisissez **Passerelles de données locales**.

   ![Cherchez « Passerelle de données locale »](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Sous **Passerelles de données locales**, sélectionnez **Ajouter**.

   ![Ajouter une passerelle de données](./media/logic-apps-gateway-connection/add-gateway.png)

1. Sous **Créer une passerelle de connexion**, fournissez ces informations pour votre ressource de passerelle. Sélectionnez **Créer** lorsque vous avez terminé.

   | Propriété | Description |
   |----------|-------------|
   | **Nom de la ressource** | Nom de votre ressource de passerelle, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`) des traits de soulignement (`_`), des parenthèses (`(`, `)`) et des points (`.`). |
   | **Abonnement** | Votre abonnement Azure, qui doit être identique à celui de l’installation de la passerelle et de l’application logique. L’abonnement par défaut est basé sur le compte Azure que vous avez utilisé pour vous connecter. |
   | **Groupe de ressources** | Le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) que vous souhaitez utiliser |
   | **Lieu** | La même région que l’emplacement sélectionné pour le service cloud de la passerelle pendant [l’installation de la passerelle](../logic-apps/logic-apps-gateway-install.md). Autrement, votre installation de passerelle ne s’affichera pas dans la liste **Nom d’installation** pour sélection. L’emplacement de votre application logique peut être différent de l’emplacement de votre ressource de passerelle. |
   | **Nom de l’installation** | Si l’installation de votre passerelle n’est pas encore sélectionnée, sélectionnez la passerelle que vous avez installée précédemment. Les installations de passerelle précédemment liées ne s’afficheront pas dans la liste pour sélection. |
   |||

   Voici un exemple :

   ![Fournir des détails pour créer votre passerelle de données locale](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Se connecter à des données locales

Après avoir créé votre ressource de passerelle et avoir associé votre abonnement Azure à cette ressource, vous pouvez maintenant créer une connexion entre votre application logique et votre source de données locale à l’aide de la passerelle.

1. Dans le portail Azure, créez ou ouvrez votre application logique dans le Concepteur d’application logique.

1. Ajoutez un connecteur prenant en charge des connexions locales, tel que **SQL Server**.

1. Sélectionnez l’option **Se connecter via la passerelle de données locale**. 

1. Pour **Passerelles**, sélectionnez la ressource de passerelle que vous avez créée.

   > [!NOTE]
   > La liste de passerelles comprend les ressources de passerelle dans d’autres régions, car l’emplacement de votre application logique peut différer de l’emplacement de votre ressource de passerelle.

1. Fournissez un nom de connexion unique et les autres informations requises, qui dépendent de la connexion que vous souhaitez créer.

   Un nom de connexion unique vous aidera à trouver facilement cette connexion ultérieurement, en particulier si vous créez plusieurs connexions. Le cas échéant, incluez également le nom de domaine complet dans votre nom d’utilisateur.
   
   Voici un exemple :

   ![Créer une connexion entre une application logique et une passerelle de données](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Sélectionnez **Créer** lorsque vous avez terminé. 

Votre connexion de passerelle est maintenant prête et votre application logique peut l’utiliser.

## <a name="edit-connection"></a>Modifier la connexion

Pour mettre à jour les paramètres pour une connexion de passerelle, vous pouvez modifier votre connexion.

1. Pour rechercher toutes les connexions d’API pour votre application logique uniquement, dans le menu de votre application logique, sous **Outils de développement**, sélectionnez **Connexions d’API**.
   
   ![Accédez au menu de votre application logique, puis sélectionnez « Connexions d’API »](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Sélectionnez la connexion de passerelle que vous souhaitez, puis sélectionnez **Modifier une connexion d’API**.

   > [!TIP]
   > Si vos mises à jour ne prennent pas effet, essayez d’[arrêter et redémarrer le compte de service Windows de passerelle](../logic-apps/logic-apps-gateway-install.md#restart-gateway) pour votre installation de passerelle.

Pour rechercher toutes les connexions d’API associées à votre abonnement Azure : 

* Dans le menu principal Azure, accédez à **Tous les services** > **Web** > **Connexions d’API**.
* Ou, dans le menu Azure principal, accédez à **Toutes les ressources**. Réglez le filtre **Type** sur **Connexion d’API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Supprimer la ressource de passerelle

Pour créer une autre ressource de passerelle, lier l’installation de votre passerelle à une autre ressource de passerelle ou supprimer la ressource de passerelle, vous pouvez effectivement supprimer celle-ci sans que cela affecte l’installation de la passerelle. 

1. Dans le menu principal Azure, sélectionnez **Toutes les ressources**. Cherchez et sélectionnez votre ressource de passerelle.

1. Le cas échéant, dans votre menu de ressource de passerelle, sélectionnez **Passerelle de données locale**. Dans la barre d’outils de ressources de la passerelle, sélectionnez **Supprimer**.

   Par exemple :

   ![Supprimer une passerelle](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q** : Pourquoi ne puis-je pas voir mon installation de la passerelle lorsque je crée la ressource de passerelle dans Azure ? <br/>
**R** : Ce problème peut se produire pour les raisons suivantes :

* L’installation de votre passerelle est déjà enregistrée et revendiquée par une autre ressource de passerelle dans Azure. Les installations de passerelle ne s’affichent pas dans la liste des instances après la création de ressources de passerelle pour elles. Pour vérifier vos inscriptions de passerelle dans le portail Azure, passez en revue toutes vos ressources Azure avec le type de **Passerelles de données locales** pour *tous* les abonnements Azure.

* L’identité Azure AD octroyée à la personne qui a installé la passerelle diffère de celle de la personne qui s’est connectée au portail Azure. Vérifiez que vous êtes connecté avec la même identité que celle qui a installé la passerelle.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser vos applications logiques](./logic-apps-securing-a-logic-app.md)
* [Exemples et scénarios courants pour les applications logiques](./logic-apps-examples-and-scenarios.md)
