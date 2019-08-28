---
title: Vue d’ensemble des disques managés de stockage sur disque Azure pour les machines virtuelles Windows | Microsoft Docs
description: Vue d’ensemble des disques managés Azure, qui gère à votre place les comptes de stockage si vous utilisez des machines virtuelles Windows Azure
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 08/15/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c71d51580c064b5b2b0bbbbf7bf95d50aa40f72f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515553"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction aux disques managés Azure

Un disque managé Azure est un disque dur virtuel (VHD). Vous pouvez le voir comme un disque physique sur un serveur local mais virtualisé. Les disques managés Azure sont stockés en tant qu’objet blob de page, qui sont un objet de stockage d’E/S aléatoire dans Azure. Nous appelons un disque managé « managé » car il s’agit d’une abstraction sur les objets blob de pages, les conteneurs d’objets de couleur et les comptes de stockage Azure. Avec les disques managés, il vous suffit d’approvisionner le disque et Azure s’occupe du reste.

Lorsque vous choisissez d’utiliser des disques managés Azure avec vos charges de travail, Azure crée et gère le disque pour vous. Les types de disques disponibles sont les disques Ultra, les disques SSD Premium, les disques SSD Standard et les disques durs Standard. Pour plus d’informations sur chaque type de disque individuel, consultez [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md)