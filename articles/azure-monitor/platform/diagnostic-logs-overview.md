---
title: Présentation des journaux de diagnostic Azure
description: En savoir plus sur les journaux de Diagnostic Azure dans Azure Monitor et comment vous pouvez les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244875"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Présentation des journaux de diagnostic Azure

**Journaux de diagnostic** fournissent des informations riches et fréquentes sur l’opération d’une ressource Azure. Azure Monitor propose deux types de journaux de diagnostic :

* **Journaux de locataire** : ces journaux proviennent des services au niveau du locataire qui existent en dehors d’un abonnement Azure, tels que les journaux Azure Active Directory.
* **Journaux de ressources** : ces journaux proviennent des services Azure qui déploient des ressources au sein d’un abonnement Azure, tels que les groupes de sécurité réseau ou les comptes de stockage.

    ![Comparaison entre les journaux de diagnostic des ressources et les autres types de journaux d’activité](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Le contenu de ces journaux d’activité varie en fonction du service Azure et du type de ressource. Les compteurs de règles du groupe de sécurité réseau et les audits de coffres de clés sont deux exemples de types de journaux de diagnostic.

Ces journaux diffèrent le [journal d’activité](activity-logs-overview.md). Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement à l’aide du Gestionnaire de ressources, par exemple, la création d’une machine virtuelle ou la suppression d’une application logique. Le journal d’activité est un journal de niveau d’abonnement. Les journaux de diagnostic des ressources, quant à eux, donnent un aperçu des opérations qui ont été effectuées au sein d’une ressource (par exemple, l’obtention d’un secret à partir d’un coffre de clés).

Ces journaux d’activité diffèrent également des journaux de diagnostic de système d’exploitation invité. Les journaux de diagnostic de système d’exploitation invité sont collectés par un agent exécuté sur une machine virtuelle ou un autre type de ressource pris en charge. Les journaux de diagnostic des ressources ne nécessitent aucun agent et capturent les données relatives à la ressource à partir de la plateforme Azure, alors que les journaux de diagnostic du système d’exploitation invité capturent les données provenant du système d’exploitation et des applications exécutées sur la machine virtuelle.

Toutes les services ne prennent pas en charge les journaux de diagnostic décrits ici. [Cet article contient une section répertoriant les services prenant en charge les journaux de diagnostic](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de diagnostic
Voici ce que vous pouvez faire avec les journaux de diagnostic :

![Positionnement logique des journaux de diagnostic](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Enregistrez-les dans un [**compte de stockage**](../../azure-monitor/platform/archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic des ressources**.
* [Stream leur **Event Hubs** ](diagnostic-logs-stream-event-hubs.md) pour l’ingestion par un service tiers ou d’une solution d’analytique personnalisées, telles que Power BI.
* Analysez-les avec [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), les données sont écrites immédiatement dans Azure Monitor, et il est inutile d’écrire les données dans le stockage avant.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hubs qui n’est pas dans le même abonnement que celui générant des journaux d’activité. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

> [!NOTE]
>  Il n’est pas possible actuellement d’archiver les journaux de flux du réseau sur un compte de stockage situé derrière un réseau virtuel sécurisé.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic

Les journaux de diagnostic des ressources sont configurés à l’aide des paramètres de diagnostic des ressources. Les journaux de diagnostic des locataires sont configurés à l’aide des paramètres de diagnostic des locataires. **Paramètres de diagnostic** pour un contrôle de service :

* Où les journaux de diagnostic et les métriques sont envoyés (compte de stockage, Event Hubs et/ou Azure Monitor).
* Les catégories de journal envoyées et les données de mesure également envoyées.
* La durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
    - Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. Sinon, la valeur peut être n’importe quel nombre de jours compris entre 1 et 365.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou Log Analytics sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures.

Ces paramètres sont configurés dans les paramètres de diagnostic dans le portail, avec Azure PowerShell et les commandes CLI, ou à l’aide du [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> L’envoi de métriques à plusieurs dimensions via les paramètres de diagnostic n’est actuellement pas pris en charge. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Services, catégories et schémas pris en charge pour les journaux de diagnostic

[Consultez cet article](../../azure-monitor/platform/diagnostic-logs-schema.md) pour obtenir la liste complète des services pris en charge et des catégories de journaux et des schémas utilisés par ces services.

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analyser les journaux du stockage Azure avec Azure Monitor](collect-azure-metrics-logs.md)
