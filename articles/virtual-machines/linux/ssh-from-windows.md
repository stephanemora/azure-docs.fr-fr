---
title: Utiliser des clés SSH avec Windows pour les machines virtuelles Linux
description: Apprenez à créer et à utiliser des clés SSH sur un ordinateur Windows pour vous connecter à une machine virtuelle Linux dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034809"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Comment utiliser des clés SSH avec Windows sur Azure

Cet article décrit plusieurs méthodes de génération et d’utilisation de clés SSH *Secure Shell* sur un ordinateur Windows pour créer une machine virtuelle Linux et s’y connecter dans Azure. Pour utiliser des clés SSH à partir d’un client Linux ou macOS, consultez le guide [rapide](mac-create-ssh-keys.md) ou [détaillé](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Packages Windows et clients SSH
Vous vous connectez à des machines virtuelles Linux et gérez ces machines dans Azure à l’aide d’un *client SSH*. Les ordinateurs qui exécutent Linux ou macOS ont généralement une suite de commandes SSH pour générer et gérer les clés SSH et établir les connexions SSH. 

Les ordinateurs Windows n’ont pas toujours des commandes SSH comparables installées. Les versions récentes de Windows 10 sont dotées de [commandes client OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) permettant de créer et de gérer des clés SSH, ainsi que d’établir des connexions SSH à partir d’une invite de commandes. Les versions récentes de Windows 10 intègrent également le [sous-système Windows pour Linux](https://docs.microsoft.com/windows/wsl/about), afin d’exécuter des utilitaires et d’y accéder, comme un client SSH intégré nativement dans un interpréteur de commandes Bash. 

Voici une liste des packages d’autres clients SSH Windows courants que vous pouvez installer localement :

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git pour Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Vous pouvez aussi utiliser les utilitaires SSH disponibles dans Bash dans [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Accédez à Cloud Shell dans votre navigateur web en entrant [https://shell.azure.com](https://shell.azure.com) ou dans le [portail Azure](https://portal.azure.com). 
* Accédez à Cloud Shell sous forme de terminal à partir de Visual Studio Code en installant [l’extension de compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH
Les sections suivantes décrivent deux options de création d’une paire de clés SSH sur Windows. Vous pouvez utiliser une commande d’interpréteur de commandes (`ssh-keygen`) ou un outil GUI (PuTTYgen). Notez également que lorsque vous utilisez PowerShell pour créer une clé, vous devez charger la clé publique au format ssh.com (SECSH). Si vous utilisez l’interface CLI, convertissez la clé au format OpenSSH avant le chargement. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Créer des clés SSH avec ssh-keygen

Si vous exécutez un interpréteur de commandes sur Windows qui prend en charge des outils du client SSH (ou si vous utilisez Azure Cloud Shell), créez une paire de clés SSH à l’aide de la commande `ssh-keygen`. Tapez la commande suivante et suivez les invites. Si une paire de clés SSH existe dans l’emplacement choisi, les fichiers sont remplacés. 

```bash
ssh-keygen -t rsa -b 2048
```

Pour plus d’informations, consultez les étapes [rapides](mac-create-ssh-keys.md) ou [détaillées](create-ssh-keys-detailed.md) pour créer les clés SSH avec `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Créer des clés SSH avec PuTTYgen

Si vous préférez utiliser un outil GUI pour créer les clés SSH, vous pouvez utiliser le générateur de clé PuTTYgen, inclus dans le [package de téléchargement de PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Pour créer une paire de clés SSH RSA avec PuTTYgen :

1. Démarrez PuTTYgen.

2. Cliquez sur **Générer**. Par défaut, PuTTYgen génère une clé SSH-2 RSA 2048 bits.

4. Survolez la zone vide avec la souris pour donner un caractère aléatoire à la clé.

5. Une fois la clé publique générée, vous pouvez entrer une phrase secrète et la confirmer. Vous êtes invité à entrer la phrase secrète quand vous vous authentifiez auprès de la machine virtuelle avec votre clé SSH privée. Sans cette phrase secrète, toute personne qui récupère votre clé privée peut se connecter à une machine virtuelle ou un service utilisant cette clé. Nous vous recommandons de créer une phrase secrète. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.

6. La clé publique apparaît en haut de la fenêtre. Vous copiez l’intégralité de cette clé publique et la collez dans le portail Azure ou dans un modèle Azure Resource Manager quand vous créez une machine virtuelle Linux. Vous pouvez également sélectionner **Enregistrer la clé publique** pour enregistrer une copie sur votre ordinateur :

    ![Enregistrer le fichier de clé publique PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Pour enregistrer la clé privée au format de clé privée PuTTy (fichier .ppk), vous pouvez aussi sélectionner **Enregistrer la clé privée**. Vous aurez besoin du fichier .ppk par la suite si vous voulez utiliser PuTTY pour établir une connexion SSH à la machine virtuelle.

    ![Enregistrer le fichier de clé privée PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Si vous voulez enregistrer la clé privée au format OpenSSH, le format de clé privée utilisé par de nombreux clients SSH, sélectionnez **Conversions** > **Exporter la clé OpenSSH**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fournir une clé publique SSH lors du déploiement d’une machine virtuelle

Pour créer une machine virtuelle Linux qui utilise des clés SSH pour l’authentification, fournissez votre clé publique SSH à la création de la machine virtuelle à l’aide du portail Azure ou d’autres méthodes.

L’exemple suivant montre comment copier et coller cette clé publique dans le portail Azure lorsque vous créez une machine virtuelle Linux. Généralement, la clé publique est ensuite stockée dans le répertoire ~/.ssh/authorized_key sur votre nouvelle machine virtuelle.

   ![Utiliser la clé publique lorsque vous créez une machine virtuelle dans le portail Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

L’une des méthode permettant d’établir une connexion SSH à votre machine virtuelle Linux à partir de Windows est d’utiliser un client SSH. Il s’agit de la méthode par défaut si vous avez un client SSH installé sur votre système Windows ou si vous utilisez les outils SSH dans Bash dans Azure Cloud Shell. Si vous préférez un outil GUI, vous pouvez vous connecter avec PuTTY.  

### <a name="use-an-ssh-client"></a>Utiliser un client SSH
Une fois la clé publique déployée sur votre machine virtuelle Azure et la clé privée sur votre système local, établissez une connexion SSH sur votre machine virtuelle à l’aide de l’adresse IP ou du nom DNS de votre machine virtuelle. Remplacez *azureuser* et *myvm.westus.cloudapp.azure.com* dans la commande suivante par le nom d’administrateur et le nom de domaine complet (ou adresse IP) :

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si vous avez configuré une phrase secrète au moment de créer votre paire de clés, entrez-la quand vous y êtes invité pendant le processus de connexion.

Si la machine virtuelle utilise la stratégie juste-à-temps, vous devez demander à y accéder avant de pouvoir vous connecter à cette machine virtuelle. Pour plus d’informations sur la stratégie juste-à-temps, consultez [Gérer l’accès à la machine virtuelle à l’aide de la stratégie juste-à-temps](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Se connecter avec PuTTY

Si vous avez installé le [package de téléchargement de PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) et généré précédemment un fichier de clé privée PuTTY (.ppk), vous pouvez vous connecter à une machine virtuelle Linux avec PuTTY.

1. Démarrez PuTTY.

2. Renseignez le nom d’hôte ou l’adresse IP de votre machine virtuelle à partir du portail Azure :

    ![Ouvrir une nouvelle connexion PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Sélectionnez la catégorie **Connexion** > **SSH** > **Auth**. Recherchez et sélectionnez votre clé privée PuTTY (fichier .ppk) :

    ![Sélectionner votre clé privée PuTTY pour l’authentification](./media/ssh-from-windows/putty-auth-dialog.png)

4. Cliquez sur **Ouvrir** pour vous connecter à votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* Pour connaître les étapes détaillées, les options et des exemples avancés d’utilisation de clés SSH, consultez [Étapes détaillées pour créer des paires de clés SSH](create-ssh-keys-detailed.md).

* Vous pouvez également utiliser PowerShell dans Azure Cloud Shell pour générer des clés SSH et établir des connexions SSH sur des machines virtuelles Linux. Consultez [Démarrage rapide de PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Si vous avez des difficultés à utiliser SSH pour vous connecter à vos machines virtuelles Linux, consultez [Résoudre les problèmes de connexion SSH à une machine virtuelle Linux Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
