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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580388"
---
![Graphique présentant les spécifications Dsv3.](media/vm-disk-performance/dsv3-documentation.jpg)

- Le débit disque maximum *non mis en cache* est la limite maximale de stockage par défaut que la machine virtuelle est en mesure de gérer.
- La limite de débit de stockage *mis en cache* maximale est une limite distincte lorsque vous activez la mise en cache de l’hôte.

La mise en cache de l’hôte rapproche le stockage de la machine virtuelle sur laquelle vous pouvez écrire ou lire rapidement. La quantité de stockage disponible pour la machine virtuelle pour la mise en cache de l’hôte se trouve dans la documentation. Par exemple, Standard_D8s_v3 correspond à 200 Gio de stockage cache.

Vous pouvez activer la mise en cache de l’hôte lorsque vous créez votre machine virtuelle et que vous attachez des disques. Vous pouvez également activer et désactiver la mise en cache de l’hôte de vos disques sur une machine virtuelle existante.

![Capture d’écran montrant la mise en cache de l’hôte.](media/vm-disk-performance/host-caching.jpg)

Vous pouvez ajuster la mise en cache de l’hôte pour correspondre à vos exigences de charge de travail pour chaque disque. Vous pouvez définir la mise en cache de l’hôte :

- **Lecture seule** : Pour les charges de travail qui effectuent uniquement des opérations de lecture
- **Lecture/écriture** : Pour les charges de travail qui effectuent un équilibre entre opérations de lecture et d’écriture

Si votre charge de travail ne suit pas l’un de ces modèles, il est déconseillé d’utiliser la mise en cache de l’hôte.

Examinons quelques exemples de différents paramètres de mise en cache de l’hôte pour voir comment ils agissent sur le flux de données et les performances. Dans ce premier exemple, nous allons examiner ce qui se passe avec les demandes d’E/S lorsque le paramètre de mise en cache de l’hôte est défini sur **Lecture seule**.

**Configuration** :

- Standard_D8s_v3
  - E/S par seconde mises en cache : 16 000
  - E/S par seconde non mises en cache : 12 800
- Disque de données P30
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture seule**

Lorsqu’une lecture est effectuée et que les données souhaitées sont disponibles dans le cache, celui-ci renvoie les données demandées. Il n’est pas nécessaire de lire depuis le disque. Cette lecture est comptabilisée dans les limites mises en cache de la machine virtuelle.

![Diagramme montrant une correspondance de lecture avec mise en cache de l’hôte sur lecture.](media/vm-disk-performance/host-caching-read-hit.jpg)

Lorsqu’une lecture est effectuée et que les données souhaitées ne sont *pas* disponibles dans le cache, la demande de lecture est relayée au disque. Ensuite, le disque la met à la disposition à la fois au cache et de la machine virtuelle. Cette lecture est comptabilisée dans la limite non mise en cache ainsi que dans celle mise en cache de la machine virtuelle.

![Diagramme montrant une non-correspondance de lecture avec mise en cache de l’hôte sur lecture.](media/vm-disk-performance/host-caching-read-miss.jpg)

Lorsqu’une écriture est effectuée, elle doit être écrite dans le cache et dans le disque pour être considérée comme terminée. Cette écriture est comptabilisée dans la limite non mise en cache ainsi que dans celle mise en cache de la machine virtuelle.

![Diagramme montrant une écriture avec mise en cache de l’hôte sur lecture.](media/vm-disk-performance/host-caching-write.jpg)

Ensuite, examinons ce qui se passe avec les demandes d’E/S lorsque le paramètre de mise en cache de l’hôte est défini sur **Lecture/écriture**.

**Configuration** :

- Standard_D8s_v3
  - E/S par seconde mises en cache : 16 000
  - E/S par seconde non mises en cache : 12 800
- Disque de données P30
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**

Une lecture est gérée de la même façon qu’une lecture seule. Les écritures sont la seule chose qui est différente pour la mise en cache en lecture/écriture. Lors de l’écriture avec la mise en cache de l’hôte définie sur **Lecture/écriture**, il suffit d’écrire l’écriture dans le cache de l’hôte pour que l’opération soit considérée comme terminée. L’écriture est ensuite écrite tardivement dans le disque dans le cadre d’un processus d’arrière-plan. Cela signifie qu’une écriture est comptabilisée pour les E/S de mise en cache lors de son écriture dans le cache. Lorsqu’elle est écrite tardivement sur le disque, elle est comptabilisée vers les E/S non mises en cache.

![Diagramme montrant une écriture avec mise en cache de l’hôte sur lecture/écriture.](media/vm-disk-performance/host-caching-read-write.jpg)

Poursuivons avec notre machine virtuelle Standard_D8s_v3. La différence est que cette fois, nous allons activer la mise en cache de l’hôte sur les disques. En outre, la limite d’IOPS de la machine virtuelle est désormais de 16 000 IOPS. Trois disques P30 sous-jacents qui peuvent gérer chacun 5 000 IOPS sont attachés à la machine virtuelle.

**Configuration** :

- Standard_D8s_v3
  - E/S par seconde mises en cache : 16 000
  - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**
- Deux disques de données P30 x 2
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**

![Diagramme montrant un exemple de mise en cache de l’hôte.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

L’application utilise une machine virtuelle Standard_D8s_v3 avec mise en cache activée. Elle effectue une demande de 15 000 IOPS. Les demandes sont décomposées en 5 000 IOPS pour chaque disque sous-jacent attaché. Aucune limitation des performances ne se produit.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinées de mise en cache et de non mise en cache

Les limites de mise en cache d’une machine virtuelle sont séparées de ses limites de non-mise en cache. Cela signifie que vous pouvez activer la mise en cache de l’hôte sur les disques attachés à une machine virtuelle tout en désactivant la mise en cache de l’hôte sur d’autres disques. Cette configuration permet à vos machines virtuelles d’obtenir un total d’E/S de stockage égal à la limite mise en cache plus la limite non mise en cache.

Nous allons parcourir un exemple pour vous aider à comprendre comment ces limites fonctionnent ensemble. Nous allons continuer avec la machine virtuelle Standard_D8s_v3 et la configuration avec disques Premium attachés.

**Configuration** :

- Standard_D8s_v3
  - E/S par seconde mises en cache : 16 000
  - E/S par seconde non mises en cache : 12 800
- Disque de système d’exploitation P30
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**
- Deux disques de données P30 x 2
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Lecture/écriture**
- Deux disques de données P30 x 2
  - IOPS : 5 000
  - Mise en cache de l’hôte : **Désactivé**

![Diagramme montrant un exemple de mise en cache de l’hôte avec stockage étendu.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Dans ce cas, l’application qui s’exécute sur cette machine virtuelle Standard_D8s_v3 est activée pour une demande de 25 000 IOPS. La requête est divisée en 5 000 IOPS sur chacun des disques attachés. Trois disques utilisent la mise en cache de l’hôte et deux disques ne l’utilisent pas.

- Étant donné que les trois disques qui utilisent la mise en cache de l’hôte se trouvent dans les limites de 16 000, ces demandes sont correctement honorées. Aucune limitation des performances de stockage ne se produit.
- Étant donné que les deux disques qui n’utilisent pas la mise en cache de l’hôte se trouvent dans les limites de non-mise en cache de 12 800, ces requêtes sont également effectuées correctement. Aucune limitation ne se produit.

