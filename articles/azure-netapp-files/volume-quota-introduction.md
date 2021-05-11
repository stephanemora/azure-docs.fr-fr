---
title: Présentation du quota de volume pour Azure NetApp Files | Microsoft Docs
description: Fournit une vue d'ensemble du quota de volume. Fournit également des informations de référence sur la surveillance et la gestion du volume et de la capacité des pools.
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: d648630e02cbf8c1e7c771f77a3d2342fd9731f7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293976"
---
# <a name="understand-volume-quota"></a>Présentation du quota de volume

Cet article fournit une vue d'ensemble du quota de volume pour Azure NetApp Files. Il fournit également des informations de référence qui peuvent vous aider à surveiller et gérer la capacité d'un volume ou un pool de capacité.  

## <a name="behaviors-of-volume-quota"></a>Comportements liés au quota de volume 

* La capacité de stockage d'un volume Azure NetApp Files est limitée à la taille définie (quota) du volume. 

* Lorsque la consommation du volume atteint la limite maximale, ni le volume ni le pool de capacité sous-jacent n'augmentent automatiquement. Au lieu de cela, le volume reçoit une condition « espace insuffisant ». Toutefois, vous pouvez [redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) en fonction des besoins. Vous devez activement [surveiller la capacité d'un volume](monitor-volume-capacity.md) et le pool de capacité sous-jacent.

* Selon le type de pool de capacité, la taille (quota) d'un volume Azure NetApp Files a un impact sur les performances de sa bande passante et sur la capacité approvisionnée.  Pour plus d'informations, consultez [Type de pool QoS automatique](azure-netapp-files-understand-storage-hierarchy.md#qos_types). 

* La capacité consommée par les [instantanés](snapshots-introduction.md) du volume est comptabilisée dans l'espace approvisionné du volume. 

* Le quota de volume ne s'applique pas à un [volume de destination de réplication](cross-region-replication-introduction.md).

* Consultez [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md) pour en savoir plus sur le calcul de la consommation de capacité et du dépassement de la consommation de capacité.

## <a name="next-steps"></a>Étapes suivantes

* [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Surveiller la capacité d'un volume](monitor-volume-capacity.md)
* [Redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Questions fréquentes (FAQ) sur la gestion de la capacité](azure-netapp-files-faqs.md#capacity-management-faqs)