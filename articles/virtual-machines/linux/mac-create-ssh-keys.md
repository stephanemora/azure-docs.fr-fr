---
title: Créer et utiliser une paire de clés SSH pour les machines virtuelles Linux dans Azure | Microsoft Docs
description: Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure afin d’améliorer la sécurité du processus d’authentification.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2018
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Étapes rapides : Créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure
Avec une paire de clés SSH (secure shell), vous pouvez créer des machines virtuelles sur Azure qui utilisent par défaut des clés SSH pour l’authentification, éliminant ainsi la nécessité de recourir aux mots de passe pour la connexion. Cet article décrit comment générer et utiliser rapidement une paire de clés publique et privée SSH pour des machines virtuelles Linux. Vous pouvez effectuer ces étapes avec Azure Cloud Shell, un hôte Linux ou macOS, le sous-système Windows pour Linux ou d’autres outils qui prennent en charge OpenSSH. 

Pour obtenir plus d’informations et des exemples, consultez [Étapes détaillées pour créer des paires de clés SSH](create-ssh-keys-detailed.md).

Pour découvrir d’autres méthodes permettant de générer et d’utiliser des clés SSH sur un ordinateur Windows, consultez [Utilisation de clés SSH avec Windows sur Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH
Utilisez la commande `ssh-keygen` pour générer des fichiers de clés publique et privée SSH créés par défaut dans le répertoire `~/.ssh`. Vous pouvez spécifier un autre emplacement et une phrase secrète supplémentaire (un mot de passe pour accéder au fichier de clé privée) quand vous y êtes invité. Si une paire de clés SSH existe dans l’emplacement actuel, les fichiers sont remplacés.

```bash
ssh-keygen -t rsa -b 2048
```

Si vous utilisez [Azure CLI 2.0](/cli/azure) pour créer votre machine virtuelle, vous pouvez éventuellement générer des fichiers de clés SSH publique et privée en exécutant la commande [az vm create](/cli/azure/vm#az_vm_create) avec l’option `--generate-ssh-keys`. Les clés sont stockées dans le répertoire ~/.ssh. Notez que cette option de commande ne remplace pas les clés qui existent déjà dans l’emplacement, le cas échéant.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fournir la clé publique SSH pendant le déploiement d’une machine virtuelle
Pour créer une machine virtuelle Linux qui utilise des clés SSH pour l’authentification, spécifiez votre clé publique SSH quand vous créez la machine virtuelle à l’aide du portail Azure, de l’interface CLI, des modèles Resource Manager ou d’autres méthodes :

* [Créer une machine virtuelle Linux avec le portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux avec Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux à l’aide d’un modèle Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Si vous n’êtes pas familiarisé avec le format des clés publiques SSH, vous pouvez voir votre clé publique en exécutant `cat` comme suit, en remplaçant `~/.ssh/id_rsa.pub` par l’emplacement de votre propre fichier de clé publique :

```bash
cat ~/.ssh/id_rsa.pub
```

Si vous copiez et collez le contenu du fichier de clé publique pour l’utiliser dans le portail Azure ou dans un modèle Resource Manager, veillez à ne pas copier pas les espaces blancs supplémentaires. Par exemple, si vous utilisez macOS, vous pouvez diriger le fichier de clé publique (par défaut, `~/.ssh/id_rsa.pub`) sur **pbcopy** pour en copier le contenu (d’autres programmes Linux ont la même fonction, par exemple, **xclip**).

La clé publique que vous placez sur votre machine virtuelle Linux dans Azure est stockée par défaut dans `~/.ssh/id_rsa.pub`, sauf si vous avez changé l’emplacement quand vous avez créé les clés. Si vous utilisez [Azure CLI 2.0](/cli/azure) pour créer votre machine virtuelle avec une clé publique existante, spécifiez la valeur ou l’emplacement de cette clé publique en exécutant la commande [az vm create](/cli/azure/vm#az_vm_create) avec l’option `--ssh-key-value`. 

## <a name="ssh-to-your-vm"></a>Établir une connexion SSH sur votre machine virtuelle
Une fois la clé publique déployée sur votre machine virtuelle Azure et la clé privée sur votre système local, établissez une connexion SSH sur votre machine virtuelle à l’aide de l’adresse IP ou du nom DNS de votre machine virtuelle. Remplacez *azureuser* et *myvm.westus.cloudapp.azure.com* dans la commande suivante par le nom d’administrateur et le nom de domaine complet (ou adresse IP) :

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si vous avez fourni un mot de passe lorsque vous avez créé votre paire de clés, saisissez-le lorsque vous y êtes invité pendant le processus de connexion. (Le serveur est ajouté à votre dossier `~/.ssh/known_hosts` et vous n’avez pas à vous connecter à nouveau tant que la clé publique sur votre machine virtuelle Azure n’est pas modifiée ou que le nom de serveur n’est pas supprimé du dossier `~/.ssh/known_hosts`.)

Les machines virtuelles créées à l’aide de clés SSH sont par défaut configurées avec les mots de passe désactivés, ce qui rend les tentatives de déchiffrement par force brute bien plus coûteuses et par conséquent difficiles. 

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit la création d’une paire de clés SSH simple pour une utilisation rapide. 

* Si vous avez besoin d’aide supplémentaire pour utiliser votre paire de clés SSH, consultez [Étapes détaillées pour créer et gérer des paires de clés SSH](create-ssh-keys-detailed.md).

* Si vous rencontrez des problèmes avec les connexions SSH sur une machine virtuelle Azure, consultez [Résoudre les problèmes de connexion SSH sur une machine virtuelle Linux Azure](troubleshoot-ssh-connection.md).


