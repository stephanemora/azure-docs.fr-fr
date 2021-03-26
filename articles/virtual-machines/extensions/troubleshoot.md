---
title: Résolution des problèmes liés aux échecs d’extension de machine virtuelle Windows
description: En savoir plus sur la résolution des problèmes pour les échecs d’extension de machine virtuelle Windows dans Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564796"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Dépannage des échecs d’extension de machine virtuelle Windows dans Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Affichage de l’état de l’extension
Les modèles Azure Resource Manager sont exécutables à partir d’Azure PowerShell. Une fois que le modèle est exécuté, l'état de l'extension peut être affiché à partir d'Azure Resource Explorer ou des outils de ligne de commande.

Voici un exemple :

Azure PowerShell :

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Voici l'exemple de sortie :

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Dépannage des échecs d’extension

### <a name="rerun-the-extension-on-the-vm"></a>Réexécuter l’extension sur la machine virtuelle
Si vous exécutez des scripts sur la machine virtuelle à l’aide de l’extension de script personnalisé, cela peut générer une erreur indiquant que la machine virtuelle a été créée avec succès mais que le script a échoué. Dans ces conditions, la méthode recommandée pour corriger cette erreur consiste à supprimer l'extension et exécuter le modèle à nouveau.
Remarque : cette fonctionnalité sera ultérieurement améliorée de manière à ne plus devoir désinstaller l'extension.

#### <a name="remove-the-extension-from-azure-powershell"></a>Suppression de l’extension d’Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Une fois que l'extension a été supprimée, le modèle peut être réexécuté pour exécuter les scripts sur la machine virtuelle.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Déclencher un nouveau GoalState sur la machine virtuelle
Vous pouvez remarquer qu’une extension n’a pas été exécutée ou ne peut pas s’exécuter en raison d’un « générateur de certificats CRP Windows Azure » manquant (ce certificat est utilisé pour sécuriser le transport des paramètres protégés de l’extension).
Ce certificat est automatiquement régénéré lors du redémarrage de l’agent invité Windows à partir de la machine virtuelle :
- Ouvrir le Gestionnaire des tâches
- Accéder à l’onglet Détails
- Rechercher le processus WindowsAzureGuestAgent.exe
- Cliquez avec le bouton droit, puis sélectionnez « Terminer la tâche ». Le processus sera redémarré automatiquement.


Vous pouvez également déclencher un nouveau GoalState sur la machine virtuelle, en exécutant « VM Reapply ». L’API VM [Reapply](/rest/api/compute/virtualmachines/reapply), introduite en 2020, permet de réappliquer l’état d’une machine virtuelle. Nous vous recommandons de le faire à un moment où vous pouvez tolérer un court temps d’arrêt des machines virtuelles. Reapply ne provoque pas en elle-même le redémarrage d’une machine virtuelle, qui la plupart du temps ne se produit pas. Cependant, il est très peu probable qu’une autre mise à jour en attente du modèle de machine virtuelle soit appliquée lorsque Reapply déclenche un nouveau GoalState et qu’une autre modification exige un redémarrage. 

Portail Azure :

Sur le portail, sélectionnez la machine virtuelle. Dans le volet gauche, sous **Support + dépannage**, sélectionnez **Redéployer + réappliquer**, puis **Reapply**.


Azure PowerShell *(remplacez « RG Name » et « VM Name » par le nom de votre groupe de ressources et celui de votre machine virtuelle)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(remplacez « RG Name » et « VM Name » par le nom de votre groupe de ressources et celui de votre machine virtuelle)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Si « VM Reapply » n’a pas fonctionné, vous pouvez ajouter un nouveau disque de données vide à la machine virtuelle sur le Portail de gestion Azure, puis le supprimer une fois le certificat rajouté.