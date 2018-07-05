---
title: Exécuter des scripts shell dans une machine virtuelle Linux sur Azure
description: Cette rubrique explique comment exécuter des scripts au sein d’une machine virtuelle Azure Linux à l’aide de la commande Run
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 93972c88676cceef07b39b4a4ed34bab5b74cbd3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334871"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Exécuter des scripts shell dans votre machine virtuelle Linux avec la commande Run

La commande Run utilise l’agent de machine virtuelle pour exécuter des scripts shell au sein d’une machine virtuelle Linux Azure. Ces scripts peuvent être utilisés pour la gestion générale des machines ou applications, et servir à diagnostiquer et corriger rapidement des problèmes d’accès aux machines virtuelles et de réseau afin de ramener la machine virtuelle à un état correct.

## <a name="benefits"></a>Avantages

Il existe plusieurs options pour accéder à vos machines virtuelles. La commande Run peut exécuter à distance des scripts sur vos machines virtuelles à l’aide de l’agent de machine virtuelle. Elle peut être utilisée via le portail Azure, l’[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), l’[interface de ligne de commande Azure (CLI)](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ou [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Cette fonctionnalité est utile dans tous les scénarios qui impliquent l’exécution d’un script au sein d’une machine virtuelle ; elle constitue un des seuls moyens de dépanner et de corriger une machine virtuelle dont le port RDP ou SSH n’est pas ouvert en raison d’une configuration incorrecte du réseau ou de l’utilisateur administrateur.

## <a name="restrictions"></a>Restrictions

Voici une liste des restrictions présentes lors de l’utilisation de la commande Run.

* La sortie est limitée aux derniers 4 096 octets
* La durée minimale d’exécution d’un script est d’environ 20 secondes
* Les scripts s’exécutent par défaut en tant qu’un utilisateur avec élévation de privilèges sous Linux
* Vous ne pouvez exécuter qu’un script à la fois
* Les scripts qui demandent des informations (mode interactif) ne sont pas pris en charge.
* Vous ne pouvez pas annuler un script en cours d’exécution
* La durée maximale d’exécution d’un script est de 90 minutes (le script expiré après ce délai)

## <a name="azure-cli"></a>Azure CLI

Voici un exemple utilisant la commande [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) pour exécuter un script shell sur une machine virtuelle Linux Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Pour exécuter des commandes en tant qu’un autre utilisateur, vous pouvez utiliser `sudo -u` pour spécifier un compte d’utilisateur à utiliser.

## <a name="azure-portal"></a>Portail Azure

Accédez à une machine virtuelle dans [Azure](https://portal.azure.com) et sélectionnez la **commande Run** sous **OPERATIONS**. Une liste des commandes pouvant s’exécuter sur la machine virtuelle apparaît.

![Liste des commandes Run](./media/run-command/run-command-list.png)

Choisissez une commande à exécuter. Certaines des commandes peuvent avoir des paramètres d’entrée facultatifs ou obligatoires. Pour ces commandes, les paramètres sont présentés en tant que champs de texte vous permettant de fournir les valeurs d’entrée. Pour chaque commande, vous pouvez afficher le script en cours d’exécution en développant **Afficher le script**. La commande **RunShellScript** est différent des autres commandes car elle vous permet de fournir votre propre script personnalisé. 

> [!NOTE]
> Les commandes intégrées ne sont pas modifiables.

Une fois la commande choisie, cliquez sur **Run** pour exécuter le script. Le script s’exécute puis, une fois terminé, renvoie la sortie et toutes les erreurs dans la fenêtre de sortie. La capture d’écran suivante montre un exemple de sortie de l’exécution de la commande **ifconfig**.

![Sortie du script d’une commande Run](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Commandes disponibles

Ce tableau affiche la liste des commandes disponibles pour les machines virtuelles Linux. La commande **RunShellScript** peut être utilisée pour exécuter n’importe quel script personnalisé de votre choix.

|**Name**|**Description**|
|---|---|
|**RunShellScript**|Exécute un script de shell Linux.|
|**ifconfig**| Obtenez la configuration de toutes les interfaces réseau.|

## <a name="limiting-access-to-run-command"></a>Limitation de l’accès à la commande Run

Répertorier les commandes Run ou afficher les détails d’une commande nécessite l’autorisation `Microsoft.Compute/locations/runCommands/read`, disponible avec le rôle intégré [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et les rôles supérieurs.

L’exécution d’une commande nécessite l’autorisation `Microsoft.Compute/virtualMachines/runCommand/action`, disponible avec le rôle [Collaborateur](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) et les rôles supérieurs.

Vous pouvez utiliser un des rôles [intégrés](../../role-based-access-control/built-in-roles.md) ou créer un rôle [personnalisé](../../role-based-access-control/custom-roles.md) afin d’exécuter la commande Run.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Exécuter des scripts dans votre machine virtuelle Linux](run-scripts-in-vm.md) pour en savoir plus sur les autres méthodes pour exécuter à distance des commandes et des scripts dans votre machine virtuelle.
