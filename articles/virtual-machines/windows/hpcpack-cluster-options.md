---
title: Options de cluster HPC Pack Windows dans Azure | Microsoft Docs
description: Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) Windows dans le cloud Azure
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165755"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Options de HPC Pack pour créer et gérer un cluster pour des charges de travail HPC Windows dans Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options Azure de création de clusters HPC Pack pour exécuter des charges de travail Windows. Il existe également des options de création de clusters HPC Pack pour l’exécution de [charges de travail HPC Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack dans les machines virtuelles Azure et les groupes de machines virtuelles identiques
### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager
* (GitHub) [Modèles de cluster HPC Pack 2016 mise à jour 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modèles de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Démarrage rapide) [Créer un cluster HPC Pack 2012 R2](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Démarrage rapide) [Création d’un cluster HPC Pack 2012 R2 avec une image de nœud de calcul personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Images d’ordinateur virtuel Azure
Parcourez les [Images HPC Pack sur Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) si vous souhaitez créer votre propre cluster HPC Pack dans Azure.


### <a name="tutorials"></a>Didacticiels
* [Didacticiel : déployer un cluster HPC Pack 2016 dans Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gérer un cluster HPC Pack 2016 dans Azure avec Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>Déploiement de clusters HPC Pack 2012 R2 dans le modèle de déploiement classique
* [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Didacticiel : prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Agrandir et réduire les ressources de calcul Azure dans un cluster HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Passer à Azure à partir de HPC Pack 2012 R2
* [Intégration à des instances de travail Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Intégration à Azure Batch avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Didacticiel : configurer un cluster hybride avec HPC Pack dans Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Envoi de tâche

* [Envoyer des travaux à un cluster HPC Pack dans Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






