---
title: Vue d’ensemble des alertes et de la surveillance des notifications dans Azure
description: Vue d’ensemble des alertes dans Azure. Alertes, alertes classiques, interface d’alertes.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: cf01ddd53741a77df61e3dc1946eb836640034d0
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345144"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Vue d’ensemble des alertes dans Microsoft Azure 

Cet article décrit les alertes Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser.  


## <a name="what-are-alerts-in-microsoft-azure"></a>Que sont les alertes dans Microsoft Azure ?
Les alertes vous avertissent de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes avant que les utilisateurs de votre système ne les remarquent. 

Cet article décrit l’expérience d’alerte unifiée dans Azure Monitor, qui inclut maintenant Log Analytics et Application Insights. La [précédente expérience d’alerte](alerts-overview.md) et les anciens types d’alertes sont appelés **alertes classiques**. Vous pouvez afficher cette expérience antérieure et les type d’alerte plus anciens en cliquant sur **Afficher les alertes classiques** en haut de la page d’alerte. 

## <a name="overview"></a>Vue d’ensemble

Le diagramme ci-dessous représente le flux des alertes. 

![Flux d’alertes](media/alerts-overview/Azure-Monitor-Alerts.svg)

Les règles d’alerte sont séparées des alertes et de l’action effectuée lorsqu’une alerte se déclenche. 

**Règle d’alerte** : la règle d’alerte capture la cible et les critères d’alerte. La règle d’alerte peut être activée ou désactivée. Les alertes se déclenchent uniquement lorsqu’elles sont activées. 

Les principaux attributs des règles d’alerte sont les suivants :

La **ressource cible** définit l’étendue et les signaux disponibles pour les fonctions de génération d’alertes. Une cible peut être n’importe quelle ressource Azure. Exemples de cibles : une machine virtuelle, un compte de stockage, un groupe de machines virtuelles identiques, un espace de travail Log Analytics ou une ressource Application Insights. Pour certaines ressources (par exemple, les machines virtuelles), vous pouvez spécifier plusieurs ressources comme cible de la règle d’alerte.

**Signal** : des signaux sont émis par la ressource cible. Ils peuvent être de plusieurs types (Métrique, Journal d’activité, Application Insights et Journal).

**Critères** : les critères sont une combinaison du signal et de la logique appliqués à une ressource cible. Exemples : 
   - Pourcentage d’UC > 70 %
   - Temps de réponse du serveur > 4 ms 
   - Nombre de résultats d’une requête de journal > 100

**Nom de l’alerte** : nom spécifique pour la règle d’alerte configurée par l’utilisateur

**Description de l’alerte** : description de la règle d’alerte configurée par l’utilisateur

**Gravité** : gravité de l’alerte une fois que les critères spécifiés dans la règle d’alerte sont remplis. La gravité peut être comprise entre 0 et 4.

**Action** : action spécifique mise en œuvre quand l’alerte est déclenchée. Pour plus d’informations, consultez [Groupes d’actions](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Sur quoi portent les alertes ?

Vous pouvez définir des alertes sur des métriques et des journaux, comme décrit dans les [sources de données de surveillance](../../monitoring-and-diagnostics/monitoring-data-sources.md). Ces vérifications portent notamment sur les revendications suivantes :
- Valeurs de métrique
- Requêtes de recherche de journal
- Événements du journal d'activité
- Contrôle d’intégrité de la plateforme Azure sous-jacente
- Tests de disponibilité des sites web

## <a name="manage-alerts"></a>Gérer les alertes
Vous pouvez définir l’état d’une alerte afin d’indiquer où elle se situe dans le processus de résolution. Lorsque les critères spécifiés dans la règle d’alerte sont remplis, une alerte est créée ou déclenchée, et elle a le statut *Nouveau*. Vous pouvez changer son état après l’avoir reconnue ou fermée. Les changements d’état sont stockés dans l’historique de l’alerte.

Les états d’alerte suivants sont pris en charge.

| État | Description |
|:---|:---|
| Nouveau | Le problème vient d’être détecté et n’a pas encore été examiné. |
| Reconnu | Un administrateur a révisé l’alerte et a commencé à travailler sur celle-ci. |
| Fermés | Le problème a été résolu. Après qu’une alerte a été fermée, vous pouvez la rouvrir en modifiant son état. |

L’**état d’alerte** est différent et indépendant de la **condition d’analyse**. L’état de l’alerte est défini par l’utilisateur. La condition de l’analyse est définie par le système. Lorsqu’une alerte se déclenche, la condition de l’alerte est considérée comme *déclenchée*. Lorsque la condition sous-jacente qui provoque le déclenchement de l’alerte disparaît, la condition de surveillance de l’alerte est considérée comme *résolue*. L’état de l’alerte n’est pas modifié jusqu’à ce que l’utilisateur la modifie. Découvrez comment [modifier l’état de vos alertes et de vos groupes intelligents](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Groupes intelligents 
Les groupes intelligents sont disponibles dans la préversion. 

Les groupes intelligents sont des agrégations d’alertes reposant sur des algorithmes d’apprentissage machine qui permettent de réduire le bruit des alertes et de faciliter la résolution des problèmes. [En savoir plus sur les groupes intelligents](https://aka.ms/smart-groups) et sur [leur gestion](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Expérience d’alertes 
La page des alertes par défaut fournit un résumé des alertes qui sont créées dans une fenêtre de temps spécifique. Elle affiche le nombre total d’alertes pour chaque niveau de gravité avec des colonnes identifiant le nombre total d’alertes dans chaque état pour chaque niveau de gravité. Sélectionnez l’un des niveaux de gravité pour ouvrir la page [Toutes les alertes](#all-alerts-page) filtrée sur ce niveau de gravité.

Elle n’affiche pas et ne suit pas les [alertes classiques](#classic-alerts) plus anciennes. Vous pouvez modifier les abonnements ou filtrer des paramètres pour mettre à jour la page. 

![Page des alertes](media/alerts-overview/alerts-page.png)

Vous pouvez filtrer cet affichage en sélectionnant des valeurs dans les menus déroulants en haut de la page.

| Colonne | Description |
|:---|:---|
| Abonnement | Sélectionnez jusqu’à cinq abonnements Azure. Seules les alertes dans les abonnements sélectionnés sont incluses dans la vue. |
| Groupe de ressources | Sélectionnez un seul groupe de ressources. Seules les alertes avec des cibles dans le groupe de ressources sélectionné sont incluses dans la vue. |
| Période | Seules les alertes déclenchées dans la fenêtre de temps sélectionnée seront incluses dans l’affichage. Les valeurs prises en charge sont : dernière heure, dernières 24 heures, 7 derniers jours et 30 derniers jours. |

Sélectionnez les valeurs suivantes en haut de la page Alertes pour ouvrir une autre page.

| Valeur | Description |
|:---|:---|
| Nombre total d’alertes | Nombre total d’alertes correspondant aux critères sélectionnés. Sélectionnez cette valeur pour ouvrir l’affichage Toutes les alertes sans filtre. |
| Groupes intelligents | Nombre total de groupes intelligents créés par les alertes correspondant aux critères sélectionnés. Sélectionnez cette valeur pour ouvrir la liste des groupes intelligents dans l’affichage Toutes les alertes.
| Nombre total de règles d’alerte | Nombre total de règles d’alerte dans l’abonnement et le groupe de ressources sélectionnés. Sélectionnez cette valeur pour ouvrir l’affichage Règles, filtré sur l’abonnement et le groupe de ressources sélectionnés.


## <a name="manage-alert-rules"></a>Gérer les règles d’alerte
Cliquez sur **Gérer les règles d’alerte** pour afficher la page **Règles**. La page **Règles** est un emplacement unique permettant de gérer toutes les règles d’alerte de vos abonnements Azure. Elle répertorie toutes les règles d’alerte et peut être triée en fonction des ressources cibles, des groupes de ressources, du nom de règle ou de l’état. Les règles d’alerte peuvent également être modifiées, activées ou désactivées à partir de cette page.  

 ![alertes-règles](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte
Les alertes peuvent être créées de manière homogène, quel que soit le service de surveillance ou le type de signal. Toutes les alertes déclenchées et les détails connexes sont disponibles sur une seule page.
 
Vous créez une nouvelle règle d’alerte à l’aide des trois étapes suivantes :
1. Choisissez la _cible_ de l’alerte.
1. Sélectionnez le _signal_ parmi les signaux disponibles pour la cible.
1. Spécifiez la _logique_ à appliquer aux données du signal.
 
Ce processus de création simplifié ne nécessite plus que vous connaissiez la source de la surveillance ou les signaux pris en charge avant de sélectionner une ressource Azure. La liste des signaux disponibles est automatiquement filtrée en fonction de la ressource cible sélectionnée. Également basée sur la cible, elle vous guide dans la définition de la logique de la règle d’alerte automatique.  

Pour plus d’informations sur la création de règles d’alerte, voir [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Les alertes sont disponibles dans plusieurs services de surveillance Azure. Pour plus d’informations sur la façon d’utiliser chacun de ces services et le moment opportun pour le faire, voir [Surveillance des applications et des ressources Azure](../../azure-monitor/overview.md). Le tableau suivant fournit la liste des types de règles d’alerte disponibles dans Azure. Il répertorie aussi les éléments actuellement pris en charge dans chaque expérience d’alerte.

Par le passé, Azure Monitor, Application Insights, Log Analytics et Service Health disposaient de fonctionnalités d’alerte distinctes. Au fil du temps, Azure a amélioré et combiné l’interface utilisateur et les différentes méthodes de génération d’alertes. Cette consolidation est toujours en cours. Par conséquent, certaines fonctionnalités ne sont pas encore disponibles dans le nouveau système d’alertes.  

| **Source de la surveillance** | **Type de signal**  | **Description** | 
|-------------|----------------|-------------|
| État d’intégrité du service | Journal d’activité  | Non pris en charge. Consultez [Créer des alertes de journal d’activité sur les notifications de service](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Tests de disponibilité web | Non pris en charge. Consultez [Alertes de test web](../../application-insights/app-insights-monitor-web-app-availability.md). Disponibles pour tout site web instrumenté pour envoyer des données à Application Insights. Réception d’une notification lorsque la réactivité ou la disponibilité d’un site web est inférieure aux attentes. |


## <a name="all-alerts-page"></a>Page Toutes les alertes 
Cliquez sur Nombre total d’alertes pour afficher la page Toutes les alertes. Vous obtenez une liste des alertes qui ont été créées dans la fenêtre de temps sélectionnée. Vous pouvez afficher une liste des alertes individuelles ou une liste des groupes intelligents contenant les alertes. Sélectionnez la bannière en haut de la page pour basculer entre les affichages.

![Page Toutes les alertes](media/alerts-overview/all-alerts-page.png)

Vous pouvez filtrer l’affichage en sélectionnant les valeurs suivantes dans les menus déroulants en haut de la page.

| Colonne | Description |
|:---|:---|
| Abonnement | Sélectionnez jusqu’à cinq abonnements Azure. Seules les alertes dans les abonnements sélectionnés sont incluses dans la vue. |
| Groupe de ressources | Sélectionnez un seul groupe de ressources. Seules les alertes avec des cibles dans le groupe de ressources sélectionné sont incluses dans la vue. |
| Type de ressource | Sélectionnez un ou plusieurs types de ressources. Seules les alertes avec des cibles du type sélectionné sont incluses dans la vue. Cette colonne n’est disponible qu’après qu’un groupe de ressources a été spécifié. |
| Ressource | Sélectionnez une ressource. Seules les alertes ayant ces ressources pour cible sont incluses dans l’affichage. Cette colonne n’est disponible qu’après qu’un type de ressource a été spécifié. |
| Severity | Sélectionnez un niveau de gravité d’alerte ou *Tous* pour inclure les alertes de tous les niveaux de gravité. |
| Condition de surveillance | Sélectionnez une condition de surveillance ou *Tous* pour inclure les alertes de conditions. |
| État d’alerte | Sélectionnez un état d’alerte ou *Tous* pour inclure les alertes d’états. |
| Service de surveillance | Sélectionnez un service ou *Tous* pour inclure tous les services. Seules les alertes créées par des règles utilisant ce service comme cible sont incluses. |
| Période | Seules les alertes déclenchées dans la fenêtre de temps sélectionnée seront incluses dans l’affichage. Les valeurs prises en charge sont : dernière heure, dernières 24 heures, 7 derniers jours et 30 derniers jours. |

Cliquez sur **Colonnes** en haut de la page pour sélectionner les colonnes à afficher. 

## <a name="alert-detail-page"></a>Page Détails de l’alerte
La page Détails de l’alerte s’affiche lorsque vous sélectionnez une alerte. Elle fournit des détails de l’alerte et vous permet d’en modifier l’état.

![Détails de l’alerte](media/alerts-overview/alert-detail2.png)

La page Détails de l’alerte comprend les sections suivantes.

| Section | Description |
|:---|:---|
| Essentials | Affiche les propriétés et autres informations importantes sur l’alerte. |
| Historique | Répertorie chaque action effectuée par l’alerte et toutes les modifications apportées à l’alerte. Actuellement limité aux changements d’état. |
| Groupe intelligent | Informations sur le groupe intelligent dans lequel l’alerte se trouve. Le *nombre d’alertes* fait référence au nombre d’alertes incluses dans le groupe intelligent. Cela comprend les autres alertes du même groupe intelligent qui ont été créées au cours des 30 derniers jours, quel que soit le filtre de temps dans la page de la liste des alertes. Sélectionnez une alerte pour afficher ses détails. |
| Détails supplémentaires | Affiche des informations contextuelles supplémentaires sur l’alerte, qui sont généralement spécifiques du type de source ayant créé l’alerte. |


## <a name="classic-alerts"></a>Alertes classiques 

Les métriques Azure Monitor et la fonctionnalité d’alerte du journal d'activité avant juin 2018 sont appelées « Alertes classiques ». 

Pour plus d’informations, consultez [Alertes classiques](./../../azure-monitor/platform/alerts-classic.overview.md).


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les groupes intelligents](https://aka.ms/smart-groups)
- [En savoir plus sur les groupes d’actions](../../azure-monitor/platform/action-groups.md)
- [Gestion des instances d’alertes dans Azure](https://aka.ms/managing-alert-instances)
- [Gestion des groupes intelligents](https://aka.ms/managing-smart-groups)





