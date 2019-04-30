---
title: Importer des machines virtuelles à partir d’un autre labo dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment importer des machines virtuelles à partir d’un autre labo vers le labo actuel dans Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125897"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importer des machines virtuelles à partir d’un autre labo dans Azure DevTest Labs
Le service Azure DevTest Labs améliore considérablement la gestion des machines virtuelles pour les activités de développement et test. Il vous permet de déplacer une machine virtuelle d’un labo à un autre quand les exigences de l’infrastructure ou de l’équipe changent. Voici quelques scénarios courants où vous pouvez être amené à effectuer cette opération :

- Une personne de l’équipe rejoint un autre groupe au sein de l’entreprise et souhaite déplacer les machines virtuelles de développement vers le labo de la nouvelle équipe.
- Le groupe a atteint le quota de niveau d’abonnement et souhaite diviser les équipes en plusieurs abonnements.
- L’entreprise prévoit de passer à la topologie Express Route (ou à une autre topologie réseau) et l’équipe souhaite déplacer les machines virtuelles afin d’utiliser cette nouvelle infrastructure.

## <a name="solution-and-constraints"></a>Solution et contraintes
Azure DevTest Labs permet à un propriétaire de labo d’importer des machines virtuelles d’un labo source vers un labo de destination. Le propriétaire de labo peut fournir un nouveau nom pour la machine virtuelle de destination dans le processus. Le processus d’importation inclut toutes les dépendances telles que les disques, les planifications, les paramètres réseau, etc. Le processus prend un certain temps ; il est impacté par le nombre et la taille des disques attachés à la machine source et par la distance jusqu’à la destination (par exemple, entre la région USA Est et la Asie Sud-Est). Une fois le processus d’importation terminé, la machine virtuelle source reste arrêtée et la nouvelle est en cours d’exécution dans le labo de destination.

Vous devez avoir deux contraintes principales en tête quand vous planifiez l’importation de machines virtuelles vers un autre labo :

- L’importation de machines virtuelles entre des abonnements et entre des régions est prise en charge, mais les abonnements doivent être associés au même locataire Azure Active Directory.
- Les machines virtuelles ne doivent pas être dans un état comme pouvant être revendiquées dans le labo source.

De plus, pour pouvoir importer une machine virtuelle d’un labo vers un autre, vous devez être le propriétaire de la machine virtuelle du labo source et le propriétaire du labo dans le labo de destination.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Étapes d’importation d’une machine virtuelle à partir d’un autre labo
Vous pouvez importer une machine virtuelle d’un labo vers un autre seulement à l’aide d’Azure PowerShell et de l’API REST.

### <a name="use-powershell"></a>Utiliser PowerShell
Téléchargez le fichier de script PowerShell ImportVirtualMachines.ps1 à partir du [dépôt Git Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) sur votre disque local.

#### <a name="import-a-single-vm"></a>Importer une seule machine virtuelle
Exécutez le script ImportVirtualMachines.ps1 pour importer une seule machine virtuelle d’un labo source vers un labo de destination. Vous pouvez spécifier un nouveau nom pour la machine virtuelle en cours de copie à l’aide du paramètre DestinationVirtualMachineName.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importation de toutes les machines virtuelles
Quand vous exécutez le script ImportVirtualMachines.ps1, si vous ne spécifiez pas de machine virtuelle dans le labo source, le script importe toutes les machines virtuelles du labo source vers le labo de destination.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Avec l’API REST
Appelez l’API REST par rapport au labo cible/de destination et passez les informations relatives au labo source, à l’abonnement et à la machine virtuelle en tant que paramètres, comme indiqué dans l’exemple suivant :

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le redimensionnement d’une machine virtuelle, consultez [Redimensionner une machine virtuelle](devtest-lab-resize-vm.md).
- Pour plus d’informations sur le redéploiement d’une machine virtuelle, consultez [Redéploiement d’une machine virtuelle](devtest-lab-redeploy-vm.md).
