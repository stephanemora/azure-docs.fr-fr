---
title: Support Azure pour les machines virtuelles de 2e génération
description: Vue d’ensemble de la prise en charge Azure pour les machines virtuelles de 2e génération
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/03/2019
ms.author: lahugh
ms.openlocfilehash: 6f03826bf0b82150fa89ad6e17cbcb76f98bb835
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790035"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Support des machines virtuelles de 2e génération dans Azure

Le support des machines virtuelles de 2e génération est désormais disponible dans Azure. Vous ne pouvez pas modifier la génération d’une machine virtuelle après l’avoir créée. Passez bien en revue les considérations relatives sur cette page avant de choisir une génération.

Les machines virtuelles de 2e génération prennent en charge des fonctionnalités clés qui ne sont pas prises en charge par des machines virtuelles de 1ère génération. Ces fonctionnalités incluent la mémoire augmentée, Intel Software Guard Extensions (Intel SGX) et mémoire persistante virtualisée (vPMEM). Les machines virtuelles locales de 2e génération présentent des fonctionnalités qui ne sont pas encore prises en charge dans Azure. Pour en savoir, consultez la section [Fonctionnalités et capacités](#features-and-capabilities).

Les machines virtuelles de 2e génération utilisent la nouvelle architecture de démarrage basée sur UEFI alors que les machines virtuelles de 1ère génération utilisaient l’architecture basée sur le BIOS. Comparées aux machines virtuelles de 1ère génération, les machines virtuelles de 2e génération peuvent avoir des temps d’installation et de démarrage améliorés. Pour obtenir une vue d’ensemble des machines virtuelles de 2e génération et de quelques-unes des différences entre 1ère et 2e génération, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tailles des machines virtuelles de 2e génération

Les machines virtuelles de 1ère génération sont prises en charge dans toutes les tailles dans Azure. Azure prend désormais en charge la 2e génération de machines virtuelles des séries sélectionnées suivantes :

* [Série B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Série DC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* Séries [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) et [Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Série Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Série Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Série HB](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [Série HC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* Séries [Ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) et [Lsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [Série NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) et [série NCv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Série ND](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [Série NVv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

> [!NOTE]
> L’utilisation d’images de machine virtuelle de 2e génération pour les machines virtuelles de la série MV2 est généralement disponible, car la série MV2 fonctionne exclusivement avec les images de machine virtuelle de 2e génération. Les images de machine virtuelle de génération 1 ne sont pas prises en charge sur les machines virtuelles de série MV2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Images de machine virtuelle de 2e génération dans la Place de marché Azure

Les machines virtuelles de 2e génération prennent en charge les images de la Place de marché suivantes :

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.0

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
| Disque OS > 2 To                    | :x:                | :heavy_check_mark: |
| Disque/image/système d’exploitation d’échange personnalisé         | :heavy_check_mark: | :heavy_check_mark: |
| Prise en charge de groupes de machines virtuelles identiques | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Sauvegarde/restauration                    | :heavy_check_mark: | :heavy_check_mark: |
| Galerie d’images partagées              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Création d’une machine virtuelle de 2e génération

### <a name="marketplace-image"></a>Image de la Place de marché

Dans le portail Azure ou Azure CLI, vous pouvez créer des machines virtuelles de 2e génération à partir d’une image de la Place de marché qui prend en charge le démarrage UEFI.

#### <a name="azure-portal"></a>Portail Azure

Les images de génération 2 pour Windows et SLES sont incluses dans la même offre serveur que celle des images Gen1. Du point de vue du flux, cela signifie que vous sélectionnez l’offre et le SKU à partir du portail de votre machine virtuelle. Si le SKU prend en charge les images de génération 1 et de génération 2, vous pouvez choisir de créer une machine virtuelle de génération 2 à partir de l’onglet *Avancé* dans le flux de création de machine virtuelle.

Les SKU suivants prennent en charge les images de génération 1 et de génération 2 :

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

Quand vous sélectionnez une offre correspondant au SKU Windows Server, sous l’onglet **Avancé**, vous pouvez créer une machine virtuelle **Génération 1** (BIOS) ou **Génération 2** (UEFI). Si vous sélectionnez **Génération 2**, vérifiez que la taille de la machine virtuelle sélectionnée sous l’onglet **Général** est [prise en charge pour les machines virtuelles de génération 2](#generation-2-vm-sizes).

![Sélectionner une machine virtuelle de génération 1 ou de génération 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Vous pouvez également utiliser PowerShell pour créer une machine virtuelle en référençant directement le SKU de génération 1 ou de génération 2.

Par exemple, utilisez l’applet de commande PowerShell suivante pour obtenir une liste des SKU dans l’offre `WindowsServer`.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Si vous créez une machine virtuelle avec le système d’exploitation Windows Server 2012, vous devez sélectionner le SKU de machine virtuelle génération 1 (BIOS) ou génération 2 (UEFI), comme suit :

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

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

* **J’ai un fichier .vhd de ma machine virtuelle de 2e génération locale. Puis-je utiliser ce fichier .vhd pour créer une machine virtuelle de 2e génération dans Azure ?**
  Oui, vous pouvez placer votre fichier .vhd de 2e génération sur Azure et l’utiliser pour créer une machine virtuelle de 2e génération. Pour ce faire, procédez comme suit :
    1. Chargez le fichier .vhd sur un compte de stockage situé dans la région où vous souhaitez créer votre machine virtuelle.
    1. Créez un disque managé à partir du fichier .vhd. Affectez à la propriété Génération Hyper-V la valeur V2. Les commandes PowerShell suivantes définissent la propriété Génération Hyper-V au moment de la création d’un disque managé.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Une fois le disque disponible, créez une machine virtuelle en y attachant ce disque. La machine virtuelle créée est alors une machine virtuelle de 2e génération.
    Une fois la machine virtuelle de 2e génération créée, vous pouvez généraliser l’image de cette machine virtuelle. En généralisant l’image, vous pouvez l’utiliser pour créer plusieurs machines virtuelles.

* **Comment augmenter la taille du disque du système d’exploitation ?**  
  Les disques d’une taille supérieure à 2 To sont nouveaux pour les machines virtuelles de 2e génération. Par défaut, les disques de système d’exploitation sont inférieurs à 2 To pour les machines virtuelles de 2e génération. Vous pouvez augmenter la taille du disque jusqu’à la taille maximum recommandée de 4 To. Utilisez Azure CLI ou le portail Azure pour augmenter la taille du disque. Pour en savoir sur comment étendre des disques par programmation, consultez [Redimensionner un disque](expand-os-disk.md).

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

* Découvrez comment [préparer un disque dur virtuel](prepare-for-upload-vhd-image.md) à charger à partir de systèmes locaux sur Azure.
