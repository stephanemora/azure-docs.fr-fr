---
title: Exécuter des scripts PowerShell dans une machine virtuelle Windows dans Azure
description: Cette rubrique explique comment exécuter des scripts PowerShell au sein d’une machine virtuelle Azure Windows à l’aide de la commande Run
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ddbac24020110e32792286a1ac64070316cfb081
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332712"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Exécuter des scripts PowerShell dans votre machine virtuelle Windows avec la commande Run

La commande Run utilise l’agent de machine virtuelle pour exécuter des scripts PowerShell au sein d’une machine virtuelle Windows Azure. Ces scripts peuvent être utilisés pour la gestion générale des machines ou applications, et servir à diagnostiquer et corriger rapidement des problèmes d’accès aux machines virtuelles et de réseau afin de ramener la machine virtuelle à un état correct.

## <a name="benefits"></a>Avantages

Il existe plusieurs options pour accéder à vos machines virtuelles. La commande Run peut exécuter à distance des scripts sur vos machines virtuelles à l’aide de l’agent de machine virtuelle. Elle peut être utilisée via le portail Azure, l’[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), l’[interface de ligne de commande Azure (CLI)](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ou [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Cette fonctionnalité est utile dans tous les scénarios dans lesquels vous exécutez un script au sein d’une machine virtuelle, et elle constitue une des seules méthodes capables de corriger une machine virtuelle dépourvue d’un port RDP ou SSH ouvert en raison d’une configuration incorrecte du réseau ou de l’administrateur.

## <a name="restrictions"></a>Restrictions

Les restrictions suivantes s'appliquent lors de l'utilisation de la commande Run :

* La sortie est limitée aux derniers 4096 octets
* La durée minimale d’exécution d’un script est d’environ 20 secondes
* Les scripts s’exécutent en tant que système sous Windows
* Vous ne pouvez exécuter qu’un script à la fois
* Vous ne pouvez pas annuler un script en cours d’exécution
* La durée maximale d’exécution d’un script est de 90 minutes (le script expiré après ce délai)

**PermissionsConfig-OrchestratorUsersGroup***GroupName***-OrchestratorUser***UserName***\-remote** 

## <a name="run-a-command"></a>Exécuter une commande

Accédez à une machine virtuelle dans [Azure](https://portal.azure.com) et sélectionnez la **commande Run** sous **OPERATIONS**. Une liste des commandes pouvant s’exécuter sur la machine virtuelle apparaît.

![Liste des commandes Run](./media/run-command/run-command-list.png)

Choisissez une commande à exécuter. Certaines des commandes peuvent avoir des paramètres d’entrée facultatifs ou obligatoires. Pour ces commandes, les paramètres sont présentés en tant que champs de texte vous permettant de fournir les valeurs d’entrée. Pour chaque commande, vous pouvez afficher le script en cours d’exécution en développant **Afficher le script**. La commande **RunPowerShellScript** est différent des autres commandes car elle vous permet de fournir votre propre script personnalisé.

> [!NOTE]
> Les commandes intégrées ne sont pas modifiables.

Une fois la commande choisie, cliquez sur **Run** pour exécuter le script. Le script s’exécute puis, une fois terminé, renvoie la sortie et toutes les erreurs dans la fenêtre de sortie. La capture d’écran suivante montre un exemple de sortie de l’exécution de la commande **RDPSettings**.

![Sortie du script d’une commande Run](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Commandes

Ce tableau affiche la liste des commandes disponibles pour les machines virtuelles Windows. La commande **RunPowerShellScript** peut être utilisée pour exécuter n’importe quel script personnalisé de votre choix.

|**Name**|**Description**|
|---|---|
|**RunPowerShellScript**|Exécute un script PowerShell|
|**EnableRemotePS**|Configure la machine pour activer PowerShell à distance.|
|**EnableAdminAccount**|Vérifie si le compte Administrateur local est désactivé, et si tel est le cas, l’active.|
|**IPConfig**| Affiche des informations détaillées pour l’adresse IP, le masque de sous-réseau et la passerelle par défaut de chaque carte liée à TCP/IP.|
|**RDPSettings**|Vérifie les paramètres du registre et les paramètres de la stratégie de domaine. Suggère les actions de la stratégie si la machine fait partie d’un domaine, ou remplace les paramètres par les valeurs par défaut.|
|**ResetAccountPassword**| Réinitialise le mot de passe du compte Administrateur intégré.|
|**ResetRDPCert**|Supprime le certificat SSL lié à l’écouteur RDP et restaure les valeurs par défaut pour la sécurité de l’écouteur RDP. Utilisez ce script si vous rencontrez des problèmes avec le certificat.|
|**SetRDPPort**|Définit le numéro de port par défaut ou spécifié par l’utilisateur pour les connexions Bureau à distance. Active la règle de pare-feu pour l’accès entrant au port.|

## <a name="powershell"></a>PowerShell

L’exemple suivant utilise l’applet de commande [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) pour exécuter un script PowerShell sur une machine virtuelle Azure.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitation de l’accès à la commande Run

Répertorier les commandes Run ou afficher les détails d’une commande nécessite l’autorisation `Microsoft.Compute/locations/runCommands/read`, disponible avec le rôle intégré [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et les rôles supérieurs.

L’exécution d’une commande nécessite l’autorisation `Microsoft.Compute/virtualMachines/runCommand/action`, disponible avec le rôle [Collaborateur](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) et les rôles supérieurs.

Vous pouvez utiliser un des rôles [intégrés](../../role-based-access-control/built-in-roles.md) ou créer un rôle [personnalisé](../../role-based-access-control/custom-roles.md) afin d’exécuter la commande Run.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Exécuter des scripts dans votre machine virtuelle Windows](run-scripts-in-vm.md) pour en savoir plus sur les autres méthodes pour exécuter à distance des commandes et des scripts dans votre machine virtuelle.