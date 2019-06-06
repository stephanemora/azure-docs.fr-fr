---
title: Règles d’action pour les alertes Azure Monitor
description: Comprendre quelles sont les règles d’action et comment configurer et de les gérer.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 6e97826499842a257f6402bd5268edc4cd6a486e
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734911"
---
# <a name="action-rules-preview"></a>Règles d’action (version préliminaire)

Règles d’action permettent de définir des actions (ou la suppression des actions) dans toutes les portées de Resource Manager (abonnement, groupe de ressources ou ressource). Ils ont une variété de filtres qui vous permettent de limiter au sous-ensemble spécifique d’instances d’alerte que vous souhaitez agir sur. 

## <a name="why-and-when-should-you-use-action-rules"></a>Quand et pourquoi les doit utiliser des règles d’action ?

### <a name="suppression-of-alerts"></a>Suppression d’alertes

Il existe souvent des nombreux scénarios où il serait utile de supprimer les notifications générées par des alertes, qui peut aller de suppression pendant une fenêtre de maintenance planifiée à suppression pendant les heures non ouvrées. Par exemple, l’équipe responsable de la « ContosoVM » souhaite supprimer les notifications d’alerte pour le week-end à venir dans la mesure où 'ContosoVM' est en cours d’une maintenance planifiée. Pendant qu’ils peuvent désactiver chaque alerte règle configuré manuellement sur « ContosoVM » (et réactiver il valider la maintenance), il n’est pas une expérience simple. Règles d’action permettent de définir la suppression d’alerte à l’échelle avec la possibilité de configurer la période de suppression de manière flexible. En revenant à l’exemple précédent, l’équipe peut maintenant définir une règle d’action sur « ContosoVM », ce qui supprimera toutes les notifications d’alerte pour le week-end.


### <a name="actions-at-scale"></a>Actions à l’échelle

Bien que les règles d’alerte vous permettent de définir le groupe d’actions qui se déclenche lorsque l’alerte est générée, les clients ont tendance souvent à avoir un groupe d’actions courantes dans leur étendue des opérations. Par exemple, une équipe chargée du groupe de ressources 'ContosoRG' définiront probablement le même groupe d’actions pour toutes les règles d’alerte définies dans « ContosoRG ». Règles d’action permettent de simplifier ce processus en vous permettant de définir les actions à l’échelle, afin qu’un groupe d’actions peut être déclenché pour toute alerte générée sur l’étendue configurée. En revenant à l’exemple précédent, l’équipe peut maintenant définir une règle d’action sur « ContosoRG » qui déclenchera le même groupe d’actions pour toutes les alertes générées dans celui-ci.


## <a name="configuring-an-action-rule"></a>Configuration d’une règle d’action

Vous pouvez accéder à la fonctionnalité en sélectionnant **gérer actions** à partir des page d’accueil dans Azure Monitor les alertes. Puis sélectionnez **règles d’Action (version préliminaire)** . Vous pouvez y accéder en sélectionnant **règles d’Action (version préliminaire)** du tableau de bord de la page d’accueil pour les alertes.

![Règles d’action à partir de la page d’accueil de Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Sélectionnez **+ nouvelle règle d’Action**. 

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-new-rule.png)

Également, vous pouvez également choisir de créer une règle d’action lors de la configuration d’une règle d’alerte.

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-alert-rule.png)

Vous devez maintenant voir le flux de création de règle action ouverte. Configurer les éléments suivants : 

![Nouveau flux de création de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>`Scope`

Tout d’abord, choisissez l’étendue, autrement dit, ressource cible, groupe de ressources ou abonnement. Vous avez également la possibilité d’enfoncée pour sélectionner une combinaison quelconque des options ci-dessus (dans un seul abonnement). 

![Étendue de la règle action](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>critères de filtre

Vous pouvez en outre définir des filtres pour affiner jusqu'à un sous-ensemble spécifique des alertes sur l’étendue définie. 

Les filtres disponibles sont : 

* **Gravité** : Sélectionnez un ou plusieurs niveaux de gravité des alertes. Gravité = priorité 1 signifie que la règle de l’action est applicable pour toutes les alertes de gravité en tant que priorité 1.
* **Surveiller le Service**: Filtre basé sur le service de surveillance d’origine. Il s’agit également à sélection multiple. Par exemple, le Service de surveillance = « Application Insights » signifie que la règle de l’action s’applique pour tous les « Application Insights » en fonction d’alertes.
* **Type de ressource**: Filtre basé sur un type de ressource spécifique. Il s’agit également à sélection multiple. Par exemple, Type de ressource = signifie « Machines virtuelles » que la règle de l’action est applicable pour toutes les Machines virtuelles.
* **ID de règle d’alerte**: Vous permet de filtrer des règles d’alerte spécifiques à l’aide de l’ID du Gestionnaire de ressources de la règle d’alerte.
* **Surveiller la Condition**: Filtre pour les instances d’alerte avec « Déclenché » ou « Résolu » en tant que la condition du moniteur.
* **Description** : Expression régulière de la mise en correspondance dans la description définie dans le cadre de la règle d’alerte.
* **Contexte de l’alerte (charge utile)** : Correspondance d’expression régulière dans le [contexte de l’alerte](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) champs d’une instance d’alerte.

Ces filtres sont appliqués conjointement un à l’autre. Par exemple, si je définis le « Type de ressource » = « Machines virtuelles » et « Gravité » = « Sev0 », puis j’ai filtrée pour toutes les alertes « Sev0 » sur uniquement mes machines virtuelles. 

![Filtres de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuration du groupe de suppression ou l’action

Ensuite, configurez la règle d’action pour la suppression d’alerte ou prise en charge du groupe action. Vous ne pouvez pas choisir les deux. La configuration s’applique à toutes les instances d’alerte de mise en correspondance les filtres et portée précédemment définie.

#### <a name="suppression"></a>Suppression

Si vous sélectionnez **suppression**, configurer la durée de la suppression des actions et notifications. Choisissez une des opérations suivantes :
* **À partir de maintenant (toujours)** : Supprime toutes les notifications indéfiniment.
* **À une heure planifiée**: Supprimer les notifications au sein d’une durée limitée.
* **Avec une périodicité**: Supprimer une planification de périodicité, qui peut être quotidienne, hebdomadaire ou mensuelle.

![Suppression de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Groupe d’actions

Si vous sélectionnez **groupe d’actions** dans le bouton bascule, ajoutez un groupe d’actions existant ou créez-en un. 

> [!NOTE]
> Vous pouvez associer qu’un seul groupe d’actions à une règle d’action.

![Groupe d’actions de règle de action](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Détails de la règle action

Enfin, configurez les détails suivants pour la règle d’action
* Nom
* Groupe de ressources dans lequel il sera enregistré
* Description 

## <a name="example-scenarios"></a>Exemples de scénarios

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénario 1 : Suppression des alertes en fonction de gravité

Contoso souhaite supprimer les notifications pour toutes les alertes Sev4 sur toutes les machines virtuelles au sein de leur abonnement 'ContosoSub' chaque week-end.

**Solution :** Créer une règle d’action avec
* Scope = 'ContosoSub'
* Filtres
    * Gravité = 'Sev4'
    * Type de ressource = « Machines virtuelles »
* Suppression avec une périodicité définie sur toutes les semaines, et de vérifier les « Samedi » et « Dimanche »

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénario 2 : Suppression des alertes basées sur le contexte de l’alerte (charge utile)

Contoso souhaite supprimer les notifications pour tous les journaux d’alertes générées pour 'Ordinateur-01' dans 'ContosoSub' indéfiniment comme il est passer en revue la maintenance.

**Solution :** Créer une règle d’action avec
* Scope = 'ContosoSub'
* Filtres
    * Surveiller le Service = 'Log Analytique'
    * Contexte de l’alerte (charge utile) contient 'Ordinateur-01'
* Suppression de la valeur ' à partir de maintenant (toujours) »

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénario 3 : Groupe d’actions défini à un groupe de ressources

Contoso a défini [une alerte de mesure à un niveau d’abonnement](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), mais il veut définir les actions qui déclenchent spécifiquement pour les alertes générées à partir de leur groupe de ressources 'ContosoRG'.

**Solution :** Créer une règle d’action avec
* Scope = 'ContosoRG'
* Aucun filtre
* Groupe d’actions défini sur « ContosoActionGroup »

> [!NOTE]
> **Groupes d’actions définies dans les règles d’action et de règles d’alerte fonctionnent indépendamment, avec aucune déduplication**. Dans le scénario décrit ci-dessus, s’il existe un groupe d’actions défini pour la règle d’alerte, il déclenchera conjointement avec le groupe d’actions défini dans la règle d’action. 

## <a name="managing-your-action-rules"></a>La gestion de vos règles d’action

Vous pouvez afficher et gérer vos règles d’action à partir de l’affichage de liste, comme indiqué ci-dessous.

![Mode liste de règles d’action](media/alerts-action-rules/action-rules-list-view.png)

À ce stade, vous pouvez les règles d’action d’activer/désactiver/supprimer à l’échelle en sélectionnant la case à cocher en regard. En cliquant sur n’importe quelle règle action ouvre sa page de configuration, ce qui vous permet de mettre à jour sa définition et activer/désactiver.

## <a name="best-practices"></a>Bonnes pratiques

Créé avec des alertes de journal le ['nombre de résultats'](alerts-unified-log.md) option Générer **une seule instance d’alerte** utilisant le résultat de recherche entière (qui peut être par exemple sur plusieurs ordinateurs). Dans ce scénario, si une règle d’action utilise le filtre « Contexte de l’alerte (charge utile) », il s’agira sur l’instance d’alerte tant qu’il existe une correspondance. Dans le scénario 2 comme décrit précédemment, si les résultats de recherche pour l’alerte de journal générés contiennent 'Ordinateur-01' et 'Ordinateur-02', la notification entière est supprimée (autrement dit, aucune notification n’est générée pour 'Ordinateur-02').

![Règles d’action et des alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Meilleures exploiter les alertes de journal avec des règles d’action, nous vous conseillons de créer des alertes de journal avec le [mesure de métrique](alerts-unified-log.md) option. À l’aide de cette option, instances distinctes d’alerte sont générées en fonction du champ de groupe défini. Puis dans le scénario 2, instances d’alerte distincts sont générés pour 'Ordinateur-01' et 'Ordinateur-02'. Avec la règle d’action décrite dans le scénario, que la notification pour 'Ordinateur-01' serait supprimée alors que la notification pour 'Ordinateur-02' continue à se déclenchent comme d’habitude.

![Règles d’action et des alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Forum Aux Questions

* Q. Lorsque vous configurez une règle d’action, j’aimerais voir tous les possible qui se chevauchent action règles afin que j’évite les notifications en double. Est-il possible de faire ?

    R. Une fois que vous définissez une étendue lors de la configuration d’une règle d’action, vous pouvez voir une liste des règles d’action qui se chevauchent dans la même portée (le cas échéant). Ce chevauchement peut être une des options suivantes :
    * Une correspondance exacte : Par exemple, la règle de l’action que vous définissez et la règle d’action qui se chevauchent sont sur le même abonnement.
    * Un sous-ensemble : Par exemple, la règle de l’action que vous définissez est sur un abonnement, et la règle d’action qui se chevauchent se trouve sur un groupe de ressources au sein de l’abonnement.
    * Un sur-ensemble : Par exemple, la règle de l’action que vous définissez se trouve sur un groupe de ressources, et la règle d’action qui se chevauchent est sur l’abonnement qui contient le groupe de ressources.
    * Une intersection : Par exemple, la règle de l’action que vous définissez est sur '1' et '2', et la règle d’action qui se chevauchent est sur « VM2 » et « VM3 ».

    ![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-overlapping.png)

* Q. Alors que la configuration d’une règle d’alerte, il est possible de savoir s’il existe déjà défini de règles d’action qui peut agir sur la règle d’alerte que je définis ?

    R. Une fois que vous définissez la ressource cible pour votre règle d’alerte, vous pouvez voir la liste des règles d’action qui agissent sur la même étendue (le cas échéant) en cliquant sur « Vue configuré actions » dans la section « Actions ». Cette liste est renseignée selon les scénarios suivants pour l’étendue :
    * Une correspondance exacte : Par exemple, vous définissez la règle d’alerte et la règle d’action sont sur le même abonnement.
    * Un sous-ensemble : Par exemple, la règle d’alerte que vous définissez est sur un abonnement, et la règle d’action se trouve sur un groupe de ressources au sein de l’abonnement.
    * Un sur-ensemble : Par exemple, la règle d’alerte que vous définissez se trouve sur un groupe de ressources, et la règle d’action se trouve sur l’abonnement qui contient le groupe de ressources.
    * Une intersection : Par exemple, la règle d’alerte que vous définissez est sur '1' et '2', et la règle d’action est sur « VM2 » et « VM3 ».
    
    ![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* Q. Puis-je voir les alertes qui ont été supprimés par une règle d’action ?

    R. Dans le [page liste des alertes](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), il existe une colonne supplémentaire qui peut être choisie appelée « Suppression Status ». Si la notification pour une instance d’alerte a été supprimée, il serait affichent l’état dans la liste.

    ![Instances d’alerte supprimées](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* Q. S’il existe une règle d’action avec un groupe d’actions et l’autre avec la suppression active sur la même étendue, que se passe-t-il ?

    R. **Suppression de toujours est prioritaire sur la même étendue**.

* Q. Que se passe-t-il si j’ai une ressource analysée dans les deux règles d’action séparée ? Obtenir une ou deux notifications ? Par exemple « VM2 » dans ce scénario :

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    R. Pour chaque alerte sur « VM1 » et « VM3 », 'AG1' groupe d’actions se déclenche une seule fois. Pour chaque alerte sur '2', 'AG1' groupe d’actions est déclenchée deux fois (**règles d’action ne dupliquent pas retirer des actions**). 

* Q. Que se passe-t-il si j’ai une ressource analysée dans les deux règles d’action séparée et une appelle pour action pendant qu’une autre pour la suppression ? Par exemple, « VM2 » dans ce scénario :

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    R. Pour chaque alerte sur « VM1 », groupe d’actions 'AG1' se déclenche une seule fois. Actions et notifications pour chaque alerte sur « VM2 » et « VM3 » seront supprimées. 

* Q. Que se passe-t-il si j’ai une règle d’alerte et une règle d’action définie pour la même ressource appelant des groupes d’actions différents ? Par exemple, « VM1 » dans ce scénario :

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    R. Pour chaque alerte sur « VM1 », groupe d’actions 'AG1' se déclenche une seule fois. Chaque fois que la règle d’alerte « règle1 » est déclenchée, elle déclenche également « AG2 » par ailleurs. **Groupes d’actions définies dans les règles d’action et de règles d’alerte fonctionnent indépendamment, avec aucune déduplication**. 

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les alertes dans Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
