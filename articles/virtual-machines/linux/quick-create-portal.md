---
title: 'Démarrage rapide : Créer une machine virtuelle Linux dans le portail Azure'
description: Dans ce démarrage rapide, vous allez apprendre à utiliser le Portail Azure pour créer une machine virtuelle Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bc1dd56cd024ee65e29f227f4ec11cde436e388d
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80294774"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Démarrage rapide : Créer une machine virtuelle Linux dans le portail Azure

Le Portail Azure peut être utilisé pour créer des machines virtuelles Azure. Le portail Azure est une interface utilisateur basée sur un navigateur permettant de créer des ressources Azure. Ce démarrage rapide explique comment utiliser le portail Azure pour déployer une machine virtuelle Linux exécutant Ubuntu 18.04 LTS. Pour voir votre machine virtuelle en action, vous établissez également une connexion SSH à la machine virtuelle et installez le serveur web NGINX.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-ssh-key-pair"></a>Créer la paire de clés SSH

Vous aurez besoin d’une paire de clés SSH pour suivre ce guide de démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape.

Ouvrez un interpréteur de commandes bash et utilisez [ssh-keygen](https://www.ssh.com/ssh/keygen/) pour créer une paire de clés SSH. Si vous n’avez pas d’interpréteur de commandes bash sur votre ordinateur local, vous pouvez utiliser [Azure Cloud Shell](https://shell.azure.com/bash).


1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu situé en haut de la page, sélectionnez l’icône `>_` pour ouvrir Cloud Shell.
1. Assurez-vous que Cloud Shell indique **Bash** dans l’angle supérieur gauche. S’il s’agit de PowerShell, utilisez la liste déroulante pour sélectionner **Bash** et sélectionnez **Confirmer** pour passer à l’interpréteur de commandes Bash.
1. Tapez `ssh-keygen -t rsa -b 2048` pour créer la clé SSH. 
1. Vous êtes invité à entrer un fichier dans lequel enregistrer la paire de clés. Appuyez simplement sur **Entrée** pour enregistrer dans l’emplacement par défaut, indiqué entre crochets. 
1. Vous êtes invité à entrer une phrase secrète. Vous pouvez taper une phrase secrète pour votre clé SSH ou appuyer sur **Entrée** pour continuer sans phrase secrète.
1. La commande `ssh-keygen` génère des clés publiques et privées portant le nom par défaut `id_rsa` dans `~/.ssh directory`. La commande renvoie le chemin d’accès complet à la clé publique. Utilisez le chemin d’accès à la clé publique pour afficher son contenu avec `cat` en tapant `cat ~/.ssh/id_rsa.pub`.
1. Copiez la sortie de cette commande et enregistrez-la quelque part pour l’utiliser plus loin dans cet article. Il s’agit de votre clé publique. Elle vous sera utile au moment de configurer votre compte administrateur pour vous connecter à votre machine virtuelle.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Tapez **machines virtuelles** dans la zone de recherche.
1. Sous **Services**, sélectionnez **Machines virtuelles**.
1. Sur la page **Machines virtuelles**, sélectionnez **Ajouter**. La page **Créer une machine virtuelle** s’ouvre.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez le nom *myResourceGroup*. 

    ![Créer un groupe de ressources pour votre machine virtuelle](./media/quick-create-portal/project-details.png)

1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Région** et *Ubuntu 18.04 LTS* comme **Image**. Conservez les autres valeurs par défaut.

    ![Section Détails de l’instance](./media/quick-create-portal/instance-details.png)

1. Sous **Compte d’administrateur**, sélectionnez **Clé publique SSH**, tapez votre nom d’utilisateur, puis collez votre clé publique. Supprimez les espaces blancs au début ou à la fin de votre clé publique.

    ![Compte d’administrateur](./media/quick-create-portal/administrator-account.png)

1. Sous **Règles des ports d’entrée** > **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** et **HTTP (80)** dans la liste déroulante. 

    ![Ports ouverts pour RDP et HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.

1. Sur la page **Create a virtual machine** (Créer une machine virtuelle), vous pouvez voir les détails de la machine virtuelle que vous allez créer. Lorsque vous êtes prêt, sélectionnez **Créer**.

Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle. Lorsque le déploiement est terminé, passez à la section suivante.

    
## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Créez une connexion SSH avec la machine virtuelle.

1. Sélectionnez le bouton **Se connecter** sur la page de présentation de la machine virtuelle. 

    ![Portail 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Sur la page **Se connecter à la machine virtuelle**, conservez les options par défaut pour vous connecter par adresse IP sur le port 22. Dans **Se connecter à l’aide d’un compte local de machine virtuelle**, une commande de connexion s’affiche. Sélectionnez le bouton pour copier la commande. L’exemple suivant montre la commande de connexion SSH :

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Avec le même interpréteur de commandes Bash que celui utilisé pour créer votre paire de clés SSH (vous pouvez rouvrir Cloud Shell en resélectionnant `>_` ou en accédant à `https://shell.azure.com/bash`), collez la commande de connexion SSH dans l’interpréteur de commandes pour créer une session SSH.

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web NGINX. Dans votre session SSH, mettez à jour vos sources de package, puis installez le dernier package NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Lorsque vous avez terminé, tapez `exit` pour quitter la session SSH.


## <a name="view-the-web-server-in-action"></a>Voir le serveur web en action

Utilisez le navigateur web de votre choix pour visualiser la page d’accueil NGINX par défaut. Entrez l’adresse IP publique de la machine virtuelle comme adresse web. Vous trouverez l’adresse IP publique sur la page de vue d’ensemble de la machine virtuelle ou en tant que partie de la chaîne de connexion SSH que vous avez utilisée précédemment.

![Site par défaut NGINX](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour cela, sélectionnez le groupe de ressources de la machine virtuelle, sélectionnez **Supprimer**, puis confirmez le nom du groupe de ressources à supprimer.

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, créé un Groupe de sécurité réseau et une règle, et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Linux.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Linux](./tutorial-manage-vm.md)
