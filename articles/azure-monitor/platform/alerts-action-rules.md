---
title: Règles d’action pour les alertes Azure Monitor
description: Pour comprendre ce que sont les règles d’action dans Azure Monitor, et comment les configurer et les gérer.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6585890395d7656f239ac3098cd374ecd4757842
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80618986"
---
# <a name="action-rules-preview"></a>Règles d’action (préversion)

Les règles d’action vous permettent de définir ou de supprimer des actions à tout niveau d’Azure Resource Manager (abonnement Azure, groupe de ressources ou ressource cible). Elles disposent de différents filtres vous permettant de limiter le sous-ensemble des instances d’alerte sur lesquelles vous souhaitez agir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Pourquoi et quand doit-on utilise des règles d’action ?

### <a name="suppression-of-alerts"></a>Suppression d’alertes

Il existe de nombreux scénarios dans lesquels il est utile de supprimer les notifications générées par les alertes. Ces scénarios vont de la suppression au cours d’une fenêtre de maintenance planifiée à la suppression pendant les heures creuses. Par exemple, l’équipe responsable de **ContosoVM** souhaite supprimer les notifications d’alertes pour le week-end à venir, car **ContosoVM** aura une maintenance planifiée. 

Bien que l’équipe puisse désactiver manuellement chaque règle d’alerte configurée sur **ContosoVM** (et la réactiver après la maintenance), ce n’est pas un processus simple. Les règles d’action vous permettent de définir une suppression d’alerte adaptée tout en configurant de façon flexible la période de cette suppression. Dans l’exemple précédent, l’équipe peut définir une règle d’action sur **ContosoVM** qui supprime toutes les notifications d’alertes pendant le week-end.


### <a name="actions-at-scale"></a>Actions adaptées

Bien que les règles d’alerte vous permettent de définir le groupe d’actions qui se déclenche quand l’alerte est générée, les clients ont souvent un groupe d’actions commun sur l’étendue des opérations. Par exemple, une équipe responsable du groupe de ressources **ContosoRG** définira probablement le même groupe d’actions pour toutes les règles d’alerte définies au sein de **ContosoRG**. 

Les règles d’action vous aident à simplifier ce processus. En définissant des actions adaptées, un groupe d’actions peut être déclenché pour toute alerte générée sur l’étendue configurée. Dans l’exemple précédent, l’équipe peut définir une règle d’action sur **ContosoRG** qui déclenchera le même groupe d’actions pour toutes les alertes générées.

> [!NOTE]
> Les règles d’action ne s’appliquent actuellement pas aux alertes Azure Service Health.

## <a name="configuring-an-action-rule"></a>Configuration d’une règle d’action

Vous pouvez accéder à la fonctionnalité en sélectionnant **Gérer les actions** dans la page d’accueil **Alertes** dans Azure Monitor. Ensuite, sélectionnez **Règles d’action (préversion)** . Vous pouvez accéder aux règles en sélectionnant **Règles d’action (préversion)** dans le tableau de bord de la page d’accueil des alertes.

![Règles d’action de la page d’accueil d’Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Sélectionnez **+ Nouvelle règle d’action**. 

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-new-rule.png)

Vous pouvez également créer une règle d’action tout en configurant une règle d’alerte.

![Ajouter une nouvelle règle d’action](media/alerts-action-rules/action-rules-alert-rule.png)

Vous devez maintenant voir la page de flux pour la création de règles d’action. Configurez les éléments suivants : 

![Nouveau flux de création de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Étendue

Choisissez en premier lieu l’étendue (abonnement Azure, groupe de ressources ou ressource cible). Vous pouvez également sélectionner plusieurs étendues au sein d’un même abonnement.

![Étendue de la règle action](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critères de filtre

Vous pouvez aussi définir des filtres pour les limiter à un sous-ensemble d’alertes spécifique. 

Les filtres disponibles sont : 

* **Gravité** : option permettant de sélectionner une ou plusieurs gravités d’alerte. En définissant **Sev1**, la règle d’action s’applique à toutes les alertes définies sur Sev1.
* **Service de surveillance** : filtre basé sur le service de supervision d’origine. Ce filtre est également à sélection multiple. Par exemple, si **Service de supervision = « Application Insights »** , cela signifie que la règle d’action s’applique à toutes les alertes basées sur Application Insights.
* **Type de ressource** :  filtre basé sur un type de ressource spécifique. Ce filtre est également à sélection multiple. Par exemple, si **Type de ressource = « Machines virtuelles »** , cela signifie que la règle d’action s’applique à toutes les machines virtuelles.
* **ID de règle d’alerte** : option permettant de filtrer des règles d’alerte spécifiques via l’ID Resource Manager de la règle d’alerte.
* **Condition de surveillance** :  filtre pour les instances d’alerte avec la condition de supervision **Déclenché** ou **Résolu**.
* **Description** : correspondance d’expression régulière (regex) qui définit une correspondance de chaîne par rapport à la description, définie dans le cadre de la règle d’alerte. Par exemple, **la description contient « prod »** correspond à toutes les alertes contenant la chaîne « prod » dans leur description.
* **Contexte de l’alerte (charge utile)**  : correspondance d’expression régulière qui définit une correspondance de chaîne par rapport aux champs de contexte d’alerte de la charge utile d’une alerte. Par exemple, **le contexte de l’alerte (charge utile) contient « Computer-01 »** correspond à toutes les alertes dont les charges utiles contiennent la chaîne « Computer-01 ».

Ces filtres sont appliqués conjointement l’un à l’autre. Par exemple, si vous définissez **« Type de ressource » = Machines virtuelles** et **« Gravité » = Sev0**, vous filtrez toutes les alertes **Sev0** de vos machines virtuelles uniquement. 

![Filtres de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuration ou suppression d’un groupe d'actions

Configurez ensuite la règle d’action de la suppression d’alerte ou de la prise en charge du groupe d’actions. Vous ne pouvez pas choisir les deux. La configuration s’applique à toutes les instances d’alerte qui correspondent aux filtres et à l’étendue précédemment définis.

#### <a name="suppression"></a>Suppression

Si vous sélectionnez **suppression**, configurez la durée de la suppression des actions et des notifications. Choisissez l’une des options suivantes :
* **À partir de maintenant (toujours)**  : Supprime toutes les notifications indéfiniment.
* **À une heure planifiée** : Supprime les notifications dans une période limitée.
* **Avec périodicité** : Supprime les notifications selon une planification quotidienne, hebdomadaire ou mensuelle récurrente.

![Suppression de règle d’action](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Groupe d’actions

Si vous sélectionnez **Groupe d’actions** sur l’option de bascule, ajoutez un groupe d’actions existant ou créez-en un. 

> [!NOTE]
> Vous ne pouvez associer qu’un seul groupe d'actions à une règle d’action.

![Ajouter ou créer une nouvelle règle d’action en sélectionnant le groupe d’actions](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Détails de la règle d’action

Enfin, configurez les détails suivants pour la règle d’action :
* Nom
* Groupe de ressources dans lequel elle est enregistrée
* Description 

## <a name="example-scenarios"></a>Exemples de scénarios

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scénario 1 : Suppression des alertes en fonction de la gravité

Contoso souhaite supprimer les notifications pour toutes les alertes Sev4 sur toutes les machines virtuelles au sein de l’abonnement **ContosoSub** chaque week-end.

**Solution :** Créer une règle d’action avec :
* Étendue = **ContosoSub**
* Filtres
    * Gravité = **Sev4**
    * Type de ressource = **Machines virtuelles**
* Suppression définie sur une périodicité hebdomadaire, et **Samedi** et **Dimanche** cochés

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scénario 2 : Suppression des alertes basée sur le contexte de l’alerte (charge utile)

Contoso souhaite supprimer les notifications pour toutes les alertes de journal générées pour **Computer-01** dans **ContosoSub** indéfiniment, car il fait l’objet d’une maintenance.

**Solution :** Créer une règle d’action avec :
* Étendue = **ContosoSub**
* Filtres
    * Service de supervision = **Log Analytics**
    * Le contexte de l’alerte (charge utile) contient **Computer-01**
* Suppression définie sur **À partir de maintenant (toujours)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scénario 3 : Groupe d’actions défini pour un groupe de ressources

Contoso a défini [une alerte de métrique au niveau d’un abonnement](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), mais souhaite définir les actions déclenchées spécifiquement pour des alertes générées depuis le groupe de ressources **ContosoRG**.

**Solution :** Créer une règle d’action avec :
* Étendue = **ContosoRG**
* Aucun filtre
* Groupe d’actions défini sur **ContosoActionGroup**

> [!NOTE]
> *Les groupes d’actions définis dans les règles d’action et les règles d’alerte fonctionnent indépendamment, sans déduplication*. Dans le scénario décrit plus haut, si un groupe d’actions est défini pour la règle d’alerte, il se déclenche conjointement avec le groupe d’actions défini dans la règle d’action. 

## <a name="managing-your-action-rules"></a>Gestion de vos règles d’actions

Vous pouvez afficher et gérer vos règles d’action à partir de l’affichage de liste :

![Affichage de liste des règles d’action](media/alerts-action-rules/action-rules-list-view.png)

Ici, vous pouvez activer, désactiver ou supprimer des règles d’action adaptées en cochant la case adjacente. Lorsque vous sélectionnez une règle d’action, sa page de configuration s’ouvre. La page vous aide à mettre à jour la définition de la règle d’action et à l’activer ou la désactiver.

## <a name="best-practices"></a>Meilleures pratiques

Les alertes de journal créées avec l’option [nombre de résultats](alerts-unified-log.md) génèrent une seule instance d’alerte via le résultat de la recherche (qui peut s’étendre sur plusieurs ordinateurs). Dans ce scénario, si une règle d’action utilise le filtre **Contexte de l’alerte (charge utile)** , il agit sur l’instance d’alerte tant qu’il existe une correspondance. Dans le scénario 2, décrit précédemment, si les résultats de la recherche pour l’alerte de journal générée contiennent **Computer-01** et **Computer-02**, la notification entière est supprimée. Aucune notification n’est générée pour **Computer-02**.

![Règles d’action et alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Pour utiliser au mieux les alertes de journal avec des règles d’action, créez des alertes de journal avec l’option [mesure de métrique](alerts-unified-log.md). Des instances d’alerte distinctes sont générées par cette option, en fonction du champ de groupe défini. Ainsi, dans le scénario 2, des instances d’alerte distinctes sont générées pour **Computer-01** et **Computer-02**. En raison de la règle d’action décrite dans le scénario, seule la notification pour **Computer-01** est supprimée. La notification pour **Computer-02** continue à se déclencher normalement.

![Règles d’action et alertes de journal (nombre de résultats)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Lors de la configuration d’une règle d’action, j’aimerais voir toutes les règles d’action pouvant se chevaucher afin d’éviter des notifications en double. Est-ce possible ?

Après avoir défini une étendue lors de la configuration d’une règle d’action, vous pouvez afficher une liste des règles d’action qui se chevauchent sur la même étendue (le cas échéant). Ce chevauchement peut être l’une des options suivantes :

* Correspondance exacte : Par exemple, la règle d’action que vous définissez et la règle d’action qui chevauche sont sur le même abonnement.
* Sous-ensemble : Par exemple, la règle d’action que vous définissez est sur un abonnement, et la règle d’action qui chevauche se trouve sur un groupe de ressources au sein de l’abonnement.
* Sur-ensemble : Par exemple, la règle d’action que vous définissez est sur un groupe de ressources, et la règle d’action qui chevauche se trouve sur l’abonnement qui contient le groupe de ressources.
* Intersection : Par exemple, la règle d’action que vous définissez est sur **VM1** et **VM2**, et la règle d’action qui chevauche est sur **VM2** et **VM3**.

![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Lors de la configuration d’une règle d’alerte, est-il possible de savoir s’il y a déjà des règles d’action définies qui pourraient avoir une influence sur la règle d’alerte que je suis en train de définir ?

Après avoir défini la ressource cible de la règle d’alerte, vous pouvez afficher la liste des règles d’action qui agissent sur la même étendue (le cas échéant) en sélectionnant **Afficher les actions configurées** sous la section **Actions**. Cette liste est renseignée en fonction des scénarios suivants pour l’étendue :

* Correspondance exacte : Par exemple, la règle d’alerte que vous définissez et la règle d’action sont sur le même abonnement.
* Sous-ensemble : Par exemple, la règle d’alerte que vous définissez est sur un abonnement, et la règle d’action se trouve sur un groupe de ressources au sein de l’abonnement.
* Sur-ensemble : Par exemple, la règle d’alerte que vous définissez est sur un groupe de ressources, et la règle d’action se trouve sur l’abonnement qui contient le groupe de ressources.
* Intersection : Par exemple, la règle d’alerte que vous définissez est sur **VM1** et **VM2**, et la règle d’action est sur **VM2** et **VM3**.
    
![Règles d’action qui se chevauchent](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Puis-je afficher les alertes qui ont été supprimées par une règle d’action ?

Dans la [page Liste des alertes](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), vous pouvez choisir une colonne supplémentaire appelée **Statut de suppression**. Si la notification d’une instance d’alerte a été supprimée, elle aura ce statut dans la liste.

![Instances d’alerte supprimées](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Que se passe-t-il s’il y a une règle d’action avec un groupe d'actions et une autre avec une suppression active sur la même étendue ?

La suppression est toujours prioritaire sur la même étendue.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Que se passe-t-il si je dispose d’une ressource supervisée dans deux règles d’action différentes ? Est-ce que je reçois une ou deux notifications ? Par exemple, **VM2** dans le scénario suivant :

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Pour chaque alerte sur VM1 et VM3, le groupe d’actions AG1 se déclenche une seule fois. Pour chaque alerte sur **VM2**, le groupe d’actions AG1 est déclenché deux fois, car les règles d’action ne dédupliquent pas les actions. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Que se passe-t-il si je dispose d’une ressource surveillée dans deux règles d’action différentes et qu’une d’entre elles incite une action tandis que l’autre incite une suppression ? Par exemple, **VM2** dans le scénario suivant :

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Pour chaque alerte sur VM1, le groupe d’actions AG1 se déclenche une seule fois. Les actions et les notifications pour chaque alerte sur VM2 et VM3 seront supprimées. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Que se passe-t-il si je dispose d’une règle d’alerte et d’une règle d’action définie pour la même ressource qui appelle différents groupes d’actions ? Par exemple, **VM1** dans le scénario suivant :

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Pour chaque alerte sur VM1, le groupe d’actions AG1 se déclenche une seule fois. Chaque fois que la règle d’alerte « rule1 » est déclenchée, elle déclenche également « AG2 ». Les groupes d’actions définis dans les règles d’action et les règles d’alerte fonctionnent indépendamment, sans déduplication. 

## <a name="next-steps"></a>Étapes suivantes

- [Plus d’informations sur les alertes dans Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
