---
title: Se connecter à SharePoint à partir d’Azure Logic Apps
description: Superviser et gérer des ressources dans SharePoint Online ou SharePoint Server en local à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 04/27/2021
tags: connectors
ms.openlocfilehash: 750253d5607262614cf8576c376b261616361266
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285445"
---
# <a name="connect-to-sharepoint-resources-with-azure-logic-apps"></a>Se connecter à des ressources SharePoint avec Azure Logic Apps

Pour automatiser les tâches qui surveillent et gèrent les ressources, telles que des fichiers, dossiers, listes et éléments, dans SharePoint Online ou SharePoint Server en local, vous pouvez créer des workflows d’intégration automatisés en utilisant Azure Logic Apps et le connecteur SharePoint.

La liste suivante décrit des exemples de tâches que vous pouvez automatiser :

* Superviser à quel moment les fichiers ou éléments sont créés, changés ou supprimés.
* Créer, obtenir, mettre à jour ou supprimer des éléments.
* Ajouter, obtenir ou supprimer des pièces jointes. Obtenir le contenu de pièces jointes.
* Créer, copier, mettre à jour ou supprimer des fichiers. 
* Mettre à jour les propriétés de fichier. Obtenir le contenu, les métadonnées ou les propriétés d’un fichier.
* Lister ou extraire des dossiers.
* Obtenir des listes ou des vues de liste.
* Définir le statut d’approbation du contenu.
* Résoudre des personnes.
* Envoyer des requêtes HTTP à SharePoint.
* Obtenir les valeurs d’une entité.

Dans votre workflow d’application logique, vous pouvez utiliser un déclencheur qui supervise les événements dans SharePoint et met la sortie à la disposition d’autres actions. Vous pouvez ensuite utiliser les actions pour effectuer diverses tâches dans SharePoint. Vous pouvez également inclure d’autres actions qui utilisent la sortie des actions SharePoint. Par exemple, si vous récupérez régulièrement des fichiers provenant de SharePoint, vous pouvez envoyer des alertes par e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md). Ou essayez ce [de démarrage rapide pour créer votre premier exemple de workflow d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’adresse de votre site SharePoint et vos informations d’identification utilisateur. Vous avez besoin de ces informations d’identification pour autoriser votre workflow à accéder à votre compte SharePoint.

* Pour les connexions à un serveur SharePoint local, vous devez [installer et configurer la passerelle de données](../logic-apps/logic-apps-gateway-install.md) sur un ordinateur local et une [ressource de passerelle de données déjà créée dans Azure](../logic-apps/logic-apps-gateway-connection.md).

  Vous pouvez ensuite sélectionner la ressource de passerelle à utiliser lorsque vous créez la connexion SharePoint Server à partir de votre workflow.

* Le workflow d’application logique dans lequel vous devez accéder à votre site ou serveur SharePoint.

  * Pour démarrer le workflow avec un déclencheur SharePoint, vous avez besoin d’un workflow d’application logique vierge.
  * Pour ajouter une action SharePoint, votre workflow doit déjà avoir un déclencheur.

## <a name="connect-to-sharepoint"></a>Se connecter à SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>Ajouter un déclencheur

1. Dans le portail Azure, Visual Studio Code ou Visual Studio, ouvrez votre workflow d’application logique dans le concepteur d’applications logiques, si ce n’est pas déjà fait.

1. Dans la zone de recherche du concepteur, entrez `sharepoint` comme terme de recherche. Sélectionnez le connecteur **SharePoint**.

1. Dans la liste **Déclencheurs**, sélectionnez le déclencheur que vous souhaitez utiliser.

1. Lorsque vous êtes invité à vous connecter et à créer une connexion, choisissez l’une des options suivantes :

   * Pour SharePoint Online, sélectionnez **Se connecter** et authentifiez-vous avec vos informations d’identification.
   * Pour SharePoint Server, sélectionnez **Se connecter via la passerelle de données locale**. Fournissez les informations de la demande concernant la ressource de passerelle à utiliser, le type d’authentification et les autres informations nécessaires.

1. Sélectionnez **Créer** lorsque vous avez terminé.

   Une fois que votre workflow a créé la connexion, le déclencheur sélectionné s’affiche.

1. Fournissez les informations nécessaires à la configuration du déclencheur et poursuivez la construction de votre workflow.

## <a name="add-an-action"></a>Ajouter une action

1. Dans le portail Azure, Visual Studio Code ou Visual Studio, ouvrez votre workflow d’application logique dans le concepteur d’applications logiques, si ce n’est pas déjà fait.

1. Choisissez l’une des options suivantes :

   * Pour ajouter une action en tant que dernière étape, sélectionnez **Nouvelle étape**.
   * Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Sélectionnez le signe plus ( **+** ), puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une opération**, dans la zone de recherche, entrez `sharepoint` comme terme de recherche. Sélectionnez le connecteur **SharePoint**.

1. Dans la liste **Actions**, sélectionnez l’action que vous souhaitez utiliser.

1. Lorsque vous êtes invité à vous connecter et à créer une connexion, choisissez l’une des options suivantes :

   * Pour SharePoint Online, sélectionnez **Se connecter** et authentifiez-vous avec vos informations d’identification.
   * Pour SharePoint Server, sélectionnez **Se connecter via la passerelle de données locale**. Fournissez les informations de la demande concernant la ressource de passerelle à utiliser, le type d’authentification et les autres informations nécessaires.

1. Sélectionnez **Créer** lorsque vous avez terminé.

   Une fois que votre workflow a créé la connexion, l’action sélectionnée s’affiche.

1. Fournissez les informations nécessaires à la configuration de l’action et poursuivez la construction de votre workflow.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques sur ce connecteur, notamment au sujet des déclencheurs, des actions et des limites décrits dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/sharepoint/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)