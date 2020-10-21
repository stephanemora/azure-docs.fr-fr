---
title: Fichier include
description: Fichier include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: f5ac97812f973a20f6ee4c2dea34baaeb91203af
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016473"
---
![Documentation Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Le débit disque maximum **non mis en cache** est la limite maximale de stockage par défaut que la machine virtuelle est en mesure de gérer. La limite de débit de stockage **mis en cache** maximale est une limite distincte lorsque vous activez la mise en cache de l’hôte. La mise en cache de l’hôte rapproche le stockage de la machine virtuelle sur laquelle vous pouvez écrire ou lire rapidement. La quantité de stockage disponible pour la machine virtuelle pour la mise en cache de l’hôte se trouve dans la documentation. Par exemple, Standard_D8s_v3 correspond à 200 Gio de stockage cache.

L’activation de la mise en cache de l’hôte peut être effectuée lors de la création de votre machine virtuelle et de l’attachement des disques. Vous pouvez également l’ajuster pour activer et désactiver la mise en cache de l’hôte de vos disques sur une machine virtuelle existante.

![Mise en cache de l'hôte](media/vm-disk-performance/host-caching.jpg)

La mise en cache de l’hôte peut être ajustée pour correspondre à vos exigences de charge de travail pour chaque disque. Vous pouvez définir la mise en cache de l’hôte sur Lecture seule pour les charges de travail qui effectuent uniquement des opérations de lecture et sur Lecture/écriture pour les charges de travail qui effectuent un mélange équilibré d’opérations de lecture et d’écriture. Si votre charge de travail ne suit pas l’un de ces modèles, il est déconseillé d’utiliser la mise en cache de l’hôte. 

Examinons quelques exemples de différents paramètres de mise en cache de l’hôte et voyons comment ils agissent sur le flux de données et les performances. Dans ce premier exemple, nous allons examiner ce qui se passe avec les demandes d’E/S lorsque le paramètre de mise en cache de l’hôte est défini sur **Lecture seule**.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de données P30 
    - IOPS : 5 000
    - **Mise en cache de l’hôte : Lecture seule** 

Lorsqu’une lecture est effectuée et que les données souhaitées sont disponibles dans le cache, celui-ci renvoie les données demandées et il n’est pas nécessaire de lire à partir du disque. Cette lecture est comptabilisée dans les limites mises en cache de la machine virtuelle.

![Correspondance de lecture avec mise en cache de l’hôte sur Lecture](media/vm-disk-performance/host-caching-read-hit.jpg)

Lorsqu’une lecture est effectuée et que les données souhaitées ne sont **pas** disponibles dans le cache, la demande de lecture est relayée au disque, qui le met à la disposition du cache puis de la machine virtuelle. Cette lecture est comptabilisée dans la limite non mise en cache ainsi que dans celle mise en cache de la machine virtuelle.

![Échec de lecture avec mise en cache de l’hôte sur Lecture](media/vm-disk-performance/host-caching-read-miss.jpg)

Lorsqu’une écriture est effectuée, elle doit être écrite dans le cache et dans le disque pour être considérée comme terminée. Cette écriture est comptabilisée dans la limite non mise en cache ainsi que dans celle mise en cache de la machine virtuelle.

![Écriture avec mise en cache de l’hôte sur Lecture](media/vm-disk-performance/host-caching-write.jpg)

Dans l’exemple suivant, nous allons examiner ce qui se passe avec les demandes d’E/S lorsque le paramètre de mise en cache de l’hôte est défini sur **Lecture/écriture**.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de données P30 
    - IOPS : 5 000
    - **Mise en cache de l’hôte : Lecture/écriture** 

Les lectures sont gérées de la même manière qu’avec l’option Lecture seule. Seules les écritures sont différentes. Lors de l’écriture avec la mise en cache de l’hôte définie sur Lecture/écriture, il suffit d’écrire l’écriture dans le cache de l’hôte pour que l’opération soit considérée comme terminée. L’écriture est ensuite écrite tardivement dans le disque dans le cadre d’un processus d’arrière-plan. Cela signifie que les écritures sont comptabilisées dans les opérations d’E/S mises en cache lorsqu’elles sont écrites dans le cache, et dans les opérations d’E/S non mises en cache lorsqu’elles sont écrites tardivement dans le disque.

![Écriture avec mise en cache de l’hôte sur Lecture/écriture](media/vm-disk-performance/host-caching-read-write.jpg)

Poursuivons avec un exemple avec notre machine virtuelle Standard_D8s_v3. À l’exception de cette fois, nous allons activer la mise en cache de l’hôte sur les disques. La limite d’E/S par seconde de la machine virtuelle est maintenant de 16 000 E/S par seconde. Trois disques P30 sous-jacents qui peuvent gérer 5 000 E/S par seconde sont attachés à la machine virtuelle.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30 
    - IOPS : 5 000
    - Mise en cache de l’hôte : Lecture/écriture 
- 2 Disques de données P30
    - IOPS : 5 000
    - Mise en cache de l’hôte : Lecture/écriture

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
    - Mise en cache de l’hôte : Lecture/écriture
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte : Lecture/écriture
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte : Désactivé

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
- **Pourcentage d’IOPS de disque de données consommées** : pourcentage calculé en divisant les opérations d’IOPS de disque de données terminées par les IOPS de disque de données approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les E/S par seconde de votre disque de données.
- **Pourcentage de bande passante consommée du disque de données** : pourcentage calculé par la bande passante consommée par rapport à la bande passante de disque de données approvisionnée. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque de données.
- **Pourcentage d’IOPS du disque du système d’exploitation consommées** : pourcentage calculé en divisant les opérations d’IOPS de disque de système d’exploitation terminées par les IOPS de disque du système d’exploitation approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les E/S par seconde de votre disque du système d’exploitation.
- **Pourcentage de bande passante du disque de système d'exploitation consommée** : pourcentage calculé par le débit du disque du système d’exploitation consommé par rapport au débit de disque du système d’exploitation approvisionné. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque du système d’exploitation.

Métriques permettant de diagnostiquer les limitations des E/S de machine virtuelle :
- **Pourcentage d’IOPS en cache de machine virtuelle consommées** : pourcentage calculé en divisant le total des opérations d’IOPS terminées par le nombre maximal d’IOPS en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS en cache de votre machine virtuelle.
- **Pourcentage de bande passante en cache consommée par la machine virtuelle** - pourcentage calculé en divisant la bande passante de disque totale terminée par le débit maximal en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante en cache de votre machine virtuelle.
- **Pourcentage d’IOPS non mises en cache de machine virtuelle consommées** : pourcentage calculé en divisant le total des opérations d’IOPS terminées sur une machine virtuelle par la limite d’IOPS non mises en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS non mises en cache de votre machine virtuelle.
- **Pourcentage de bande passante non mise en cache consommée par la machine virtuelle** - pourcentage calculé en divisant la bande passante de disque totale terminée sur une machine virtuelle par le débit maximal approvisionné de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante non mise en cache de votre machine virtuelle.

## <a name="storage-io-utilization-metrics-example"></a>Exemples de métriques d’utilisation des E/S de stockage
Examinons un exemple d’utilisation de ces nouvelles métriques d’utilisation des E/S de stockage pour nous aider à localiser un goulot d’étranglement dans notre système. La configuration du système est exactement la même que dans l’exemple précédent, sauf que cette fois, le disque de système d’exploitation attaché n’est **pas** mis en cache.

Configuration :
- Standard_D8s_v3 
    - E/S par seconde mises en cache : 16 000
    - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30 
    - IOPS : 5 000
    - Mise en cache de l’hôte : Désactivé
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte : Lecture/écriture
- 2 Disques de données P30 X 2
    - IOPS : 5 000
    - Mise en cache de l’hôte : Désactivé

