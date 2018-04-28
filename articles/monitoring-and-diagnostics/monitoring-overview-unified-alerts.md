---
title: Explorer la nouvelle expérience Alertes dans Azure Monitor | Microsoft Docs
description: Découvrez comment la nouvelle expérience d’alertes simple et évolutive d’Azure facilite la création, l’affichage et la gestion des alertes
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nouvelle expérience Alertes dans Azure Monitor

## <a name="overview"></a>Vue d'ensemble

> [!NOTE]
> Cet article décrit les alertes plus récentes. Les alertes classiques Azure Monitor plus anciennes sont décrites dans [Vue d’ensemble des alertes classiques](monitoring-overview-alerts.md). 
>
>

Azure Monitor offre une nouvelle expérience pour les alertes. L’ancienne expérience d’alertes se trouve désormais dans l’onglet Alertes (classiques). La nouvelle expérience Alertes présente les avantages suivants par rapport à l’expérience Alertes (classiques) :

-   **Meilleur système de notification** : toutes les alertes plus récentes utilisent des [groupes d’actions]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), qui sont des groupes nommés de notifications et d’actions qui peuvent être réutilisées dans plusieurs alertes.  Les alertes métriques classiques et les alertes Log Analytics plus anciennes n’utilisent pas de groupes d’actions. 
- **Une expérience de création unifiée** : la création de toutes les alertes pour les métriques, les journaux et le journal d’activité pour Azure Monitor, Log Analytics et Application Insights se fait à partir d’un même emplacement. 
- **Affichage des alertes Log Analytics déclenchées dans le portail Azure** : à présent, vous pouvez également afficher les alertes Log Analytics déclenchées dans votre abonnement. Auparavant, elles se trouvaient dans un portail distinct. 
- **Séparation des alertes déclenchées et des règles d’alerte** : les règles d’alerte (c’est-à-dire la définition des conditions qui déclenchent des alertes) et les alertes déclenchées (c’est-à-dire les instances d’activation de règles d’alerte) sont différenciées de manière à offrir des vues distinctes pour les opérations et la configuration.
- **Un flux de travail amélioré** : la nouvelle expérience de création Alertes guide l’utilisateur tout au long du processus de configuration d’une règle d’alerte, ce qui facilite la découverte des éléments appropriés aux alertes.
 
Les alertes métriques plus récentes présentent notamment les améliorations suivantes :
-   **Latence améliorée** : les alertes métriques plus récentes peuvent être exécutées toutes les minutes. Les alertes métriques les plus anciennes s’exécutent toujours à une fréquence de 5 minutes. Les alertes de journal ont toujours un délai de plus d’une minute en raison du temps nécessaire à la réception des journaux. 
-   **Prise en charge de plusieurs métriques multidimensionnelles** : vous pouvez avertir sur des métriques dimensionnelles ce qui vous permet d’analyser un segment intéressant de la métrique.
-   **Contrôle renforcé des conditions de métrique** : vous pouvez définir des règles d’alerte plus riches. Les alertes plus récentes prennent en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
-   **Surveillance combinée de plusieurs métriques** : vous pouvez surveiller plusieurs métriques (actuellement jusqu’à deux) avec une seule règle. Une alerte est déclenchée si les deux métriques violent leurs seuils respectifs durant la période spécifiée.
-   **Métriques à partir des journaux** (version préliminaire publique limitée) : certaines données de journal vers Log Analytics peuvent maintenant être extraites et converties en métriques d’Azure Monitor et faire l’objet d’une alerte comme les autres métriques. 



Les sections suivantes décrivent plus en détail le fonctionnement de la nouvelle expérience.

## <a name="alert-rules-terminology"></a>Terminologie des règles d’alerte
La nouvelle expérience Alertes utilise les concepts suivants pour distinguer les objets de règle d’alerte et d’alerte déclenchée, tout en unifiant l’expérience de création pour les différents types d’alerte.

- **Ressource cible** : une cible peut être n’importe quelle ressource Azure. Une ressource cible définit l’étendue et les signaux disponibles pour les fonctions de génération d’alertes. Exemples de cibles : une machine virtuelle, un compte de stockage, un groupe de machines virtuelles identiques, un espace de travail Log Analytics ou une ressource Application Insights.

- **Critères** : les critères sont une combinaison du signal et de la logique appliqués à une ressource cible. Exemples : pourcentage d’UC > 70 %, temps de réponse du serveur > 4 ms, nombre de résultats d’une requête de journal > 100, etc. 

- **Signal** : des signaux sont émis par la ressource cible et peuvent être de plusieurs types. Les types de signaux pris en charge incluent **Métrique**, **Journal d’activité**, **Application Insights** et **Journal**.

- **Logique** : la logique définie par l’utilisateur sert à vérifier si le signal respecte la plage ou les valeurs attendues.  
 
- **Action** : action spécifique mise en œuvre quand l’alerte est déclenchée. Exemple : envoi d’un courrier électronique à une adresse e-mail ou appel d’une URL de webhook. Plusieurs actions peuvent se produire lors du déclenchement d’une alerte. Ces alertes prennent en charge les groupes d’actions.  
 
- **Règle d’alerte** : condition qui provoque le déclenchement de l’alerte. La règle d’alerte comprend la cible et les critères d’alerte. La règle d’alerte peut être activée ou désactivée.
 
    > [!NOTE]
    > Cela diffère de l’expérience Alertes (classiques), où l’alerte représente à la fois la règle et l’alerte déclenchée et peut par conséquent présenter les états Avertissement, Active ou Désactivée.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Emplacement unique pour afficher et gérer les alertes
L’objectif de l’expérience Alertes est d’offrir un emplacement unique pour l’affichage et la gestion de toutes vos alertes Azure. Les sous-sections suivantes décrivent les fonctions de chaque écran de la nouvelle expérience.

### <a name="alerts-overview-page"></a>Page de présentation des alertes
La page de présentation **Analyse - Alertes** affiche un résumé agrégé de toutes les alertes déclenchées et des règles d’alerte configurées/activées. Elle affiche également une liste de toutes les alertes déclenchées. Modifier les paramètres d’abonnement ou de filtre met à jour les agrégats et la liste des alertes déclenchées.

> [!NOTE]
> Les alertes déclenchées affichées dans Alertes sont limitées aux alertes de journaux et de métrique prises en charge ; la vue d’ensemble d’Azure Monitor affiche le nombre d’alertes déclenchées, y compris celles figurant dans les anciennes alertes Azure.

 ![alertes-présentation](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gestion des règles d’alerte
**Analyse - Alertes > Règles** est une page unique permettant de gérer toutes les règles d’alerte de vos abonnements Azure. Elle répertorie toutes les règles d’alerte (activées ou désactivées) et peut être triée en fonction des ressources cibles, des groupes de ressources, du nom de règle ou de l’état. Les règles d’alerte peuvent également être activées/désactivées ou modifiées à partir de cette page.  

 ![alertes-règles](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Une expérience de création d’alerte pour toutes les sources d’analyse
Dans la nouvelle expérience Alertes, les alertes peuvent être créées de manière homogène, quel que soit le service de surveillance ou le type de signal. Toutes les alertes déclenchées et les détails connexes sont disponibles sur une seule page.  
 
La création d’une alerte est une tâche en trois étapes, l’utilisateur sélectionne tout d’abord une cible pour l’alerte, il sélectionne ensuite le signal approprié, puis spécifie la logique à appliquer sur le signal dans le cadre de la règle d’alerte. Ce processus de création simplifié ne nécessite plus que l’utilisateur connaisse la source de surveillance ou les signaux pris en charge avant de sélectionner une ressource Azure. L’expérience de création commune filtre automatiquement la liste des signaux disponibles en fonction de la ressource cible sélectionnée et guide la création de la logique d’alerte

Vous trouverez plus d’informations sur la création des types d’alerte suivants [ici](monitor-alerts-unified-usage.md).
- Alertes de métrique
- Alertes de journal (Log Analytics)
- Alertes de journal (Journaux d’activité)
- Alertes de journal (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Alertes prises en charge dans la nouvelle expérience
Les alertes sont disponibles dans plusieurs services de surveillance Azure. Pour plus d’informations sur l’utilisation de ces services, [consultez cet article](./monitoring-overview.md). Voici la liste des types d’alertes disponibles sur Azure et actuellement pris en charge par la nouvelle expérience Alertes. 


| **Type de signal** | **Source de l’analyse** | **Description** | 
|-------------|----------------|-------------|
| Métrique | Azure Monitor | Également appelées [alertes de métrique en temps quasi réel](monitoring-near-real-time-metric-alerts.md), elles prennent en charge l’évaluation des conditions de métrique jusqu’à une fréquence de 1 minute et autorisent les règles de métriques multiples et multidimensionnelles. Une liste de types de ressources pris en charge est disponible [ici](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Métrique | Azure Monitor | [Les alertes de métriques classiques plus anciennes](monitoring-overview-alerts.md) ne sont pas prises en charge dans la nouvelle expérience Alertes. Vous les trouverez dans l’onglet Alertes (classiques) dans le portail Azure. Les alertes classiques prennent en charge certains types de métriques qui n’ont pas encore été déplacés vers les alertes plus récentes. Pour obtenir la liste complète, consultez [Métriques prises en charge](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics).
| Journaux  | Log Analytics | Reçoivent des notifications ou exécutent des actions automatisées lorsqu’une requête de recherche dans les journaux concernant les métriques et/ou des données d’événement répond à certains critères. Les alertes Log Analytics plus anciennes sont toujours disponibles, mais sont [copiées dans la nouvelle expérience](monitoring-alerts-extend.md). En outre, une [version préliminaire des *Journaux Log Analytics comme métriques*](monitoring-alerts-extend-tool.md) est disponible. La version préliminaire vous permet de prendre certains types de journaux et de les convertir en métriques. Vous pourrez alors mettre des alertes dessus à l’aide de la nouvelle expérience d’alerte. La version préliminaire est utile si vous avez des journaux non-Azure que vous souhaitez utiliser avec des métriques Azure Monitor en mode natif. |
| Journal d’activité | Journaux d’activité (général) | Contient les enregistrements de toutes les actions Créer, Mettre à jour et Supprimer exécutées via la cible sélectionnée (ressource/groupe de ressources/abonnement). |
| Journal d’activité  | Service Health | Non pris en charge dans la nouvelle expérience Alertes. Consultez [Créer des alertes de journal d’activité sur les notifications de service](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Journaux  | Application Insights | Comprend des journaux contenant les détails des performances de votre application. Vous pouvez utiliser une requête Analytics pour définir les conditions applicables aux actions à accomplir sur la base des données de l’application. |
| Métrique | Application Insights | Non pris en charge dans la nouvelle expérience Alertes. Consultez [Alertes de métriques](../application-insights/app-insights-alerts.md) |
| Tests de disponibilité web | Application Insights | Alerte non prise en charge dans l’expérience Alertes.  Consultez [Alertes de test web](../application-insights/app-insights-monitor-web-app-availability.md). Disponible pour tout site web instrumenté pour envoyer des données à Application Insights. Réception d’une notification lorsque la réactivité ou la disponibilité d’un site web est inférieure aux attentes. |




## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment utiliser la nouvelle expérience Alertes pour créer, afficher et gérer des alertes](monitor-alerts-unified-usage.md)
- [Approfondissez vos connaissances sur les alertes de journal dans l’expérience Alertes](monitor-alerts-unified-log.md)
- [Approfondissez vos connaissances sur les alertes de métrique dans l’expérience Alertes](monitoring-near-real-time-metric-alerts.md)
- [Approfondissez vos connaissances sur les alertes de journal d’activité dans l’expérience Alertes](monitoring-activity-log-alerts-new-experience.md)
