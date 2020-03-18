---
title: Se connecter à un serveur SFTP avec SSH
description: Automatiser les tâches qui supervisent, créent, gèrent, envoient et reçoivent des fichiers pour un serveur SFTP à l’aide de SSH et d’Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128410"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Superviser, créer et gérer des fichiers SFTP à l’aide de SSH et d’Azure Logic Apps

Pour automatiser les tâches qui surveillent, créent, envoient et reçoivent des fichiers sur un serveur [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) à l’aide du protocole [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), vous pouvez générer et automatiser les workflows d’intégration à l’aide d’Azure Logic Apps et du connecteur SFTP-SSH. SFTP est un protocole réseau qui fournit un accès aux fichiers, le transfert de fichiers et la gestion des fichiers sur n’importe quel flux de données fiable. Voici quelques exemples de tâches que vous pouvez automatiser :

* Superviser à quel moment des fichiers sont ajoutés ou changés.
* Obtenir, créer, copier, renommer, mettre à jour, lister et supprimer des fichiers.
* Créez des dossiers.
* Obtenir les métadonnées et le contenu des fichiers.
* Extraire des archives dans des dossiers.

Vous pouvez utiliser des déclencheurs qui surveillent les événements sur votre serveur SFTP et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent diverses tâches sur votre serveur SFTP. Vous pouvez également faire en sorte que d’autres actions de votre application logique utilisent la sortie d’actions SFTP. Par exemple, si vous récupérez régulièrement des fichiers de votre serveur SFTP, vous pouvez envoyer des alertes par e-mail au sujet de ces fichiers et de leur contenu en utilisant le connecteur Office 365 Outlook ou le connecteur Outlook.com. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

Pour connaître les différences entre le connecteur SFTP-SSH et le connecteur SFTP, consultez la section [Comparer SFTP-SSH et SFTP](#comparison) plus loin dans cette rubrique.

## <a name="limits"></a>limites

* Les actions SFTP-SSH prenant en charge la [segmentation](../logic-apps/logic-apps-handle-large-messages.md) peuvent gérer des fichiers jusqu’à 1 Go, tandis que les actions SFTP-SSH ne prenant pas en charge la segmentation peuvent gérer des fichiers jusqu’à 50 Mo. Bien que la taille de segment par défaut soit de 15 Mo, cette taille peut changer dynamiquement, à compter de 5 Mo et augmenter progressivement jusqu’à 50 Mo maximum, selon différents facteurs tels que la latence du réseau, le temps de réponse du serveur, etc.

  > [!NOTE]
  > Pour les applications logiques utilisées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur avec l’étiquette ISE applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

  Vous pouvez passer outre ce comportement adaptatif lorsque vous [spécifiez une taille de bloc constante](#change-chunk-size) à utiliser à la place. Cette taille peut être comprise entre 5 Mo et 50 Mo. Par exemple, supposons que vous disposez d’un fichier de 45 Mo et d’un réseau pouvant prendre en charge cette taille de fichier sans latence. La segmentation adaptative génère plusieurs appels plutôt qu’un seul. Pour réduire le nombre d’appels, vous pouvez essayer de définir une taille de bloc de 50 Mo. Dans un autre scénario, si votre application logique a un délai d’expiration, par exemple lorsque vous utilisez des blocs de 15 Mo, vous pouvez essayer de réduire la taille à 5 Mo.

  La taille de segment est associée à une connexion, ce qui signifie que vous pouvez utiliser la même connexion pour les actions prenant en charge la segmentation, puis pour les actions ne prenant pas en charge la segmentation. Dans ce cas, la taille de segment pour les actions ne prenant pas en charge les plages de segmentation est comprise entre 5 Mo et 50 Mo. Ce tableau indique les actions SFTP-SSH prenant en charge la segmentation :

  | Action | Prise en charge de la segmentation | Remplacement de la prise en charge de la taille de bloc |
  |--------|------------------|-----------------------------|
  | **Copier un fichier** | Non | Non applicable |
  | **Créer un fichier** | Oui | Oui |
  | **Créer un dossier** | Non applicable | Non applicable |
  | **Supprimer un fichier** | Non applicable | Non applicable |
  | **Extraire une archive dans un dossier** | Non applicable | Non applicable |
  | **Obtenir le contenu d’un fichier** | Oui | Oui |
  | **Obtenir le contenu d’un fichier à l’aide du chemin** | Oui | Oui |
  | **Obtenir les métadonnées d’un fichier** | Non applicable | Non applicable |
  | **Obtenir les métadonnées d’un fichier à l’aide du chemin** | Non applicable | Non applicable |
  | **Répertorier les fichiers dans un dossier** | Non applicable | Non applicable |
  | **Renommer le fichier** | Non applicable | Non applicable |
  | **Mettre à jour un fichier** | Non | Non applicable |
  ||||

* Les déclencheurs SFTP-SSH ne prennent pas en charge la segmentation des messages. Quand ils demandent le contenu des fichiers, les déclencheurs sélectionnent uniquement les fichiers dont la taille est inférieure ou égale à 15 Mo. Pour obtenir des fichiers supérieurs à 15 Mo, suivez plutôt ce modèle :

  1. Utilisez un déclencheur SFTP-SSH qui retourne uniquement des propriétés de fichier comme **Quand un fichier est ajouté ou modifié (propriétés uniquement)** .

  1. Suivez le déclencheur avec l’action SFTP-SSH **Obtenir le contenu du fichier**, qui lit le fichier complet et utilise implicitement la segmentation des messages.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparer SFTP/SSH par rapport à SFTP

Voici les autres principales différences entre le connecteur SFTP-SSH et le connecteur SFTP où le connecteur SFTP-SSH offre les fonctionnalités suivantes :

* Il utilise la [bibliothèque SSH.NET](https://github.com/sshnet/SSH.NET), qui est une bibliothèque Secure Shell (SSH) open source prenant en charge .NET.

* Fournit l’action **Créer un dossier**, qui crée un dossier au niveau du chemin spécifié sur le serveur SFTP.

* Fournit l’action **Renommer le fichier**, qui renomme un fichier sur le serveur SFTP.

* Met en cache *jusqu’à 1 heure* la connexion au serveur SFTP, ce qui améliore les performances et réduit le nombre de tentatives de connexion au serveur. Pour définir la durée de ce comportement de mise en cache, modifiez la propriété [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) dans la configuration SSH sur votre serveur SFTP.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Vos informations d’identification de compte et adresse du serveur SFTP, qui permettent à votre application logique d’accéder à votre compte SFTP. Vous devez également accéder à une clé privée SSH et au mot clé privé SSH. Pour utiliser la segmentation lors du chargement de fichiers volumineux, vous devez disposer d’autorisations d’accès en lecture et en écriture au dossier racine de votre serveur SFTP. Sinon, vous obtiendrez une erreur « 401 non autorisé ».

  > [!IMPORTANT]
  >
  > Le connecteur SFTP-SSH prend en charge *uniquement* ces clés privées, formats, algorithmes et empreintes digitales :
  >
  > * **Formats de clé privée** : les clés RSA (Rivest Shamir Adleman) et DSA (Digital Signature Algorithm) aux formats OpenSSH et ssh.com. Si votre clé privée est au format de fichier PuTTY (.ppk), commencez par [convertir la clé au format de fichier OpenSSH (.pem)](#convert-to-openssh).
  >
  > * **Algorithmes de chiffrement** : DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC, and AES-256-CBC
  >
  > * **Empreinte digitale** : MD5
  >
  > Après avoir ajouté le déclencheur SFTP-SSH ou l’action souhaitée pour votre application logique, vous devez fournir les informations de connexion pour votre serveur SFTP. Quand vous fournissez votre clé privée SSH pour cette connexion, ***vous ne devez pas entrer ou modifier manuellement la clé***, car ceci pourrait entraîner l’échec de la connexion. Veillez plutôt à ***copier la clé*** à partir de votre fichier de clé privée SSH, puis à la ***coller*** dans les informations de connexion. 
  > Pour plus d’informations, consultez la section [Se connecter à SFTP avec SSH](#connect) plus loin dans cet article.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte SFTP. Pour démarrer avec un déclencheur SFTP-SSH, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action SFTP-SSH, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="how-sftp-ssh-triggers-work"></a>Fonctionnement des déclencheurs SFTP-SSH

Les déclencheurs SFTP/SSH fonctionnent en interrogeant le système de fichiers SFTP et en recherchant tout fichier modifié depuis la dernière interrogation. Certains outils permettent de conserver l’horodatage lorsque les fichiers sont modifiés. Dans ce cas, vous devez désactiver cette fonctionnalité pour permettre l’exécution du déclencheur. Voici quelques paramètres communs :

| Client SFTP | Action |
|-------------|--------|
| Winscp | Accédez à **Options** > **Préférences** > **Transférer** > **Modifier** > **Conserver l’horodatage** > **Désactiver** |
| FileZilla | Accédez à **Transférer** > **Conserver les horodatages des fichiers transférés** > **Désactiver** |
|||

Quand un déclencheur détecte un nouveau fichier, il vérifie que le nouveau fichier est complet et non partiellement écrit. Par exemple, un fichier peut être en cours de modification lorsque le déclencheur vérifie le serveur de fichiers. Pour éviter de retourner un fichier partiellement écrit, le déclencheur note l’horodatage du fichier qui comporte des modifications récentes, mais ne retourne pas immédiatement ce fichier. Le déclencheur retourne le fichier uniquement lors d’une nouvelle interrogation du serveur. Parfois, ce comportement peut entraîner un retard correspondant à jusqu’à deux fois l’intervalle d’interrogation du déclencheur.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Convertir une clé PuTTY au format OpenSSH

Si votre clé privée est au format PuTTY (qui utilise l’extension de nom de fichier .ppk pour PuTTY Private Key), convertissez d’abord la clé au format OpenSSH, qui utilise l’extension .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Système d’exploitation UNIX

1. Si ce n’est déjà fait, installez les outils PuTTY sur votre système. Vous pouvez utiliser la commande suivante, par exemple :

   `sudo apt-get install -y putty`

1. Exécutez cette commande, qui crée un fichier que vous pouvez utiliser avec le connecteur SFTP-SSH :

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Par exemple :

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Système d’exploitation Windows

1. Si ce n’est déjà fait, [téléchargez la dernière version du générateur de clé PuTTY (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), puis lancez le générateur.

1. Sur l’écran suivant, sélectionnez **Load**.

   ![Sélectionner « Load »](./media/connectors-sftp-ssh/puttygen-load.png)

1. Accédez à votre fichier de clé privée au format PuTTY, puis sélectionnez **Open**.

1. Dans le menu **Conversions**, sélectionnez **Export OpenSSH key**.

   ![Sélectionner « Export OpenSSH key »](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Enregistrez le fichier de clé privée avec l’extension `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Se connecter à SFTP avec SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Pour les applications logiques vides, dans la zone de recherche, entrez `sftp ssh` comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité.

   -ou-

   Pour les applications logiques existantes, sous la dernière étape où vous souhaitez ajouter une action, sélectionnez **Nouvelle étape**. Dans la zone de recherche, entrez `sftp ssh` en guise de filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Fournissez les informations nécessaires pour votre connexion.

   > [!IMPORTANT]
   >
   > Lorsque vous entrez votre clé privée SSH dans la propriété **Clé privée SSH**, suivez ces étapes supplémentaires pour vous assurer que vous fournissez la valeur complète et correcte pour cette propriété. Une clé non valide entraîne l’échec de la connexion.

   Bien qu’il est possible d’utiliser n’importe quel éditeur de texte, vous trouverez ici des exemples d’étapes montrant comment copier et coller correctement votre clé à l’aide de Notepad.exe.

   1. Ouvrez votre fichier de clé privée SSH dans un éditeur de texte. Dans cet exemple, les étapes utilisent le Bloc-notes.

   1. Dans le menu **Edition** du Bloc-notes, choisissez **Sélectionner tout**.

   1. Sélectionnez **Edition** > **Copier**.

   1. Dans le déclencheur SFTP-SSH ou l’action que vous avez ajoutés, collez la clé *complète* que vous avez copiée dans la propriété **Clé privée SSH**, qui prend en charge plusieurs lignes.  ***Assurez-vous que vous collez*** la clé. ***N’entrez pas manuellement ou ne modifiez pas la clé***.

1. Après avoir entré les informations de connexion, sélectionnez **Créer**.

1. Fournissez maintenant les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Remplacer la taille de bloc

Pour remplacer le comportement adaptatif par défaut utilisé par la segmentation, vous pouvez spécifier une taille de bloc constante comprise entre 5 Mo et 50 Mo.

1. En haut à droite de l’action, sélectionnez le bouton représentant des points de suspension ( **…** ), puis **Paramètres**.

   ![Ouvrir les paramètres SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Sous **Transfert de contenu**, dans la propriété **Taille de segment**, entrez une valeur entière comprise entre `5` et `50`, par exemple : 

   ![Spécifier la taille de bloc à utiliser à la place](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Quand vous avez terminé, cliquez sur **Terminé**.

## <a name="examples"></a>Exemples

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - déclencheur SSH : Lors de l’ajout ou de la modification d’un fichier

Ce déclencheur démarre un flux de travail d’application logique quand un fichier est ajouté ou changé sur un serveur SFTP. Par exemple, vous pouvez ajouter une condition qui vérifie et extrait le contenu du fichier si ce contenu répond à une condition spécifiée. Vous pouvez ensuite ajouter une action qui obtient le contenu du fichier et le place dans un dossier sur le serveur SFTP.

**Exemple en entreprise** : vous pouvez utiliser ce déclencheur pour superviser l’apparition dans un dossier SFTP de nouveaux fichiers représentant les commandes des clients. Vous pouvez ensuite utiliser une action SFTP comme **Obtenir le contenu du fichier** afin d’obtenir le contenu de la commande à des fins de traitement et stocker cette commande dans une base de données de commandes.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - action SSH : Obtenir le contenu à l’aide du chemin

Cette action obtient le contenu d’un fichier sur un serveur SFTP. Par exemple, vous pouvez ajouter le déclencheur de l’exemple précédent et une condition à laquelle le contenu du fichier doit satisfaire. Si la condition est vérifiée, l’action qui obtient le contenu peut s’exécuter.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques sur ce connecteur, notamment au sujet des déclencheurs, des actions et des limites décrits dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> Pour les applications logiques utilisées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur avec l’étiquette ISE applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
