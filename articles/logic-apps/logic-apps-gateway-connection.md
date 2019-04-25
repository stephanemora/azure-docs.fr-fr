---
title: Accéder à des sources de données locales à partir d’Azure Logic Apps | Microsoft Docs
description: Se connecter à des sources de données locales à partir d’applications logiques en créant une passerelle de données locale
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 2b9e1c153c3fa9b17145eb6c3c8f3ed02e3bf40f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304067"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Se connecter à des sources de données locales à partir d’Azure Logic Apps

Pour accéder à des sources de données locales à partir de vos applications logiques, créez une ressource de passerelle de données locales dans le portail Azure. Vos applications logiques peuvent ensuite utiliser les [connecteurs locaux](../logic-apps/logic-apps-gateway-install.md#supported-connections). Cet article explique comment créer votre ressource de passerelle Azure*après* avoir [téléchargé et installé la passerelle sur votre ordinateur local](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Pour vous connecter à des réseaux virtuels Azure, envisagez de créer un [*environnement de service d’intégration*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) à la place. 

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

* [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Conditions préalables

* Vous avez déjà [téléchargé et installé la passerelle de données sur votre ordinateur local](../logic-apps/logic-apps-gateway-install.md).

* L’installation de votre passerelle n’est pas encore associée à la ressource de la passerelle Azure. Vous pouvez lier votre installation de passerelle uniquement à une ressource de passerelle, ce qui se produit lorsque vous créez la ressource de passerelle et que vous sélectionnez votre installation de passerelle. Cette liaison rend l’installation de la passerelle indisponible pour d’autres ressources.

* Lorsque vous vous connectez au portail Azure et que vous créez votre ressource de passerelle, vous devez utiliser le compte de connexion qui a été utilisé pour [installer la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md#requirements), ainsi que le même [abonnement Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) qui a été utilisé pour installer la passerelle. Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Pour créer et gérer les ressources de la passerelle dans le portail Azure, votre [compte de service Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) doit disposer au moins des autorisations de **Contributeur**. La passerelle de données locale fonctionne comme un service Windows et elle est configurée pour utiliser `NT SERVICE\PBIEgwService` comme informations d’identification pour la connexion au service Windows. 

  > [!NOTE]
  > Le compte de service Windows diffère du compte utilisé pour la connexion à des sources de données locales, et du compte professionnel ou scolaire Azure utilisé pour se connecter aux services cloud.

## <a name="download-and-install-gateway"></a>Télécharger et installer la passerelle

Avant de continuer avec les étapes décrites dans cet article, vérifiez qu’une passerelle est déjà installée sur l’ordinateur local.
Si ce n’est déjà fait, suivez les étapes pour [télécharger et installer la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Créer une ressource Azure pour la passerelle

Après avoir installé la passerelle sur un ordinateur local, vous devez ensuite créer une ressource Azure pour votre passerelle. Cette étape associe également votre ressource de passerelle à votre abonnement Azure.

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">Portail Azure</a>. Veillez à utiliser la même adresse professionnelle ou scolaire Azure que celle utilisée pour installer la passerelle.

2. Dans le menu principal Azure, choisissez **Créer une ressource** > 
**Intégration** > **Passerelle de données locale**.

   ![Cherchez « Passerelle de données locale »](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Dans la page **Créer une passerelle de connexion**, fournissez ces informations pour votre ressource de passerelle :

   | Propriété | Description | 
   |----------|-------------|
   | **Name** | Le nom pour votre ressource de passerelle | 
   | **Abonnement** | Le nom de votre abonnement Azure, qui doit être le même que celui de votre application logique. L’abonnement par défaut est basé sur le compte Azure que vous avez utilisé pour vous connecter. | 
   | **Groupe de ressources** | Le nom du [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) pour organiser les ressources connexes | 
   | **Lieu** | Azure limite cet emplacement à la région sélectionnée pour le service cloud de la passerelle pendant [l’installation de la passerelle](../logic-apps/logic-apps-gateway-install.md). <p>**Remarque**: Assurez-vous que l’emplacement de la ressource de passerelle correspond à l’emplacement du service cloud de celle-ci. Autrement, votre installation de passerelle pourrait ne pas figurer dans la liste des passerelles installées dans laquelle vous allez opérer une sélection à l’étape suivante. Vous pouvez utiliser des régions différentes pour votre ressource de passerelle et votre application logique. | 
   | **Nom de l’installation** | Si l’installation de votre passerelle n’est pas encore sélectionnée, sélectionnez la passerelle que vous avez installée précédemment. | 
   | | | 

   Voici un exemple : 

   ![Fournir des détails pour créer votre passerelle de données locale](./media/logic-apps-gateway-connection/createblade.png)

4. Pour ajouter la ressource de passerelle à votre tableau de bord Azure, choisissez **Épingler au tableau de bord**. Lorsque vous êtes prêt, choisissez **Créer**.

   Pour rechercher ou consulter votre passerelle de données à tout moment, dans le menu principal Azure, choisissez **Tous les services**. 
   Dans la fenêtre de recherche, entrez « passerelles de données locales », puis choisissez **Passerelles de données locales**.

   ![Recherche sur « Passerelle de données locale »](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Se connecter à des données locales

Après avoir créé votre ressource de passerelle et avoir associé votre abonnement Azure à cette ressource, vous pouvez maintenant créer une connexion entre votre application logique et votre source de données locale à l’aide de la passerelle.

1. Dans le portail Azure, créez ou ouvrez votre application logique dans le Concepteur d’application logique.

2. Ajoutez un connecteur prenant en charge des connexions locales, tel que **SQL Server**.

3. Maintenant configurez votre connexion :

   1. Sélectionnez l’option **Se connecter via la passerelle de données locale**. 

   2. Pour **Passerelles**, sélectionnez la ressource de passerelle que vous avez créée précédemment. 

      Même si votre emplacement de connexion de passerelle doit être dans la même région que votre application logique, vous pouvez choisir une passerelle dans une autre région.

   3. Fournissez un nom de connexion unique et les autres informations requises. 

      Un nom de connexion unique vous aidera à identifier facilement cette connexion ultérieurement, notamment quand vous créez plusieurs connexions. Le cas échéant, incluez également le nom de domaine complet dans votre nom d’utilisateur.
   
      Voici un exemple : 

      ![Créer une connexion entre une application logique et une passerelle de données](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Lorsque vous êtes prêt, choisissez **Créer**. 

Votre connexion de passerelle est maintenant prête et votre application logique peut l’utiliser.

## <a name="edit-connection"></a>Modifier la connexion

Après avoir créé une connexion de passerelle pour votre application logique, vous pouvez mettre à jour les paramètres pour cette connexion spécifique.

1. Recherchez votre connexion de passerelle :

   * Pour rechercher toutes les connexions d’API pour votre application logique uniquement, dans le menu de votre application logique, sous **Outils de développement**, sélectionnez **Connexions d’API**. 
   
     ![Accédez à votre application logique, puis sélectionnez « Connexions d’API ».](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Pour rechercher toutes les connexions d’API associées à votre abonnement Azure : 

     * Dans le menu principal Azure, accédez à **Tous les services** > **Web** > **Connexions d’API**. 
     * Ou, dans le menu Azure principal, accédez à **Toutes les ressources**.

2. Sélectionnez la connexion de passerelle que vous souhaitez, puis choisissez **Modifier une connexion d’API**.

   > [!TIP]
   > Si vos mises à jour ne prennent pas effet, essayez d’[arrêter et redémarrer la service Windows de passerelle](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Supprimer la ressource de passerelle

Pour créer une autre ressource de passerelle, associer votre passerelle à une autre ressource ou supprimer la ressource de passerelle, vous pouvez effectivement supprimer celle-ci sans que cela affecte l’installation de la passerelle. 

1. Dans le menu Azure principal, accédez à **Toutes les ressources**. 

2. Cherchez et sélectionnez votre ressource de passerelle.

3. Le cas échéant, dans votre menu de ressource de passerelle, sélectionnez **Passerelle de données locale**. 

4. Dans la barre d’outils de ressource, choisissez **Supprimer**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser vos applications logiques](./logic-apps-securing-a-logic-app.md)
* [Exemples et scénarios courants pour les applications logiques](./logic-apps-examples-and-scenarios.md)
