---
title: Supervision de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Superviser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f046304121e0aed8efa1bbc2535d34186eba3496
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713705"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Comment surveiller SAP HANA (grandes instances) sur Azure

SAP HANA sur Azure (grandes instances) est semblable aux autres déploiements IaaS, dans le sens où vous devez surveiller les actions du système d’exploitation et de l’application, et comment les applications consomment les ressources suivantes :

- UC
- Mémoire
- Bande passante réseau
- Espace disque

Avec les machines virtuelles Azure, vous devez déterminer si les classes de ressources ci-dessus sont suffisantes, ou si elles s’épuisent. Voici plus de détails sur chacune des classes :

**Consommation des ressources du processeur :** Le ratio que SAP défini pour certaines charges de travail sur HANA est appliqué pour vous assurer qu’il doit y avoir suffisamment de ressources processeur disponibles pour travailler avec les données sont stockées en mémoire. Toutefois, il peut arriver que HANA consomme un grand nombre de requêtes d’exécution du processeur en raison d’index manquants ou de problèmes similaires. Cela signifie que vous devez surveiller la consommation des ressources du processeur de l’unité de grande instance HANA, ainsi que les ressources du processeur consommées par les services HANA spécifiques.

**Consommation de mémoire :** Est important de surveiller à partir dans HANA, ainsi qu’en dehors de HANA sur l’unité. Dans HANA, surveillez comment les données consomment la mémoire allouée à HANA, afin de respecter les instructions de dimensionnement de SAP. Vous devez également surveiller la consommation de mémoire au niveau de la grande instance afin de vous assurer que les logiciels supplémentaires non HANA installés ne consomment pas trop de mémoire, et ne concurrencent donc pas HANA en termes de mémoire.

**Bande passante réseau :** La passerelle de réseau virtuel Azure est limitée en bande passante de déplacement des données dans le réseau virtuel Azure, il est donc utile de surveiller les données reçues par toutes les machines virtuelles Azure au sein d’un réseau virtuel pour déterminer la façon dont vous êtes les limites de la passerelle Azure référence (SKU) que vous avez sélectionné. Sur l’unité de grande instance HANA, il est pertinent de surveiller également le trafic réseau entrant et sortant, et de suivre les volumes qui sont gérés au fil du temps.

**Espace disque :** La consommation d’espace disque augmente généralement au fil du temps. Les causes les plus courantes sont les suivantes : augmentation du volume de données, exécution de sauvegardes des journaux de transaction, stockage des fichiers de trace et création d’instantanés du stockage. Par conséquent, il est important de surveiller l’espace disque et de gérer l’espace disque associé à l’unité de grande instance HANA.

Pour les **références SKU de Type II** des grandes instances HANA, le serveur est fourni avec des outils de diagnostic système préchargés. Vous pouvez utiliser ces outils de diagnostic pour vérifier l’intégrité du système. Exécutez la commande suivante pour générer le fichier journal de vérification d’intégrité sur /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Lorsque vous contactez l’équipe de Support Microsoft pour résoudre un problème, vous pouvez également être invité à utiliser ces outils de diagnostic pour fournir les fichiers journaux. Pour zipper le fichier, vous pouvez utiliser la commande suivante.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Étapes suivantes**

- Voir [Comment surveiller SAP HANA (grandes instances) sur Azure](troubleshooting-monitoring.md).