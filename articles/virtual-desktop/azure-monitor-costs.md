---
title: Surveiller les estimations de tarification des coûts de Windows Virtual Desktop - Azure
description: Comment estimer les coûts et la tarification pour l’utilisation d’Azure Monitor pour Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 5bd89a734a20c913bacca1f5531aa76d76418c80
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448217"
---
# <a name="estimate-azure-monitor-costs"></a>Estimer les coûts d’Azure Monitor

Azure Monitor Logs est un service qui collecte, indexe et stocke les données générées par votre environnement. Pour cette raison, le modèle de tarification d’Azure Monitor est basé sur la quantité de données qui ont été introduites et traitées (ou « ingérées ») par votre espace de travail Log Analytics en gigaoctets par jour. Le coût d’un espace de travail Log Analytics n’est pas uniquement basé sur le volume de données collectées, mais aussi sur le forfait Azure que vous avez sélectionné et sur la durée de stockage des données générées par votre environnement.

Cet article explique les éléments suivants pour vous aider à comprendre le fonctionnement de la tarification dans Azure Monitor :

- Comment estimer les coûts de stockage et d’ingestion de données avant d’activer cette fonctionnalité
- Comment mesurer et contrôler l’ingestion et le stockage pour réduire les coûts lors de l’utilisation de cette fonctionnalité

>[!NOTE]
> L’ensemble des tailles et prix indiqués dans cet article ne sont que des exemples pour illustrer le fonctionnement de l’estimation. Pour une évaluation plus précise en fonction de votre modèle de tarification Azure Monitor Log Analytics et de votre région Azure, consultez la [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Estimer les coûts de stockage et d’ingestion de données

Nous vous recommandons d’utiliser un ensemble prédéfini de données écrites sous forme de journaux dans votre espace de travail Log Analytics. Dans l’exemple suivant, nous allons examiner les données facturables dans la configuration par défaut

Les jeux de données prédéfinis pour Azure Monitor pour Windows Virtual Desktop sont les suivants :

- Compteurs de performances des hôtes de session
- Journaux des événements Windows à partir des hôtes de session
- Diagnostics Windows Virtual Desktop à partir de l’infrastructure de service

Vos coûts d’ingestion et de stockage de données dépendent de la taille, de l’intégrité et de l’utilisation de votre environnement. Les exemples d’estimation que nous allons utiliser dans cet article pour calculer les plages de coûts auxquelles vous pouvez vous attendre en vous basant sur des machines virtuelles saines qui s’exécutent dans le cadre d’une utilisation légère à intensive sont basés sur nos [recommandations de dimensionnement des machines virtuelles](/remote/remote-desktop-services/virtual-machine-recs), afin de calculer l’éventail de coûts de stockage et d’ingestion de données à attendre.

La machine virtuelle à utilisation légère que nous allons utiliser dans notre exemple comprend les composants suivants :

- 4 processeurs virtuels, 1 disque
- 16 sessions par hôte
- Durée moyenne de session de 2 heures (120 minutes)
- 100 processus par session

La machine virtuelle à utilisation intensive que nous allons utiliser dans notre exemple comprend les composants suivants :

- 6 processeurs virtuels, 1 disque
- 6 sessions par hôte
- Durée moyenne de session de 4 heures (240 minutes)
- 200 processus par session

## <a name="estimating-performance-counter-ingestion"></a>Estimation de l’ingestion des compteurs de performances

Les compteurs de performances montrent comment fonctionnent les ressources système. L’ingestion des données des compteurs de performances dépend de la taille et de l’utilisation de votre environnement. Dans la plupart des cas, les compteurs de performances devraient représenter 80 à 99 % de votre ingestion de données pour Azure Monitor pour Windows Virtual Desktop.

Avant de commencer à estimer, il est important que vous compreniez que chaque compteur de performances envoie des données à une fréquence spécifique. Nous avons défini un taux par minute par défaut (vous pouvez également modifier ce taux dans vos paramètres), mais ce taux sera appliqué à différents facteurs de multiplication en fonction du compteur. Les facteurs suivants affectent le taux :

- Pour le facteur de machine virtuelle, chaque compteur envoie des données par machine virtuelle dans votre environnement au taux d’échantillonnage par minute par défaut quand la machine virtuelle est en cours d’exécution. Vous pouvez estimer le nombre d’enregistrements que ces compteurs envoient par jour en multipliant le taux d’échantillonnage par minute par défaut par le nombre de machines virtuelles de votre environnement, puis en multipliant ce nombre par le temps moyen d’exécution de la machine virtuelle par jour.

   Pour récapituler :

   Fréquence d’échantillonnage par minute par défaut × nombre de cœurs de processeur dans la référence SKU de la machine virtuelle × nombre de machines virtuelle × temps d’exécution moyen de la machine virtuelle par jour = nombre d’enregistrements envoyés par jour

- Pour le facteur par processeur, chaque compteur envoie au taux d’échantillonnage par minute par défaut par processeur virtuel dans chaque machine virtuelle de votre environnement quand la machine virtuelle est en cours d’exécution. Vous pouvez estimer le nombre d’enregistrements que les compteurs envoient par jour en multipliant le taux d’échantillonnage par minute par défaut par le nombre de cœurs de processeur dans la référence SKU de la machine virtuelle, puis en multipliant ce nombre par le nombre de minutes d’exécution de la machine virtuelle et le nombre de machines virtuelles dans votre environnement.

   Pour récapituler :
   
   Taux d’échantillonnage par minute par défaut × nombre de cœurs de processeur dans la référence SKU de machine virtuelle × nombre de minutes d’exécution de la machine virtuelle × nombre de machines virtuelles = nombre d’enregistrements envoyés par jour

- Pour le facteur par disque, chaque compteur envoie des données au taux d’échantillonnage par défaut pour chaque disque de chaque machine virtuelle de votre environnement. Le nombre d’enregistrements que ces compteurs envoient par jour est égal au taux d’échantillonnage par minute par défaut multiplié par le nombre de disques dans la référence SKU de la machine virtuelle, multiplié par 60 minutes par heure, puis multiplié par les heures d’activité moyennes pour une machine virtuelle.

   Pour récapituler :

   Fréquence d’échantillonnage par minute par défaut × nombre de disques dans la référence SKU de la machine virtuelle × 60 minutes par heure × nombre de machines virtuelles × temps d’exécution moyen de la machine virtuelle par jour = nombre d’enregistrements envoyés par jour

- Pour le facteur par session, chaque compteur envoie des données au taux d’échantillonnage par défaut pour chaque session de votre environnement pendant que la session est connectée. Vous pouvez estimer le nombre d’enregistrements que ces compteurs envoient par jour en multipliant le taux d’échantillonnage par minute par le nombre moyen de sessions par jour et la durée de session moyenne.

   Pour récapituler :

   Taux d’échantillonnage par minute × sessions par jour × durée moyenne de la session = nombre d’enregistrements envoyés par jour

- Pour le facteur par processus, chaque compteur envoie des données à la fréquence par défaut de chaque processus de chaque session de votre environnement. Vous pouvez estimer le nombre d’enregistrements que ces compteurs vont envoyer par jour en multipliant le taux d’échantillonnage par minute par le nombre moyen de sessions par jour, puis en le multipliant par la durée moyenne de la session et par le nombre moyen de processus par session.
  
   Pour récapituler :

   Taux d’échantillonnage par minute par défaut × sessions par jour × durée moyenne de la session × nombre moyen de processus par session = nombre d’enregistrements envoyés par jour

Le tableau suivant répertorie les 20 compteurs de performances qu’Azure Monitor pour Windows Virtual Desktop collecte et leurs taux par défaut :

| Nom du compteur | Taux d’échantillonnage par défaut | Facteur de fréquence |
|--------------|---------------------|------------------|
| Espace libre du disque logique (C:) \\ % | 60 secondes  | Par disque             |
| Logical Disk(C:)\\Avg. Longueur de file d'attente de disque   | 30 secondes    | Par disque             |
| Logical Disk(C:)\\Avg. Disk sec/Transfer  | 60 secondes       | Par disque             |
| Logical Disk(C:)\\Current Disk Queue Length  | 30 secondes      | Par disque             |
| Mémoire(\*)\\Mo disponibles | 30 secondes    | Par machine virtuelle  |
| Memory(\*)\\Page Faults/sec | 30 secondes   | Par machine virtuelle  |
| Memory(\*)\\Pages/sec | 30 secondes   | Par machine virtuelle  |
| Memory(\*)\\% Committed Bytes in Use | 30 secondes    | Par machine virtuelle  |
| PhysicalDisk(\*)\\Avg. Longueur de file d'attente de disque | 30 secondes      | Par disque             |
| PhysicalDisk(\*)\\Avg. Disk sec/Read | 30 secondes  | Par disque             |
| PhysicalDisk(\*)\\Avg. Disk sec/Transfer | 30 secondes  | Par disque             |
| PhysicalDisk(\*)\\Avg. Disk sec/Write | 30 secondes | Par disque             |
| Processor Information(_Total)\\% Processor Time | 30 secondes | Par cœur/processeur         |
| Terminal Services(\*)\\Active Sessions          | 60 secondes | Par machine virtuelle  |
| Terminal Services(\*)\\Inactive Sessions        | 60 secondes | Par machine virtuelle  |
| Terminal Services(\*)\\Total Sessions | 60 secondes | Par machine virtuelle  |
| Retard d’entrée utilisateur par processus(\*)\\Délai d’entrée maximal         | 30 secondes | Par processus          |
| Retard d’entrée utilisateur par session(\*)\\Délai d’entrée maximal        | 30 secondes | par session          |
| RemoteFX Network(\*)\\Current TCP RTT | 30 secondes | Par machine virtuelle  |
| RemoteFX Network(\*)\\Current UDP Bandwidth     | 30 secondes | Par machine virtuelle  |

Si nous estimons que chaque taille d’enregistrement est de 200 octets, un exemple de machine virtuelle exécutant une charge de travail légère sur le taux d’échantillonnage par défaut envoie environ 90 mégaoctets de données de compteurs de performances par jour et par machine virtuelle. Dans le même temps, un exemple de machine virtuelle exécutant une charge de travail intensive envoie environ 130 mégaoctets de données de compteurs de performances par jour et par machine virtuelle. Toutefois, la taille des enregistrements et l’utilisation de l’environnement peuvent varier, donc les mégaoctets par jour utilisés par votre déploiement peuvent être différents.

Pour plus d’informations sur les compteurs de performances de délai d’entrée, consultez [Compteurs de performances de délai d’entrée utilisateur](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Estimation de l’ingestion du journal des événements Windows

Les journaux des événements Windows sont des sources de données collectées par les agents Log Analytics sur des machines virtuelles Windows. Vous pouvez collecter des événements à partir de journaux standard comme Système et Application, ainsi que des journaux personnalisés créés par les applications que vous souhaitez surveiller.

Il s’agit des événements Windows par défaut pour Azure Monitor pour Windows Virtual Desktop :

- Application
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- Système
- Microsoft-FSLogix-Apps/Operational
- Microsoft-FSLogix-Apps/Admin

Les événements Windows sont envoyés chaque fois que les conditions de l’événement sont satisfaites dans l’environnement. Les ordinateurs dans des états sains enverront moins d’événements que les ordinateurs dans des états non sains. Étant donné que le nombre d’événements est imprévisible, nous utilisons une plage de 1 000 à 10 000 événements par machine virtuelle par jour, en prenant des exemples d’environnements sains pour cette estimation. Par exemple, si nous estimons que chaque taille d’enregistrement d’événement dans cet exemple est de 1 500 octets, cela nous donne environ 2 à 15 mégaoctets de données d’événement par jour pour l’environnement spécifié.

Pour plus d’informations sur les événements Windows, consultez [Propriétés des enregistrements des événements Windows](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Estimation de l’ingestion des diagnostics

Le service de diagnostic crée des journaux d’activité pour les actions des utilisateurs et des administrateurs.

Voici les noms des journaux d’activité suivis par le compteur de diagnostic :

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Le service envoie des informations de diagnostic chaque fois que l’environnement répond aux conditions requises pour créer un enregistrement. Étant donné que le nombre d’enregistrements de diagnostic est imprévisible, nous utilisons une plage de 500 à 1000 événements par machine virtuelle par jour, sur la base d’exemples d’environnements sains pour cette estimation.

Par exemple, si nous estimons que chaque taille d’enregistrement de diagnostic dans cet exemple est de 200 octets, le nombre total de données ingérées est inférieur à 1 Mo par machine virtuelle par jour.

Pour en savoir plus sur les catégories du journal d’activité, consultez la section [Diagnostics Windows Virtual Desktop](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Estimation des coûts totaux

Enfin, nous estimons le coût total. Dans cet exemple, supposons que nous fournissons les résultats suivants en fonction des valeurs d’exemple des sections précédentes :

| Source de données        | Estimation de la taille par jour (en mégaoctets)   |
|-------------------------------------|------------------------------------------|
| Compteurs de performance   | 90-130 |
| Événements    | 2-15 |
| Diagnostics Windows Virtual Desktop | \< 1 |

Dans cet exemple, le nombre total de données ingérées pour Azure Monitor pour Windows Virtual Desktop est compris entre 92 et 145 mégaoctets par machine virtuelle par jour. En d’autres termes, tous les 31 jours, chaque machine virtuelle ingère environ 3 à 5 gigaoctets de données.

Avec le modèle de paiement à l’utilisation par défaut pour la [tarification de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/), vous pouvez estimer le coût mensuel de la collecte et du stockage de données d’Azure Monitor. En fonction de l’ingestion de vos données, vous pouvez également considérer le modèle de réservation de capacité pour la tarification de Log Analytics.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Gérer l’ingestion de vos données afin de réduire les coûts

Cette section explique comment mesurer et gérer l’ingestion des données afin de réduire les coûts.

Pour en savoir plus sur la gestion des droits et des autorisations d’accès aux classeurs, consultez [Contrôle d’accès](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>La suppression de points de données aura un impact sur leurs visuels correspondants dans Azure Monitor pour Windows Virtual Desktop.

### <a name="log-analytics-settings"></a>Paramètres de Log Analytics

Voici quelques suggestions pour optimiser vos paramètres Log Analytics pour gérer l’ingestion des données :

- Utilisez un espace de travail Log Analytics désigné pour vos ressources Windows Virtual Desktop pour vous assurer que Log Analytics collecte uniquement les compteurs de performance et les événements des machines virtuelles dans votre déploiement Windows Virtual Desktop.
- Ajustez vos paramètres de stockage Log Analytics pour gérer les coûts. Vous pouvez réduire la période de rétention, évaluer si un niveau de tarification de stockage fixe est plus rentable ou définir des limites sur la quantité de données que vous pouvez ingérer pour limiter l’impact d’un déploiement défectueux. Pour plus d’informations, consultez [Gérer l’utilisation et les coûts des journaux d’Azure Monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Supprimer les données excédentaires

Notre configuration par défaut est le seul jeu de données recommandé pour Azure Monitor pour Windows Virtual Desktop. Vous avez toujours la possibilité d’ajouter des points de données supplémentaires et de les afficher dans les diagnostics de l’hôte : hébergez un hôte ou créez des graphiques personnalisés pour eux. Toutefois, les données ajoutées augmentent votre coût pour Log Analytics. Vous pouvez les supprimer pour faire des économies.

### <a name="measure-and-manage-your-performance-counter-data"></a>Mesurer et gérer vos données de compteur de performances

Vos coûts de surveillance réels dépendent de la taille, de l’utilisation et de l’intégrité de votre environnement. Pour comprendre comment mesurer l’ingestion des données dans votre espace de travail Log Analytics, consultez [Comprendre le volume des données de journal ingérées](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume).

Les compteurs de performances utilisés par les hôtes de session seront probablement votre plus grande source de données ingérées pour Azure Monitor pour Windows Virtual Desktop. Le modèle de requête personnalisé suivant pour un espace de travail Log Analytics peut suivre la fréquence et le nombre de mégaoctets ingérés par compteur de performances au cours du dernier jour :

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Veillez à remplacer les valeurs d’espace réservé du modèle par les valeurs utilisées par votre environnement, sinon la requête ne fonctionnera pas.

Cette requête affiche tous les compteurs de performances que vous avez activés sur l’environnement, pas seulement ceux par défaut pour Azure Monitor pour Windows Virtual Desktop. Ces informations peuvent vous aider à comprendre les domaines à cibler pour réduire les coûts, par exemple réduire la fréquence d’un compteur ou le supprimer complètement.

Vous pouvez également réduire les coûts en supprimant des compteurs de performances. Pour savoir comment supprimer des compteurs de performances ou modifier des compteurs existants pour réduire leur fréquence, consultez la section [Configurer les compteurs de performances](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Gérer les journaux d’événements Windows

Les événements Windows sont peu susceptibles de provoquer un pic d’ingestion des données lorsque tous les hôtes sont sains. Un hôte défectueux peut augmenter le nombre d’événements envoyés au journal, mais les informations peuvent être essentielles pour la résolution des problèmes de l’hôte. Nous vous recommandons de les conserver. Pour en savoir plus sur la gestion des journaux d’événements Windows, consultez la section [Configurer les journaux d’événements Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Gérer les diagnostics

Les diagnostics de Windows Virtual Desktop devraient représenter moins de 1 % de vos coûts de stockage de données. Nous vous déconseillons donc de les supprimer. Pour gérer les diagnostics de Windows Virtual Desktop, [utilisez Log Analytics pour la fonction de diagnostic](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les Azure Monitor pour Windows Virtual Desktop, consultez les articles suivants :

- [Monitoring d’un déploiement avec Azure Monitor pour Windows Virtual Desktop](azure-monitor.md).
- Utilisez le [Glossaire](azure-monitor-glossary.md) pour en savoir plus sur les termes et les concepts.
- Si vous rencontrez un problème, consultez notre [guide de résolution des problèmes](troubleshoot-azure-monitor.md) pour obtenir de l’aide.
- Consultez [Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor](../azure-monitor/usage-estimated-costs.md) pour en savoir plus sur la gestion des coûts de surveillance.
