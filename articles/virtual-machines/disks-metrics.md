---
title: Métriques de disque
description: Exemples de métriques de bursting de disque
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674973"
---
# <a name="disk-performance-metrics"></a>Mesures de performances de disque
Azure propose des métriques dans le portail Azure qui fournissent des informations sur la façon dont vos machines virtuelles et vos disques fonctionnent. Vous pouvez également récupérer les métriques via un appel d’API. Cet article est divisé en 3 sous-sections :

- **Métriques de profondeur de file d’attente, de débit et d’E/S de disque** : ces métriques vous permettent de voir les performances de stockage sous l’angle d’un disque et d’une machine virtuelle.
- **Métriques de bursting de disque** : ces métriques offrent une observabilité de notre fonctionnalité de [bursting](disk-bursting.md) sur nos disques Premium.
- **Métriques d’utilisation des E/S de stockage** : ces métriques aident à diagnostiquer les goulots d’étranglement dans vos performances de stockage sur disques. 

Les métriques sont émises toutes les minutes, à l’exception de la mesure du pourcentage de crédits de bursting, qui est émise toutes les 5 minutes.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Métriques de profondeur de file d’attente, de débit et d’E/S de disque
Les métriques suivantes sont disponibles pour l’obtention d’informations sur les performances des machines virtuelles et des disques en matière de profondeur de file d’attente, de débit et d’E/S :

- **Longueur de file d’attente du disque du système d’exploitation** : Le nombre de requêtes d’E/S en attente de lecture ou d’écriture sur le disque du système d’exploitation.
- **Octets lus/s sur disque du système d’exploitation** : Le nombre d’octets lus en une seconde à partir du disque du système d’exploitation.
- **Opérations de lecture/s sur disque du système d’exploitation** : Le nombre d’opérations d’entrée lues en une seconde à partir du disque du système d’exploitation.
- **Octets/s écrits sur disque du système d’exploitation** : Le nombre d’octets écrits en une seconde à partir du disque du système d’exploitation.
- **Opérations d’écriture/s sur disque du système d’exploitation** : Le nombre d’opérations de sortie écrites en une seconde à partir du disque du système d’exploitation.
- **Longueur de file d’attente du disque de données** : Le nombre de requêtes d’E/S en attente de lecture ou d’écriture sur les disques de données.
- **Octets lus/s sur disque de données** : Le nombre d’octets lus en une seconde à partir du disque des disques de données.
- **Opérations de lecture/s sur disque de données** : Le nombre d’opérations d’entrée lues en une seconde à partir des disques de données.
- **Octets écrits/s sur disque de données** : Le nombre d’octets écrits en une seconde à partir du disque des disques de données.
- **Opérations d’écriture/s sur disque de données** : Le nombre d’opérations de sorties écrites en une seconde à partir du disque des disques de données.
- **Disk Read Bytes/Sec** : Le nombre total d’octets lus en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Disk Read Operations/Sec** : Le nombre d’opérations d’entrée lues en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Disk Write Bytes/Sec** : Le nombre d’octets écrits en une seconde à partir de tous les disques attachés à une machine virtuelle.
- **Disk Write Operations/Sec** : Le nombre d’opérations de sortie écrites en une seconde à partir de tous les disques attachés à une machine virtuelle.

## <a name="bursting-metrics"></a>Métriques de bursting
Les métriques suivantes permettent l’observabilité de notre fonctionnalité [bursting](disk-bursting.md) sur nos disques Premium :

- **Bandwidth en rafale max. du disque de données** : limite de débit que peut atteindre le ou les disques de données en rafale.
- **Bandwidth en rafale max. du disque de système d’exploitation** : limite de débit que peut atteindre le disque du système d’exploitation en rafale.
- **IOPS en rafale max. du disque de données** : limite d’E/S par seconde que le ou les disques de données peuvent atteindre en rafale.
- **IOPS en rafale max. du disque de système d’exploitation** : limite d’E/S par seconde que peut atteindre le disque du système d’exploitation en rafale.
- **Bandwidth cible du disque de données** : limite de débit que le ou les disques de données peuvent atteindre sans bursting.
- **Bandwidth cible du disque de système d’exploitation** : limite de débit que le disque du système d’exploitation peut atteindre sans bursting.
- **IOPS cible du disque de données** : limite d’E/S par seconde que le ou les disques de données peuvent atteindre sans bursting.
- **IOPS cible du disque de système d’exploitation** : limite d’E/S par seconde que le disque de système d’exploitation peut atteindre sans bursting.
- **Pourcentage de crédits de bits/s en rafale utilisés par le disque de données** : pourcentage cumulé du débit en rafale utilisé pour le ou les disques de données. Émis selon un intervalle de 5 minutes.
- **Pourcentage de crédits de bits/s en rafale utilisés par le disque de système d’exploitation** : pourcentage cumulé du débit en rafale utilisé pour le disque de système d’exploitation. Émis selon un intervalle de 5 minutes.
- **Pourcentage de crédits d’E/S en rafale utilisés par le disque de données** : pourcentage cumulé de la rafale IOPS utilisée pour le ou les disques de données. Émis selon un intervalle de 5 minutes.
- **Pourcentage de crédits d’E/S en rafale utilisés par le disque de système d’exploitation** : pourcentage cumulé de la rafale IOPS utilisée pour le disque de système d’exploitation. Émis selon un intervalle de 5 minutes.

## <a name="storage-io-utilization-metrics"></a>Métriques d’utilisation des E/S de stockage
Les métriques suivantes aident à diagnostiquer les goulots d’étranglement dans votre combinaison Machine virtuelle et Disque. Ces métriques sont uniquement disponibles dans le cadre de l’utilisation d’une machine virtuelle Premium. Ces métriques sont disponibles pour tous les types de disques, sauf le type Ultra. 

Mesures permettant de diagnostiquer les limitations des E/S disque :

- **Pourcentage d’IOPS du disque de données consommées** : Le pourcentage calculé en divisant les opérations d’IOPS de disque de données terminées par les IOPS de disque de données approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS de votre disque de données.
- **Pourcentage de bande passante du disque de données consommée** : Le pourcentage calculé par la bande passante consommée par rapport à la bande passante de disque de données approvisionnée. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque de données.
- **Pourcentage d’IOPS du disque de système d’exploitation consommées** : Le pourcentage calculé en divisant les opérations d’IOPS de disque de système d’exploitation terminées par les IOPS de disque du système d’exploitation approvisionnées. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS de votre disque de système d’exploitation.
- **Pourcentage de bande passante du disque de système d’exploitation consommée** : Le pourcentage calculé par le débit du disque du système d’exploitation consommé par rapport au débit de disque du système d’exploitation approvisionné. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante de votre disque de système d’exploitation.

Métriques permettant de diagnostiquer les limitations des E/S de machine virtuelle :

- **Pourcentage d’IOPS en cache de machine virtuelle consommées** : Le pourcentage calculé en divisant le total des opérations d’IOPS terminées par le nombre maximal d’IOPS en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS en cache de votre machine virtuelle.
- **Pourcentage de bande passante en cache consommée par la machine virtuelle** : Le pourcentage calculé en divisant la bande passante de disque totale terminée par le débit maximal en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante en cache de votre machine virtuelle.
- **Pourcentage d’IOPS non mises en cache de machine virtuelle consommées** : Le pourcentage calculé en divisant le total des opérations d’IOPS terminées sur une machine virtuelle par la limite d’IOPS non mises en cache de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par les IOPS non mises en cache de votre machine virtuelle.
- **Pourcentage de bande passante non mise en cache consommée par la machine virtuelle** : Le pourcentage calculé en divisant la bande passante de disque totale terminée sur une machine virtuelle par le débit maximal approvisionné de la machine virtuelle. Si cette valeur est de 100 %, votre application en cours d’exécution sera limitée par la bande passante non mise en cache de votre machine virtuelle.

## <a name="storage-io-metrics-example"></a>Exemple de métriques d’E/S de stockage

Examinons un exemple d’utilisation de ces nouvelles métriques d’utilisation des E/S de stockage pour nous aider à localiser un goulot d’étranglement dans notre système. La configuration système est identique à l’exemple précédent, sauf que cette fois, le disque de système d’exploitation attaché n’est *pas* mis en cache.

**Configuration** :

- Standard_D8s_v3
  - E/S par seconde mises en cache : 16 000
  - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Désactivé**
- Deux disques de données P30 x 2
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**
- Deux disques de données P30 x 2
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Désactivé**

Exécutons un test d’évaluation sur cette combinaison de machine virtuelle et de disque qui crée une activité d’E/S. Pour savoir comment créer un benchmark des E/S de stockage sur Azure, consultez [Créer un benchmark de votre application sur Stockage sur disque Azure](disks-benchmarks.md). À partir de l’outil d’évaluation, vous pouvez voir que la combinaison de machines virtuelles et de disques peut atteindre 22 800 IOPS :

![Capture d’écran de la sortie f i o montrant r=22.8k mis en surbrillance.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



La Standard_D8s_v3 peut atteindre un total de 28 600 d’IOPS. À l’aide des métriques, nous allons examiner ce qui se passe et identifier notre goulot d’étranglement d’e/s du stockage. Dans le volet de gauche, sélectionnez **Métriques** :

![Capture d’écran montrant les métriques mises en surbrillance dans le volet de gauche.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Examinons tout d’abord la métrique **Pourcentage d’IOPS en cache de machine virtuelle consommées** :

![Capture d’écran montrant le pourcentage d’IOPS consommées mises en cache sur une machine virtuelle.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Cette métrique nous informe que 61 % des 16 000 IOPS allouées aux IOPS en cache sur la machine virtuelle sont utilisées. Ce pourcentage signifie que le goulot d’étranglement d’E/S de stockage n’est pas dans les disques mis en cache, car la valeur n’est pas 100 %. Examinons à présent la métrique **Pourcentage d’IOPS non mises en cache de machine virtuelle consommées** :

![Capture d’écran montrant le pourcentage d’IOPS consommées non mises en cache sur une machine virtuelle.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Cette métrique est à 100 %. Elle nous indique que l’ensemble des 12 800 IOPS allouées aux IOPS non mises en cache sur la machine virtuelle sont utilisées. Pour y remédier, il est possible de définir une plus grande taille de machine virtuelle, qui permettrait de traiter les E/S supplémentaires. Avant cela, examinons le disque attaché pour voir combien d’IOPS il détecte. Examinez le disque de système d’exploitation en observant le **pourcentage d’IOPS du disque de système d’exploitation consommées** :

![Capture d’écran montrant le pourcentage d’IOPS consommées sur le disque du système d’exploitation.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Cette métrique nous indique qu’environ 90 % des 5 000 IOPS approvisionnées pour ce disque de système d’exploitation P30 sont utilisées. Ce pourcentage signifie qu’il n’y a aucun goulot d’étranglement au niveau du disque du système d’exploitation. Nous allons maintenant examiner les disques de données attachés à la machine virtuelle en observant le **pourcentage d’IOPS du disque de données consommées** :

![Capture d’écran montrant le pourcentage d’IOPS consommées sur le disque du système d’exploitation.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Cette métrique nous indique que le pourcentage moyen d’IOPS consommées dans l’ensemble des disques attachés est d’environ 42 %. Ce pourcentage est calculé sur la base des E/S par seconde utilisées par les disques, et qui ne sont pas délivrées à partir du cache de l’hôte. Examinons de plus près cette métrique en appliquant le *fractionnement* à ces métriques et en fractionnant par la valeur LUN :

![Capture d’écran montrant le pourcentage d’IOPS consommées sur le disque du système d’exploitation avec fractionnement.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Cette métrique nous indique que les disques de données attachés à LUN 3 et 2 sont utilisés à hauteur d’environ 85 % de leurs IOPS approvisionnées. Voici un diagramme des E/S dans l’architecture de la machine virtuelle et des disques :

![Diagramme de l’exemple de métriques d’E/S de stockage.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Monitor Metrics](../azure-monitor/essentials/data-platform-metrics.md)
- [Description de l’agrégation de métriques](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../azure-monitor/alerts/alerts-metric.md)