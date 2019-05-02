---
title: Vue d’ensemble des disques managés de stockage sur disque Azure pour les machines virtuelles Windows | Microsoft Docs
description: Vue d’ensemble des disques managés Azure, qui gère à votre place les comptes de stockage si vous utilisez des machines virtuelles Windows Azure
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725817"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction aux disques managés Azure

Un disque managé Azure est un disque dur virtuel (VHD). Vous pouvez le voir comme un disque physique sur un serveur local mais virtualisé. Les disques managés Azure sont stockés en tant qu’objet blob de page, qui sont un objet de stockage d’E/S aléatoire dans Azure. Nous appelons un disque managé « managé » car il s’agit d’une abstraction sur les objets blob de pages, les conteneurs d’objets de couleur et les comptes de stockage Azure. Avec les disques managés, il vous suffit d’approvisionner le disque et Azure s’occupe du reste.

Lorsque vous choisissez d’utiliser des disques managés Azure avec vos charges de travail, Azure crée et gère le disque pour vous. Les types de disques disponibles sont les disques Ultra Solid State Drive (SSD) (préversion), SSD Premium, SSD Standard et disque dur standard. Pour plus d’informations sur chaque type de disque individuel, consultez [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md)