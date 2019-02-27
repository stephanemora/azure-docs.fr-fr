---
title: Limites des ressources pour Azure NetApp Files | Microsoft Docs
description: Décrit les limites des ressources Azure NetApp Files, y compris les limites des comptes NetApp, des pools de capacité, des volumes et du sous-réseau délégué.
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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 196d85917e0a9900e141d58bff171beeb8540409
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430011"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites des ressources pour Azure NetApp Files

Comprendre les limites des ressources pour Azure NetApp Files vous permet de gérer vos volumes.

- Chaque abonnement Azure peut présenter un maximum de 10 comptes NetApp.
- Chaque compte NetApp peut disposer d'un maximum de 25 pools de capacité.
- Chaque pool de capacité peut appartenir à un seul compte NetApp.  
- La taille minimale d’un pool de capacité est de 4 Tio et sa taille maximale est de 500 Tio. 
- Chaque pool de capacité peut disposer d'un maximum de 500 volumes.
- La taille minimale d’un volume est de 100 Gio et sa taille maximale est de 92 Tio.
- Chaque volume peut disposer d'un maximum de 255 instantanés.
- Chaque réseau virtuel Azure peut présenter un seul sous-réseau délégué à Azure NetApp Files.

**Étapes suivantes**

[Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
