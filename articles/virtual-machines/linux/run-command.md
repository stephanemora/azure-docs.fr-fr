---
title: Exécuter des scripts shell dans une machine virtuelle Linux sur Azure
description: Cette rubrique explique comment exécuter des scripts au sein d’une machine virtuelle Azure Linux à l’aide de la commande Run
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6550b6e3f59ff7e6bac39dfc1abcf829256122d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376350"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Exécuter des scripts shell dans votre machine virtuelle Linux avec la commande Run

La commande Run utilise l’agent de machine virtuelle pour exécuter des scripts shell au sein d’une machine virtuelle Linux Azure. Ces scripts peuvent être utilisés pour la gestion générale des machines ou applications, et servir à diagnostiquer et corriger rapidement des problèmes d’accès aux machines virtuelles et de réseau afin de ramener la machine virtuelle à un état correct.

## <a name="benefits"></a>Avantages

Il existe plusieurs options pour accéder à vos machines virtuelles. La commande Run peut exécuter à distance des scripts sur vos machines virtuelles à l’aide de l’agent de machine virtuelle. Elle peut être utilisée via le portail Azure, une [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) ou [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) pour des machines virtuelles Linux.

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
* Une connectivité sortante à partir de la machine virtuelle est nécessaire pour retourner les résultats du script.

> [!NOTE]
> Pour fonctionner correctement, la commande Run a besoin d’une connectivité (port 443) aux adresses IP publiques Azure. Si l’extension n’a pas accès à ces points de terminaison, il se peut que les scripts s’exécutent correctement, mais qu’ils ne retournent pas les résultats. Si vous bloquez le trafic sur la machine virtuelle, vous pouvez utiliser des [balises de service](../../virtual-network/security-overview.md#service-tags) pour autoriser le trafic à destination des adresses IP publiques Azure en utilisant la balise `AzureCloud`.

## <a name="available-commands"></a>Commandes disponibles

Ce tableau affiche la liste des commandes disponibles pour les machines virtuelles Linux. La commande **RunShellScript** peut être utilisée pour exécuter n’importe quel script personnalisé de votre choix. Quand vous utilisez Azure CLI ou PowerShell pour exécuter une commande, la valeur que vous fournissez pour le paramètre `--command-id` ou `-CommandId` doit être l’une des valeurs listées ci-dessous. Quand vous spécifiez une valeur qui n’est pas une commande disponible, vous recevez cette erreur.

```error
The entity was not found in this Azure location
```

|**Nom**|**Description**|
|---|---|
|**RunShellScript**|Exécute un script de shell Linux.|
|**ifconfig**| Obtenez la configuration de toutes les interfaces réseau.|

## <a name="azure-cli"></a>D’Azure CLI

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

### <a name="powershell"></a>PowerShell

L'exemple suivant utilise la cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) pour exécuter un script PowerShell sur une machine virtuelle Azure. Pour la cmdlet, le script référencé dans le paramètre `-ScriptPath` doit se situer au même emplacement qu'elle.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitation de l’accès à la commande Run

Le listage des commandes Run ou l’affichage des détails d’une commande nécessite l’autorisation `Microsoft.Compute/locations/runCommands/read` au niveau de l’abonnement, ce dont disposent le rôle intégré [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et les rôles supérieurs.

L’exécution d’une commande nécessite l’autorisation `Microsoft.Compute/virtualMachines/runCommand/action` au niveau de l’abonnement, ce que possèdent le rôle [Collaborateur de machines virtuelles](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) et les rôles supérieurs.

Vous pouvez utiliser un des rôles [intégrés](../../role-based-access-control/built-in-roles.md) ou créer un rôle [personnalisé](../../role-based-access-control/custom-roles.md) afin d’exécuter la commande Run.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Exécuter des scripts dans votre machine virtuelle Linux](run-scripts-in-vm.md) pour en savoir plus sur les autres méthodes pour exécuter à distance des commandes et des scripts dans votre machine virtuelle.
