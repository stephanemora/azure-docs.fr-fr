---
title: Benchmarks de performances Azure NetApp Files pour Linux | Microsoft Docs
description: Décrit les benchmarks de performances relatifs à Azure NetApp Files pour Linux.
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 814474418386041d2b20759c4fb2accc534e8b9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355859"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Benchmarks de performances Azure NetApp Files pour Linux

Cet article décrit les benchmarks de performances relatifs à Azure NetApp Files pour Linux.

## <a name="linux-scale-out"></a>Scale-out Linux

Cette section décrit les benchmarks de performances du débit de charge de travail Linux et des IOPS de charge de travail.

### <a name="linux-workload-throughput"></a>Débit de la charge de travail Linux  

Le graphique ci-dessous représente une charge de travail séquentielle de 64 Kio et une plage de travail de 1 Tio. Il indique qu’un seul volume d’Azure NetApp Files peut gérer entre environ 1 600 Mio/s de pures écritures séquentielles et environ 4 500 Mio/s de pures lectures séquentielles.  

Le graphique illustre des diminutions de 10 % à la fois, de la lecture pure à l’écriture pure. Il montre ce que vous pouvez attendre quand vous utilisez des ratios lecture/écriture variables (100%:0%, 90%:10%, 80%:20%, etc.).

![Débit de la charge de travail Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>IOPS de la charge de travail Linux  

Le graphique ci-dessous représente une charge de travail aléatoire de 4 Kio et une plage de travail de 1 Tio. Le graphique montre qu’un volume d’Azure NetApp Files peut gérer entre 130 000 écritures aléatoires pures et 460 000 lectures aléatoires pures.  

Ce graphique illustre des diminutions de 10 % à la fois, de la lecture pure à l’écriture pure. Il montre ce que vous pouvez attendre quand vous utilisez des ratios lecture/écriture variables (100%:0%, 90%:10%, 80%:20%, etc.).

![IOPS de la charge de travail Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Scale-up Linux  

Les graphiques de cette section montrent les résultats des tests de validation de l’option de montage côté client avec NFSv3. Pour plus d’informations, consultez la [section `nconnect` des options de montage Linux](performance-linux-mount-options.md#nconnect).

Les graphiques comparent les avantages de `nconnect` par rapport à un volume monté non `connected`. Dans les graphiques, FIO a généré la charge de travail à partir d’une seule instance D32s_v4 dans la région Azure us-west2 à l’aide d’une charge de travail séquentielle 64 Kio, la plus grande taille d’E/S prise en charge par Azure NetApp Files au moment du test représenté ici. Azure NetApp Files prend désormais en charge des tailles d’E/S plus élevées. Pour plus d’informations, consultez les [sections `rsize` et `wsize` des options de montage Linux](performance-linux-mount-options.md#rsize-and-wsize).

### <a name="linux-read-throughput"></a>Débit de lecture Linux  

Les graphiques suivants montrent des lectures séquentielles de 64 Kio d’environ 3500 Mio/s de lectures avec `nconnect`, approximativement 2,3 fois plus qu’avec l’option sans `nconnect`.

![Débit de lecture Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Débit d’écriture Linux  

Les graphiques suivants montrent des écritures séquentielles. Ils indiquent que `nconnect` n’a pas d’avantage notable pour les écritures séquentielles. 1 500 Mio/s correspond à peu près à la limite supérieure du volume en écriture séquentielle et à la limite de sortie de l’instance D32s_v4.

![Débit d’écriture Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>E/S de lecture Linux  

Les graphiques suivants montrent des lectures aléatoires de 4 Kio d’environ 200 000 IOPS de lecture avec `nconnect`, environ 3 fois plus qu’avec l’option sans `nconnect`.

![IOPS de lecture Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>IOPS d’écriture Linux  

Les graphiques suivants montrent des écritures aléatoires de 4 Kio d’environ 135 000 IOPS d’écriture avec `nconnect`, environ 3 fois plus qu’avec l’option sans `nconnect`.

![IOPS d’écriture Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Étapes suivantes

- [Azure NetApp Files : Tirer le meilleur parti de votre stockage cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
