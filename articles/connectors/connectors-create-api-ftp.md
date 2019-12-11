---
title: Se connecter à un serveur FTP
description: Créer, surveiller et gérer des fichiers sur un serveur FTP avec Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: c7b8c1ac94fd35a4a0cb30ad32d8c6ce39edc058
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789785"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Créer, superviser et gérer des fichiers FTP avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur FTP, vous pouvez créer des tâches et des flux de travail automatisés qui créent, supervisent, envoient et reçoivent des fichiers via votre compte sur un serveur FTP, ainsi que d’autres actions, par exemple :

* Superviser à quel moment des fichiers sont ajoutés ou changés.
* Obtenir, créer, copier, mettre à jour, lister et supprimer des fichiers.
* Obtenir les métadonnées et le contenu des fichiers.
* Extraire des archives dans des dossiers.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre serveur FTP, et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions d’exécution dans vos applications logiques pour la gestion des fichiers sur votre serveur FTP. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions FTP. Par exemple, si vous obtenez régulièrement des fichiers à partir de votre serveur FTP, vous pouvez envoyer un e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>limites

* Le connecteur FTP ne prend en charge que FTPS (FTP sur SSL en mode explicite) ; il n’est pas compatible avec le protocole FTPS implicite.

* Par défaut, les actions FTP peuvent lire ou écrire des fichiers dont la taille est *inférieure ou égale à 50 Mo*. Pour gérer les fichiers dont la taille est supérieure à 50 Mo, les actions FTP prennent en charge la [segmentation des messages](../logic-apps/logic-apps-handle-large-messages.md). L’action **Obtenir le contenu du fichier** utilise implicitement la segmentation.

* Les déclencheurs FTP ne prennent pas en charge la segmentation. Quand ils demandent le contenu des fichiers, les déclencheurs sélectionnent uniquement les fichiers dont la taille est inférieure ou égale à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle :

  * Utilisez un déclencheur FTP qui retourne des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)** .

  * Suivez le déclencheur avec l’action FTP **Obtenir le contenu du fichier**, qui lit le fichier complet et utilise implicitement la segmentation.

## <a name="how-ftp-triggers-work"></a>Fonctionnement des déclencheurs FTP

Les déclencheurs FTP fonctionnent en interrogeant le système de fichiers FTP et en recherchant tout fichier changé depuis la dernière interrogation. Certains outils permettent de conserver l’horodatage lorsque les fichiers sont modifiés. Dans ce cas, vous devez désactiver cette fonctionnalité pour permettre l’exécution du déclencheur. Voici quelques paramètres communs :

| Client SFTP | Action |
|-------------|--------|
| Winscp | Accédez à **Options** > **Préférences** > **Transférer** > **Modifier** > **Conserver l’horodatage** > **Désactiver** |
| FileZilla | Accédez à **Transférer** > **Conserver les horodatages des fichiers transférés** > **Désactiver** |
|||

Quand un déclencheur détecte un nouveau fichier, il vérifie que le nouveau fichier est complet et non partiellement écrit. Par exemple, un fichier peut être en cours de modification lorsque le déclencheur vérifie le serveur de fichiers. Pour éviter de retourner un fichier partiellement écrit, le déclencheur note l’horodatage du fichier qui comporte des modifications récentes, mais ne retourne pas immédiatement ce fichier. Le déclencheur retourne le fichier uniquement lors d’une nouvelle interrogation du serveur. Parfois, ce comportement peut entraîner un retard correspondant à jusqu’à deux fois l’intervalle d’interrogation du déclencheur.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Vos informations d’identification de compte et adresse du serveur hôte FTP

  Le connecteur FTP a besoin que votre serveur FTP soit accessible à partir d’Internet, et configuré pour fonctionner en mode *passif*. Vos informations d’identification permettent à votre application logique de créer une connexion et d’accéder à votre compte FTP.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte FTP. Pour démarrer avec un déclencheur FTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action FTP, démarrez votre application logique avec un autre déclencheur, par exemple le déclencheur **Périodicité**.

## <a name="connect-to-ftp"></a>Se connecter à FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « ftp » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité.

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**, puis sélectionnez **Ajouter une action**. Dans la zone de recherche, entrez « ftp » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Choisissez le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Fournissez les informations nécessaires pour votre connexion, puis choisissez **Créer**.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="examples"></a>Exemples

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Déclencheur FTP : Lors de l’ajout ou de la modification d’un fichier

Ce déclencheur démarre un flux de travail d’application logique quand il détecte qu’un fichier est ajouté ou modifié sur un serveur FTP. Par exemple, vous pouvez ajouter une condition qui vérifie le contenu du fichier pour savoir s’il peut être obtenu. Enfin, vous pouvez ajouter une action qui obtient le contenu du fichier et le place dans un dossier sur le serveur SFTP.

**Exemple en entreprise** : vous pouvez utiliser ce déclencheur pour superviser l’apparition dans un dossier FTP de nouveaux fichiers qui décrivent les commandes des clients. Vous pouvez ensuite utiliser une action FTP comme **Obtenir le contenu du fichier** pour pouvoir récupérer le contenu de la commande à des fins de traitement, et stocker cette commande dans une base de données de commandes.

Voici un exemple illustrant ce déclencheur : **Lors de l’ajout ou de la modification d’un fichier**

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « ftp » comme filtre. Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de l’ajout ou de la modification d’un fichier - FTP**

   ![Recherche et sélection du déclencheur FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Fournissez les informations nécessaires pour votre connexion, puis choisissez **Créer**.

   Par défaut, ce connecteur transfère les fichiers au format texte. Pour transférer des fichiers au format binaire, par exemple quand l’encodage est utilisé, sélectionnez **Transport binaire**.

   ![Création d’une connexion serveur FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. En regard de la zone **Dossier**, sélectionnez l’icône de dossier pour faire apparaître une liste. Afin de trouver le dossier que vous souhaitez superviser pour les fichiers nouveaux ou modifiés, sélectionnez le chevron droit ( **>** ), accédez à ce dossier et sélectionnez-le.

   ![Recherche et sélection du dossier à superviser](./media/connectors-create-api-ftp/select-folder.png)  

   Votre dossier sélectionné s’affiche dans la zone **Dossier**.

   ![Dossier sélectionné](./media/connectors-create-api-ftp/selected-folder.png)  

À présent que votre application logique dispose d’un déclencheur, ajoutez les actions que vous souhaitez exécuter lorsqu’elle trouve un fichier nouveau ou modifié. Pour cet exemple, vous pouvez ajouter une action FTP qui obtient le contenu nouveau ou mis à jour.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Action FTP : Get content

Cette action obtient le contenu d’un fichier sur un serveur FTP quand ce fichier est ajouté ou mis à jour. Par exemple, vous pouvez ajouter le déclencheur de l’exemple précédent, et une action qui obtient le contenu du fichier après l’ajout ou la modification de celui-ci.

Voici un exemple illustrant cette action : **Get content**

1. Sous le déclencheur ou n’importe quel autre action, choisissez **Nouvelle étape**.

1. Dans la zone de recherche, entrez « ftp » comme filtre. Dans la liste des actions, sélectionnez cette action : **Obtenir le contenu d’un fichier - FTP**

   ![Sélection de l’action FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Si vous disposez déjà d’une connexion à vos compte et serveur FTP, passez à l’étape suivante. Sinon, fournissez les informations nécessaires pour cette connexion, puis choisissez **Créer**.

   ![Création d’une connexion serveur FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Après l’ouverture de l’action **Obtenir le contenu du fichier**, cliquez à l’intérieur de la zone **Fichier** pour que la liste de contenu dynamique s’affiche. Vous pouvez à présent sélectionner des propriétés pour les sorties des étapes précédentes. Dans la liste de contenu dynamique, sélectionnez la propriété **Contenu du fichier**, qui renferme le contenu du fichier ajouté ou mis à jour.  

   ![Recherche et sélection du fichier](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   La propriété **Contenu du fichier** apparaît maintenant dans la zone **Fichier**.

   ![Propriété « Contenu du fichier » sélectionnée](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Enregistrez votre application logique. Pour tester votre flux de travail, ajoutez un fichier au dossier FTP que votre application logique supervise désormais.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence du connecteur](/connectors/ftpconnector/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)