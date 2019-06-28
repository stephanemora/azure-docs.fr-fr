---
title: Règles d’action pour les alertes Azure Monitor
description: Pour comprendre ce que sont les règles d’action, et comment les configurer et les gérer.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 6e97826499842a257f6402bd5268edc4cd6a486e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734911"
---
# <a name="action-rules-preview"></a>Règles d’action (préversion)

Les règles d’action vous permettent de définir des actions (des suppressions d’actions) à l’échelle de n’importe quel Resource Manager (abonnement, groupe de ressources ou ressources). Elles disposent d’une variété de filtres vous permettant de limiter le sous-ensemble des instances d’alerte sur lesquels vous souhaitez agir. 

## <a name="why-and-when-should-you-use-action-rules"></a>Pourquoi et quand doit-on utilise des règles d’action ?

### <a name="suppression-of-alerts"></a>Suppression d’alertes

Il y a souvent de nombreux scénarios où la suppression des notifications générées par les alertes serait utile, d’une suppression au cours d’une maintenance planifiée à la suppression en dehors des heures de travail. Par exemple, l’équipe responsable de « ContosoVM » souhaite supprimer les notifications d’alertes pour le week-end à venir, car « ContosoVM » aura une maintenance planifiée. Bien qu’ils soient en mesure de désactiver chaque règle d’alerte configurée sur « ContosoVM » manuellement (et de les réactiver après la maintenance), cela n’en demeure pas moins compliqué. Les règles d’alertes vous permettent de définir une suppression d’alerte adaptée tout en configurant de façon flexible la période de cette suppression. Pour reprendre l’exemple précédent, l’équipe peut maintenant définir une règle d’action sur « ContosoVM » qui supprimera toutes les notifications d’alertes pendant le week-end.


### <a name="actions-at-scale"></a>Actions adaptées

Bien que les règles d’alertes vous permettent de définir le groupe d'actions qui décide de quand est générée l’alerte, le groupe d’actions courant des clients a tendance à ne pas être adapté aux opérations. Par exemple, une équipe responsable du groupe de ressources « ContosoRG » définira probablement le même groupe d’actions pour toutes les règles d’alertes définies au sein de « ContosoRG ». Les règles d’actions peuvent simplifier ce processus en vous permettant de définir des actions adaptées, de sorte qu’un groupe d'actions puisse être déclenché pour chaque alerte générée à la portée configurée. Pour reprendre l’exemple précédent, l’équipe peut maintenant définir une règle d’action sur « ContosoRG » qui déclenchera le même groupe d'actions pour toutes les alertes générées.


## <a name="configuring-an-action-rule"></a>Configuration d’une règle d’action

Vous pouvez accéder à cette fonctionnalité en sélectionnant **Gérer les actions** sur la page d’accueil Alertes dans Azure Monitor. Puis sélectionnez **Règles d’action (préversion)** . Vous pouvez y accéder en sélectionnant **Règles d’action (préversion)** sur le tableau de bord de la page d’accueil des Alertes.

![Règles d’action de la page d’accueil d’Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Sélectionnez **+ Nouvelle règle d’action**. 

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-new-rule.png)

Vous pouvez également choisir de créer une règle d’action tout en configurant une règle d’alerte.

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-alert-rule.png)

Le flux de création de règle d’action doit maintenant s’ouvrir. Configurez les éléments suivants : 

![Nouveau flux de création de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Étendue

Choisissez en premier lieu l’étendue, autrement dit la ressource, le groupe de ressources ou l’abonnement cible. Vous avez aussi la possibilité de faire une sélection multiple d’une combinaison des trois (au sein d’un seul abonnement). 

![Étendue de la règle action](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critères de filtre

Vous pouvez aussi définir des filtres pour limiter d’autant plus un sous-ensemble d’alertes spécifiques sur l’étendue définie. 

Les filtres disponibles sont : 

* **Gravité** : Sélectionnez une ou plusieurs gravités d’alerte. En définissant Sev1, la règle d’alerte s’applique à toutes les alertes à la gravité Sev1.
* **Service de surveillance** : Filtre basé sur le service de surveillance d’origine. Il s’agit également d’une sélection multiple. Par exemple, si service de surveillance = « Application Insights », cela signifie que la règle d’action s’applique à toutes les alertes basées sur « Application Insights ».
* **Type de ressource** : Filtre basé sur un type de ressource spécifique. Il s’agit également d’une sélection multiple. Par exemple, si Type de ressource = « Machines virtuelles », cela signifie que la règle d’action s’applique à toutes les machines virtuelles.
* **ID de règle d’alerte** : Vous permet de filtrer des règles d’alerte spécifiques via l’ID du gestionnaire des ressources de la règle d’alerte.
* **Condition de surveillance** : Filtrez les instances d’alerte avec la condition de surveillance « Déclenché » ou « Résolu ».
* **Description** : Correspondance d’expression régulière dans la description définie dans le cadre de la règle d’alerte.
* **Contexte de l’alerte (charge utile)** : Correspondance d’expression régulière dans les champs [contexte de l’alerte](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) d’une instance d’alerte.

Ces filtres sont appliqués conjointement l’un à l’autre. Par exemple, si je définis « Type de ressource » sur « Machines virtuelles » et « Gravité » sur « Sev0 », je filtre ainsi toutes les alertes « Sev0 » de mes machines virtuelles uniquement. 

![Filtres de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuration ou suppression d’un groupe d'actions

Configurez ensuite la règle d’action de la suppression d’alerte ou de la prise en charge du groupe d'actions. Vous ne pouvez pas choisir les deux. La configuration s’applique à toutes les instances d’alerte qui correspondent aux filtres et à l’étendue précédemment définis.

#### <a name="suppression"></a>Suppression

Si vous sélectionnez **suppression**, configurez la durée de la suppression des actions et des notifications. Choisissez l’un des éléments suivants :
* **À partir de maintenant (toujours)** : Supprime toutes les notifications indéfiniment.
* **À une heure planifiée** : Supprime les notifications dans une période limitée.
* **Avec périodicité** : Supprime sur une base récurrente, qu’elle soit quotidienne, hebdomadaire ou mensuelle.

![Suppression de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Groupe d’actions

Si vous sélectionnez **Groupe d’actions** sur l’option de bascule, ajoutez un groupe d’actions existant ou créez-en un. 

> [!NOTE]
> Vous ne pouvez associer qu’un seul groupe d'actions à une règle d’action.

![Groupe d’actions et règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Détails de la règle d’action

Enfin, configurez les détails suivants pour la règle d’action
* Nom
* Groupe de ressources dans lequel elle sera enregistrée
* Description 

## <a name="example-scenarios"></a>Exemples de scénarios

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénario 1 : Suppression des alertes en fonction de la gravité

Contoso souhaite supprimer les notifications pour toutes les alertes Sev4 sur toutes les machines virtuelles au sein de leur abonnement « ContosoSub » chaque week-end.

**Solution :** Créer une règle d’action où
* Étendue = « ContosoSub »
* Filtres
    * Gravité = « Sev4 »
    * Type de ressource = « Machines virtuelles »
* Suppression définie sur une périodicité hebdomadaire, et « Samedi » et « Dimanche » cochés

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénario 2 : Suppression des alertes basée sur le contexte de l’alerte (charge utile)

Contoso souhaite supprimer les notifications pour tous les journaux d’alertes générées pour « Computer-01 » dans « ContosoSub » définitivement comme lorsqu’il est en maintenance.

**Solution :** Créer une règle d’action où
* Étendue = « ContosoSub »
* Filtres
    * Service de surveillance = « Log Analytics »
    * Contexte de l’alerte (charge utile) contient « Computer-01 »
* Suppression définie sur « À partir de maintenant (toujours) »

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénario 3 : Groupe d’actions défini pour un groupe de ressources

Contoso a défini [une alerte de métrique au niveau d’un abonnement](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), mais souhaite définir les actions déclenchées spécifiquement lorsque des alertes sont générées depuis leur groupe de ressources « ContosoRG ».

**Solution :** Créer une règle d’action où
* Étendue = « ContosoRG »
* Aucun filtre
* Groupe d’actions défini sur « ContosoActionGroup »

> [!NOTE]
> **Groupes d’actions définis dans les règles d’action et les règles d’alerte fonctionnent indépendamment, sans déduplication**. Dans le scénario décrit ci-dessus, s’il existe un groupe d’actions défini pour la règle d’alerte, il se déclenchera conjointement avec le groupe d’actions défini dans la règle d’action. 

## <a name="managing-your-action-rules"></a>Gestion de vos règles d’actions

Vous pouvez afficher et gérer vos règles d’action à partir de l’affichage de liste, comme indiqué ci-dessous.

![Affichage de liste des règles d’action](media/alerts-action-rules/action-rules-list-view.png)

Ici, vous pouvez activer/désactiver/supprimer des règles d’action à l’échelle en cochant la case adjacente. En cliquant sur une règle d’action, vous ouvrez sa page de configuration, ce qui vous permet de mettre à jour sa définition et de l’activer ou la désactiver.

## <a name="best-practices"></a>Bonnes pratiques

Les alertes de journaux créées avec l’option [nombre de résultats](alerts-unified-log.md) génèrent **une seule instance d’alerte** via le résultat de la recherche (qui peut se faire sur plusieurs ordinateurs, par exemple). Dans ce scénario, si une règle d’action utilise le filtre « Contexte d’alerte (charge utile) », elle agira sur l’instance d’alerte tant qu’il existe une correspondance. Dans le scénario 2 comme décrit précédemment, si les résultats de recherche pour l’alerte de journal générés contiennent 'Computer-01' et 'Computer-02', la notification entière est supprimée (autrement dit, aucune notification n’est générée pour 'Computer-02').

![Règles d’action et alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Pour tirer parti au mieux des alertes de journal avec des règles d’action, nous vous conseillons de créer des alertes de journaux avec l’option [mesure de métrique](alerts-unified-log.md). Via cette option, des instances d’alerte distinctes sont générées en fonction du champ Groupe défini. Dans le scénario 2, les instances d’alerte distinctes sont générées pour « Computer-01 » et « Computer-02 ». Avec la règle d’action décrite dans le scénario, seule la notification de « Computer-01 » est supprimée, tandis que la notification de « Computer-02 » continue de se déclencher normalement.

![Règles d’action et alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Forum Aux Questions

* Q. Lors de la configuration d’une règle d’action, j’aimerais voir toutes règles d’action pouvant se chevaucher afin d’éviter les notifications en double. Est-ce possible ?

    R. Une fois une étendue définie lors de la configuration d’une règle d’action, vous pouvez afficher une liste des règles d’action qui se chevauchent sur la même étendue. Ce chevauchement peut être l’une des options suivantes :
    * Correspondance exacte : Par exemple, la règle d’action que vous définissez et la règle d’action qui chevauche sont sur le même abonnement.
    * Sous-ensemble : Par exemple, la règle d’action que vous définissez est définie sur un abonnement, et la règle d’action qui chevauche se trouve sur un groupe de ressources au sein de l’abonnement.
    * Sur-ensemble : Par exemple, la règle d’action que vous définissez est définie sur un groupe de ressources, et la règle d’action qui chevauche se trouve sur l’abonnement qui contient le groupe de ressources.
    * Intersection : Par exemple, la règle d’action que vous définissez est définie sur « VM1 » et « VM2 », et la règle d’action qui chevauche est sur le « VM2 » et « VM3 ».

    ![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-overlapping.png)

* Q. Lors de la configuration d’une règle d’alerte, est-il possible de savoir s’il y a déjà des règles d’action définies qui pourraient avoir une influence sur la règle d’alerte que je suis en train de définir ?

    R. Une fois que vous avez défini la ressource cible de la règle d’alerte, vous pouvez afficher la liste des règles d’action qui agissent sur la même étendue (le cas échéant) en cliquant sur « Afficher les actions configurées » sous la section « Actions ». Cette liste est renseignée en fonction des scénarios suivants pour l’étendue :
    * Correspondance exacte : Par exemple, la règle d’alerte que vous définissez et la règle d’action sont sur le même abonnement.
    * Sous-ensemble : Par exemple, la règle d’alerte que vous définissez est définie sur un abonnement, et la règle d’action se trouve sur un groupe de ressources au sein de l’abonnement.
    * Sur-ensemble : Par exemple, la règle d’alerte que vous définissez est définie sur un groupe de ressources, et la règle d’action se trouve sur l’abonnement qui contient le groupe de ressources.
    * Intersection : Par exemple, la règle d’alerte que vous définissez est définie sur « VM1 » et « VM2 », et la règle d’action est sur le « VM2 » et « VM3 ».
    
    ![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* Q. Puis-je afficher les alertes qui ont été supprimées par une règle d’action ?

    R. Dans la [page Liste des alertes](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), vous trouverez une colonne supplémentaire qui peut être sélectionnée nommée « Statut de suppression ». Si la notification d’une instance d’alerte a été supprimée, elle aura ce statut dans la liste.

    ![Instances d’alerte supprimées](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* Q. Que se passe-t-il s’il y a une règle d’action avec un groupe d'actions et une autre avec une suppression active sur la même étendue ?

    R. **La suppression est toujours prioritaire sur la même étendue**.

* Q. Que se passe-t-il si je dispose d’une ressource surveillée dans deux règles d’action différentes ? Est-ce que je reçois une ou deux notifications ? Prenez « VM2 » pour exemple dans ce scénario :

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    R. Pour chaque alerte sur « VM1 » et « VM3 », le groupe d'actions 'AG1' se déclenche une seule fois. Pour chaque alerte sur « VM2 », le groupe d'actions « AG1 » est déclenché deux fois (**les règles d’action ne dédoublent pas les actions**). 

* Q. Que se passe-t-il si je dispose d’une ressource surveillée dans deux règles d’action différentes et qu’une d’entre elles incite une action tandis que l’autre incite une suppression ? Prenez « VM2 » pour exemple dans ce scénario :

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    R. Pour chaque alerte sur « VM1 », le groupe d'actions 'AG1' se déclenche une seule fois. Les actions et les notifications de chaque alerte sur « VM2 » et « VM3 » seront supprimées. 

* Q. Que se passe-t-il si je dispose d’une règle d’alerte et d’une règle d’action définie pour la même ressource qui appelle différents groupes d’actions ? Prenez « VM1 » pour exemple dans ce scénario :

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    R. Pour chaque alerte sur « VM1 », le groupe d'actions 'AG1' se déclenche une seule fois. Chaque fois que la règle d’alerte « rule1 » est déclenchée, elle déclenche également « AG2 ». **Groupes d’actions définis dans les règles d’action et les règles d’alerte fonctionnent indépendamment, sans déduplication**. 

## <a name="next-steps"></a>Étapes suivantes

- [Plus d’informations sur les alertes dans Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
