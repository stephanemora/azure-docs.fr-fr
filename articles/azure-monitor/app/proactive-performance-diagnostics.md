---
title: Détection intelligente – anomalies de performances | Microsoft Docs
description: La détection intelligente analyse les données de télémétrie de votre application et vous avertit des problèmes potentiels. Cette fonctionnalité ne nécessite aucune configuration.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 4bcbac5aa9d02aa7e996951b1d1bb334be5b2c9d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227024"
---
# <a name="smart-detection---performance-anomalies"></a>Détection intelligente – Anomalies de performances

>[!NOTE]
>Vous pouvez effectuer une migration de vos ressources Application Insights vers la détection intelligente des alertes (préversion). La migration crée des règles d’alerte pour les différents modules de détection intelligente. Vous pouvez alors gérer et configurer ces règles comme n’importe quelle autre règle d’alerte Azure Monitor. Vous avez également la possibilité de configurer des groupes d’actions pour ces règles, qui offrent différents moyens d’agir et de déclencher des notifications en cas de nouvelle détection.
>
> Pour plus d’informations sur le processus de migration, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).

[Application Insights](./app-insights-overview.md) analyse automatiquement les performances de votre application web et peut vous avertir des éventuels problèmes.

Cette fonctionnalité ne requiert aucune configuration particulière, à l'exception de la configuration de la [langue prise en charge](./platforms.md) par votre application pour Application Insights. Elle est active lorsque votre application génère suffisamment de données de télémétrie.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quand pouvez-vous recevoir une notification de détection intelligente ?

Application Insights a détecté que les performances de votre application se sont dégradées dans l’une des manières suivantes :

* **Dégradation du temps de réponse** : votre application a démarré en répondant plus lentement qu’auparavant aux demandes. Les changements peuvent avoir été rapides, par exemple en raison d’une régression dans votre dernier déploiement. Ou ils peuvent avoir été progressifs, probablement du fait d’une fuite de mémoire. 
* **Dégradation de la durée de dépendance** : votre application appelle une API REST, une base de données ou autre dépendance. La dépendance répond plus lentement qu’auparavant.
* **Modèle de performances lentes** : votre application semble rencontrer un problème de performances qui n’affecte que certaines demandes. Par exemple, les pages se chargent plus lentement dans un type de navigateur que dans d’autres, ou les demandes sont traitées plus lentement à partir d’un serveur particulier. À l’heure actuelle, nos algorithmes examinent le temps de chargement des pages, le temps de réponse aux demandes et le temps de réponse des dépendances.  

Pour établir une base de référence des performances normales, la détection intelligente nécessite au moins huit jours de volume suffisant de données de télémétrie. Lorsque votre application a été exécutée pendant cette période, les anomalies significatives déclenchent une notification.


## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?

Non, une notification ne signifie pas que votre application rencontre réellement un problème. Il s’agit simplement d’une suggestion pour vous amener à examiner un élément de plus près.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?

Les notifications incluent des informations de diagnostic. Voici un exemple :


![Voici un exemple de détection Dégradation du temps de réponse du serveur](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Tri**. La notification vous indique le nombre d’utilisateurs ou le nombre d’opérations affectés. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée**. Le problème affecte-t-il tout le trafic, ou certaines pages seulement ? Se limite-t-il à des navigateurs ou emplacements particuliers ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. Souvent, les informations de diagnostic dans la notification suggèrent la nature du problème. Par exemple, si le temps de réponse ralentit lorsque le taux de demandes est élevé, cela peut indiquer que votre serveur ou vos dépendances dépassent leur capacité. 

    Sinon, ouvrez le panneau Performances dans Application Insights. Vous y trouverez les données [Profiler](profiler.md). Si des exceptions sont levées, vous pouvez également essayer le [Débogueur de capture instantanée](./snapshot-debugger.md).

## <a name="configure-email-notifications"></a>Configurer des notifications par courrier électronique

Les notifications de détection intelligente sont activées par défaut. Elles sont envoyées aux utilisateurs disposant d’un accès [Lecteur de surveillance](../../role-based-access-control/built-in-roles.md#monitoring-reader) et [Contributeur de surveillance](../../role-based-access-control/built-in-roles.md#monitoring-contributor) à l’abonnement dans lequel se trouve la ressource Application Insights. Pour modifier la notification par défaut, cliquez sur **Configurer** dans la notification par e-mail ou ouvrez **Paramètres de détection intelligente** dans Application Insights. 
  
  ![Paramètres de détection intelligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Vous pouvez utiliser le lien **se désabonner** dans l’e-mail de détection intelligente pour ne plus recevoir les notifications par e-mail.

Les e-mails relatifs aux anomalies de performances de la détection intelligente se limitent à un seul e-mail par jour et par ressource Application Insights. Le courrier électronique n’est envoyé que si au moins un nouveau problème a été détecté ce jour-là. Vous n’obtiendrez pas plusieurs fois le même message. 

## <a name="faq"></a>Questions fréquentes (FAQ)

* *Les équipes Microsoft consultent-elles mes données ?*
  * Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](./data-retention-privacy.md).
* *Analysez-vous toutes les données collectées par Application Insights ?*
  * Actuellement, nous analysons le temps de réponse des demandes, le temps de réponse des dépendances et le temps de chargement des pages. L’analyse d’autres métriques se trouve dans notre backlog à venir.

* À quels types d’application cette détection s’applique-t-elle ?
  * Ces dégradations sont détectées dans n’importe quelle application qui génère les données de télémétrie appropriées. Si vous avez installé Application Insights dans votre application web, les demandes et les dépendances sont automatiquement suivies. Toutefois, dans les services back-end ou d’autres applications, si vous avez inséré des appels à [TrackRequest()](./api-custom-events-metrics.md#trackrequest) ou [TrackDependency](./api-custom-events-metrics.md#trackdependency), la détection intelligente fonctionnera de la même manière.

* *Puis-je créer mes propres règles de détection d’anomalies ou personnaliser des règles existantes ?*

  * Pas encore, mais vous pouvez :
    * [configurer des alertes](../alerts/alerts-log.md) qui vous indiquent qu'une métrique dépasse un seuil ;
    * [exporter la télémétrie](./export-telemetry.md) vers une [base de données](../../stream-analytics/app-insights-export-sql-stream-analytics.md) ou vers [Power BI](./export-power-bi.md), où vous pouvez les analyser vous-même.
* *À quelle fréquence l’analyse est-elle réalisée ?*

  * Nous exécutons l’analyse tous les jours sur la télémétrie du jour précédent (jour complet en heure UTC).
* *Cela remplace-t-il les [alertes de métrique](../alerts/alerts-log.md) ?*
  * Non. Nous ne détectons pas tous les comportements que vous pouvez considérer comme étant anormaux.


* *Si je ne fais rien suite à une notification, un rappel me sera-t-il envoyé ?*
  * Non, vous ne recevez qu’un message pour chaque problème. Si le problème persiste, il sera mis à jour dans le panneau de flux de détection intelligente.
* *J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*
  * Dans la vue d’ensemble Application Insights de votre application, cliquez sur la vignette **Détection intelligente**. Vous y trouverez toutes les notifications jusqu’à 90 jours en arrière.

## <a name="how-can-i-improve-performance"></a>Comment améliorer les performances ?
Les réponses lentes et les échecs font partie des expériences les plus frustrantes pour les utilisateurs de site web, comme vous l’avez sans doute appris à vos dépens. Il est donc important de résoudre les problèmes.

### <a name="triage"></a>Tri
Tout d’abord, est-il important ? Si une page est toujours lente à charger, mais que seul 1 % des utilisateurs de votre site doivent la consulter, vous avez sans doute des choses plus importantes à faire. Toutefois, si seulement 1 % des utilisateurs ouvrent cette page, mais qu’elle génère systématiquement des exceptions, il peut être judicieux d’investiguer.

Utilisez la déclaration d’impact, telle que les utilisateurs affectés ou le pourcentage de trafic, comme guide général. Soyez conscient que cela ne suffit peut-être pas. Recueillez d’autres informations pour confirmer.

Examinez les paramètres du problème. Si le problème est lié à la géographie, configurez des [tests de disponibilité](./monitor-web-app-availability.md) incluant cette région : il peut y avoir des problèmes de réseau dans cette zone.

### <a name="diagnose-slow-page-loads"></a>Diagnostiquer des chargements de page lents
Où est le problème ? Le serveur est-il lent à répondre, la page est-elle trop longue ou le navigateur a-t-il besoin d’une trop grande quantité de travail pour l’afficher ?

Ouvrez le panneau métrique des navigateurs. L'affichage segmenté du temps de chargement de la page de navigateur montre le délai qui s'écoule. 

* Si le **temps d’envoi de demande** est élevé, soit le serveur répond lentement, soit la demande est une publication contenant une importante quantité de données. Examinez les [mesures de performances](./performance-counters.md) pour étudier les temps de réponse.
* Configurez le [suivi des dépendances](./asp-net-dependencies.md) pour voir si cette lenteur est due à des services externes ou à votre base de données.
* Si le **Temps de réception de réponse** est prédominant, votre page et ses composants dépendants (JavaScript, CSS, images, etc., mais aucune donnée chargée de manière asynchrone) sont longs à s’afficher. Configurez un [test de disponibilité](./monitor-web-app-availability.md)et veillez à définir l'option de chargement des éléments dépendants. Lorsque vous obtenez des résultats, ouvrez les détails d’un résultat et développez-les pour afficher les temps de chargement de différents fichiers.
* Des **durées de traitement du client** prolongées suggèrent que l'exécution des scripts est lente. Si la raison n’est pas évidente, pensez à ajouter un code de minutage et à envoyer les heures dans les appels trackMetric.

### <a name="improve-slow-pages"></a>Améliorer les pages lentes
Il existe un site web plein de conseils sur l’amélioration des temps de chargement des réponses serveur et des pages, et nous n’essaierons pas de tous les répéter ici. Voici quelques conseils que vous connaissez sans doute déjà, simplement pour susciter votre réflexion :

* Chargement lent dû à de grands fichiers : chargez les scripts et les autres éléments de façon asynchrone. Utiliser le regroupement de scripts. Divisez la page principale en widgets chargeant leurs données séparément. N’envoyez pas d’ancien langage HTML pour les longs tableaux : utilisez un script pour demander les données JSON ou un autre format compact, puis renseignez le tableau sur place. Il existe des frameworks parfaitement adaptés pour vous aider dans ces tâches. (Ils incluent également de longs scripts, bien entendu.)
* Ralentir les dépendances de serveur : prendre en compte des emplacements géographiques de vos composants. Par exemple, si vous utilisez Azure, assurez-vous que le serveur web et la base de données se trouvent dans la même région. Les requêtes récupèrent-elles plus d’informations que nécessaire ? La mise en mémoire cache ou en lot peut-elle aider ?
* Problèmes de capacité : examinez les métriques de serveur des temps de réponse et le nombre de demandes. Si les temps de réponse présentent des pics disproportionnés en termes de nombre de requêtes, vos serveurs sont étirés.


## <a name="server-response-time-degradation"></a>Dégradation du temps de réponse du serveur

La notification de dégradation du temps de réponse indique :

* Le temps de réponse par rapport au temps de réponse normal pour cette opération.
* Le nombre d’utilisateurs affectés.
* Temps de réponse moyen et temps de réponse au 90e centile pour cette opération, le jour de la détection et sept jours auparavant. 
* Nombre de demandes de cette opération le jour de la détection et sept jours avant.
* Corrélation entre la dégradation de cette opération et les dégradations de dépendances associées. 
* Liens pour vous aider à diagnostiquer le problème.
  * Les traces de Profiler peuvent vous aider à visualiser où le plus de temps est consacré à l’opération. Le lien est disponible si des exemples de trace de Profiler existent pour cette opération. 
  * Rapports de performance dans l’Explorateur de mesures, dans lequel vous pouvez segmenter et traiter une plage de temps/des filtres pour cette opération.
  * Recherche de cet appel pour afficher certaines de ses propriétés.
  * Rapports d’échec – Si le nombre > 1, cela signifie que des échecs dans cette opération ont pu contribuer à la détérioration des performances.

## <a name="dependency-duration-degradation"></a>Dégradation de la durée de dépendance

Les applications modernes adoptent souvent une approche de conception de microservices qui, dans de nombreux cas, s’appuie fortement sur les services externes. Par exemple, si votre application s’appuie sur une plateforme de données ou sur un fournisseur de services critiques comme Cognitive Services.   

Exemple de notification de dégradation de dépendance :

![Voici un exemple de détection de la dégradation de la durée de dépendance](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Notez qu’il vous indique :

* La durée par rapport au temps de réponse normal pour cette opération
* Le nombre d’utilisateurs affectés
* Durée moyenne et durée au 90e centile pour cette dépendance le jour de la détection et sept jours avant
* Nombre d’appels de dépendance le jour de la détection et sept jours avant
* Liens pour vous aider à diagnostiquer le problème
  * Rapports de performances dans l’Explorateur de mesures de cette dépendance
  * Recherches de ces appels de dépendance pour afficher les propriétés des appels
  * Rapports d’échec – Si le nombre > 1, cela signifie que des appels de dépendance ont échoué pendant la période de détection, qui ont pu contribuer à la dégradation de la durée. 
  * Ouverture de la fonctionnalité Analytics permettant de calculer cette durée de dépendance et un nombre  

## <a name="smart-detection-of-slow-performing-patterns"></a>Détection intelligente de modèles de performances lentes 

Application Insights recherche les problèmes de performances pouvant toucher uniquement une partie de vos utilisateurs ou bien l’ensemble des utilisateurs mais dans certains cas de figure seulement. Par exemple, si une page est chargée plus lentement sur un type de navigateur spécifique par rapport à d’autres, ou si un serveur particulier gère les demandes plus lentement que les autres serveurs. Elle peut aussi détecter les problèmes liés à des combinaisons de propriétés, tels que des pages qui se chargent lentement dans une zone géographique pour des clients utilisant un système d’exploitation particulier.  

Les anomalies de ce type, qui sont difficiles à détecter par simple inspection des données, sont plus courantes que vous ne l’imaginez. Souvent, elles sont révélées par les plaintes des clients. Mais il est déjà trop tard : les utilisateurs concernés sont déjà passés à la concurrence !

À l’heure actuelle, nos algorithmes examinent le temps de chargement des pages, le temps de réponse aux demandes et le temps de réponse des dépendances.  

Vous n’avez pas à définir de seuils ni à configurer des règles. Des algorithmes d’apprentissage automatique (« Machine Learning ») et d’exploration de données sont utilisés pour détecter les schémas anormaux.

![Dans l’alerte reçue par courrier électronique, cliquez sur le lien pour ouvrir le rapport de diagnostic dans Azure](./media/proactive-performance-diagnostics/03.png)

* **Quand** (When) montre l'heure à laquelle le problème a été détecté.
* **What** décrit le problème détecté, ainsi que les caractéristiques du jeu d’événements que nous avons trouvé, qui a affiché le comportement du problème.
* Le tableau compare l’ensemble peu performant avec le comportement moyen de tous les autres événements.

Cliquez sur les liens pour ouvrir Metric Explorer et consulter les rapports, filtrés en fonction de l’heure et des propriétés de l’ensemble s’exécutant lentement.

Modifiez l’intervalle de temps et les filtres pour explorer la télémétrie.

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Profiler](profiler.md) 
* [Débogueur de capture instantanée](./snapshot-debugger.md)
* [Analyse](../logs/log-analytics-tutorial.md)
* [Diagnostics intelligents Analytics](../logs/log-query-overview.md)

La détection intelligente est automatique. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](../alerts/alerts-log.md)
* [Tests web de disponibilité](./monitor-web-app-availability.md)