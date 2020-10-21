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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016544"
---
![Menu Métriques](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Toutefois, la taille Standard_D8s_v3 peut atteindre un total de 28 600 IOPS. À l’aide des métriques, nous allons examiner ce qui se passe et identifier le goulot d’étranglement d’E/S du stockage. Commencez par localiser le bouton Métriques du menu de gauche et sélectionnez-le :

![Menu Métriques](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Examinons tout d’abord la métrique **Pourcentage d’IOPS en cache de machine virtuelle consommées** :

![Pourcentage d’IOPS en cache de machine virtuelle consommées](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Cette métrique nous informe que sur les 16 000 IOPS allouées aux IOPS en cache sur la machine virtuelle, seuls 61 % sont utilisés. Cela signifie que le goulot d’étranglement d’E/S de stockage n’est pas dans les disques mis en cache, car la valeur n’est pas 100 %. Examinons tout d’abord la métrique **Pourcentage d’IOPS non mises en cache de machine virtuelle consommées** :

![Pourcentage d’IOPS non mises en cache de machine virtuelle consommées](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Cette métrique est 100 %, ce qui nous indique que l’ensemble des 12 800 IOPS allouées aux IOPS non mises en cache sur la machine virtuelle sont utilisées. Pour y remédier, il est possible de définir une plus grande taille de machine virtuelle, qui permettrait de traiter les E/S supplémentaires. Avant cela, examinons le disque attaché pour voir combien d’IOPS il détecte. Nous allons d’abord examiner le disque de système d’exploitation en observant le **pourcentage d’IOPS du disque de système d’exploitation consommées** :

![Pourcentage d’IOPS du disque de système d’exploitation consommées](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Cette métrique nous indique que sur les 5 000 IOPS approvisionnées pour ce disque de système d’exploitation P30, environ 90 % sont utilisés. Cela signifie qu’il n’y a pas de goulot d’étranglement sur le disque de système d’exploitation. Nous allons maintenant examiner les disques de données attachés à la machine virtuelle en observant le **pourcentage d’IOPS du disque de données consommées** :

![Pourcentage d’IOPS du disque de données consommées](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Cette métrique nous indique que le pourcentage moyen d’IOPS consommées dans l’ensemble des disques attachés est d’environ 42 %. Ce pourcentage est calculé sur la base des IOPS utilisées par les disques et qui ne sont pas délivrées à partir du cache de l’hôte. Examinons de plus près cette métrique en appliquant le **fractionnement** à ces métriques et en fractionnant par la valeur LUN :

![Pourcentage d’IOPS du disque de données consommées avec fractionnement](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Cette métrique nous indique que les disques de données attachés à LUN 3 et 2 sont utilisés à hauteur d’environ 85 % de leurs IOPS approvisionnées. Voici un diagramme des E/S dans l’architecture de la machine virtuelle et des disques :

![Exemple de diagramme des métriques d’E/S de stockage](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
