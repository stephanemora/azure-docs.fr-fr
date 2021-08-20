---
title: 'Démarrage rapide : Portail web de Metrics Advisor'
titleSuffix: Azure Cognitive Services
description: Découvrez comment commencer à utiliser le portail web de Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: 324b84255bd5939940a7819c521a452c1c49c8e8
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341832"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Démarrage rapide : Surveiller votre première métrique à l’aide du portail web

Lorsque vous provisionnez une instance Metrics Advisor, vous pouvez utiliser les API et l’espace de travail web pour travailler avec ce service. L’espace de travail web peut être utilisé comme un moyen simple de rapidement prendre en main le service. Il fournit également un moyen visuel de configurer les paramètres, de personnaliser votre modèle et d’effectuer une analyse de la cause racine. 

* Intégrer vos données de métrique
* Afficher vos métriques et visualisations
* Affiner les configurations de détection
* Explorer les insights de diagnostic
* Créer des alertes d’anomalie et s’y abonner

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une fois que vous disposez de votre abonnement Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Créer une ressource Metrics Advisor"  target="_blank">créez une ressource Metrics Advisor</a> dans le portail Azure pour déployer votre instance Metrics Advisor.  

    
> [!TIP]
> * 10 à 30 minutes peuvent être nécessaires pour le déploiement de votre ressource Metrics Advisor. Sélectionnez **Accéder à la ressource** une fois le déploiement réussi.
> * Si vous souhaitez utiliser l’API REST pour interagir avec le service, vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez. Vous pouvez les trouver sous l’onglet **Clés et points de terminaison**, dans la ressource créée.


Ce document utilise une base de données SQL comme exemple pour la création de votre premier moniteur.

## <a name="sign-in-to-your-workspace"></a>Vous connecter à votre espace de travail

Après avoir créé votre ressource, connectez-vous au [portail Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2143774) avec votre compte Active Directory. Dans la page d’accueil, sélectionnez votre **annuaire**, votre **abonnement** et l’**espace de travail** que vous venez de créer, puis sélectionnez **Prise en main**. Pour l’intégration des données de séries chronologiques, sélectionnez **Ajouter un flux de données** dans le menu de gauche.

 
Vous pouvez créer une ressource Metrics Advisor à chaque région disponible. Vous pouvez changer d’espace de travail dans le portail Metrics Advisor à tout moment.


## <a name="onboard-time-series-data"></a>Intégrer des données de série chronologique

Metrics Advisor fournit des connecteurs pour différentes sources de données comme SQL Database, Azure Data Explorer ou le stockage Table Azure. Les étapes de connexion des données sont similaires pour les différents connecteurs, même si certains paramètres de configuration peuvent varier. Consultez [Connecter différentes sources de flux de données](../data-feeds-from-different-sources.md) pour différents paramètres de connexion de données.

Ce guide de démarrage rapide utilise une base de données SQL comme exemple. Vous pouvez également ingérer vos propres données en suivant la même procédure.


### <a name="data-schema-requirements-and-configuration"></a>Configuration requise et configuration des schémas de données

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>Configurer les paramètres de connexion et les requêtes

[Ajoutez le flux de données](../how-tos/onboard-your-data.md) en vous connectant à votre source de données de série chronologique. Commencez par sélectionner les paramètres suivants :

* **Type de source** : Type de source de données dans laquelle vos données de série chronologique sont stockées.
* **Fréquence** : Intervalle entre des points de données consécutifs dans vos données de série chronologique. Par exemple, Annuel, Mensuel, Quotidien. L’intervalle le plus bas pris en charge par la personnalisation est de 60 secondes.
* **Ingérer des données depuis (UTC)**  : Heure de début du premier horodatage à ingérer. 


<!-- Next, specify the **Connection string** with the credentials for your data source, and a custom **Query**, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information. -->

:::image type="content" source="../media/connection-settings.png" alt-text="Paramètres de connexion" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>Charger les données

Après avoir entré la chaîne de connexion et la chaîne de requête, sélectionnez **charger les données**. Dans le cadre de cette opération, Metrics Advisor vérifie la connexion et l’autorisation de chargement des données, vérifie les paramètres nécessaires (@IntervalStart et @IntervalEnd) qui doivent être utilisés dans la requête et vérifie le nom de colonne de la source de données. 

En cas d’erreur à cette étape :
1. Vérifiez d’abord si la chaîne de connexion est valide. 
2. Vérifiez ensuite que les autorisations sont suffisantes et que l’accès est accordé à l’adresse IP du worker.
3. Vérifiez ensuite si les paramètres requis (@IntervalStart et @IntervalEnd) sont utilisés dans votre requête. 

### <a name="schema-configuration"></a>Configuration du schéma

Une fois les données chargées en exécutant la requête et affichées comme ci-dessous, sélectionnez les champs appropriés.


|Sélection  |Description  |Notes  |
|---------|---------|---------|
|**Timestamp**     | Horodatage d’un point de données. S’il est omis, Metrics Advisor utilise à la place l’horodatage de l’ingestion du point de données. Pour chaque flux de données, vous pouvez spécifier au plus une colonne comme horodatage.        | Optionnel. Doit être spécifié avec au plus une colonne.       |
|**Mesure**     |  Valeurs numériques dans le flux de données. Pour chaque flux de données, vous pouvez spécifier plusieurs mesures, mais au moins une colonne doit être sélectionnée comme mesure.        | Doit être spécifiée avec au moins une colonne.        |
|**Dimension**     | Valeurs catégorielles. Une combinaison de différentes valeurs identifie une série chronologique à une seule dimension, par exemple : pays, langue, locataire. Vous pouvez sélectionner un nombre nul ou arbitraire de colonnes comme dimensions. Remarque : Si vous sélectionnez une colonne qui n’est pas une chaîne comme dimension, soyez vigilant avec l’explosion de dimension. | Optionnel.        |
|**Ignorer**     | Suppression de la colonne sélectionnée.        | Optionnel. Pour que les sources de données prennent en charge l’utilisation d’une requête pour obtenir des données, il n’existe aucune option « Ignorer ».       |


:::image type="content" source="../media/schema-configuration.png" alt-text="Configuration du schéma" lightbox="../media/schema-configuration.png":::

Après avoir configuré le schéma, sélectionnez **Vérifier le schéma**. Dans le cadre de cette opération, Metrics Advisor effectue les vérifications suivantes :
- Indique si l’horodateur des données interrogées se trouve dans un seul intervalle. 
- Indique s’il existe des valeurs en double retournées pour la même combinaison de dimensions dans un intervalle de métrique.  

### <a name="automatic-roll-up-settings"></a>Paramètres de regroupement automatique

> [!IMPORTANT]
> Si vous souhaitez activer l’**analyse de la cause racine** et d’autres fonctionnalités de diagnostic, les paramètres de regroupement automatique doivent être configurés. Une fois activé, les paramètres de regroupement automatique ne peuvent pas être modifiés.

Metrics Advisor peut effectuer automatiquement une agrégation (SUM/MAX/MIN...) sur chaque dimension lors de l’ingestion, puis crée une hiérarchie qui sera utilisée dans l’analyse de la cause racine et d’autres fonctionnalités de diagnostic. Pour plus d’informations, consultez [Paramètres de regroupement automatique](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Donnez un nom personnalisé au flux de données, qui s’affichera dans votre espace de travail. Sélectionnez **Envoyer**. 

## <a name="tune-detection-configuration"></a>Ajuster la configuration de détection

Une fois le flux de données ajouté, Metrics Advisor tente d’ingérer les données de métrique à partir de la date de début spécifiée. Il faut un certain temps pour ingérer complètement les données et vous pouvez afficher l’état d’ingestion en sélectionnant **Progression de l’ingestion** en haut de la page de flux de données. Si les données sont ingérées, Metrics Advisor applique la détection et continue à surveiller la source pour détecter de nouvelles données.

Lorsque la détection est appliquée, sélectionnez l’une des métriques listées dans le flux de données pour rechercher la **page de détails des métriques** pour : 
- Afficher les visualisations de toutes les tranches de série chronologique sous cette métrique
- Mettre à jour la configuration de la détection afin de répondre aux résultats attendus
- Configurer une notification pour les anomalies détectées

:::image type="content" source="../media/metric-details.png" alt-text="Détails de la métrique" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Afficher les insights de diagnostic

Après l’ajustement de la configuration de la détection, les anomalies détectées doivent refléter les anomalies réelles dans vos données. Metrics Advisor effectue une analyse sur les métriques multidimensionnelles pour localiser la cause racine dans une dimension spécifique, ainsi qu’une analyse de mesures croisées à l’aide de « graphique de métriques ». 

Pour afficher les insights de diagnostic, sélectionnez les points rouges dans les visualisations de série chronologique, qui représentent les anomalies détectées. Une fenêtre s’affiche avec un lien vers la page d’analyse des incidents. 

:::image type="content" source="../media/incident-link.png" alt-text="Lien d’incident" lightbox="../media/incident-link.png":::

Après avoir sélectionné le lien, vous serez orienté vers la page d’analyse des incidents qui se rapporte à l’anomalie correspondante, avec toute une palette d’insights de diagnostic. Il existe 3 étapes majeures pour diagnostiquer un incident :

### <a name="check-summary-of-current-incident"></a>Vérifier le résumé de l’incident actuel

En haut, il y aura un résumé comprenant des informations de base, des actions et des suivis ainsi qu’une cause racine analysée. Les informations de base incluent la « série Top impactée » avec un diagramme « impact Start & End Time », « gravité incident » et « nombre total d’anomalies incluses ».

La cause racine analysée est un résultat analysé automatiquement. Metrics Advisor analyse toutes les anomalies capturées dans une série chronologique au sein d’une mesure avec des valeurs de dimension différentes au même horodatage. Il effectue ensuite la corrélation, le clustering pour regrouper les anomalies associées et générer un avis de cause racine.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Résumé du diagnostic de l’incident" lightbox="../media/diagnostics/incident-summary.png":::

Sur cette base, vous pouvez déjà obtenir une vue directe de l'état anormal actuel, de l'impact de l'incident et de la cause racine la plus potentielle. De cette façon, il est possible de prendre une mesure immédiate pour résoudre l’incident dès que possible. 

### <a name="view-cross-dimension-diagnostic-insights"></a>Visualiser les aperçus du diagnostic transdimensionnel

Après avoir obtenu des informations de base et une analyse automatique, vous pouvez obtenir des informations plus détaillées sur l’état anormal des autres dimensions dans la même mesure de façon holistique à l’aide de l'**arborescence de diagnostics**.

Pour les métriques avec plusieurs dimensions, le conseiller de métriques classe la série chronologique dans une hiérarchie, nommée « arborescence de diagnostic ». Par exemple, une mesure « chiffre d’affaires » est analysée par deux dimensions : « région » et « catégorie ». Malgré les valeurs de dimension concrètes, il doit y avoir une valeur de dimension **agrégée**, telle que **« Somme »** . Ensuite, les séries chronologiques « Région » = **« Somme »** et « Catégorie » = **« Somme »** sont classées comme nœud racine dans l’arborescence. Lorsqu’une anomalie est capturée à la dimension **« Somme »** , elle peut être extraite et analysée pour localiser la valeur de dimension spécifique qui a contribué le plus à l’anomalie du nœud parent. Cliquez sur chaque nœud pour développer les informations détaillées.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Affichage des dimensions croisées des diagnostics d’incidents" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights-using-metrics-graph"></a>Afficher les informations de diagnostic des mesures croisées à l’aide du « graphique de métriques »

Parfois, il est difficile d’analyser un problème en vérifiant l’état anormal d’une mesure unique, et vous devez mettre en corrélation plusieurs métriques ensemble. Les clients sont en mesure de configurer un « graphique de métriques » qui indique les relations entre les métriques. En tirant parti des résultats de diagnostic inter-dimensions, la cause racine est limitée à une valeur de dimension spécifique. Utilisez ensuite le « graphique de métriques » et filtrez en fonction de la dimension de la cause racine analysée pour vérifier l’état d’anomalie sur d’autres mesures.
Après avoir cliqué sur le lien, vous serez orienté vers la page d’analyse des incidents qui se rapporte à l’anomalie correspondante, avec toute une palette d’insights de diagnostic. Les trois sections de la page Détails de l’incident correspondent aux trois étapes principales pour diagnostiquer un incident. 

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Analyse des mesures croisées des diagnostics d’incidents" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

Toutefois, vous pouvez également faire pivoter d’autres informations de diagnostic en tirant parti de fonctionnalités supplémentaires pour descendre dans la hiérarchie des anomalies par dimension, afficher des anomalies similaires et comparer les métriques. Pour plus d’informations, consultez [Comment : diagnostiquer un incident](../how-tos/diagnose-an-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Recevoir une notification quand de nouvelles anomalies sont détectées

Si vous souhaitez être alerté quand une anomalie est détectée dans vos données, vous pouvez créer un abonnement pour une ou plusieurs de vos métriques. Metrics Advisor utilise des hooks pour envoyer des alertes. Trois types de hooks sont pris en charge : les hooks d’e-mail, les webhooks et Azure DevOps. Nous allons utiliser un webhook à titre d’exemple. 

### <a name="create-a-web-hook"></a>Créer un webhook

Un webhook est le point d’entrée permettant de faire remarquer une anomalie par programmation à partir du service Metrics Advisor, qui appelle une API fournie par l’utilisateur lorsqu’une alerte est déclenchée. Pour plus d’informations sur la façon de créer un hook, reportez-vous à la section **Créer un hook** dans [Comment : Configurer des alertes et recevoir des notifications avec un hook](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configuration des paramètres d'alerte

Après la création d’un hook, un paramètre d’alerte détermine quelles notifications envoyer et de quelle manière. Vous pouvez définir plusieurs paramètres d’alerte pour chaque métrique. Deux paramètres importants sont **Alerte pour** qui spécifie les anomalies à inclure, et **Filtrer les options d’anomalie** qui définit les anomalies à inclure dans l’alerte. Consultez la section **Ajouter ou modifier des paramètres d’alerte** dans [Comment : Configurer des alertes et obtenir des notifications à l’aide d’un hook](../how-tos/alerts.md#add-or-edit-alert-settings) pour plus d’informations.


## <a name="next-steps"></a>Étapes suivantes

- [Intégrer vos flux de données](../how-tos/onboard-your-data.md)
    - [Gérer les flux de données](../how-tos/manage-data-feeds.md)
    - [Configurations pour différentes sources de données](../data-feeds-from-different-sources.md)
- [Utiliser l’API REST ou les bibliothèques clientes](./rest-api-and-client-library.md)
- [Configurer des métriques et affiner la configuration de la détection](../how-tos/configure-metrics.md)
