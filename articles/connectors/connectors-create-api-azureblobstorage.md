---
title: Se connecter au Stockage Blob Azure - Azure Logic Apps
description: Créer et gérer des objets Blob dans Stockage Azure avec Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d57ea1a881980203b1c8f216239b27b64f0d71cd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051042"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Créer et gérer des objets Blob dans Stockage Blob Azure avec Azure Logic Apps

Cet article explique comment vous pouvez accéder et gérer les fichiers stockés en tant qu’objets Blob dans votre compte de stockage Azure à partir d’une application logique avec le connecteur Stockage Blob Azure. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour gérer vos fichiers. Par exemple, vous pouvez générer des applications logiques qui créent, obtiennent, mettent à jour et suppriment des fichiers dans votre compte de stockage.

Supposons que vous disposez d’un outil qui est mis à jour sur un site web Azure. Celui-ci agit comme le déclencheur de votre application logique. Lorsque cet événement se produit, votre application logique peut mettre à jour un fichier dans votre conteneur de stockage Blob, qui est une action dans votre application logique.

> [!NOTE]
> Logic Apps ne prend pas en charge la connexion directe à des comptes de stockage Azure à travers des pare-feu. Pour accéder à ces comptes de stockage, utilisez une des options suivantes :
>
> * Créez un [environnement de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), qui peut se connecter à des ressources dans un réseau virtuel Azure.
>
> * Si vous utilisez déjà Gestion des API, vous pouvez utiliser ce service pour ce scénario. Pour plus d’informations, consultez [Architecture d’intégration d’entreprise simple](https://aka.ms/aisarch).

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour obtenir des informations techniques spécifiques aux connecteurs, consultez la [référence du connecteur Stockage Blob Azure](/connectors/azureblobconnector/).

## <a name="limits"></a>limites

* Par défaut, les actions de Stockage Blob Azure peuvent lire ou écrire des fichiers dont la taille est *inférieure ou égale à 50 Mo*. Pour gérer les fichiers d’une taille supérieure à 50 Mo, mais n’excédant pas 1 024 Mo, les actions de Stockage Blob Azure prennent en charge la [segmentation du message](../logic-apps/logic-apps-handle-large-messages.md). L’action **Obtenir le contenu de l’objet blob** utilise implicitement la segmentation.

* Les déclencheurs de Stockage Blob Azure ne prennent pas en charge la segmentation. Quand ils demandent du contenu de fichiers, les déclencheurs sélectionnent uniquement des fichiers dont la taille est inférieure ou égale à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle :

  * Utilisez un déclencheur Stockage Blob Azure qui retourne des propriétés de fichier, comme **Quand un blob est ajouté ou modifié (propriétés uniquement)** .

  * Suivez le déclencheur avec l’action Stockage Blob Azure **Obtenir le contenu de l’objet blob**, qui lit le fichier complet et utilise implicitement la segmentation.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un [compte de stockage Azure et un conteneur de stockage](../storage/blobs/storage-quickstart-blobs-portal.md)

* L’application logique où vous devez avoir accès à votre compte de stockage d’objets Blob Azure. Pour démarrer votre application logique avec un déclencheur Stockage Blob Azure, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Ajouter un déclencheur de stockage d’objets blob

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Cet exemple montre comment vous pouvez démarrer un flux de travail d’application logique avec le déclencheur **Quand un blob est ajouté ou modifié (propriétés uniquement)** lorsque les propriétés d’un objet Blob sont ajoutées ou mises à jour dans votre conteneur de stockage.

1. Dans le [portail Azure](https://portal.azure.com) ou Visual Studio, créez une application logique vide, qui ouvre le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

2. Dans la zone de recherche, saisissez le filtre « blob azure ». Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité.

   Cet exemple utilise ce déclencheur : **Quand un blob est ajouté ou modifié (propriétés uniquement)**

   ![Sélectionner le déclencheur](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion de stockage d’objets Blob maintenant](#create-connection). Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour le déclencheur.

   Dans cet exemple, sélectionnez le conteneur et le dossier que vous souhaitez analyser.

   1. Dans la zone **Conteneur**, sélectionnez l’icône de dossier.

   2. Dans la liste des dossiers, cliquez sur le chevron droit ( **>** ), puis naviguez jusqu’au dossier et sélectionnez-le.

      ![Sélectionner le dossier](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Sélectionnez l’intervalle et la fréquence à laquelle vous souhaitez que le déclencheur recherche des modifications dans le dossier.

4. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

5. Continuez maintenant à ajouter une ou plusieurs actions à votre application logique pour les tâches à effectuer avec les résultats du déclencheur.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Ajouter une action de stockage d’objets Blob

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Dans cet exemple, l’application logique commence avec le [déclencheur de périodicité](../connectors/connectors-native-recurrence.md).

1. Dans le [portail Azure](https://portal.azure.com) ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. Cet exemple utilise le portail Azure.

2. Dans le Concepteur d’application logique, sous le déclencheur ou l’action, sélectionnez **Nouvelle étape**.

   ![Ajouter une action](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Pour ajouter une action entre des étapes, déplacez votre souris sur la flèche de connexion. Choisissez le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

3. Dans la zone de recherche, saisissez le filtre « blob azure ». Dans la liste des actions, sélectionnez l’action souhaitée.

   Cet exemple utilise cette action : **Obtenir le contenu de l’objet blob**

   ![Action select](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion Stockage Blob Azure maintenant](#create-connection).
Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour l’action.

   Dans cet exemple, sélectionnez le fichier souhaité.

   1. Dans la zone **Blob**, sélectionnez l’icône de dossier.
  
      ![Sélectionner le dossier](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Recherchez et sélectionnez le fichier souhaité en fonction de l’**ID** d’objet Blob. Vous pouvez retrouver cet **ID** dans les métadonnées de l’objet blob qui sont retournées par le déclencheur de stockage d’objets blob décrit précédemment.

5. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.
Pour tester votre application logique, assurez-vous que le dossier sélectionné contient un objet Blob.

Cet exemple obtient uniquement le contenu d’un objet Blob. Pour afficher le contenu, ajoutez une autre action qui crée un fichier avec l’objet Blob à l’aide d’un autre connecteur. Par exemple, ajoutez une action OneDrive qui crée un fichier basé sur le contenu de l’objet Blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Se connecter à un compte de stockage

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que les déclencheurs, actions et limites, comme décrit dans le fichier Open API (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
