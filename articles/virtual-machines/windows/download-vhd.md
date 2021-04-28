---
title: Télécharger un VHD Windows à partir d’Azure
description: Téléchargez un VHD Windows à l’aide du Portail Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565285"
---
# <a name="download-a-windows-vhd-from-azure"></a>Télécharger un VHD Windows à partir d’Azure

Dans cet article, vous apprendrez à télécharger un fichier de disque dur virtuel (VHD) Windows à partir d’Azure à l’aide du Portail Azure.

## <a name="optional-generalize-the-vm"></a>Facultatif : Généraliser la machine virtuelle

Si vous souhaitez utiliser le disque dur virtuel (VHD) en tant qu’[image](tutorial-custom-images.md) pour créer d’autres machines virtuelles, vous devez utiliser [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) pour généraliser le système d’exploitation. Dans le cas contraire, vous devrez effectuer une copie du disque pour chaque machine virtuelle que vous souhaitez créer.

Pour utiliser le VHD en tant qu’image pour créer d’autres machines virtuelles, généralisez la machine virtuelle.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/).
2. [Connectez-vous à la machine virtuelle](connect-logon.md). 
3. Sur la machine virtuelle, ouvrez la fenêtre d’invite de commandes en tant qu’administrateur.
4. Remplacez le répertoire par *%windir%\system32\sysprep* et exécutez sysprep.exe.
5. Dans la boîte de dialogue Outil de préparation du système, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience) du système** et vérifiez que **Généraliser** est sélectionné.
6. Dans Options d’arrêt, sélectionnez **Arrêter**, puis cliquez sur **OK**. 

Si vous ne souhaitez pas généraliser votre machine virtuelle actuelle, vous pouvez toujours créer une image généralisée en [créant d’abord un instantané du disque du système d’exploitation](#alternative-snapshot-the-vm-disk), en créant une machine virtuelle à partir de l’instantané, puis en généralisant la copie.

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un disque VHD associé à une machine virtuelle en cours d’exécution à partir d’Azure. Si vous souhaitez conserver la machine virtuelle en cours d’exécution, vous pouvez [créer un instantané, puis le télécharger](#alternative-snapshot-the-vm-disk).

1. Dans le menu Hub du Portail Azure, cliquez sur **Machines virtuelles**.
1. Sélectionnez la machine virtuelle dans la liste.
1. Dans le panneau de la machine virtuelle, cliquez sur **Arrêter**.

### <a name="alternative-snapshot-the-vm-disk"></a>Alternative : Prendre un instantané du disque de machine virtuelle

Prenez un instantané du disque à télécharger.

1. Sélectionnez la machine virtuelle dans le [portail](https://portal.azure.com).
2. Sélectionnez **Disques** dans le menu de gauche, puis sélectionnez le disque dont vous voulez prendre un instantané. Les détails du disque s’affichent.  
3. Sélectionnez **Créer une capture instantanée** dans le menu en haut de la page. La page **Créer une capture instantanée** s’ouvre.
4. Dans **Nom**, saisissez le nom de l’instantané. 
5. Pour **Type d’instantané**, sélectionnez **Complet** ou **Incrémentiel**.
6. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

Votre instantané sera créé sous peu, et pourra ensuite être utilisé pour télécharger ou créer une autre machine virtuelle.

> [!NOTE]
> Si vous n’arrêtez pas d’abord la machine virtuelle, l’instantané ne sera pas propre. L’instantané sera dans le même état que si la machine virtuelle avait été mise hors tension ou s’était bloquée au moment où l’instantané a été réalisé.  Bien que cette méthode soit généralement sans danger, elle peut causer des problèmes si les applications en cours d’exécution à ce moment-là ne sont pas résistantes aux pannes.
>  
> Cette méthode est recommandée uniquement pour les machines virtuelles avec un seul disque de système d’exploitation. Les machines virtuelles avec un ou plusieurs disques de données doivent être arrêtées avant le téléchargement ou avant la création d’un instantané du disque du système d’exploitation et de chaque disque de données.

## <a name="generate-download-url"></a>Générer l’URL de téléchargement

Pour télécharger le fichier VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1. Sur la page de la machine virtuelle, cliquez sur **Disques** dans le menu de gauche.
1. Sélectionnez le disque du système d’exploitation de la machine virtuelle.
1. Sur la page du disque, sélectionnez **Exportation de disque** dans le menu de gauche.
1. Le délai d’expiration par défaut de l’URL est *3600* secondes (une heure). Vous devrez peut-être augmenter cette valeur pour les disques de système d’exploitation Windows ou les disques de données volumineux. **36000** secondes (10 heures) sont généralement suffisantes.
1. Cliquez sur **Générer l’URL**.

> [!NOTE]
> Le délai d’expiration est augmenté par rapport à la valeur par défaut afin de laisser suffisamment de temps pour télécharger le fichier volumineux de VHD pour un système d’exploitation Windows Server. Le téléchargement de disques durs virtuels volumineux peut prendre plusieurs heures, en fonction de votre connexion et de la taille de la machine virtuelle. 
> 
> 

## <a name="download-vhd"></a>Télécharger un VHD

1. Sous l’URL générée, cliquez sur Télécharger le fichier de disque dur virtuel.
1. Vous devrez peut-être cliquer sur **Enregistrer** dans votre navigateur pour commencer le téléchargement. Le nom par défaut du fichier VHD est *abcd*.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger un fichier de VHD sur Azure](upload-generalized-managed.md). 
- [Créez des disques managés à partir de disques non managés dans un compte de stockage](attach-disk-ps.md).
- [Gérez des disques Azure avec PowerShell](tutorial-manage-data-disk.md).
