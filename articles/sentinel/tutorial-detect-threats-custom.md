---
title: Créer des règles d’analytique personnalisées pour détecter des menaces avec Azure Sentinel | Microsoft Docs
description: Ce tutoriel explique comment créer des règles d’analytique personnalisées pour détecter des menaces de sécurité avec Azure Sentinel. Profitez des avantages du regroupement des événements, du regroupement des alertes et de l'enrichissement des alertes, et découvrez à quoi correspond le préfixe AUTO DISABLED (Désactivée automatiquement).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453933"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Tutoriel : Créer des règles d’analytique personnalisées pour détecter des menaces

Maintenant que vous avez [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous pouvez créer des règles d'analytique personnalisées pour détecter les menaces et les comportements anormaux au sein de votre environnement. Ces règles recherchent des événements ou des ensembles d'événements spécifiques au sein de votre environnement, vous préviennent lorsque certains seuils ou conditions d'événements sont atteints, génèrent des incidents que votre centre des opérations de sécurité (SOC) doit trier et examiner, et répondent aux menaces grâce à des processus de suivi et de correction automatisés. 

Ce tutoriel vous aide à créer des règles personnalisées pour détecter les menaces avec Azure Sentinel.

Au terme de ce tutoriel, vous pourrez :
> [!div class="checklist"]
> * Créer des règles d’analytique
> * Définir le mode de traitement des événements et des alertes
> * Définir le mode de génération des alertes et des incidents
> * Choisir des réponses automatisées aux menaces pour vos règles

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Créer une règle d’analytique personnalisée avec une requête planifiée

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Analytique**.

1. Sur la barre d'action du haut de l'écran, sélectionnez **+Créer**, puis **Règle de requête planifiée**. Cela entraîne l’ouverture de l’**Assistant de règle analytique**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Créer une requête planifiée" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Assistant de règle analytique - Onglet Général

- Entrez un **Nom** unique et une **Description**. 

- Dans le champ **Tactique**, vous pouvez choisir les catégories d’attaque en fonction desquelles la règle doit être classifiée. Celles-ci reposent sur les tactiques du framework [MITRE ATT&CK](https://attack.mitre.org/).

- Choisissez la **Gravité** d'alerte qui convient. 

- Quand vous créez la règle, son **État** est **Activé** par défaut. Cela signifie qu’elle va s’exécuter dès que vous aurez fini de la créer. Si vous ne souhaitez pas qu’elle s’exécute immédiatement, sélectionnez **Désactivé**. Dans ce cas, la règle est ajoutée à votre onglet **Règles actives**, à partir duquel vous pouvez l’activer quand vous en avez besoin.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Démarrer la création d’une règle d’analytique personnalisée":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Définir la logique de requête de règle et configurer les paramètres

Sous l'onglet **Définir la logique de la règle**, vous pouvez soit écrire une requête directement dans le champ **Requête de règle**, soit créer la requête dans l'espace de travail Log Analytics, puis la copier et la coller ici.

- Les requêtes sont écrites en langage de requête Kusto (KQL). Apprenez-en davantage sur les [concepts](/azure/data-explorer/kusto/concepts/) et les [requêtes](/azure/data-explorer/kusto/query/) KQL, et consultez ce [guide de référence rapide](/azure/data-explorer/kql-quick-reference).

- L'exemple illustré dans cette capture d'écran interroge la table *SecurityEvent* pour afficher un type d'[événements de connexion Windows ayant échoué](/windows/security/threat-protection/auditing/event-4625).

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Configurer la logique et les paramètres de règle de requête" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Voici un autre exemple de requête qui vous prévient lorsqu'un nombre anormal de ressources est créé dans [Activité Azure](../azure-monitor/platform/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Meilleures pratiques en matière de requête de règle
    > - La requête doit comporter 1 à 10 000 caractères, et ne doit pas contenir « `search *` » ou « `union *` ».
    >
    > - L’utilisation de fonctions ADX pour créer des requêtes Azure Data Explorer à l’intérieur de la fenêtre de requête Log Analytics **n’est pas prise en charge**.
    >
    > - Lorsque vous utilisez la fonction **`bag_unpack`** dans une requête, si vous projetez les colonnes sous forme de champs à l'aide de « `project field1` » et que la colonne n'existe pas, la requête échoue. Pour éviter ce problème, vous devez projeter la colonne comme suit :
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Enrichissement des alertes

> [!IMPORTANT]
> Les fonctionnalités d'enrichissement des alertes sont actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
    
- Utilisez la section de configuration **Mappage d'entités** pour mapper les paramètres de vos résultats de requête avec des entités reconnues par Azure Sentinel. Les entités enrichissent les résultats des règles (alertes et incidents) avec des informations essentielles qui constituent la base de tout processus d'investigation et des actions correctives ultérieures. Il s'agit également des critères qui vous permettent de regrouper les alertes en incidents sous l’onglet **Paramètres des incidents**.

    Apprenez-en davantage sur les [entités d'Azure Sentinel](entities-in-azure-sentinel.md).

    Consultez [Mapper des champs de données avec des entités dans Azure Sentinel](map-data-fields-to-entities.md) pour obtenir des instructions complètes sur le mappage des entités, ainsi que des informations importantes sur la [compatibilité descendante](map-data-fields-to-entities.md#notes-on-the-new-version).

- Utilisez la section de configuration **Détails personnalisés** pour extraire les éléments des données d'événement de votre requête et les faire apparaître dans les alertes produites par cette règle, ce qui vous donne une visibilité immédiate sur le contenu des événements dans vos alertes et incidents.

    Apprenez-en davantage sur la présentation de détails personnalisés dans les alertes, et consultez les [instructions complètes](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Planification des requêtes et seuil d'alerte

- Dans la section **Planification de la requête**, définissez les paramètres suivants :

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Définir la planification de requête et le regroupement d’événements" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Définissez le paramètre **Exécuter la requête tou(te)s les** pour définir la fréquence d'exécution de la requête (au maximum, toutes les 5 minutes ou au minimum, tous les 14 jours).

    - Définissez le paramètre **Rechercher les données des derniers** pour déterminer la période des données couvertes par la requête. Par exemple, l’interrogation peut porter sur les 10 dernières minutes de données ou sur les 6 dernières heures de données. Le maximum est de 14 jours.

        > [!NOTE]
        > **Intervalles entre les requêtes et période de recherche arrière**
        >
        >  Ces deux paramètres sont indépendants l’un de l’autre, jusqu’à un certain point. Vous pouvez exécuter une requête sur un court intervalle qui couvre une période plus longue que l’intervalle (en ayant en fait des requêtes qui se chevauchent), mais vous ne pouvez pas exécuter une requête sur un intervalle qui dépasse la période de couverture, sinon vous avez des écarts dans la couverture globale des requêtes.
        >
        > **Délai d'ingestion**
        >
        > Pour tenir compte de la **latence** qui peut se produire entre la génération d'un événement à la source et son ingestion dans Azure Sentinel, et pour assurer une couverture complète sans duplication de données, Azure Sentinel exécute les règles d'analytique planifiées avec un **décalage de cinq minutes** par rapport à l'heure planifiée.
        >
        > Pour savoir pourquoi ce délai est nécessaire et comment il permet de résoudre ce problème, consultez l'excellent billet de blog de Ron Marsiano, « [Gestion du délai d'ingestion dans les règles d'alerte planifiées d'Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851) ».

- Utilisez la section **Seuil d'alerte** pour définir le niveau de sensibilité de la règle. Par exemple, affectez la valeur **Est supérieur à** au paramètre **Générer une alerte quand le nombre de résultats de la requête**, puis entrez le nombre 1000 pour que la règle génère une alerte uniquement si la requête retourne plus de 1000 résultats à chaque exécution. Ce champ est obligatoire. Par conséquent, si vous ne souhaitez pas définir de seuil (en d'autres termes, si vous souhaitez que votre alerte enregistre chaque événement), entrez 0 dans le champ numérique.
    
### <a name="results-simulation"></a>Simulation des résultats

Dans la zone **Simulation des résultats**, sur la partie droite de l'Assistant, sélectionnez **Tester avec les données actuelles**. Azure Sentinel affiche le graphique des résultats (événements de journal) qui seraient générés par la requête en fonction de la planification actuellement définie sur la base des 50 dernières exécutions. Si vous modifiez la requête, resélectionnez **Tester avec les données actuelles** pour mettre le graphe à jour. Le graphe montre le nombre de résultats sur la période définie, laquelle est déterminée par les paramètres de la section **Planification de la requête**.
  
Voici à quoi peut ressembler la simulation des résultats pour la requête de la capture d’écran ci-dessus. Le côté gauche est l’affichage par défaut, et le côté droit est ce que vous voyez lorsque vous pointez sur un point dans le temps sur le graphe.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Captures d’écran de simulation des résultats":::

Si vous constatez que votre requête déclenche un trop grand nombre d'alertes ou des alertes trop fréquentes, vous pouvez essayer les paramètres des sections **Planification de la requête**  et **Seuil d'alerte** [et](#query-scheduling-and-alert-threshold)sélectionner à nouveau **Tester avec les données actuelles**.

### <a name="event-grouping-and-rule-suppression"></a>Regroupement des événements et suppression des règles

> [!IMPORTANT]
> La fonctionnalité de regroupement des événements est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
    
- Sous **Regroupement d’événements**, choisissez l’une des deux façons de gérer le regroupement d’**événements** dans **alertes** : 

    - **Regrouper tous les événements dans une seule alerte** (paramètre par défaut). La règle génère une alerte à chaque exécution, tant que la requête retourne plus de résultats que le **seuil d’alerte** spécifié au-dessus. L’alerte comprend un résumé de tous les événements retournés dans les résultats. 

    - **Déclencher une alerte pour chaque événement**. La règle génère une alerte pour chaque événement renvoyé par la requête. Cela est utile si vous souhaitez que les événements s’affichent individuellement ou si vous souhaitez les regrouper selon certains paramètres (par utilisateur, nom d’hôte ou autre chose). Vous pouvez définir ces paramètres dans la requête.
    
        Actuellement, le nombre d’alertes qu’une règle peut générer est plafonné à 20. Si, dans une règle particulière, l'option **Regroupement des événements** est définie sur **Déclencher une alerte pour chaque événement**, et si la requête de la règle renvoie plus de 20 événements, les 19 premiers événements génèrent chacun une alerte unique, et la 20e alerte résume l'ensemble des événements renvoyés. En d'autres termes, la 20e alerte correspond à ce qui serait généré sous l'option **Regrouper tous les événements dans une seule alerte**.

    > [!NOTE]
    > Quelle est la différence entre les **événements** et les **alertes** ?
    >
    > - Un **événement** est une description d'une occurrence unique d'une action. Par exemple, une entrée unique dans un fichier journal peut être comptabilisée en tant qu’événement. Dans ce contexte, un événement fait référence à un résultat unique retourné par une requête dans une règle d’analytique.
    >
    > - Une **alerte** est un ensemble d’événements qui, pris ensemble, sont significatifs du point de vue de la sécurité. Une alerte peut contenir un événement unique si l’événement a des implications significatives en matière de sécurité, par exemple, une connexion administrative en provenance d’un pays étranger en dehors des heures de bureau.
    >
    > - Au fait, que sont les **incidents** ? La logique interne d’Azure Sentinel crée des **incidents** à partir d’**alertes** ou de groupes d’alertes. La file d'attente des incidents est le point central du travail des analystes du SOC : triage, investigation et correction.
    > 
    > Azure Sentinel ingère des événements bruts de certaines sources de données, et des alertes déjà traitées d’autres sources. Il est important de noter la source dont il s’agit à tout moment.

- Dans la section **Suppression**, vous pouvez activer le paramètre **Arrêter l’exécution de la requête une fois l’alerte générée** en choisissant **Activé** si, une fois que vous recevez une alerte, vous souhaitez interrompre l’exécution de cette règle pendant une période supérieure à l’intervalle d’interrogation. Si vous activez cette option, vous devez affecter au paramètre **Arrêter l’exécution de la requête pendant** la valeur correspondant à la durée d’arrêt de la requête, à savoir 24 heures au maximum.

## <a name="configure-the-incident-creation-settings"></a>Configurer les paramètres de création d’incident

Sous l’onglet **Incident Settings**, vous pouvez choisir si et comment Azure Sentinel doit transformer les alertes en incidents actionnables. Si vous ne changez pas cet onglet, Azure Sentinel crée un seul incident distinct à partir de chaque alerte. Vous pouvez choisir de ne pas créer d’incident ou de regrouper plusieurs alertes en un seul incident en changeant les paramètres de cet onglet.

> [!IMPORTANT]
> L'onglet Paramètres des incidents est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Définir les paramètres de création d’incident et de regroupement d’alertes":::

### <a name="incident-settings"></a>Paramètres de l’incident

Dans la section **Paramètres des incidents**, le paramètre **Créer des incidents à partir d'alertes déclenchées par cette règle analytique** est **Activé** par défaut, ce qui signifie qu'Azure Sentinel crée un incident distinct à partir de chaque alerte déclenchée par la règle.
    
- Si vous ne souhaitez pas que cette règle entraîne la création d’incidents (par exemple, si cette règle vise uniquement à collecter des informations pour une analyse future), affectez-lui la valeur **Désactivé**.

- Si vous souhaitez créer un incident unique à partir d'un groupe d'alertes, au lieu d'en créer un seul par alerte, consultez la section suivante.

### <a name="alert-grouping"></a>Regroupement des alertes

Dans la section **Regroupement des alertes**, si vous souhaitez qu’un seul incident soit généré à partir d’un groupe de jusqu’à 150 alertes similaires ou récurrentes, définissez l’option **Regrouper les alertes déclenchées par cette règle analytique en un seul incident par** sur **Activé**, puis définissez les paramètres suivants.

- **Limiter le groupe aux alertes créées dans la période de temps sélectionnée** : Déterminez le délai d’exécution dans lequel les alertes similaires ou récurrentes vont être regroupées. Toutes les alertes correspondantes dans ce délai d’exécution génèrent collectivement un incident ou un ensemble d’incidents (en fonction des paramètres de regroupement ci-dessous). Les alertes situées en dehors de ce délai d’exécution génèrent un incident ou un ensemble d’incidents distinct.

- **Regrouper les alertes déclenchées par cette règle analytique en un seul incident par** : Choisissez la base sur laquelle les alertes sont regroupées :

    - **Regrouper les alertes en un seul incident si toutes les entités correspondent** : Les alertes sont regroupées si elles partagent des valeurs identiques pour chacune des entités mappées (définies sous l’onglet Définir la logique de la règle ci-dessus). Il s'agit du paramètre recommandé.

    - **Regrouper toutes les alertes déclenchées par cette règle en un seul incident** : Toutes les alertes générées par cette règle sont regroupées même si elles ne partagent pas de valeurs identiques.

    - **Regrouper les alertes en un seul incident si les entités sélectionnées correspondent** : Les alertes sont regroupées si elles partagent des valeurs identiques pour certaines des entités mappées (que vous pouvez sélectionner dans la liste déroulante). Vous pouvez utiliser ce paramètre si, par exemple, vous souhaitez créer des incidents distincts en fonction des adresses IP sources ou cibles.

- **Rouvrir les incidents correspondants fermés** : Si un incident a été résolu et fermé et que, par la suite, une autre alerte est générée, qui devrait être associée à cet incident, définissez ce paramètre sur **Activé** si vous voulez que l’incident fermé soit rouvert, ou laissez-le défini sur **Désactivé** si vous voulez que l’alerte crée un autre incident.
    
    > [!NOTE]
    > **150 alertes** peuvent être regroupées au sein d'un même incident. Si plus de 150 alertes sont générées par une règle qui les regroupe dans un incident unique, un nouvel incident est généré avec les mêmes détails que l’incident d’origine, et les alertes excédentaires sont regroupées dans le nouvel incident.

## <a name="set-automated-responses-and-create-the-rule"></a>Définir des réponses automatisées et créer la règle

1. Sous l’onglet **Réponse automatisée**, sélectionnez les playbooks à exécuter automatiquement quand une alerte est générée par la règle personnalisée. Pour plus d’informations sur la création et l’automatisation de playbooks, consultez [Répondre aux menaces](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Définir les paramètres de réponse automatisée":::

1. Sélectionnez **Examiner et créer** pour examiner tous les paramètres de votre nouvelle règle d'alerte. Lorsque le message « validation réussie » apparaît, sélectionnez **Créer** pour initialiser votre règle d'alerte.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Vérifier tous les paramètres et créer la règle":::

## <a name="view-the-rule-and-its-output"></a>Afficher la règle et sa sortie
  
- Vous trouverez la règle personnalisée (de type « Planifiée ») que vous venez de créer dans le tableau situé sous l'onglet **Règles actives** de l'écran **Analytique** principal. Dans cette liste, vous pouvez activer, désactiver ou supprimer chaque règle.

- Pour voir les résultats de vos règles d’alerte, accédez à la page **Incidents**, où vous pouvez ensuite trier, [examiner les incidents](tutorial-investigate-cases.md) et corriger les menaces.

> [!NOTE]
> Les alertes générées dans Azure Sentinel sont disponibles via [Microsoft Graph Security](/graph/security-concept-overview). Pour plus d’informations, consultez la [documentation sur les alertes Microsoft Graph Security](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Dépannage

### <a name="issue-no-events-appear-in-query-results"></a>Problème : Aucun événement ne s’affiche dans les résultats de la requête

Si le **regroupement d’événements** a la valeur **Déclencher une alerte pour chaque événement**, dans certains scénarios, lorsque vous affichez les résultats de la requête ultérieurement (par exemple, en cas de retour aux alertes d’un incident), il est possible qu’aucun résultat de requête ne s’affiche. Cela est dû au fait que la connexion de l’événement à l’alerte est effectuée par le hachage des informations de l’événement particulier et l’inclusion du hachage dans la requête. Si les résultats de la requête ont changé depuis la génération de l’alerte, le hachage n’est plus valide et aucun résultat ne s’affiche. 

Pour afficher les événements, supprimez manuellement la ligne avec le hachage de la requête de la règle, puis exécutez la requête.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problème : Une règle planifiée n'a pas été exécutée, ou le préfixe AUTO DISABLED (Désactivée automatiquement) a été ajouté à son nom

Bien que cela soit rare, une règle de requête planifiée peut échouer. Azure Sentinel classe les échecs comme passagers ou permanents, en fonction de leur type et des circonstances qui y ont conduit.

#### <a name="transient-failure"></a>Échec passager

Un échec passager se produit en raison d'une circonstance temporaire. Dès que la situation redevient normale, la règle est exécutée. Voici quelques exemples d'échecs classés comme passagers par Azure Sentinel :

- La requête d'une règle met trop de temps à s'exécuter et expire.
- Problèmes de connectivité entre les sources de données et Log Analytics, ou entre Log Analytics et Azure Sentinel.
- Tous les autres échecs nouveaux et inconnus sont considérés comme passagers.

En cas d'échec passager, Azure Sentinel essaie à nouveau d'exécuter la règle à intervalles prédéterminés et toujours croissants, jusqu'à un certain stade. Passé ce stade, la règle ne se réexécute qu'à la prochaine heure planifiée. Un échec passager ne provoque jamais la désactivation automatique de la règle.

#### <a name="permanent-failure---rule-auto-disabled"></a>Échec permanent - désactivation automatique de la règle

Un échec permanent se produit suite à une modification des conditions qui permettent à la règle de s'exécuter. Sans intervention humaine, celle-ci ne reviendra pas à son ancien état. Voici quelques exemples d'échecs classés comme permanents :

- L'espace de travail cible (sur lequel la requête de la règle était exécutée) a été supprimé.
- La table cible (sur laquelle la requête de la règle était exécutée) a été supprimée.
- Azure Sentinel a été supprimé de l'espace de travail cible.
- Une fonction utilisée par la requête de la règle n'est plus valide ; elle a été modifiée ou supprimée.
- Les autorisations d'accès à l'une des sources de données de la requête de la règle ont été modifiées.
- Une des sources de données de la requête de la règle a été supprimée ou déconnectée.

**Dans le cas d'un nombre prédéterminé d'échecs permanents consécutifs, du même type et sur la même règle,** Azure Sentinel cesse d'essayer d'exécuter la règle et effectue ce qui suit :

- Il désactive la règle.
- Il ajoute le préfixe **« AUTO DISABLED »** (Désactivée automatiquement) au nom de la règle.
- Il ajoute la raison de l'échec (et de la désactivation) à la description de la règle.

La présence de règles désactivées automatiquement peut facilement être déterminée en triant la liste des règles par nom. Les règles désactivées automatiquement apparaissent alors en haut de la liste.

Les gestionnaires SOC doivent régulièrement rechercher la présence de règles désactivées automatiquement dans la liste des règles.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à prendre en main la détection des menaces en utilisant Azure Sentinel.

- Apprenez à [examiner des incidents dans Azure Sentinel](tutorial-investigate-cases.md).
- Apprenez-en davantage sur les [entités d'Azure Sentinel](entities-in-azure-sentinel.md).
- Apprenez à [configurer des réponses automatisées aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).
