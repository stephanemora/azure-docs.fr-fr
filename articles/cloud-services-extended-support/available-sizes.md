---
title: Tailles disponibles pour Azure Cloud Services (support étendu)
description: Tailles disponibles pour les déploiements Azure Cloud Services (support étendu).
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743904"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tailles disponibles pour Azure Cloud Services (support étendu)

Cet article décrit les tailles de machine virtuelle disponibles pour les instances Cloud Services (support étendu).   

| Famille de références |  ACU/cœur | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180 – 240* |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> Les unités ACU signalées par un astérisque (*) utilisent la technologie Intel ® Turbo pour augmenter la fréquence du processeur et améliorer les performances. Cette amélioration des performances peut varier en fonction de la taille et de la charge de travail de la machine virtuelle, et des autres charges de travail en cours d’exécution sur le même hôte.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configuration des tailles pour Cloud Services (support étendu)

Il est possible de spécifier la taille de machine d’une instance de rôle dans le cadre du modèle de service dans le fichier de définition de service. La taille du rôle détermine le nombre de cœurs de processeur, la capacité de mémoire et la taille du système de fichiers local.

Par exemple, la taille de l’instance de rôle web est ici définie sur `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Modification de la taille d’un rôle existant

Pour modifier la taille d’un rôle existant, modifiez la taille de machine virtuelle dans le fichier de définition de service (csdef), repackagez votre instance Cloud Services et redéployez-la. 

## <a name="get-a-list-of-available-sizes"></a>Affichage des tailles disponibles 

Pour récupérer la liste des tailles disponibles, consultez [Références SKU de ressources – Liste](https://docs.microsoft.com/rest/api/compute/resourceskus/list) et appliquez les filtres suivants :


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez la [FAQ](faq.md) de Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
