---
title: Niveaux de service pour Azure NetApp Files | Microsoft Docs
description: Décrit les performances de débit des niveaux de service d'Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691051"
---
# <a name="service-levels-for-azure-netapp-files"></a>Niveaux de service pour Azure NetApp Files
Azure NetApp Files prend en charge deux niveaux de service : Premium et Standard. 

## <a name="Premium"></a>Stockage Premium

Le stockage *Premium* offre jusqu'à 64 Mio/s par Tio de débit. Le niveau de performance du débit est indexé par rapport au quota de volume. Par exemple, un volume du stockage Premium avec 2 Tio de quota approvisionné (indépendamment de sa consommation réelle) présente un débit de 128 Mio/s.

## <a name="Standard"></a>Stockage Standard

Le stockage *Standard* offre jusqu'à 16 Mio/s par Tio de débit. Le niveau de performance du débit est indexé par rapport au quota de volume. Par exemple, un volume du stockage Standard avec 2 Tio de quota approvisionné (indépendamment de sa consommation réelle) présente un débit de 32 Mio/s.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [page des tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) pour connaître le prix des différents niveaux de service
- [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
