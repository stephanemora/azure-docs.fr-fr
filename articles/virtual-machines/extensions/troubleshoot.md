---
title: Résolution des problèmes liés aux échecs d’extension de machine virtuelle Windows
description: En savoir plus sur la résolution des problèmes pour les échecs d’extension de machine virtuelle Windows dans Azure
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292797"
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

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>Vérifier que l'agent de machine virtuelle est en cours d'exécution et prêt
L'agent de machine virtuelle est nécessaire pour gérer, installer et exécuter les extensions. Si l'agent de machine virtuelle n'est pas en cours d'exécution ou ne parvient pas à signaler un état Prêt à la plateforme Azure, les extensions ne fonctionneront pas correctement.

Pour résoudre les problèmes liés à l'agent de machine virtuelle, consultez les pages suivantes :
- [Résolution des problèmes liés à l'agent invité Windows Azure](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent) sur une machine virtuelle Windows
- [Résolution des problèmes liés à l'agent Linux Azure](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) sur une machine virtuelle Linux

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>Rechercher le guide de résolution des problèmes liés à votre extension
Certaines extensions disposent d'une page dédiée à la résolution des problèmes. Pour obtenir la liste de ces extensions et pages, consultez [Résolution des problèmes](./overview.md#troubleshoot-extensions).

### <a name="view-the-extensions-status"></a>Afficher l'état de l'extension
Comme expliqué ci-dessus, pour connaître l'état de l’extension, exécutez la cmdlet PowerShell suivante :
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

ou la commande CLI suivante :
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

ou, sur le portail Azure, accédez au panneau des machines virtuelles et sélectionnez Paramètres/Extensions. Vous pouvez ensuite cliquer sur l'extension pour consulter son état et son message.


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


### <a name="look-at-the-extension-logs-inside-the-vm"></a>Examiner les journaux d'une extension à l'intérieur de la machine virtuelle

Si les étapes précédentes n'ont pas fonctionné et si votre extension est toujours en état d'échec, l'étape suivante consiste à consulter ses journaux à l'intérieur de la machine virtuelle.

Sur une machine virtuelle **Windows**, les journaux des extensions se trouvent généralement à l'emplacement suivant : 
```
C:\WindowsAzure\Logs\Plugins
```
Tandis que les paramètres et les fichiers d'état de ces extensions se trouvent sous : 
```
C:\Packages\Plugins
```
<br/>

Sur une machine virtuelle **Linux**, les journaux des extensions se trouvent généralement à l'emplacement suivant : 
```
/var/log/azure/
```
Tandis que les paramètres et les fichiers d'état de ces extensions se trouvent sous : 
```
/var/lib/waagent/
```


Chaque extension est différente, mais elles obéissent généralement à des principes similaires :

Les packages d'extension et les fichiers binaires sont téléchargés sur la machine virtuelle (par exemple, _« /var/lib/waagent/custom-script/download/1 »_ pour Linux ou _« C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0 »_ pour Windows). 

Leur configuration et leurs paramètres sont transmis de la plateforme Azure au gestionnaire d'extensions par le biais de l'agent de machine virtuelle (par exemple, _« /var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config »_ pour Linux ou _« C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings »_ pour Windows)

Les gestionnaires d'extensions situés à l'intérieur de la machine virtuelle écrivent dans un fichier d'état (par exemple, _« /var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status »_ pour Linux ou _« C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status »_ pour Windows) qui est ensuite signalé à la plateforme Azure. Cet état est celui signalé par le biais de PowerShell, de l'interface CLI ou du panneau Extension de la machine virtuelle sur le portail Azure.

Ils rédigent également des journaux détaillés de leur exécution (par exemple, _« /var/log/azure/custom-script/handler.log »_ pour Linux ou _« C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log »_ pour Windows).


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>Si la machine virtuelle est recréée à partir d'une machine virtuelle existante

Dans certaines circonstances, vous pouvez être amené à créer une machine virtuelle Azure basée sur un disque spécialisé provenant d'une autre machine virtuelle Azure. Dans ce cas, il est possible que l'ancienne machine virtuelle contienne des extensions et qu'il reste donc des fichiers binaires, des journaux et des fichiers d'état. Le nouveau modèle de machine virtuelle ne connaîtra pas l'état des extensions de la machine virtuelle précédente, ce qui peut entraîner le signalement d'un état incorrect pour ces extensions. Nous vous recommandons fortement de supprimer les extensions de l'ancienne machine virtuelle avant de créer la nouvelle, puis de réinstaller ces extensions une fois la nouvelle machine virtuelle créée.
La même chose peut se produire lorsque vous créez une image généralisée à partir d'une machine virtuelle Azure existante. Nous vous invitons à supprimer les extensions pour éviter les incohérences d'état liées à celles-ci.