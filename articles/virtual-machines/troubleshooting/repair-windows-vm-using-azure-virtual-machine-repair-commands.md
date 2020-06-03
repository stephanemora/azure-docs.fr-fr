---
title: Réparer une machine virtuelle Windows à l’aide des commandes de réparation de machine virtuelle Azure | Microsoft Docs
description: Cet article explique comment utiliser les commandes de réparation de machine virtuelle Azure pour connecter le disque à une autre machine virtuelle Windows afin de corriger les erreurs éventuelles, puis recréer votre machine virtuelle d’origine.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 6520d508a025aeeecf0c1890224a0691eae09f74
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774426"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Réparer une machine virtuelle Windows à l’aide des commandes de réparation de machine virtuelle Azure

Si votre machine virtuelle Windows dans Azure rencontre une erreur de démarrage ou une erreur de disque, vous devrez peut-être appliquer la procédure d’atténuation du problème sur le disque lui-même. Comme exemple courant, citons l’échec de mise à jour d’une application qui empêche le bon démarrage de la machine virtuelle. Cet article explique comment utiliser les commandes de réparation de machine virtuelle Azure pour connecter le disque à une autre machine virtuelle Windows afin de corriger les erreurs éventuelles, puis recréer votre machine virtuelle d’origine.

> [!IMPORTANT]
> Les scripts de cet article s’appliquent uniquement aux machines virtuelles qui utilisent [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Vue d’ensemble du processus de réparation

Vous pouvez désormais utiliser les commandes de réparation de machine virtuelle Azure pour changer le disque de système d’exploitation d’une machine virtuelle. Vous n’avez plus besoin de supprimer et de recréer la machine virtuelle.

Suivez ces étapes pour résoudre le problème de la machine virtuelle :

1. Lancement d’Azure Cloud Shell
2. Exécutez az extension add/update.
3. Exécutez az vm repair create.
4. Exécutez az vm repair run.
5. Exécutez az vm repair restore.

Pour obtenir de la documentation et des instructions supplémentaires, consultez [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemple de processus de réparation

> [!NOTE]
> * Une connectivité sortante à partir de la machine virtuelle (port 443) est nécessaire pour l’exécution du script.
> * Vous ne pouvez exécuter qu’un seul script à la fois.
> * Vous ne pouvez pas annuler un script en cours d’exécution.
> * La durée maximale d’exécution d’un script est de 90 minutes (le script expire après ce délai).

1. Lancement d’Azure Cloud Shell

   Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

   Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut à droite d’un bloc de code. Vous pouvez également ouvrir Cloud Shell dans un onglet de navigateur distinct en accédant à [https://shell.azure.com](https://shell.azure.com).

   Sélectionnez **Copier** pour copier les blocs de code, collez-les ensuite dans Cloud Shell, puis sélectionnez **Entrée** pour exécuter le code.

   Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite au minimum la version 2.0.30 d’Azure CLI. Exécutez ``az --version`` pour trouver la version. Si vous devez installer ou mettre à niveau votre interface Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Si vous utilisez les commandes `az vm repair` pour la première fois, ajoutez l’extension CLI vm-repair.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Si vous avez utilisé les commandes `az vm repair`, appliquez les mises à jour à l’extension vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Exécutez `az vm repair create`. Cette commande permet de créer une copie du disque de système d’exploitation de la machine virtuelle hors service, de créer une machine virtuelle de réparation dans un nouveau groupe de ressources, et d’attacher une copie du disque de système d’exploitation.  La machine virtuelle de réparation aura la même taille et sera dans la même région que la machine virtuelle hors service spécifiée.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Exécutez `az vm repair run`. Cette commande permet d’exécuter le script de réparation spécifié sur le disque attaché via la machine virtuelle de réparation.  Si le guide de résolution des problèmes que vous utilisez spécifie un ID d’exécution, utilisez-le ici. Autrement, vous pouvez utiliser la commande `az vm repair list-scripts` pour afficher les scripts de réparation disponibles.

   ```azurecli-interactive

   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id win-hello-world --verbose
   ```

5. Exécutez `az vm repair restore`. Cette commande permet de remplacer le disque de système d’exploitation réparé par le disque de système d’exploitation d’origine de la machine virtuelle.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Vérifier et activer les diagnostics de démarrage

L’exemple suivant active l’extension de diagnostic sur la machine virtuelle nommée ``myVMDeployed``, dans le groupe de ressources nommé ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Étapes suivantes

* Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez [Dépannage d’une connexion Bureau à distance à une machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Pour les problèmes d’accès aux applications s’exécutant sur votre machine virtuelle, consultez [Résoudre les problèmes de connectivité des applications sur des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Pour plus d’informations sur l’utilisation de Resource Manager, consultez la page [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
