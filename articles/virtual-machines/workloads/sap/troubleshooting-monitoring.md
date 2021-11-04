---
title: Analyse de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: En savoir plus sur l’analyse de SAP HANA sur Azure (Grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, contperf-fy21q4
ms.openlocfilehash: d8325a9b5a3b8adaf3f39cde791e9a00f3f897e3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036596"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>Analyser SAP HANA (grandes instances) sur Azure

Cet article décrit l’analyse des grandes instances SAP HANA sur Azure (également appelée Infrastructure complète (BareMetal)).

SAP HANA sur Azure (grandes instances) n’est pas différent des autres déploiements IaaS. Il est important d’analyser le système d’exploitation et l’application. Vous souhaiterez savoir comment les applications consomment les ressources suivantes :

- UC
- Mémoire
- Bande passante du réseau
- Espace disque

Analyser vos grandes instances SAP HANA pour voir si les ressources ci-dessus sont suffisantes ou si elles sont épuisées. Les sections suivantes fournissent plus de détails sur chacune de ces ressources.

## <a name="cpu-resource-consumption"></a>Consommation des ressources d’UC

SAP définit un seuil maximal d’utilisation du processeur pour la charge de travail SAP HANA. Rester dans ce seuil garantit que vous disposez de suffisamment de ressources processeur pour travailler sur les données stockées en mémoire. Une consommation élevée du processeur peut se produire lorsque les services SAP HANA exécutent des requêtes en raison d’index manquants ou de problèmes similaires. La surveillance de la consommation d’UC de la grande instance HANA et la consommation de processeur de services HANA spécifiques sont donc critiques.

## <a name="memory-consumption"></a>Consommation de mémoire 

Il est important d’analyser la consommation de mémoire à la fois dans HANA et en dehors de HANA sur la grande instance SAP HANA. Analyser la façon dont les données consomment de la mémoire allouée par HANA afin que vous puissiez rester dans les instructions de dimensionnement de SAP. Analyser la consommation de mémoire sur la grande instance pour s’assurer que le logiciel non HANA ne consomme pas trop de mémoire. Vous ne souhaitez pas que les logiciels non HANA en concurrence avec HANA pour la mémoire.

## <a name="network-bandwidth"></a>Bande passante du réseau 

La bande passante de la passerelle de réseau virtuel Azure est limitée. La plupart des données peuvent être déplacées dans le réseau virtuel Azure. Analyser les données reçues par toutes les machines virtuelles Azure au sein d’un réseau virtuel. De cette façon, vous savez que vous êtes près des limites de la référence SKU de la passerelle Azure que vous avez sélectionnée. Il est également judicieux d’analyser le trafic réseau entrant et sortant sur la grande instance HANA pour suivre les volumes gérés dans le temps.

## <a name="disk-space"></a>Espace disque

la consommation d’espace disque augmente généralement au fil du temps. Les causes courantes sont les suivantes :
- Augmentation du volume de données au fil du temps
- Exécution des sauvegardes de fichier journal
- Stockage de fichiers de trace
- Prise de captures instantanées de stockage 

Par conséquent, il est important d’analyser l’utilisation de l’espace disque et de gérer l’espace disque associé à la grande instance HANA.

## <a name="preloaded-system-diagnostic-tools"></a>Outils de diagnostic système préchargés

Pour les **références SKU de Type II** des grandes instances HANA, le serveur est fourni avec des outils de diagnostic système préchargés. Vous pouvez utiliser ces outils de diagnostic pour vérifier l’intégrité du système.
 
Exécutez la commande suivante pour générer le fichier journal de vérification d’intégrité sur /var/log/health_check.

```
/opt/sgi/health_check/microsoft_tdi.sh
```
Lorsque vous collaborez avec l’équipe du Support Microsoft pour résoudre un problème, vous pouvez être invité à fournir des fichiers journaux à l’aide de ces outils de diagnostic. Vous pouvez zipper le fichier à l’aide de cette commande :

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>Azure Monitor pour SAP Solutions

Vous pouvez utiliser Azure Monitor pour SAP Solutions pour analyser toutes les ressources mentionnées ci-dessus, et bien plus encore. Azure Monitor pour SAP Solutions est natif à Azure. Il vous permet de collecter des données à partir d’une infrastructure et de bases de données Azure dans un emplacement unique et de corréler visuellement les données pour une résolution des problèmes plus rapide. Pour plus d’informations, voir [Surveiller SAP sur Azure](../../../virtual-machines/workloads/sap/monitor-sap-on-azure.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’analyse et la résolution des problèmes dans SAP HANA.

> [!div class="nextstepaction"]
> [Surveillance et dépannage à partir de HANA](hana-monitor-troubleshoot.md)
