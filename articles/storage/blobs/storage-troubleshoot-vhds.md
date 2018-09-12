---
title: Résoudre les problèmes de disques attachés à des machines virtuelles Azure | Microsoft Docs
description: Fournit des liens vers des ressources de dépannage pour des disques durs de machine virtuelle Azure.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 81db3a819c28aab8f4b644a940eeb5e6c5ecf3d6
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307170"
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
