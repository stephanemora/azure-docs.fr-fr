---
title: Comment créer des graphiques de performances avec Azure Monitor pour les machines virtuelles | Microsoft Docs
description: Performances est une fonctionnalité d’Azure Monitor pour les machines virtuelles qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Cet article fournit des informations sur son utilisation dans divers scénarios.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2018
ms.author: magoedte
ms.openlocfilehash: 63549768f616e60e92c853047525c18cefdaddb4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386267"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Comment créer des graphiques de performances avec Azure Monitor pour les machines virtuelles
Azure Monitor pour les machines virtuelles comprend un ensemble de graphiques de performances qui ciblent divers indicateurs de performance clés (KPI) pour vous aider à déterminer l’intégrité du fonctionnement d’une machine virtuelle. Les graphiques illustrent l’utilisation des ressources sur une période de temps pour que vous puissiez identifier les goulots d’étranglement ou les anomalies. Vous pouvez également basculer sur une perspective répertoriant toutes les machines virtuelles pour afficher l’utilisation des ressources en fonction de la métrique sélectionnée. Bien qu’il existe de nombreux éléments à prendre en compte pour le traitement des performances, Azure Monitor pour les machines virtuelles se concentre sur le système d’exploitation, représenté par le processeur, la mémoire, les cartes réseau et les disques. La fonctionnalité Performances vient compléter celle de supervision de l’intégrité et permet d’exposer les problèmes indiquant une panne éventuelle d’un composant système, de prendre en charge les réglages et l’optimisation pour améliorer l’efficacité ou de prendre en charge la planification de la capacité.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspective avec plusieurs machines virtuelles d’Azure Monitor
Dans Azure Monitor, la fonctionnalité Performances fournit un affichage avec plusieurs machines virtuelles de toutes les machines virtuelles supervisées déployées sur l’ensemble des groupes de ressources de vos abonnements ou de votre environnement.  Pour y accéder à partir d’Azure Monitor, procédez comme suit. 

1. Dans le Portail Azure, sélectionnez **Surveiller**. 
2. Choisissez **Machines virtuelles (préversion)** dans la section **Solutions**.
3. Sélectionnez l’onglet **Performances**.

![Affichage Top N List (Liste N premiers) - Performances - Insights de machine virtuelle](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

Dans l’onglet **Graphiques N premiers**, si vous avez plusieurs espaces de travail Log Analytics, choisissez celui qui est activé avec la solution à partir du sélecteur **Espace de travail** en haut de la page. Le sélecteur **Groupe** retourne les abonnements, les groupes de ressources, les [groupes d’ordinateurs](../log-analytics/log-analytics-computer-groups.md) et les groupes de machines virtuelles identiques associés à l’espace de travail sélectionné, que vous pouvez utiliser pour filtrer davantage les résultats présentés dans les graphiques sur cette page et dans les autres pages. Votre sélection s’applique seulement à la fonctionnalité Performances, et n’est pas reportée sur Intégrité ou Carte.  

Par défaut, les graphiques montrent les dernières 24 heures. À l’aide du sélecteur **TimeRange**, vous pouvez interroger les intervalles de temps précédents (jusqu’à 30 jours) pour afficher l’état des performances passées.   

Les cinq graphiques d’utilisation de capacité affichés sur la page sont :

* CPU Utilization % (% d’utilisation de l’UC) : affiche les 5 premières machines avec la moyenne d’utilisation du processeur la plus élevée 
* Mémoire disponible : affiche les 5 premières machines avec la quantité moyenne de mémoire disponible la plus basse 
* Logical Disk Space Used % (% d’espace disque logique utilisé) : affiche les 5 premières machines avec le pourcentage moyen d’espace disque utilisé le plus élevé pour tous les volumes de disque 
* Bytes Sent Rate (Taux d’octets envoyés) : affiche les 5 premières machines avec la moyenne d’octets envoyés la plus élevée 
* Bytes Received Rate (Taux d’octets reçus) : affiche les 5 premières machines avec la moyenne d’octets reçus la plus élevée 

Cliquez dans le coin supérieur droit de l’un des cinq graphiques pour ouvrir l’affichage **Top N List** (Liste N premiers).  Ici, vous pouvez voir l’utilisation des ressources pour cette métrique de performances par machine virtuelle dans un affichage de liste, ainsi que la machine virtuelle avec les tendances d’utilisation les plus élevées.  

![Affichage Top N List (Liste N premiers) pour une métrique de performances sélectionnée](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Lorsque vous cliquez sur la machine virtuelle, le volet **Propriétés** est développé à droite pour afficher les propriétés de chacun des éléments sélectionnés, telles que les informations système générées par le système d’exploitation, les propriétés de la machine virtuelle Azure, etc. Cliquer sur l’une des options sous la section **Liens rapides** permet de vous rediriger vers cette fonctionnalité, directement depuis la machine virtuelle sélectionnée.  

![Volet Propriétés de la machine virtuelle](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

Basculez vers l’onglet **Aggregated Charts** (Graphiques agrégés) pour afficher les métriques de performances filtrées par moyenne ou centiles.  

![Affichage agrégé - Performances - Insights de machine virtuelle](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

Les graphiques d’utilisation de la capacité suivants sont fournis :

* CPU Utilization % (% d’utilisation de l’UC) : affiche par défaut la moyenne et le top 95e centile 
* Mémoire disponible : affiche par défaut la moyenne, le top 5e centile et le top 10e centile 
* Logical Disk Space Used % (% d’espace disque logique utilisé) : affiche par défaut la moyenne et le 95e centile 
* Bytes Sent Rate (Taux d’octets envoyés) : affiche par défaut la moyenne d’octets envoyés 
* Bytes Received Rate (Taux d’octets reçus) : affiche par défaut la moyenne d’octets reçus

Vous pouvez également modifier la granularité des graphiques dans l’intervalle de temps en sélectionnant les options du sélecteur de centiles **Moy**, **Min**, **Max**, **50th** (50e), **90e** et **95th** (95e).   

Pour voir l’utilisation des ressources par machine virtuelle dans un affichage de liste et déterminer la machine avec les tendances d’utilisation les plus élevées, sélectionnez l’onglet **Top N List** (Liste N premiers).  La page **Top N List** (Liste N premiers) affiche les 20 premières machines triées par utilisation la plus élevée par 95e centile pour la métrique *CPU Utilization %* (% d’utilisation de l’UC).  Vous pouvez voir davantage de machines en sélectionnant **Charger plus** ; les résultats affichent maintenant les 500 premières machines. 

>[!NOTE]
>La liste ne peut pas afficher plus de 500 machines à la fois.  
>

![Exemple de page Top N List (Liste N premiers)](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Pour filtrer les résultats sur une machine virtuelle spécifique dans la liste, entrez le nom de l’ordinateur dans la zone de texte **Search by name** (Rechercher par nom).  

Si vous préférez afficher l’utilisation à partir d’une métrique de performances différente, dans la liste déroulante **Métrique**, sélectionnez **Mémoire disponible**, **Logical Disk Space Used %** (% d’espace disque logique utilisé), **Network Received Byte/s** (Octets réseau reçus/s) ou **Network Sent Byte/s** (Octets réseau envoyés/s). La liste se met à jour pour afficher l’utilisation en fonction de la métrique sélectionnée.  

Sélectionner une machine virtuelle dans la liste permet d’ouvrir le panneau **Propriétés** à droite de la page, à partir duquel vous pouvez sélectionner **Performance detail** (Détails des performances).  La page **Virtual Machine Detail** (Détails de la machine virtuelle) s’ouvre et affiche uniquement les informations relatives à cette machine virtuelle. Cela revient à accéder à la section de performances des insights de machine virtuelle directement à partir de la machine virtuelle Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Afficher les performances directement à partir d’une machine virtuelle Azure
Pour un accès direct à partir d’une machine virtuelle, procédez comme suit.

1. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
2. Choisissez une machine virtuelle dans la liste, puis, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.  
3. Sélectionnez l’onglet **Performances**. 

En plus d’inclure des graphiques d’utilisation de performances, cette page fournit un tableau affichant pour chaque disque logique détecté, sa capacité, son utilisation et la moyenne totale pour chaque mesure.  

Les graphiques d’utilisation de la capacité suivants sont fournis :

* CPU Utilization % (% d’utilisation de l’UC) : affiche par défaut la moyenne et le top 95e centile 
* Mémoire disponible : affiche par défaut la moyenne, le top 5e centile et le top 10e centile 
* Logical Disk Space Used % (% d’espace disque logique utilisé) : affiche par défaut la moyenne et le 95e centile 
* Logical Disk IOPS (IOPS de disque logique) : affiche par défaut la moyenne et le 95e centile
* Logical Disk MB/s (Mo/s de disque logique) : affiche par défaut la moyenne et le 95e centile
* Max Logical Disk Used % (% de disque logique max. utilisé) : affiche par défaut la moyenne et le 95e centile
* Bytes Sent Rate (Taux d’octets envoyés) : affiche par défaut la moyenne d’octets envoyés 
* Bytes Received Rate (Taux d’octets reçus) : affiche par défaut la moyenne d’octets reçus

![Performances - Insights de machine virtuelle directement depuis l’affichage de la machine virtuelle](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Alertes et gestion des alertes 
Les métriques de performances activées dans le cadre d’Azure Monitor pour les machines virtuelles n’incluent pas les règles d’alerte préconfigurées. Bien qu’il existe des alertes d’intégrité correspondant à des problèmes de performances détectés sur votre machine virtuelle Azure, comme une utilisation élevée de l’UC, peu de mémoire encore disponible, espace disque faible, etc., ces alertes d’intégrité sont appliquées seulement à toutes les machines virtuelles connectées au même espace de travail Log Analytics intégré à Azure Monitor pour les machines virtuelles. Si vous avez besoin de spécifier vos propres critères ou votre propre logique, vous pouvez créer des règles d’alerte personnalisées en suivant [Créer, afficher et gérer des alertes avec Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser la fonctionnalité d’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure avec Azure Monitor pour machines virtuelles](monitoring-vminsights-health.md), ou pour afficher les dépendances des applications détectées, consultez [Afficher la carte Azure Monitor pour machines virtuelles](monitoring-vminsights-maps.md). 