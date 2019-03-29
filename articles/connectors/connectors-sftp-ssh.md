---
title: Se connecter à un serveur SFTP avec SSH - Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches qui supervisent, créent, gèrent, envoient et reçoivent des fichiers pour un serveur SFTP à l’aide de SSH et d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 660d785baf12052bddf5206d8641116c9ac606aa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575094"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Superviser, créer et gérer des fichiers SFTP à l’aide de SSH et d’Azure Logic Apps

Pour automatiser les tâches qui surveillent, créent, envoient et reçoivent des fichiers sur un serveur [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) à l’aide du protocole [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), vous pouvez générer et automatiser les workflows d’intégration à l’aide d’Azure Logic Apps et du connecteur SFTP-SSH. SFTP est un protocole réseau qui fournit un accès aux fichiers, le transfert de fichiers et la gestion des fichiers sur n’importe quel flux de données fiable. Voici quelques exemples de tâches que vous pouvez automatiser : 

* Superviser à quel moment des fichiers sont ajoutés ou changés.
* Obtenir, créer, copier, renommer, mettre à jour, lister et supprimer des fichiers.
* Créez des dossiers.
* Obtenir les métadonnées et le contenu des fichiers.
* Extraire des archives dans des dossiers.

Vous pouvez utiliser des déclencheurs qui surveillent les événements sur votre serveur SFTP et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent diverses tâches sur votre serveur SFTP. Vous pouvez également faire en sorte que d’autres actions de votre application logique utilisent la sortie d’actions SFTP. Par exemple, si vous récupérez régulièrement des fichiers de votre serveur SFTP, vous pouvez envoyer des alertes par e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com.
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>limites

* Actions SFTP-SSH peuvent lire ou écrire des fichiers qui sont *1 Go ou plus petit* par la gestion des données en tant que *50 Mo pièces*, pas 1 Go pièces.

* Pour les fichiers *supérieure à 1 Go*, actions peuvent utiliser [segmentation du message](../logic-apps/logic-apps-handle-large-messages.md). Actuellement, les déclencheurs SFTP-SSH ne prennent en charge de segmentation.

Pour plus de différences, passez en revue [comparer SFTP-SSH et SFTP](#comparison) plus loin dans la section suivante.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparer SFTP/SSH par rapport à SFTP

Voici les autres principales différences entre le connecteur SFTP-SSH et le connecteur SFTP où le connecteur SFTP-SSH offre les fonctionnalités suivantes :

* Il utilise la bibliothèque <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a>, qui est une bibliothèque Secure Shell (SSH) open source prenant en charge .NET.

  > [!NOTE]
  >
  > Le connecteur SFTP-SSH prend en charge *uniquement* ces clés privées, formats, algorithmes et empreintes digitales :
  >
  > * **Formats de clé privée** : les clés RSA (Rivest Shamir Adleman) et DSA (Digital Signature Algorithm) aux formats OpenSSH et ssh.com
  > * **Algorithmes de chiffrement** : DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC, and AES-256-CBC
  > * **Empreinte digitale** : MD5

* Actions capable de lire ou écrire des fichiers *jusqu'à 1 Go* par rapport au connecteur SFTP, mais traite les données dans des pièces de 50 Mo, pas de 1 Go de pièces. Pour les fichiers supérieurs à 1 Go, les actions peuvent également utiliser la [segmentation de messages](../logic-apps/logic-apps-handle-large-messages.md). Actuellement, les déclencheurs SFTP-SSH ne prennent en charge de segmentation.

* Fournit l’action **Créer un dossier**, qui crée un dossier au niveau du chemin spécifié sur le serveur SFTP.

* Fournit l’action **Renommer le fichier**, qui renomme un fichier sur le serveur SFTP.

* Met en cache *jusqu’à 1 heure* la connexion au serveur SFTP, ce qui améliore les performances et réduit le nombre de tentatives de connexion au serveur. Pour définir la durée de ce comportement de mise en cache, modifiez la propriété <a href="https://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> dans la configuration SSH sur votre serveur SFTP.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Vos informations d’identification de compte et adresse du serveur SFTP, qui permettent à votre application logique d’accéder à votre compte SFTP. Vous devez également accéder à une clé privée SSH et au mot clé privé SSH. 

  > [!IMPORTANT]
  >
  > Le connecteur SFTP-SSH prend en charge *uniquement* ces clés privées, formats, algorithmes et empreintes digitales :
  > 
  > * **Formats de clé privée** : les clés RSA (Rivest Shamir Adleman) et DSA (Digital Signature Algorithm) aux formats OpenSSH et ssh.com
  > * **Algorithmes de chiffrement** : DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC, and AES-256-CBC
  > * **Empreinte digitale** : MD5
  >
  > Lorsque vous créez votre application logique, après avoir ajouté le déclencheur SFTP-SSH ou une action , vous devrez fournir les informations de connexion pour votre serveur SFTP. 
  > Si vous utilisez une clé privée SSH, assurez-vous de ***copier*** la clé à partir de votre fichier de clé privée SSH, puis ***collez*** cette clé dans les détails de connexion. ***N’entrez pas manuellement ou ne modifiez pas la clé*** car cela peut entraîner l’échec de la connexion. 
  > Pour plus d’informations, consultez les étapes détaillées plus loin dans cet article.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte SFTP. Pour démarrer avec un déclencheur SFTP-SSH, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action SFTP-SSH, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-sftp-with-ssh"></a>Se connecter à SFTP avec SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez « sftp ssh» comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 
   Dans la zone de recherche, entrez « sftp ssh » comme filtre. 
   Dans la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Fournissez les informations nécessaires pour votre connexion.

   > [!IMPORTANT] 
   >
   > Lorsque vous entrez votre clé privée SSH dans la propriété **Clé privée SSH**, suivez ces étapes supplémentaires pour vous assurer que vous fournissez la valeur complète et correcte pour cette propriété. 
   > Une clé non valide entraîne l’échec de la connexion.
   
   Bien qu’il est possible d’utiliser n’importe quel éditeur de texte, vous trouverez ici des exemples d’étapes montrant comment copier et coller correctement votre clé à l’aide de Notepad.exe.
    
   1. Ouvrez votre fichier de clé privée SSH dans un éditeur de texte. 
   Dans cet exemple, les étapes utilisent le Bloc-notes.

   1. Dans le menu **Edition** du Bloc-notes, choisissez **Sélectionner tout**.

   1. Sélectionnez **Edition** > **Copier**.

   1. Dans le déclencheur SFTP-SSH ou l’action que vous avez ajoutés, collez la clé *complète* que vous avez copiée dans la propriété **Clé privée SSH**, qui prend en charge plusieurs lignes. 
   ***Assurez-vous que vous collez*** la clé. ***N’entrez pas manuellement ou ne modifiez pas la clé***.

1. Après avoir entré les informations de connexion, choisissez **Créer**.

1. Fournissez maintenant les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="trigger-limits"></a>Limites de déclencheur

Les déclencheurs SFTP/SSH fonctionnent en interrogeant le système de fichiers SFTP et en recherchant tout fichier modifié depuis la dernière interrogation. Certains outils permettent de conserver l’horodatage lorsque les fichiers sont modifiés. Dans ce cas, vous devez désactiver cette fonctionnalité pour permettre l’exécution du déclencheur. Voici quelques paramètres communs :

| Client SFTP | Action | 
|-------------|--------| 
| Winscp | Accédez à **Options** > **Préférences** > **Transférer** > **Modifier** > **Conserver l’horodatage** > **Désactiver** |
| FileZilla | Accédez à **Transférer** > **Conserver les horodatages des fichiers transférés** > **Désactiver** | 
||| 

Quand un déclencheur détecte un nouveau fichier, il vérifie que le nouveau fichier est complet et non partiellement écrit. Par exemple, un fichier peut être en cours de modification lorsque le déclencheur vérifie le serveur de fichiers. Pour éviter de retourner un fichier partiellement écrit, le déclencheur note l’horodatage du fichier qui comporte des modifications récentes, mais ne retourne pas immédiatement ce fichier. Le déclencheur retourne le fichier uniquement lors d’une nouvelle interrogation du serveur. Parfois, ce comportement peut entraîner un retard correspondant à jusqu’à deux fois l’intervalle d’interrogation du déclencheur. 

Quand ils demandent le contenu du fichier, les déclencheurs n’obtiennent pas les fichiers supérieurs à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle : 

* Utilisez un déclencheur qui retourne des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)**.

* Suivez le déclencheur avec une action qui lit le fichier complet comme **Obtenir le contenu d’un fichier à l’aide du chemin** et faites en sorte que l’action utilise la [segmentation de messages](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Exemples

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - déclencheur SSH : Lors de l’ajout ou de la modification d’un fichier

Ce déclencheur démarre un flux de travail d’application logique quand un fichier est ajouté ou changé sur un serveur SFTP. Par exemple, vous pouvez ajouter une condition qui vérifie et extrait le contenu du fichier si ce contenu répond à une condition spécifiée. Vous pouvez ensuite ajouter une action qui obtient le contenu du fichier et le place dans un dossier sur le serveur SFTP. 

**Exemple en entreprise** : vous pouvez utiliser ce déclencheur pour superviser l’apparition dans un dossier SFTP de nouveaux fichiers représentant les commandes des clients. Vous pouvez ensuite utiliser une action SFTP comme **Obtenir le contenu du fichier** afin d’obtenir le contenu de la commande à des fins de traitement et stocker cette commande dans une base de données de commandes.

Quand ils demandent le contenu du fichier, les déclencheurs n’obtiennent pas les fichiers supérieurs à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle : 

* Utilisez un déclencheur qui retourne des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)**.

* Suivez le déclencheur avec une action qui lit le fichier complet comme **Obtenir le contenu d’un fichier à l’aide du chemin** et faites en sorte que l’action utilise la [segmentation de messages](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - action SSH : Obtenir le contenu à l’aide du chemin

Cette action obtient le contenu d’un fichier sur un serveur SFTP. Par exemple, vous pouvez ajouter le déclencheur de l’exemple précédent et une condition à laquelle le contenu du fichier doit satisfaire. Si la condition est vérifiée, l’action qui obtient le contenu peut s’exécuter. 

Quand ils demandent le contenu du fichier, les déclencheurs n’obtiennent pas les fichiers supérieurs à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle : 

* Utilisez un déclencheur qui retourne des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)**.

* Suivez le déclencheur avec une action qui lit le fichier complet comme **Obtenir le contenu d’un fichier à l’aide du chemin** et faites en sorte que l’action utilise la [segmentation de messages](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/sftpconnector/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
