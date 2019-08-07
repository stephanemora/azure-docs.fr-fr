---
title: Prise en charge Azure pour les machines virtuelles de 2e génération (préversion) | Microsoft Docs
description: Vue d’ensemble de la prise en charge Azure pour les machines virtuelles de 2e génération
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: e551f23c833a5ed88fe14a2f61b03581be0c74fc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479995"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Prise en charge des machines virtuelles de 2e génération (préversion) sur Azure

> [!IMPORTANT]
> La prise en charge Azure pour les machines virtuelles de 2e génération est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

La prise en charge des machines virtuelles de 2e génération est désormais disponible en préversion dans Azure. Vous ne pouvez pas modifier la génération d’une machine virtuelle après l’avoir créée. Passez bien en revue les considérations relatives sur cette page avant de choisir une génération. 

Les machines virtuelles de 2e génération prennent en charge des fonctionnalités clés qui ne sont pas prises en charge par des machines virtuelles de 1ère génération. Ces fonctionnalités incluent la mémoire augmentée, Intel Software Guard Extensions (Intel SGX) et mémoire persistante virtualisée (vPMEM). Les machines virtuelles de 2e génération disposent également de fonctionnalités non prises en charge dans Azure à l’heure actuelle. Pour en savoir, consultez la section [Fonctionnalités et capacités](#features-and-capabilities).

Les machines virtuelles de 2e génération utilisent la nouvelle architecture de démarrage basée sur UEFI alors que les machines virtuelles de 1ère génération utilisaient l’architecture basée sur le BIOS. Comparées aux machines virtuelles de 1ère génération, les machines virtuelles de 2e génération peuvent avoir des temps d’installation et de démarrage améliorés. Pour obtenir une vue d’ensemble des machines virtuelles de 2e génération et de quelques-unes des différences entre 1ère et 2e génération, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tailles des machines virtuelles de 2e génération

Les machines virtuelles de 1ère génération sont prises en charge dans toutes les tailles dans Azure. Azure prend désormais en charge en préversion la 2e génération pour les gammes de machines virtuelles sélectionnées suivantes :

* [Série B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* Séries [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) et [Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Série Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Série Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* Séries [Ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) et [Lsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [Série NCv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) et [série NCv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [Série ND](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Images de machine virtuelle de 2e génération dans la Place de marché Azure

Les machines virtuelles de 2e génération prennent en charge les images de la Place de marché suivantes :

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Local Machines virtuelles de 2e génération Azure

Azure ne prend actuellement pas en charge certaines fonctionnalités qui sont prises en charge par Hyper-V local pour les machines virtuelles de 2e génération.

| Fonctionnalités pour la 2e génération                | Hyper-V local | Azure |
|-------------------------------------|---------------------|-------|
| Démarrage sécurisé                         | :heavy_check_mark:  | :x:   |
| Machine virtuelle protégée                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sécurité basée sur la virtualisation (VBS) | :heavy_check_mark:  | :x:   |
| Format VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Fonctionnalités et capacités

### <a name="generation-1-vs-generation-2-features"></a>Fonctionnalités de 1ère et 2e générations

| Fonctionnalité | Génération 1 | Génération 2 |
|---------|--------------|--------------|
| Démarrage             | PCAT                      | UEFI                               |
| Contrôleurs de disque | IDE                       | SCSI                               |
| Tailles de machine virtuelle         | Toutes les tailles de machine virtuelle | Uniquement les machines virtuelles qui prennent en charge le stockage Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Capacités de 1ère et 2e générations

| Fonctionnalité | Génération 1 | Génération 2 |
|------------|--------------|--------------|
| Disque OS > 2 To                    | :x:                        | :heavy_check_mark: |
| Disque/image/système d’exploitation d’échange personnalisé         | :heavy_check_mark:         | :heavy_check_mark: |
| Prise en charge de groupes de machines virtuelles identiques | :heavy_check_mark:         | :heavy_check_mark: |
| Sauvegarde et récupération automatique du système                        | :heavy_check_mark:         | :x:                |
| Galerie d’images partagées              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Création d’une machine virtuelle de 2e génération

### <a name="marketplace-image"></a>Image de la Place de marché

Dans le portail Azure ou Azure CLI, vous pouvez créer des machines virtuelles de 2e génération à partir d’une image de la Place de marché qui prend en charge le démarrage UEFI.

L’offre `windowsserver-gen2preview` contient uniquement des images Windows de 2e génération. Ce package permet d’éviter toute confusion entre images de 1ère et 2e génération. Pour créer une machine virtuelle de 2e génération, sélectionnez **Images** dans cette offre et suivrez le processus standard de création d’une machine virtuelle.

Actuellement, la Place de marché offre les images Windows de 2e génération suivantes :

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Consultez la section [ Fonctionnalités et capacités](#features-and-capabilities) pour obtenir une liste actuelle des images de la Place de marché prises en charge.

### <a name="managed-image-or-managed-disk"></a>Image ou disque managé

Vous pouvez créer une machine virtuelle de 2e génération à partir d’une image managée ou d’un disque managé, de la même façon que pour une machine virtuelle de 1ère génération.

### <a name="virtual-machine-scale-sets"></a>Groupes identiques de machines virtuelles

Vous pouvez également créer des machines virtuelles de 2e génération à l’aide de groupes de machines virtuelles identiques. Dans Azure CLI, utilisez les groupes identiques Azure pour créer des machines virtuelles de 2e génération.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

* **Les machines virtuelles de 2e génération sont-elles disponibles dans toutes les régions Azure ?**  
    Oui. Par contre, toutes les [tailles de machines virtuelles de 2e génération](#generation-2-vm-sizes) ne sont pas disponibles dans chaque région. La disponibilité des machines virtuelles de 2e génération dépend des disponibilités en matière de taille.

* **Y a-t-il une différence de prix entre des machines virtuelles de 1ère et 2e générations ?**  
    Non.

* **Comment augmenter la taille du disque du système d’exploitation ?**  
  Les disques d’une taille supérieure à 2 To sont nouveaux pour les machines virtuelles de 2e génération. Par défaut, les disques de système d’exploitation sont inférieurs à 2 To pour les machines virtuelles de 2e génération. Vous pouvez augmenter la taille du disque jusqu’à la taille maximum recommandée de 4 To. Utilisez Azure CLI ou le portail Azure pour augmenter la taille du disque. Pour en savoir sur comment étendre des disques par programmation, consultez [Redimensionner un disque](expand-disks.md).

  Pour augmenter la taille du disque du système d’exploitation à partir du portail Azure :

  1. Accédez à la page des propriétés de la machine virtuelle dans le portail Azure.
  1. Pour arrêter et libérer la machine virtuelle, sélectionnez le bouton **Arrêter**.
  1. Dans la section **Disques**, sélectionnez le disque du système d’exploitation que vous souhaitez augmenter.
  1. Dans la section **Disques**, sélectionnez **Configuration**, et mettez à jour la **Taille** à la valeur souhaitée.
  1. Revenez à la page des propriétés de la machine virtuelle et **démarrez**-la.

  Vous recevrez peut-être un avertissement pour les disques de système d’exploitation plus grand que 2 To. L’avertissement ne s’applique pas aux machines virtuelles de 2e génération. Toutefois, les tailles supérieures à 4 To ne sont *pas recommandées*.

* **Les machines virtuelles de 2e génération prennent-elles en charge la mise en réseau accélérée ?**  
    Oui. Pour en savoir plus, consultez [Créer une machine virtuelle avec mise en réseau accélérée](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **La 2e génération prend-elle en charge VHDX ?**  
    Non, les machines virtuelles de 2e génération prennent en charge uniquement un disque dur virtuel.

* **Les machines virtuelles de 2e génération prennent-elles en charge les disques de stockage Ultra Azure ?**  
    Oui.

* **Puis-je migrer une machine virtuelle à partir de la 1ère génération à la 2e génération ?**  
    Non, vous ne pouvez pas modifier la génération d’une machine virtuelle après sa création. Si vous avez besoin de passer d’une génération à une autre, créez une machine virtuelle de la génération souhaitée.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [machines virtuelles de 2e génération dans Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
