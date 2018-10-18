---
title: Se connecter à un compte SFTP à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui supervisent, créent, gèrent, envoient et reçoivent des fichiers pour un serveur SFTP à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064492"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Superviser, créer et gérer des fichiers SFTP à l’aide d’Azure Logic Apps et du connecteur SFTP/SSH

Avec Azure Logic Apps et le connecteur SFTP/SSH, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent, créent, envoient et reçoivent des fichiers par le biais de votre compte sur un serveur [SFTP](https://www.ssh.com/ssh/sftp/), ainsi que d’autres actions, par exemple :

* Superviser à quel moment des fichiers sont ajoutés ou changés.
* Obtenir, créer, copier, renommer, mettre à jour, lister et supprimer des fichiers.
* Créer un dossier.
* Obtenir les métadonnées et le contenu des fichiers.
* Extraire des archives dans des dossiers.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre serveur SFTP et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions dans vos applications logiques pour effectuer des tâches sur des fichiers sur votre serveur SFTP. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions SFTP. Par exemple, si vous récupérez régulièrement des fichiers de votre serveur SFTP, vous pouvez envoyer un e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com.
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP/SSH par rapport à SFTP

Voici les quelques différences clés entre le connecteur SFTP/SSH et le connecteur [SFTP](../connectors/connectors-create-api-sftp.md). Le connecteur SFTP/SSH fournit ces fonctionnalités :

* Il utilise la bibliothèque <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a>, qui est une bibliothèque SSH (Secure Shell) open source pour .NET.

* Il prend en charge les fichiers volumineux jusqu’à **1 Go**. Le connecteur est capable de lire ou d’écrire des fichiers dont la taille peut atteindre 1 Go.

* Fournit l’action **Créer un dossier**, qui crée un dossier au niveau du chemin spécifié sur le serveur SFTP.

* Fournit l’action **Renommer le fichier**, qui renomme un fichier sur le serveur SFTP.

* Met en cache la connexion au serveur SFTP, ce qui améliore les performances et réduit le nombre de tentatives de connexion sur le serveur. 

  Vous pouvez contrôler la durée utilisée pour la mise en cache de la connexion en configurant la propriété <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> sur votre serveur SFTP. 

## <a name="how-trigger-polling-works"></a>Fonctionnement de l’interrogation du déclencheur

Les déclencheurs SFTP/SSH fonctionnent en interrogeant le système de fichiers SFTP et en recherchant tout fichier modifié depuis la dernière interrogation. Certains outils permettant de conserver l’horodatage quand les fichiers changent, vous devez dans ce cas désactiver cette fonctionnalité pour que votre déclencheur fonctionne. Voici quelques paramètres communs :

| Client SFTP | Action | 
|-------------|--------| 
| Winscp | Options → Préférences… → Transfert → Modifier… → Preserve timestamp (Conserver l’horodatage) → Désactiver |
| FileZilla | Transfert → Preserve timestamps of transferred files (Conserver les horodatages des fichiers transférés) → Désactiver | 
||| 

Quand un déclencheur détecte un nouveau fichier, il vérifie que le nouveau fichier est complet et non partiellement écrit. Par exemple, un fichier peut être en cours de modification lorsque le déclencheur vérifie le serveur de fichiers. Pour éviter de retourner un fichier partiellement écrit, le déclencheur note l’horodatage du fichier qui comporte des modifications récentes, mais ne retourne pas immédiatement ce fichier. Le déclencheur retourne le fichier uniquement lors d’une nouvelle interrogation du serveur. Parfois, ce comportement peut entraîner un retard correspondant à jusqu’à deux fois l’intervalle d’interrogation du déclencheur. 

Quand il demande le contenu du fichier, le déclencheur ne récupère pas les fichiers supérieurs à 50 Mo. Pour récupérer des fichiers supérieurs à 50 Mo, suivez ce modèle :

* Utilisez un déclencheur qui retourne des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)**. 
* Suivez le déclencheur avec une action qui lit le fichier complet comme **Obtenir le contenu d’un fichier à l’aide du chemin**.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Vos informations d’identification de compte et adresse du serveur hôte SFTP, qui autorisent votre application logique à créer une connexion et à accéder à votre compte SFTP.

  Copiez et collez le contenu complet de la clé privée SSH dans la propriété **Clé privée SSH** en suivant le format multiligne. 
  Voici des exemples d’étapes qui indiquent comment fournir la clé privée SSH à l’aide de Notepad.exe :
    
  1. Ouvrez le fichier de clé privée SSH dans Notepad.exe
  2. Dans le menu **Edition**, sélectionnez **Sélectionner tout**.
  3. Sélectionnez **Edition** > **Copier**.
  4. Quand vous créez la connexion, dans la propriété **Clé privée SSH**, collez la clé. Ne modifiez pas manuellement la propriété **Clé privée SSH**.

     Le connecteur utilise la bibliothèque SSH.NET, qui prend en charge ces formats de clé privée SSH et uniquement l’empreinte MD5 :

     * RSA 
     * DSA

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
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)