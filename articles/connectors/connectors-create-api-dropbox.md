---
title: Se connecter à Dropbox - Azure Logic Apps
description: Charger et gérer des fichiers avec les API REST de Dropbox et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312537"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Charger et gérer les fichiers de Dropbox à l’aide d’Azure Logic Apps

Avec le connecteur Dropbox et les Azure Logic Apps, vous pouvez créer des flux de travail automatisés charger et gérer des fichiers dans votre compte Dropbox. 

Cet article explique comment se connecter à Dropbox à partir de votre application logique et puis ajouter Dropbox **lorsqu’un fichier est créé** déclencheur et Dropbox **obtenir le contenu du fichier à l’aide du chemin d’accès** action.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Un [compte Dropbox](https://www.dropbox.com/), laquelle vous pouvez vous inscrire gratuitement. Vos informations d’identification de compte sont nécessaires pour créer une connexion entre votre application logique et votre compte Dropbox.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour cet exemple, vous avez besoin d’une application logique vide.

## <a name="add-trigger"></a>Ajouter un déclencheur

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Sous la zone de recherche, choisissez **Tout**. Dans la zone de recherche, entrez « dropbox » comme filtre.
Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Quand un fichier est créé**

   ![Sélectionner un déclencheur Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Connectez-vous avec vos informations d’identification de compte Dropbox et autorisez l’accès à vos données Dropbox pour Azure Logic Apps.

1. Fournissez les informations requises pour votre déclencheur. 

   Dans cet exemple, sélectionnez le dossier où vous souhaitez effectuer le suivi de la création de fichiers. Pour parcourir vos dossiers, choisissez l’icône de dossier à côté du **dossier** boîte.

## <a name="add-action"></a>Ajouter une action

Ajoutons maintenant une action qui obtient le contenu à partir de n’importe quel nouveau fichier.

1. Sous le déclencheur, choisissez **Étape suivante**. 

1. Sous la zone de recherche, choisissez **Tout**. Dans la zone de recherche, entrez « dropbox » comme filtre.
Dans la liste des actions, sélectionnez cette action : **Obtenir le contenu d’un fichier à l’aide du chemin**

1. Si vous n’avez pas déjà autorisé Azure Logic Apps pour accéder à Dropbox, autorisez un accès maintenant.

1. Pour rechercher le chemin d’accès de fichier que vous souhaitez utiliser, à côté du **chemin d’accès du fichier** , sélectionnez les points de suspension (**...** ) bouton. 

   Vous pouvez également cliquer à l’intérieur de la **chemin d’accès du fichier** et dans la liste de contenu dynamique, sélectionnez **chemin d’accès du fichier**, dont la valeur est disponible en tant que sortie à partir du déclencheur que vous avez ajouté dans la section précédente.

1. Lorsque vous avez terminé, enregistrez votre application logique.

1. Pour déclencher votre application logique, créez un nouveau fichier dans Dropbox.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que des déclencheurs, actions et limites, comme décrit par OpenAPI du connecteur (anciennement Swagger), consultez le [page de référence du connecteur](/connectors/dropbox/).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
