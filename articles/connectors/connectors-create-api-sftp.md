---
title: Se connecter à un compte SFTP à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui supervisent, créent, gèrent, envoient et reçoivent des fichiers pour un serveur SFTP à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/11/2018
ms.openlocfilehash: 8eb5d85a56e03ba8ceb646a3fbe580f8d525d8a3
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233699"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Superviser, créer et gérer des fichiers SFTP à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur SFTP, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent, créent, envoient et reçoivent des fichiers par le biais de votre compte sur un serveur [SFTP](https://www.ssh.com/ssh/sftp/), ainsi que d’autres actions, par exemple :

* Superviser à quel moment des fichiers sont ajoutés ou changés.
* Obtenir, créer, copier, mettre à jour, lister et supprimer des fichiers.
* Obtenir les métadonnées et le contenu des fichiers.
* Extraire des archives dans des dossiers.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre serveur SFTP et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions dans vos applications logiques pour effectuer des tâches sur des fichiers sur votre serveur SFTP. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions SFTP. Par exemple, si vous récupérez régulièrement des fichiers de votre serveur SFTP, vous pouvez envoyer un e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Pour les fichiers supérieurs à 50 Mo et jusqu’à 1 Go, utilisez le [connecteur SFTP-SSH](../connectors/connectors-sftp-ssh.md). Le connecteur SFTP prend en charge uniquement les fichiers d’une taille inférieure ou égale à 50 Mo, sauf si vous utilisez la [segmentation pour gérer les messages volumineux](../logic-apps/logic-apps-handle-large-messages.md). 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Vos informations d’identification et adresse du serveur hôte SFTP

   Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte SFTP.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte SFTP. Pour démarrer avec un déclencheur SFTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action SFTP, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-sftp"></a>Se connecter à SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « sftp » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 
   Dans la zone de recherche, entrez « sftp » comme filtre. 
   Sous la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Fournissez les informations nécessaires pour votre connexion, puis choisissez **Créer**.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="examples"></a>Exemples

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Déclencheur SFTP : Lorsqu’un fichier est ajouté ou modifié

Ce déclencheur démarre un flux de travail d’application logique quand il détecte qu’un fichier est ajouté ou changé sur un serveur SFTP. Par exemple, vous pouvez ajouter une condition qui vérifie le contenu du fichier pour savoir s’il peut être obtenu. Enfin, vous pouvez ajouter une action qui obtient le contenu du fichier et le place dans un dossier sur le serveur SFTP. 

**Exemple en entreprise** : vous pouvez utiliser ce déclencheur pour superviser l’apparition dans un dossier SFTP de nouveaux fichiers représentant les commandes des clients. Vous pouvez ensuite utiliser une action SFTP comme **Obtenir le contenu du fichier** afin de pouvoir obtenir le contenu de la commande à des fins de traitement et stocker cette commande dans une base de données de commandes.

### <a name="sftp-action-get-content"></a>Action SFTP : Obtenir le contenu

Cette action obtient le contenu d’un fichier sur un serveur SFTP. Par exemple, vous pouvez ajouter le déclencheur de l’exemple précédent et une condition à laquelle le contenu du fichier doit satisfaire. Si la condition est vérifiée, l’action qui obtient le contenu peut s’exécuter. 

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/sftpconnector/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)