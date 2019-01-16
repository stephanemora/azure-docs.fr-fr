---
title: Limites des ressources pour Azure NetApp Files | Microsoft Docs
description: Décrit les limites des ressources Azure NetApp Files, y compris les limites des pools de capacité, des volumes et du sous-réseau délégué.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056355"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files
Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

## <a name="capacity_pools"></a>Pools de capacités

- La taille minimale d’un pool de capacité est de 4 Tio et sa taille maximale est de 500 Tio. 
- Chaque pool de capacité peut appartenir à un seul compte NetApp. Toutefois, vous pouvez avoir plusieurs pools de capacité au sein d’un compte NetApp.  

## <a name="volumes"></a>Volumes

- La taille minimale d’un volume est de 100 Gio et sa taille maximale est de 92 Tio.
- Vous pouvez avoir un maximum de 100 volumes par abonnement Azure et par région.  

## <a name="delegated_subnet"></a>Sous-réseau délégué 

Dans chaque réseau virtuel Azure, un seul sous-réseau peut être délégué à Azure NetApp Files.

## <a name="next-steps"></a>Étapes suivantes

[Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
