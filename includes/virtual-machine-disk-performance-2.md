---
title: Fichier include
description: Fichier include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663105"
---
![Documentation Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Le débit disque maximum **non mis en cache** est la limite maximale de stockage par défaut que la machine virtuelle est en mesure de gérer. La limite de débit de stockage **mis en cache** maximale est une limite distincte lorsque vous activez la mise en cache de l’hôte. L’activation de la mise en cache de l’hôte peut être effectuée lors de la création de votre machine virtuelle et de l’attachement des disques. Vous pouvez également l’ajuster pour activer et désactiver la mise en cache de vos disques sur une machine virtuelle existante :

![Mise en cache de l'hôte](media/vm-disk-performance/host-caching.jpg)

La mise en cache de l’hôte peut être ajustée pour correspondre à vos exigences de charge de travail pour chaque disque. Vous pouvez définir la mise en cache de l’hôte en lecture seule pour les charges de travail qui effectuent uniquement des opérations de lecture et de lecture/écriture pour les charges de travail qui effectuent un mélange équilibré d’opérations de lecture et d’écriture. Si votre charge de travail ne suit pas l’un de ces modèles, vous ne pourrez malheureusement pas utiliser la mise en cache de l’hôte. 

Poursuivons avec un exemple avec notre machine virtuelle Standard_D8s_v3. À l’exception de cette fois, nous allons activer la mise en cache de l’hôte sur les disques. La limite d’E/S par seconde de la machine virtuelle est maintenant de 16 000 E/S par seconde. Trois disques P30 sous-jacents qui peuvent gérer 5 000 E/S par seconde sont attachés à la machine virtuelle.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30 
    - IOPS : 5 000
    - Mise en cache de l’hôte activée 
- 2 Disques de données P30
    - IOPS : 5 000
    - Mise en cache de l’hôte activée

![Exemple de mise en cache d’hôte](media/vm-disk-performance/host-caching-example-without-remote.jpg)

À présent, l’application qui utilise cette machine virtuelle Standard_D8s_v3 avec mise en cache est activée pour une demande de 15 000 E/S par seconde. Ces demandes sont décomposées en 5 000 E/S par seconde pour chaque disque sous-jacent attaché, et aucune limitation des performances ne se produit.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinées de mise en cache et de non mise en cache

Les limites de mise en cache d’une machine virtuelle sont séparées des limites de non mise en cache. Cela signifie que vous pouvez activer la mise en cache de l’hôte sur les disques attachés à une machine virtuelle tout en désactivant la mise en cache de l’hôte sur d’autres disques pour permettre à vos machines virtuelles d’obtenir les E/S de stockage totales de la limite de mise en cache plus la limite de non mise en cache. Passons en revue un exemple pour vous aider à intégrer la manière dont ces limites fonctionnent ensemble, et continuons avec la machine virtuelle Standard_D8s_v3 et la configuration de disques Premium attachés.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30 
    - IOPS : 5 000
    - Mise en cache de l’hôte activée 
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte activée
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte désactivée

![Exemple de mise en cache de l’hôte avec stockage étendu](media/vm-disk-performance/host-caching-example-with-remote.jpg)

À présent, l’application qui s’exécute sur cette machine virtuelle Standard_D8s_v3 est activée pour une demande de 25 000 E/S par seconde. Cette requête est décomposée en 5 000 E/S par seconde sur chaque disque sous-jacent ; 3 de ces disques utilisent la mise en cache de l’hôte et 2 des disques non. Étant donné que les 3 qui utilisent la mise en cache de l’hôte se trouvent dans les limites de mise en cache de 16 000, ces requêtes sont correctement effectuées et aucune limitation des performances de stockage ne se produit. En outre, étant donné que les 2 disques qui n’utilisent pas la mise en cache de l’hôte se trouvent dans les limites de non mise en cache de 12 800, ces requêtes sont également effectuées correctement et aucune limitation n’est effectuée.

## <a name="metrics-for-disk-performance"></a>Métriques pour les performances du disque
Nous disposons de métriques sur Azure qui fournissent des informations sur les performances de vos machines virtuelles et disques. Ces métriques peuvent être affichées visuellement via le Portail Azure ou elles peuvent être récupérées à l’aide d’un appel d’API. Les métriques sont calculées sur des intervalles d’une minute. Les métriques suivantes sont disponibles pour obtenir des informations sur les E/S des machines virtuelles et des disques ainsi que les performances de débit :
- **Longueur de file d’attente du disque du système d’exploitation** : nombre de requêtes d’E/S en attente de lecture ou d’écriture sur le disque du système d’exploitation.
- **Octets de lecture /s de disque de système d’exploitation** : nombre d’octets lus en une seconde à partir du disque du système d’exploitation.
- **Opérations de lecture/s sur disque du système d’exploitation** : nombre d’opérations d’entrée lues en une seconde à partir du disque du système d’exploitation.
- **Octets d’écriture /s de disque de système d’exploitation** : nombre d’octets écrits en une seconde à partir du disque du système d’exploitation.
- **Opérations d’écriture/s sur disque du système d’exploitation** : nombre d’opérations de sortie écrites en une seconde à partir du disque du système d’exploitation.
- **Longueur de file d’attente du disque de données** : nombre de requêtes d’E/S en attente de lecture ou d’écriture sur les disques de données.
- **Octets de lecture /s de disque de disque de données** : nombre d’octets lus en une seconde à partir du disque des disques de données.
- **Opérations de lecture/s sur disque de données** : nombre d’opérations d’entrée lues en une seconde à partir du disque des disques de données.
- **Octets d’écriture /s de disque de disque de données** : nombre d’octets écrits en une seconde à partir du disque des disques de données.
- **Opérations d’écriture/s sur disque de données** : nombre d’opérations de sorties écrites en une seconde à partir du disque des disques de données.
- **Octets lus depuis le disque par seconde** : nombre total d’octets lus en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Opérations de lecture sur disque/s** : nombre d’opérations d’entrée lues en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Octets écrits sur le disque par seconde** : nombre d’octets écrits en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Opérations d’écriture sur disque/s** : nombre d’opérations de sortie écrites en une seconde à partir de tous les disques attachés à une machine virtuelle.

## <a name="storage-io-utilization-metrics"></a>Métriques d’utilisation des E/S de stockage
Mesures permettant de diagnostiquer les limitations des E/S disque :
- **Pourcentage d’E/S par seconde consommées du disque de données** : pourcentage calculé par les E/S par seconde de disque de données terminées par rapport aux E/S par seconde de disque de données approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les E/S par seconde de votre disque de données.
- **Pourcentage de bande passante consommée du disque de données** : pourcentage calculé par la bande passante consommée par rapport à la bande passante de disque de données approvisionnée. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque de données.
- **Pourcentage d’E/S par seconde consommées du disque du système d’exploitation** : pourcentage calculé par les E/S par seconde de disque du système d’exploitation terminées par rapport aux E/S par seconde de disque du système d’exploitation approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les E/S par seconde de votre disque du système d’exploitation.
- **Pourcentage de bande passante du disque de système d'exploitation consommée** : pourcentage calculé par le débit du disque du système d’exploitation consommé par rapport au débit de disque du système d’exploitation approvisionné. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque du système d’exploitation.