---
title: Machines virtuelles de génération 2 (version préliminaire) sur Azure | Microsoft Docs
description: Vue d’ensemble des machines virtuelles de génération Azure 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: lahugh
ms.openlocfilehash: e6bb947503371e379e4d4972ddfc3614e129174b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835206"
---
# <a name="generation-2-vms-preview-on-azure"></a>Machines virtuelles de génération 2 (version préliminaire) sur Azure

> [!IMPORTANT]
> Machines virtuelles de génération 2 sont actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prise en charge pour les machines virtuelles de génération 2 (VM) est désormais disponible en version préliminaire publique sur Azure. Vous ne pouvez pas modifier la génération d’une machine virtuelle une fois que vous l’avez créé. Par conséquent, nous vous recommandons d’examiner les considérations [ici](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) ainsi que les informations sur cette page avant de choisir une génération.

Fonctionnalités clées de génération 2 machines virtuelles prise en charge, telles que : une mémoire, Intel® Software Guard Extensions (SGX) et mémoire persistante virtuel (vPMEM), qui ne sont pas pris en charge sur les machines virtuelles de génération 1. Machines virtuelles de génération 2 ont certaines fonctionnalités qui ne sont pas encore pris en charge sur Azure. Pour plus d’informations, consultez le [fonctionnalités et capacités](#features-and-capabilities) section. 

Machines virtuelles de génération 2 utilisent les nouveau vs d’architecture démarrage basé sur UEFI l’architecture de basés sur BIOS utilisé par les machines virtuelles de génération 1. Par rapport aux machines virtuelles de génération 1, machines virtuelles de génération 2 peuvent avoir de meilleures durées d’installation et de démarrage. Pour une vue d’ensemble de machines virtuelles de génération 2 et certaines des principales différences entre la génération 1 et la génération 2, consultez [dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tailles de machine virtuelle de génération 2

Machines virtuelles de génération 1 sont prises en charge par toutes les tailles de machine virtuelle dans Azure. Azure offre désormais la prise en charge de la génération 2 de la série de machine virtuelle sélectionnée suivante en version préliminaire publique :

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) et [de la série Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Série Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Série Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [La série ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) et [Lsv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Images de machine virtuelle de génération 2 dans Azure Marketplace

Machines virtuelles de génération 2 prennent en charge les images de place de marché Azure suivantes :

* Serveur Windows 2019 centre de données
* Serveur Windows 2016 Datacenter
* Serveur Windows 2012 R2 Datacenter
* Serveur Windows 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Locales et machines virtuelles de génération Azure 2

Azure ne prend pas en charge certaines des fonctionnalités qui prend en charge Hyper-V sur site pour les machines virtuelles de génération 2.

| Fonctionnalité de génération 2                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Démarrage sécurisé                         | :heavy_check_mark:  | :x:   |
| Machine virtuelle protégée                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sécurité basée sur la virtualisation (VBS) | :heavy_check_mark:  | :x:   |
| Format VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Fonctionnalités et capacités

### <a name="generation-1-vs-generation-2-features"></a>Fonctionnalités de génération 2 de Visual Studio de génération 1

| Fonctionnalité | Génération 1 | Génération 2 |
|---------|--------------|--------------|
| Démarrage             | PCAT                      | UEFI                               |
| Contrôleurs de disque | IDE                       | SCSI                               |
| Tailles de machine virtuelle         | Disponible sur toutes les tailles de machine virtuelle | Stockage pris en charge des machines virtuelles Premium uniquement |

### <a name="generation-1-vs-generation-2-capabilities"></a>Fonctionnalités de génération 2 de Visual Studio de génération 1

| Fonctionnalité | Génération 1 | Génération 2 |
|------------|--------------|--------------|
| > 2 To de disque de système d’exploitation                    | :x:                        | :heavy_check_mark: |
| Système d’exploitation / Image/échange de disque personnalisée         | :heavy_check_mark:         | :heavy_check_mark: |
| Prise en charge de machines virtuelles identiques | :heavy_check_mark:         | :heavy_check_mark: |
| Récupération automatique du système/sauvegarde                        | :heavy_check_mark:         | :x:                |
| Galerie d'images partagées              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Création d’une génération 2 machines virtuelles

### <a name="marketplace-image"></a>Image de place de marché

Machines virtuelles de génération 2 peuvent être créés à partir d’une image de place de marché (qui prend en charge le démarrage UEFI) via le portail Azure ou Azure CLI.

Le `windowsserver-gen2preview` offre contient uniquement les images de génération 2 Windows. Cela évite toute confusion en ce qui concerne les images de 2e génération 1 Visual Studio. Pour créer de génération 2 machines virtuelles, sélectionnez **Images** à partir de cette offre et suivez le processus de création de machine virtuelle standard.

Actuellement, la génération de Windows suivante 2 images sont publiés dans la place de marché Azure :

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Consultez la section de fonctionnalités pour obtenir la liste des images de place de marché pris en charge que nous continuerons d’ajouter des images supplémentaires qui prennent en charge de la génération 2.

### <a name="managed-image-or-managed-disk"></a>Image managée ou disque géré

Génération 2 machines virtuelles peuvent être créés à partir d’image managée ou disque géré dans la même manière que vous créez une génération 1 machine virtuelle.

### <a name="virtual-machine-scale-sets"></a>Groupes identiques de machines virtuelles 

Génération que 2 machines virtuelles peuvent également être créés à l’aide de machines virtuelles identiques. Vous pouvez créer la génération 2 machines virtuelles à l’aide de jeux de mise à l’échelle de machine virtuelle Azure via Azure CLI.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

* **Machines virtuelles de génération 2 prennent-ils en charge le mise en réseau accélérée ?**  
    Oui, la prise en charge des machines virtuelles de génération 2 [mise en réseau accélérée](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **.Vhdx est pris en charge sur la génération 2 ?**  
    Non, .vhd uniquement est pris en charge sur les machines virtuelles de génération 2.

* **Machines virtuelles de génération 2 prennent en charge Ultra disques SSD (Solid State) ?**  
    Oui, les machines virtuelles de génération 2 prennent en charge Ultra SSD.

* **Puis-je migrer à partir de la génération 1 pour les machines virtuelles de génération 2 ?**  
    Non, vous ne pouvez pas modifier la génération d’une machine virtuelle une fois que vous l’avez créé. Si vous avez besoin basculer entre les générations de la machine virtuelle, vous devez créer une machine virtuelle d’une autre génération.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [machines virtuelles de génération 2 dans Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).