---
title: Accéder à des sources de données locales
description: Se connecter à des sources de données locales à partir d’Azure Logic Apps en créant une ressource de passerelle de données locale Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657129"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Se connecter à des sources de données locales à partir d’Azure Logic Apps

Pour accéder aux sources de données locales à partir de vos applications logiques, vous devez créer une ressource Azure après avoir [installé la *passerelle de données locale* sur un ordinateur local](../logic-apps/logic-apps-gateway-install.md). Vos applications logiques utilisent ensuite cette ressource de passerelle Azure dans les déclencheurs et les actions fournis par les [connecteurs locaux](../connectors/apis-list.md#on-premises-connectors) qui sont disponibles pour Azure Logic Apps.

Cet article explique comment créer votre ressource de passerelle Azure pour une [passerelle précédemment installée sur votre ordinateur local](../logic-apps/logic-apps-gateway-install.md). Pour obtenir des informations détaillées sur la passerelle, consultez [Fonctionnement de la passerelle](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Pour vous connecter à des réseaux virtuels Azure, envisagez de créer un [*environnement de service d’intégration*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) à la place. 

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

* [Passerelle de données locale Microsoft Power Automate](/power-automate/gateway-reference)
* [Passerelle de données locale Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Passerelle de données locale Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Dans Azure Logic Apps, la passerelle de données locale prend en charge les [connecteurs locaux](../connectors/apis-list.md#on-premises-connectors) pour les sources de données suivantes :

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

Azure Logic Apps prend en charge les opérations de lecture et d’écriture par le biais de la passerelle de données. Toutefois, ces opérations ont des [limites quant à la taille de leur charge utile](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Bien que la passerelle proprement dite n’entraîne pas de frais supplémentaires, le [modèle tarifaire de Logic Apps](../logic-apps/logic-apps-pricing.md) s’applique à ces connecteurs et à d’autres opérations dans Azure Logic Apps.

## <a name="prerequisites"></a>Prérequis

* La passerelle de données locale doit être déjà [installée sur un ordinateur local](../logic-apps/logic-apps-gateway-install.md).

* Vous utilisez les [mêmes compte et abonnement Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que ceux utilisés lors de l’installation de cette passerelle de données. Ce compte Azure doit appartenir à un seul [client ou répertoire Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* L’installation de votre passerelle n’est pas déjà enregistrée et revendiquée par une autre ressource de passerelle Azure.

  Quand vous créez une ressource de passerelle dans le portail Azure, vous sélectionnez une installation de passerelle, qui établit un lien vers votre ressource de passerelle et uniquement vers cette ressource de passerelle. Dans Azure Logic Apps, les déclencheurs et les actions locaux utilisent ensuite la ressource de passerelle pour se connecter aux sources de données locales. Dans ces déclencheurs et actions, vous sélectionnez votre abonnement Azure et la ressource de passerelle associée que vous souhaitez utiliser. Chaque ressource de passerelle est liée à une seule installation de passerelle, elle-même liée à un seul compte Azure.

  > [!NOTE]
  > Seul l’administrateur de la passerelle peut créer la ressource de passerelle dans le portail Azure. Les principaux de service ne sont pas pris en charge. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Créer une ressource de passerelle Azure

Après avoir installé la passerelle sur un ordinateur local, créez une ressource Azure pour votre passerelle.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec le même compte Azure que celui utilisé pour installer la passerelle.

1. Dans la fenêtre de recherche du portail Azure, entrez « passerelles de données locales », puis choisissez **Passerelles de données locales**.

   ![Cherchez « Passerelle de données locale »](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Sous **Passerelles de données locales**, sélectionnez **Ajouter**.

   ![Ajouter une nouvelle ressource Azure pour la passerelle de données](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Sous **Créer une passerelle de connexion**, fournissez ces informations pour votre ressource de passerelle. Sélectionnez **Créer** lorsque vous avez terminé.

   | Propriété | Description |
   |----------|-------------|
   | **Nom de la ressource** | Fournissez pour votre ressource de passerelle un nom qui contient uniquement des lettres, des chiffres, des traits d’union (`-`) des traits de soulignement (`_`), des parenthèses (`(`, `)`) ou des points (`.`). |
   | **Abonnement** | Sélectionnez l’abonnement Azure pour le compte Azure qui a été utilisé pour l’installation de la passerelle. L’abonnement par défaut est basé sur le compte Azure que vous avez utilisé pour vous connecter. |
   | **Groupe de ressources** | Le [groupe de ressources Azure](../azure-resource-manager/management/overview.md) que vous souhaitez utiliser |
   | **Lieu** | La même région ou position sélectionnée pour le service cloud de la passerelle pendant l’[installation de la passerelle](../logic-apps/logic-apps-gateway-install.md). Autrement, votre installation de passerelle ne s’affichera pas dans la liste **Nom d’installation**. L’emplacement de votre application logique peut être différent de l’emplacement de votre ressource de passerelle. |
   | **Nom de l’installation** | Sélectionnez une installation de passerelle, qui apparaît dans la liste uniquement quand les conditions suivantes sont remplies : <p><p>- L’installation de la passerelle utilise la même région que la ressource de passerelle que vous souhaitez créer. <br>-L’installation de la passerelle n’est pas liée à une autre ressource de passerelle Azure. <br>- L’installation de la passerelle est liée au même compte Azure que celui que vous utilisez pour créer la ressource de passerelle. <br>-Votre compte Azure appartient à un seul [client ou répertoire Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) et est le même compte que celui utilisé pour l’installation de la passerelle. <p><p>Pour plus d’informations, consultez la section [Questions fréquentes (FAQ)](#faq). |
   |||

   Voici un exemple illustrant une installation de passerelle qui se trouve dans la même région que votre ressource de passerelle et qui est liée au même compte Azure :

   ![Fournir des détails pour créer une ressource de passerelle de données](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Se connecter à des données locales

Après avoir créé votre ressource de passerelle et avoir associé votre abonnement Azure à cette ressource, vous pouvez maintenant créer une connexion entre votre application logique et votre source de données locale à l’aide de la passerelle.

1. Dans le portail Azure, créez ou ouvrez votre application logique dans le Concepteur d’application logique.

1. Ajoutez un connecteur prenant en charge des connexions locales, tel que **SQL Server**.

1. Sélectionnez l’option **Se connecter via la passerelle de données locale**.

1. Sous **Passerelles**, dans la liste **Abonnements**, sélectionnez votre abonnement Azure qui a la ressource de passerelle souhaitée.

1. Dans la liste **Passerelle de connexion**, qui affiche les ressources de passerelle disponibles dans votre abonnement sélectionné, sélectionnez la ressource de passerelle de votre choix. Chaque ressource de passerelle est liée à une seule installation de passerelle.

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

   ![Accédez au menu de votre application logique, puis sélectionnez « Connexions d’API »](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Sélectionnez la connexion de passerelle que vous souhaitez, puis sélectionnez **Modifier une connexion d’API**.

   > [!TIP]
   > Si vos mises à jour ne prennent pas effet, essayez d’[arrêter et redémarrer le compte de service Windows de passerelle](../logic-apps/logic-apps-gateway-install.md#restart-gateway) pour votre installation de passerelle.

Pour rechercher toutes les connexions d’API associées à votre abonnement Azure :

* Dans le menu du portail Azure, sélectionnez **Tous les services** > **Web** > **Connexions d’API**.
* Ou, dans le menu du portail Azure, sélectionnez **Toutes les ressources**. Réglez le filtre **Type** sur **Connexion d’API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Supprimer la ressource de passerelle

Pour créer une autre ressource de passerelle, lier l’installation de votre passerelle à une autre ressource de passerelle ou supprimer la ressource de passerelle, vous pouvez effectivement supprimer celle-ci sans que cela affecte l’installation de la passerelle.

1. Dans le menu du portail Azure, sélectionnez **Toutes les ressources** ou recherchez et sélectionnez **Toutes les ressources** sur n’importe quelle page. Cherchez et sélectionnez votre ressource de passerelle.

1. Le cas échéant, dans votre menu de ressource de passerelle, sélectionnez **Passerelle de données locale**. Dans la barre d’outils de ressources de la passerelle, sélectionnez **Supprimer**.

   Par exemple :

   ![Supprimer une ressource de passerelle dans Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Forum aux questions

**Q** : Pourquoi mon installation de passerelle n’apparaît-elle pas quand je crée ma ressource de passerelle dans Azure ? <br/>
**R** : Ce problème peut se produire pour les raisons suivantes :

* Votre compte Azure doit être le même compte que celui qui est lié à l’installation de la passerelle sur l’ordinateur local. Vérifiez que vous êtes connecté au portail Azure avec la même identité que celle qui est liée à l’installation de la passerelle. En outre, assurez-vous que votre compte Azure appartient à un seul [locataire ou répertoire Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology) et qu’il est défini sur le même client ou répertoire Azure AD que celui utilisé lors de l’installation de la passerelle.

* Vos ressource de passerelle et installation de passerelle doivent utiliser la même région. Toutefois, la position de votre application logique peut être différente de celle de votre ressource de passerelle.

* L’installation de votre passerelle est déjà enregistrée et revendiquée par une autre ressource de passerelle. Ces installations ne s’affichent pas dans la liste **Nom d’installation**. Pour passer en revue vos inscriptions de passerelle dans le portail Azure, recherchez toutes vos ressources Azure qui ont le type **Passerelles de données locales** dans *tous* vos abonnements Azure. Pour dissocier l’installation de la passerelle de l’autre ressource de passerelle, consultez [Supprimer la ressource de passerelle](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser vos applications logiques](./logic-apps-securing-a-logic-app.md)
* [Exemples et scénarios courants pour les applications logiques](./logic-apps-examples-and-scenarios.md)
