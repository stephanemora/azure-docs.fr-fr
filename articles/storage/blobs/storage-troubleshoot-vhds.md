---
title: Résoudre les problèmes de disques attachés à des machines virtuelles Azure | Microsoft Docs
description: Le Stockage Blob Azure est conçu pour stocker de grandes quantités de données d’objet non structurées, telles que des données texte ou binaires. Vos applications peuvent accéder aux objets du stockage Blob à partir de PowerShell ou d’Azure CLI, à partir du code via des bibliothèques clientes de stockage Azure ou par REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: ae309efb95e2f633effcfb5723d8377f5e94d406
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779645"
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
