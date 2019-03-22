---
title: Importer des ordinateurs virtuels à partir d’un autre laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment importer des machines virtuelles à partir d’un autre laboratoire dans le laboratoire en cours.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 8b2eee0bfd32b58cd751f8bf70aff1d4f460a353
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340138"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importer des ordinateurs virtuels à partir d’un autre laboratoire dans Azure DevTest Labs
Cet article fournit des informations sur comment importer des machines virtuelles à partir d’un autre laboratoire dans votre laboratoire. 

## <a name="scenarios"></a>Scénarios
Voici quelques scénarios où vous devez importer des machines virtuelles à partir d’un laboratoire dans un autre laboratoire : 

- Une personne de l’équipe correspond à un déplacement vers un autre groupe au sein de l’entreprise et souhaite prendre le poste de travail de développeur à dev/test de la nouvelle équipe.
- Le groupe a atteint un [quota au niveau de l’abonnement](../azure-subscription-service-limits.md) et souhaite diviser les équipes en quelques abonnements
- Déplace de la société pour Express Route (ou certaines autres nouvelle topologie réseau) et l’équipe souhaite déplacer les ordinateurs virtuels à utiliser cette nouvelle infrastructure

## <a name="solution-and-constraints"></a>Solution et contraintes
Cette fonctionnalité vous permet d’importer des machines virtuelles dans un laboratoire (source) dans un autre laboratoire (destination). Vous pouvez éventuellement donner un nouveau nom pour la machine virtuelle de destination dans le processus. Le processus d’importation inclut toutes les dépendances telles que les disques, les planifications, les paramètres réseau et ainsi de suite.

Le processus de prendre un certain temps et est affecté par les facteurs suivants :

- Nombre ou la taille des disques attachés à la machine source (dans la mesure où il s’agit d’une opération de copie et pas une opération de déplacement) 
- Distance vers la destination (par exemple, est des États-Unis à Asie du Sud-est).  

Une fois que le processus est terminé, la Machine virtuelle source reste arrêt et la nouvelle un est en cours d’exécution dans le laboratoire de destination.

Il existe deux contraintes de clé à connaître lors de la planification importer des machines virtuelles à partir d’un laboratoire dans à un autre laboratoire :

- Les importations de Machine virtuelle sur les abonnements et les régions sont prises en charge, mais les abonnements doivent être associés au même locataire Azure Active Directory.
- Machines virtuelles ne doit pas être dans un état exigible dans le laboratoire de la source.
- Vous êtes le propriétaire de la machine virtuelle dans le laboratoire de la source et le propriétaire du laboratoire dans le laboratoire de destination.
- Actuellement, cette fonctionnalité est prise en charge uniquement par le biais de Powershell et API REST.

## <a name="use-powershell"></a>Utiliser PowerShell
Téléchargez le fichier ImportVirtualMachines.ps1 à partir de la [GitHub](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/ImportVirtualMachines/ImportVirtualMachines.ps1). Vous pouvez utiliser le script pour importer une seule machine virtuelle ou toutes les machines virtuelles dans le laboratoire de source dans le laboratoire de destination. 

### <a name="use-powershell-to-import-a-single-vm"></a>Utiliser PowerShell pour importer une seule machine virtuelle
L’exécution de ce script powershell nécessite l’identifiant de la machine virtuelle source et l’atelier de destination et si vous le souhaitez en fournissant un nouveau nom à utiliser pour l’ordinateur de destination :

```powershell 
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Utiliser PowerShell pour importer toutes les machines virtuelles dans le laboratoire de source
Si l’ordinateur virtuel Source n’est pas spécifié, le script importe automatiquement toutes les machines virtuelles dans les DevTest Labs.  Par exemple : 
 
```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST permet d’importer une machine virtuelle
L’appel REST est simple. Vous donner suffisamment d’informations pour identifier les ressources source et de destination. N’oubliez pas que l’opération s’effectue sur la ressource de laboratoire de destination.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Définir des stratégies pour un laboratoire](devtest-lab-get-started-with-lab-policies.md)
- [Forum Aux Questions](devtest-lab-faq.md)
