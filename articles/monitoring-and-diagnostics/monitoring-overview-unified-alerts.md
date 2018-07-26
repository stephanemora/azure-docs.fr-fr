---
title: Alertes unifiées dans Azure Monitor
description: Description d’alertes unifiées dans Azure qui vous permettent de gérer les alertes et règles d’alertes dans des services Azure.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: c4c8279a1d4638a1c5d889b53e2d9e89e458cc37
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117169"
---
# <a name="unified-alerts-in-azure-monitor"></a>Alertes unifiées dans Azure Monitor

## <a name="overview"></a>Vue d’ensemble

> [!NOTE]
>  Une nouvelle expérience d’alerte unifiée qui permet de gérer les alertes à partir de plusieurs abonnements, et introduit des états d’alerte et des groupes intelligents est actuellement disponible en préversion publique. Pour une description de cette expérience améliorée et le processus permettant de l’activer, voir la dernière section de cet article.


Cet article décrit l’expérience d’alerte unifiée dans Azure Monitor. L’[expérience d’alerte précédente](monitoring-overview-alerts.md) est disponible via l’option **Alertes (Classiques)** dans le menu d’Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Fonctionnalités de l’expérience d’alerte unifiée

L’expérience unifiée présente les avantages suivants par rapport à l’expérience classique :

-   **Meilleur système de notification** : les [groupes d’action](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) sont des groupes nommés de notifications et d’actions qui peuvent être réutilisées dans plusieurs alertes. 
- **Expérience de création unifiée** : vous pouvez gérer les alertes et règles d’alerte pour les métriques, les journaux et les journaux d’activité d’Azure Monitor, de Log Analytics et d’Application Insights au même emplacement. 
- **Affichage des alertes Log Analytics déclenchées dans le portail Azure** : vous pouvez désormais voir les alertes Log Analytics avec des alertes d’autres sources dans le portail Azure. Auparavant, les alertes provenant d’autres sources apparaissaient dans un portail distinct.
- **Séparation des alertes déclenchées et des règles d’alerte** : les règles d’alerte sont maintenant distinguées des alertes. Une règle d’alerte est la définition d’une condition qui déclenche une alerte. Une alerte est une instance de déclenchement d’une règle d’alerte.
- **Meilleur flux de travail** : l’expérience de création d’alerte unifiée vous guide tout au long du processus de configuration d’une règle d’alerte.
 
Les alertes de métrique sont améliorées par rapport aux alertes de métrique classiques :

-   **Latence améliorée** : les alertes Métrique peuvent être exécutées toutes les minutes. Les alertes Métrique classiques s’exécutent toujours à une fréquence de 5 minutes. Les alertes de journal apparaissent toujours dans un délai supérieur à une minute en raison du temps nécessaire à l’ingestion des journaux. 
-   **Prise en charge de métriques multidimensionnelles** : vous pouvez alerter sur des métriques dimensionnelles, ce qui signifie que vous pouvez surveiller une instance spécifique de la métrique.
-   **Plus de contrôle sur les conditions de métrique** : vous pouvez définir des règles d’alerte plus riches qui prennent en charge l’analyse des valeurs maximales, minimales, moyennes et totales de métriques.
-   **Surveillance combinée de plusieurs métriques** : vous pouvez surveiller jusqu’à deux métriques avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
-   **Métriques de journaux** (préversion publique limitée) : certaines données de journal envoyées à Log Analytics peuvent désormais être extraites et converties en métriques Azure Monitor, et faire l’objet d’alertes au même titre que d’autres métriques. 


## <a name="alert-rules"></a>Règles d'alerte
L’expérience des alertes unifiée utilise les concepts suivants pour distinguer les règles d’alerte et les alertes, tout en unifiant l’expérience de création pour les différents types d’alerte.

| Item | Définition |
|:---|:---|
| Règle d’alerte | Définition de la condition pour créer une alerte. Une règle d’alerte est constituée d’une _ressource cible_, d’un _signal_, de _critères_ et d’une _logique_. Une règle d’alerte n’est active que si elle est dans un état _activé_.
| Ressource cible | Définit les ressources et signaux spécifiques disponibles pour la génération d’alertes. Une cible peut être n’importe quelle ressource Azure.<br><br>Exemples : machine virtuelle, compte de stockage, groupe de machines virtuelles identiques, espace de travail Log Analytics, ressource Application Insights |
| Signal | Source de données émises par la ressource cible. Les types de signaux pris en charge incluent *Métrique*, *Journal d’activité*, *Application Insights* et *Journal*. |
| Critères | Combinaison du _signal_ et de la _logique_ appliqués à une ressource cible.<br><br>Exemples : pourcentage d’UC > 70 %, temps de réponse du serveur > 4 ms, nombre de résultats d’une requête de journal > 100, etc. |
| Logique | Logique définie par l’utilisateur pour vérifier si le signal respecte la plage ou les valeurs attendues. |
| Action | Action à effectuer lorsque l’alerte est déclenchée. Plusieurs actions peuvent se produire lors du déclenchement d’une alerte. Ces alertes prennent en charge les groupes d’actions.<br><br>Exemples : envoi d’un courrier à une adresse e-mail, appel d’une URL de webhook. |
| Condition de surveillance | Indique si la condition qui a créé une alerte Métrique a été résolue. Les règles d’alerte de métrique échantillonnent une métrique particulière à intervalles réguliers. Si les critères de la règle d’alerte sont remplis, une nouvelle alerte est créée avec une condition « Déclenché ».  Lorsque la métrique est ré-échantillonnée, si les critères sont toujours remplis, rien ne se produit.  Si les critères ne sont pas remplis, la condition de l’alerte passe à « Résolu ». Lorsque les critères sont de nouveau remplis, une autre alerte est créée avec une condition « Déclenché ». |


## <a name="alert-pages"></a>Pages d’alerte
Les alertes unifiées offrent un emplacement unique pour afficher et gérer toutes vos alertes Azure. Les sections suivantes décrivent les fonctions de chaque page de l’expérience unifiée.

### <a name="alerts-overview-page"></a>Page de présentation des alertes
La page de présentation **Alertes** affiche un résumé agrégé de toutes les alertes déclenchées et du nombre total de règles d’alerte activées. Modifier les paramètres d’abonnement ou de filtre met à jour les agrégats et la liste des alertes déclenchées.

 ![alertes-présentation](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gestion des règles d’alerte
La page **Règles** est une page unique permettant de gérer toutes les règles d’alerte de vos abonnements Azure. Elle répertorie toutes les règles d’alerte et peut être triée en fonction des ressources cibles, des groupes de ressources, du nom de règle ou de l’état. Les règles d’alerte peuvent également être modifiées, activées ou désactivées à partir de cette page.

 ![alertes-règles](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte
Les alertes peuvent être créées de manière homogène, quel que soit le service de surveillance ou le type de signal. Toutes les alertes déclenchées et les détails connexes sont disponibles sur une seule page.
 
Vous créez une nouvelle règle d’alerte à l’aide des trois étapes suivantes :
1. Choisissez la _cible_ de l’alerte.
1. Sélectionnez le _signal_ parmi les signaux disponibles pour la cible.
1. Spécifiez la _logique_ à appliquer aux données du signal.
 
Ce processus de création simplifié ne nécessite plus que vous connaissiez la source de la surveillance ou les signaux pris en charge avant de sélectionner une ressource Azure. La liste des signaux disponibles est automatiquement filtrée en fonction de la ressource cible sélectionnée, et vous guide dans la définition de la logique de la règle d’alerte.

Pour plus d’informations sur la création de règles d’alerte, voir [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](monitor-alerts-unified-usage.md).

Les alertes sont disponibles dans plusieurs services de surveillance Azure. Pour plus d’informations sur la façon d’utiliser chacun de ces services et le moment opportun pour le faire, voir [Surveillance des applications et des ressources Azure](./monitoring-overview.md). Le tableau suivant fournit la liste des types de règles d’alerte disponibles dans Azure. Il répertorie également ce que l’expérience de d’alerte unifiée prend actuellement en charge.

| **Source de la surveillance** | **Type de signal**  | **Description** | 
|-------------|----------------|-------------|
| Azure Monitor | Métrique  | Également appelées [alertes Métrique en quasi temps réel](monitoring-near-real-time-metric-alerts.md), ces alertes prennent en charge l’évaluation des conditions de métrique jusqu’à une fréquence d’une fois par minute, et autorisent les règles de métriques multiples et multidimensionnelles. Une liste de types de ressources pris en charge est disponible dans [Alertes métriques plus récentes pour les services Azure dans le portail Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>Les [alertes de métrique classiques](monitoring-overview-alerts.md) ne sont pas prises en charge dans la nouvelle expérience Alertes. Vous les trouverez dans l’onglet Alertes (classiques) dans le portail Azure. Les alertes classiques prennent en charge certains types de métriques qui n’ont pas encore été déplacés vers les alertes plus récentes. Pour obtenir la liste complète, consultez [Métriques prises en charge](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Journaux  | Reçoivent des notifications ou exécutent des actions automatisées quand une requête de recherche dans les journaux répond à certains critères. Les alertes dans Log Analytics sont [copiées dans la nouvelle expérience](monitoring-alerts-extend.md). Une [préversion des *journaux Log Analytics comme métriques*](monitoring-alerts-extend-tool.md) est disponible. La préversion vous permet de prendre certains types de journaux pour les convertir en métriques. Vous pourrez alors placer des alertes dessus à l’aide de la nouvelle expérience de génération d’alertes. La préversion est utile si vous avez des journaux non-Azure que vous souhaitez utiliser avec des métriques Azure Monitor natives. |
| Journaux d’activité | Journal d’activité | Contient les enregistrements de toutes les actions de création, de mise à jour et de suppression créées par la cible sélectionnée. |
| État d’intégrité du service | Journal d’activité  | Non pris en charge dans les alertes unifiées. Consultez [Créer des alertes de journal d’activité sur les notifications de service](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Journaux  | Comprend des journaux contenant les détails des performances de votre application. Une requête Analytics vous permet de définir les conditions applicables aux actions à accomplir sur la base des données de l’application. |
| Application Insights | Métrique | Non pris en charge dans les alertes unifiées. Voir [Alertes Métrique](../application-insights/app-insights-alerts.md). |
| Application Insights | Tests de disponibilité web | Non pris en charge dans les alertes unifiées.  Consultez [Alertes de test web](../application-insights/app-insights-monitor-web-app-availability.md). Disponibles pour tout site web instrumenté pour envoyer des données à Application Insights. Réception d’une notification lorsque la réactivité ou la disponibilité d’un site web est inférieure aux attentes. |

## <a name="enhanced-unified-alerts-public-preview"></a>Alertes unifiées améliorées (préversion publique)

Une expérience d’alertes unifiées améliorée a été publiée en préversion publique pour Azure Monitor le 1er juin 2018. Cette expérience s’appuie sur les avantages des [alertes unifiées](#overview) publiées en mars 2018, et permet de gérer et d’agréger des alertes individuelles, ainsi que de modifier l’état de l’alerte. Cette section décrit les nouvelles fonctionnalités et comment naviguer dans les nouvelles pages d’alerte dans le portail Azure.

### <a name="enhanced-unified-alerts"></a>Alertes unifiées améliorées

La nouvelle expérience fournit les fonctionnalités suivantes qui ne sont pas disponibles dans l’expérience unifiée classique :

- **Afficher les alertes sur les abonnements** : vous pouvez désormais afficher et gérer des instances individuelles d’alertes entre plusieurs abonnements dans un même affichage.
- **Gérer l’état des alertes** : les alertes ont maintenant un état qui indique si elles ont été reconnues comme fermées.
- **Organiser les alertes avec des groupes intelligents** : les groupes intelligents regroupent automatiquement des alertes associées pour que vous puissiez les gérer en tant qu’ensemble plutôt qu’individuellement.

### <a name="enable-enhanced-unified-alerts"></a>Activer les alertes unifiées améliorées
Activer la nouvelle expérience d’alerte unifiée en sélectionnant la bannière en haut de la page des alertes. Ce processus crée un magasin d’alertes qui inclut les 30 derniers jours d’alertes déclenchées sur les services pris en charge. Une fois la nouvelle expérience activée, vous pouvez basculer entre la nouvelle expérience et l’ancienne en cliquant sur cette bannière.

> [!NOTE]
>  La première activation de la nouvelle expérience peut prendre quelques minutes.

![Bannière](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Tous les abonnements auxquels vous avez accès seront inscrits lorsque vous activerez la nouvelle expérience. Même si tout l’abonnement est activé, seuls les utilisateurs ayant sélectionné la nouvelle expérience pourront l’afficher. Les autres utilisateurs ayant accès à l’abonnement doivent activer l’expérience séparément.

L’activation de la nouvelle expérience d’alerte n’affecte pas la configuration des groupes d’actions ou des notifications dans vos règles d’alerte. Elle ne modifie que la façon selon laquelle vous affichez et gérez les instances déclenchés des alertes dans le portail Azure.

### <a name="smart-groups"></a>Groupes intelligents
Les groupes intelligents réduisent le bruit, ce qui permet de gérer les alertes associées en tant qu’unité unique plutôt qu’en tant qu’alertes individuelles. Vous pouvez afficher les détails des groupes intelligents et en définir l’état comme vous le pouvez pour des alertes. Chaque alerte est un membre d’un seul groupe intelligent.

Des groupes intelligents sont automatiquement créés en utilisant le Machine Learning pour combiner des alertes associées correspondant à un même problème. Quand une alerte est créée, l’algorithme l’ajoute à un groupe intelligent ou à un groupe intelligent, nouveau ou existant, en fonction d’informations telles que des modèles historiques, la similarité des propriétés ou la similarité de la structure. 

L’algorithme ne prend actuellement en compte que les alertes du même service d’analyse au sein d’un abonnement. Grâce à cette consolidation, les groupes intelligents peuvent réduire jusqu’à 99 % du bruit d’alerte. Vous pouvez afficher la raison pour laquelle des alertes ont été incluses dans un groupe dans la page de détails du groupe intelligent.

Le nom d’un groupe intelligent correspond au nom de sa première alerte. Vous ne pouvez pas créer ou renommer un groupe intelligent.


### <a name="alert-states"></a>États d’alerte
Les alertes unifiées améliorées présentent le concept d’état d’alerte. Vous pouvez définir l’état d’une alerte afin d’indiquer où elle se situe dans le processus de résolution. Lorsqu’une alerte est créée, elle a l’état *Nouveau*. Vous pouvez changer son état après l’avoir reconnue ou fermée. Les changements d’état sont stockés dans l’historique de l’alerte.

Les états d’alerte suivants sont pris en charge.

| État | Description |
|:---|:---|
| Nouveau | Le problème vient d’être détecté et n’a pas encore été examiné. |
| Reconnu | Un administrateur a révisé l’alerte et a commencé à travailler sur celle-ci. |
| Fermés | Le problème a été résolu. Après qu’une alerte a été fermée, vous pouvez la rouvrir en modifiant son état. |

L’état d’une alerte est différent de la condition d’analyse. Des règles d’alerte de métrique peuvent définir une alerte sur une condition _Résolu_ lorsque la condition d’erreur n’est plus remplie. L’état d’alerte est défini par l’utilisateur et est indépendant de la condition d’analyse. Bien que le système puisse définir la condition de surveillance « Résolu », l’état de l’alerte n’est pas modifié tant que l’utilisateur ne le change pas.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Changement de l’état d’une alerte ou d’un groupe intelligent
Vous pouvez changer l’état d’une alerte individuelle ou gérer plusieurs alertes simultanément en définissant l’état d’un groupe intelligent.

Modifiez l’état d’une alerte en sélectionnant **Modifier l'état d'alerte** dans la vue détaillée de l’alerte. Ou modifiez l’état d’un groupe intelligent en sélectionnant **Modifier l'état du groupe intelligent** dans son affichage détaillé. Modifiez l’état de plusieurs éléments simultanément en les sélectionnant dans une liste, puis en sélectionnant **Modifier l’état** en haut de la page. 

Dans les deux cas, sélectionnez un nouvel état dans le menu déroulant. Fournissez ensuite un commentaire facultatif. Si vous modifiez un élément, vous pouvez appliquer les mêmes modifications à toutes les alertes dans le groupe intelligent.

![Modifier l’état](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Page des alertes
La page des alertes par défaut fournit un résumé des alertes qui sont créées dans une fenêtre de temps spécifique. Elle affiche le nombre total d’alertes pour chaque niveau de gravité avec des colonnes identifiant le nombre total d’alertes dans chaque état pour chaque niveau de gravité. Sélectionnez l’un des niveaux de gravité pour ouvrir la page [Toutes les alertes](#all-alerts-page) filtrée sur ce niveau de gravité.

![Page des alertes](media/monitoring-overview-unified-alerts/alerts-page.png)

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


### <a name="all-alerts-page"></a>Page Toutes les alertes 
La page Toutes les alertes vous permet d’afficher une liste des alertes qui ont été créées dans la fenêtre de temps sélectionnée. Vous pouvez afficher une liste des alertes individuelles ou une liste des groupes intelligents contenant les alertes. Sélectionnez la bannière en haut de la page pour basculer entre les affichages.

![Page Toutes les alertes](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Vous pouvez filtrer l’affichage en sélectionnant les valeurs suivantes dans les menus déroulants en haut de la page.

| Colonne | Description |
|:---|:---|
| Abonnement | Sélectionnez jusqu’à cinq abonnements Azure. Seules les alertes dans les abonnements sélectionnés sont incluses dans la vue. |
| Groupe de ressources | Sélectionnez un seul groupe de ressources. Seules les alertes avec des cibles dans le groupe de ressources sélectionné sont incluses dans la vue. |
| Type de ressource | Sélectionnez un ou plusieurs types de ressources. Seules les alertes avec des cibles du type sélectionné sont incluses dans la vue. Cette colonne n’est disponible qu’après qu’un groupe de ressources a été spécifié. |
| Resource | Sélectionnez une ressource. Seules les alertes ayant ces ressources pour cible sont incluses dans l’affichage. Cette colonne n’est disponible qu’après qu’un type de ressource a été spécifié. |
| Severity | Sélectionnez un niveau de gravité d’alerte ou *Tous* pour inclure les alertes de tous les niveaux de gravité. |
| Condition de surveillance | Sélectionnez une condition de surveillance ou *Tous* pour inclure les alertes de conditions. |
| État d’alerte | Sélectionnez un état d’alerte ou *Tous* pour inclure les alertes d’états. |
| Service de surveillance | Sélectionnez un service ou *Tous* pour inclure tous les services. Seules les alertes créées par des règles utilisant ce service comme cible sont incluses. |
| Période | Seules les alertes déclenchées dans la fenêtre de temps sélectionnée seront incluses dans l’affichage. Les valeurs prises en charge sont : dernière heure, dernières 24 heures, 7 derniers jours et 30 derniers jours. |

Cliquez sur **Colonnes** en haut de la page pour sélectionner les colonnes à afficher. 

### <a name="alert-detail-page"></a>Page Détails de l’alerte
La page Détails de l’alerte s’affiche lorsque vous sélectionnez une alerte. Elle fournit des détails de l’alerte et vous permet d’en modifier l’état.

![Détails de l’alerte](media/monitoring-overview-unified-alerts/alert-detail.png)

La page Détails de l’alerte comprend les sections suivantes.

| Section | Description |
|:---|:---|
| Essentials | Affiche les propriétés et autres informations importantes sur l’alerte. |
| Historique | Répertorie chaque action effectuée par l’alerte et toutes les modifications apportées à l’alerte. Il se limite actuellement aux changements d’état. |
| Groupe intelligent | Informations sur le groupe intelligent dans lequel l’alerte se trouve. Le *nombre d’alertes* fait référence au nombre d’alertes incluses dans le groupe intelligent. Il inclut les autres alertes incluses dans le même groupe intelligent qui ont été créées au cours des 30 derniers jours.  Et ceci, quel que soit le filtre de temps dans la page de la liste des alertes. Sélectionnez une alerte pour afficher ses détails. |
| Détails supplémentaires | Affiche des informations contextuelles supplémentaires sur l’alerte, qui sont généralement spécifiques du type de source ayant créé l’alerte. |


### <a name="smart-group-detail-page"></a>Page Détails du groupe intelligent
La page Détails du groupe intelligent s’affiche lorsque vous sélectionnez un groupe intelligent. Elle fournit des détails sur le groupe intelligent, dont le raisonnement utilisé pour créer le groupe, et vous permet d’en modifier l’état.
 
![Détails du groupe intelligent](media/monitoring-overview-unified-alerts/smart-group-detail.png)


La page Détails du groupe intelligent comprend les sections suivantes.

| Section | Description |
|:---|:---|
| Alertes | Répertorie les alertes individuelles qui sont incluses dans le groupe intelligent. Sélectionnez une alerte pour ouvrir sa page de détails. |
| Historique | Répertorie chaque action effectuée par le groupe intelligent, et toutes les modifications qui y sont apportées. Il se limite actuellement aux changements d’état et aux changements d’appartenance d’alerte. |

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment utiliser la nouvelle expérience Alertes pour créer, afficher et gérer des alertes](monitor-alerts-unified-usage.md)
- [Découvrez les alertes Journal dans l’expérience Alertes](monitor-alerts-unified-log.md)
- [Découvrez les alertes Métrique dans l’expérience Alertes](monitoring-near-real-time-metric-alerts.md)
- [Découvrez les alertes Journal d’activité dans l’expérience Alertes](monitoring-activity-log-alerts-new-experience.md)
