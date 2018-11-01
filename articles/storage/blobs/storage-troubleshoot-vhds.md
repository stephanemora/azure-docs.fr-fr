---
title: Résoudre les problèmes de disques attachés à des machines virtuelles Azure | Microsoft Docs
description: Fournit des liens vers des ressources de dépannage pour des disques durs de machine virtuelle Azure.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414164"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Résoudre les problèmes de disques attachés à des machines virtuelles Azure 

Les machines virtuelles (VM) Azure s’appuient sur des disques durs virtuels (VHD) pour le disque du système d’exploitation et tout disque de données joint. Les VHD sont stockés en tant qu’objets blob de pages dans un ou plusieurs comptes de stockage Azure. Cet article explique comment résoudre les problèmes de contenu courants pouvant survenir avec des VHD. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Résoudre les erreurs de suppression de compte de stockage pour une machine virtuelle

Dans certains cas, vous pouvez rencontrer une erreur lors de la suppression d’une ressource de stockage lorsqu’une machine virtuelle d’un déploiement Resource Manager contient des VHD attachés. Pour vous aider à résoudre ce problème, consultez l’un des articles suivants : 

  * Sur des machines virtuelles Linux : [Erreurs de suppression de stockage dans un déploiement Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Sur des machines virtuelles Windows : [Erreurs de suppression de stockage dans un déploiement Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Résoudre les redémarrages inattendus de machines virtuelles avec des VHD joints

Si vous rencontrez des redémarrages inattendus d’une machine virtuelle avec un grand nombre de VHD attachés, consultez l’un des articles suivants :

  * Sur des machines virtuelles Linux : [Redémarrages inattendus de machines virtuelles avec des VHD joints](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Sur des machines virtuelles Windows : [Redémarrages inattendus de machines virtuelles avec des VHD joints](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
