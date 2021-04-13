---
title: Fonctionnalités avancées d’Azure Metrics Explorer
description: En savoir plus sur les utilisations avancées d’Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: 444185dc41532bfa55c271e2f0027eb8b71de42f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959899"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Fonctionnalités avancées d’Azure Metrics Explorer

> [!NOTE]
> Cet article suppose que vous connaissez les caractéristiques de base de la fonctionnalité Azure Metrics Explorer d’Azure Monitor. Si vous êtes un nouvel utilisateur et que vous souhaitez apprendre à créer votre premier graphique de métriques, consultez l’article [Prise en main d’Azure Metrics Explorer](./metrics-getting-started.md).

Dans Azure Monitor, les [métriques](data-platform-metrics.md) sont une série de valeurs mesurées et de comptages qui sont collectés et stockés au fil du temps. Les métriques peuvent être standard (également appelées « plateforme ») ou personnalisées. 

Les métriques standard sont fournies par la plateforme Azure. Elles reflètent les statistiques d’intégrité et d’utilisation de vos ressources Azure. 

## <a name="resource-scope-picker"></a>Sélecteur d’étendue des ressources
Le sélecteur d’étendue des ressources vous permet d’afficher les métriques d’une ou de plusieurs ressources. Les sections suivantes expliquent comment utiliser le sélecteur d’étendue des ressources. 

### <a name="select-a-single-resource"></a>Sélectionner une seule ressource
Sélectionnez **Métriques** dans le menu **Azure Monitor** ou dans la section **Supervision** du menu d’une ressource. Choisissez ensuite **Sélectionner une étendue** pour ouvrir le sélecteur d’étendue. 

Utilisez le sélecteur d’étendue pour sélectionner les ressources dont vous souhaitez afficher les métriques. L’étendue doit être remplie si vous avez ouvert Azure Metrics Explorer à partir du menu d’une ressource. 

![Capture d’écran montrant comment ouvrir le sélecteur d’étendue des ressources.](./media/metrics-charts/scope-picker.png)

Pour certaines ressources, vous pouvez afficher uniquement les métriques d’une seule ressource à la fois. Dans le menu **Types de ressources**, ces ressources se trouvent dans la section **Tous les types de ressources**.

![Capture d’écran montrant une seule ressource.](./media/metrics-charts/single-resource-scope.png)

Après avoir sélectionné une ressource, vous voyez tous les abonnements et groupes de ressources qui contiennent cette ressource.

![Capture d’écran montrant les ressources disponibles.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Si vous souhaitez avoir la possibilité de visualiser les métriques de plusieurs ressources en même temps ou visualiser les métriques d’un abonnement ou d’un groupe de ressources, sélectionnez **Voter**.

Lorsque vous êtes satisfait de votre sélection, sélectionnez **Appliquer**.

### <a name="view-metrics-across-multiple-resources"></a>Afficher les métriques de plusieurs ressources
Certains types de ressources peuvent interroger des métriques sur plusieurs ressources. Les ressources doivent se trouver dans le même abonnement et au même emplacement. Recherchez ces types de ressources en haut du menu **Types de ressources**. 

Pour plus d’informations, consultez [Sélectionner plusieurs ressources](./metrics-dynamic-scope.md#select-multiple-resources).

![Capture d’écran montrant les types de ressources croisées.](./media/metrics-charts/multi-resource-scope.png)

Pour les types qui sont compatibles avec plusieurs ressources, vous pouvez interroger les métriques sur un abonnement ou plusieurs groupes de ressources. Pour plus d’informations, consultez [Sélectionner un groupe de ressources ou un abonnement](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Plusieurs courbes et graphiques de métriques

Dans Azure Metrics Explorer, vous pouvez créer des graphiques constitués de plusieurs courbes de métriques ou afficher plusieurs graphiques de métriques à la fois. Cette fonctionnalité vous permet de :

- Mettre en corrélation les métriques associées sur le même graphique pour voir comment une valeur est liée à une autre.
- Afficher les métriques qui utilisent différentes unités de mesure à proximité les unes des autres.
- Agréger visuellement et comparer les métriques de plusieurs ressources.

Par exemple, imaginez que vous avez cinq comptes de stockage et que vous souhaitez connaître l’espace qu’ils consomment ensemble. Vous pouvez créer un graphique en aires (empilé) qui affiche les valeurs individuelles et la somme de toutes les valeurs à des moments précis dans le temps.

### <a name="multiple-metrics-on-the-same-chart"></a>Plusieurs métriques sur le même graphique

Pour afficher plusieurs métriques sur le même graphique, commencez par [créer un graphique](./metrics-getting-started.md#create-your-first-metric-chart). Sélectionnez ensuite **Ajouter une métrique**. Répétez cette étape pour ajouter une autre métrique sur le même graphique.

> [!NOTE]
> En règle générale, vos graphiques ne doivent pas mélanger les métriques qui utilisent des unités de mesure différentes. Par exemple, évitez de mélanger une mesure qui utilise les millisecondes avec une autre qui utilise les kilo-octets. Évitez également de mélanger les métriques dont les échelles diffèrent de manière significative. 
>
> Dans ce cas, envisagez plutôt d’utiliser plusieurs graphiques. Dans Metrics Explorer, sélectionnez **Ajouter un graphique** pour créer un graphique.

### <a name="multiple-charts"></a>Plusieurs graphiques

Pour créer un autre graphique qui utilise une métrique différente, sélectionnez **Ajouter un graphique**.

Pour réorganiser ou supprimer plusieurs graphiques, sélectionnez le bouton de sélection ( **…** ) pour ouvrir le menu du graphique. Choisissez ensuite **Déplacer vers le haut**, **Déplacer vers le bas** ou **Supprimer**.

## <a name="time-range-controls"></a>Contrôles d'intervalle de temps

En plus de la modification de l’intervalle de temps à l’aide du [panneau de sélecteur d’heure](metrics-getting-started.md#select-a-time-range), vous pouvez également effectuer un panoramique et un zoom à l’aide des contrôles de la zone de graphique.
### <a name="pan"></a>Panoramique

Pour effectuer un panoramique, cliquez sur les flèches gauche et droite sur le bord du graphique.  Cette opération déplace l’intervalle de temps sélectionné vers l’arrière et vers l’avant d’une moitié de l’intervalle de temps du graphique.  Par exemple, si vous affichez les dernières 24 heures, le fait de cliquer sur la flèche gauche entraîne le décalage de l’intervalle de temps pour s’étendre sur un jour et demi à 12 heures.

La plupart des métriques prennent en charge 93 jours de rétention, mais ne vous permettent d’afficher que 30 jours à la fois.  À l’aide des contrôles panoramiques, vous pouvez examiner les 30 derniers jours, puis vous retrouver dans les 15 jours à la fois pour afficher le reste de la période de rétention.

![Image GIF animée montrant les contrôles panoramiques gauche et droit.](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

Vous pouvez cliquer et faire glisser sur le graphique pour effectuer un zoom sur une section d’un graphique.  Le zoom met à jour l’intervalle de temps du graphique pour s’étendre à votre sélection et sélectionne un plus petit fragment de temps si celui-ci est défini sur « Automatique ».  Le nouvel intervalle de temps s’applique à tous les graphiques dans Métriques.

![Image GIF animée montrant la fonctionnalité de zoom des métriques.](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation"></a>Agrégation

Lorsque vous ajoutez une métrique à un graphique, Metrics Explorer applique automatiquement une agrégation par défaut. La valeur par défaut a du sens dans les scénarios de base. Toutefois, vous pouvez utiliser une agrégation différente pour obtenir plus d’informations sur la métrique. 

Avant d’utiliser différentes agrégations sur un graphique, vous devez comprendre comment Metrics Explorer les gère. Les métriques sont une série de mesures (ou « valeurs métriques ») capturées au cours d’une période donnée. Lorsque vous tracez un graphique, les valeurs de la métrique sélectionnée sont agrégées séparément sur le *fragment de temps*. 

Vous sélectionnez la taille du fragment de temps à l’aide du [panneau du sélecteur d’heure](./metrics-getting-started.md#select-a-time-range) de Metrics Explorer. Si vous ne sélectionnez pas explicitement le fragment de temps, l’intervalle de temps actuellement sélectionné est utilisé par défaut. Une fois que le fragment de temps est déterminé, les valeurs métriques qui ont été capturées pendant chaque fragment de temps sont agrégées sur le graphique, un point de données par fragment de temps.

Par exemple, supposons qu’un graphique indique la métrique *Temps de réponse du serveur*. Il utilise l’agrégation *Moyenne* sur la période des *dernières 24 heures*. Dans cet exemple :

- Si la granularité temporelle est définie sur 30 minutes, le graphique est dessiné à partir de 48 points de données agrégés. Autrement dit, le graphique en courbes connecte 48 points dans la zone de traçage du graphique (24 heures x 2 points de données par heure). Chaque point de données représente la *moyenne* de tous les temps de réponse capturés pour les requêtes au serveur qui se sont produites pendant chacune des périodes de 30 minutes pertinentes.
- Si vous réglez la granularité temporelle à 15 minutes, vous obtenez 96 points de données agrégés.  C’est-à-dire que vous obtenez 24 heures x 4 points de données par heure.

Metrics Explorer propose cinq types d’agrégation statistique de base : somme, nombre, min., max. et moyenne. L’agrégation *Somme* est parfois appelée l’agrégation *Total*. Pour de nombreuses métriques, Metrics Explorer masque les agrégations qui ne sont pas pertinentes et ne peuvent pas être utilisées.

* **Sum** : Somme de toutes les valeurs capturées pendant l’intervalle d’agrégation.

    ![Capture d’écran d’une requête Somme.](./media/metrics-charts/request-sum.png)

* **Nombre** : Nombre de mesures capturées pendant l’intervalle d’agrégation. 
    
    Lorsque la métrique est toujours capturée avec la valeur 1, l’agrégation Nombre est égale à l’agrégation Somme. Ce scénario est courant lorsque la métrique suit le nombre d’événements distincts et que chaque mesure représente un événement. Le code émet un enregistrement de métrique à chaque fois qu’une nouvelle requête arrive.

    ![Capture d’écran d’une requête Nombre.](./media/metrics-charts/request-count.png)

* **Average** : Moyenne des valeurs métriques capturées pendant l’intervalle d’agrégation.

    ![Capture d’écran d’une requête Moyenne.](./media/metrics-charts/request-avg.png)

* **Min** : Plus petite valeur capturée pendant l’intervalle d’agrégation.

    ![Capture d’écran d’une requête Min.](./media/metrics-charts/request-min.png)

* **Max** : Plus grande valeur capturée pendant l’intervalle d’agrégation.

    ![Capture d’écran d’une requête Max.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtres

Vous pouvez appliquer des filtres aux graphiques dont les métriques ont des dimensions. Par exemple, imaginez une métrique « Nombre de transactions » qui a une dimension « Type de réponse ». Cette dimension indique si la réponse des transactions a réussi ou échoué. Si vous filtrez sur cette dimension, vous verrez une courbe de graphique uniquement pour les transactions ayant réussi (ou uniquement celles ayant échoué). 

### <a name="add-a-filter"></a>Ajouter un filtre

1. Au-dessus du graphique, sélectionnez **Ajouter un filtre**.

2. Sélectionnez une dimension (propriété) sur laquelle filtrer.

   ![Capture d’écran montrant les dimensions (propriétés) que vous pouvez filtrer.](./media/metrics-charts/028.png)

3. Sélectionnez l’opérateur que vous souhaitez appliquer à la dimension (propriété). L'opérateur par défaut est = (égal à).

   ![Capture d’écran représentant l’opérateur que vous pouvez utiliser avec le filtre.](./media/metrics-charts/filter-operator.png)

4. Sélectionnez les valeurs de dimension que vous souhaitez appliquer au filtre lors du traçage du graphique (cet exemple illustre le filtrage des transactions de stockage qui ont réussi) :

   ![Capture d’écran montrant les transactions de stockage filtrées ayant réussi.](./media/metrics-charts/029.png)

5. Après avoir sélectionné les valeurs de filtre, cliquez à côté du sélecteur de filtre pour fermer celui-ci. Le graphique montre à présent le nombre de transactions de stockage qui ont échoué :

   ![Capture d’écran montrant le nombre de transactions de stockage ayant échoué.](./media/metrics-charts/030.png)

6. Vous pouvez répéter les étapes 1 à 5 pour appliquer plusieurs filtres aux mêmes graphiques.


## <a name="metric-splitting"></a>Fractionnement des métriques

Vous pouvez fractionner une métrique par dimension pour visualiser les différents segments de la comparaison des métriques. Le fractionnement peut également vous aider à identifier les segments périphériques d’une dimension.

### <a name="apply-splitting"></a>Appliquer la division

1. Au-dessus du graphique, sélectionnez **Appliquer un fractionnement**.
 
   > [!NOTE]
   > Les graphiques qui ont plusieurs métriques ne peuvent pas utiliser la fonctionnalité de fractionnement. De même, bien qu’un graphique puisse avoir plusieurs filtres, il ne peut avoir qu’une seule dimension de fractionnement.

2. Choisissez une dimension sur laquelle segmenter le graphique :

   ![Capture d’écran montrant la dimension sélectionnée sur laquelle segmenter le graphique.](./media/metrics-charts/031.png)

   Le graphique montre à présent plusieurs courbes, une par segment de dimension :

   ![Capture d’écran montrant plusieurs lignes, une pour chaque segment de dimension.](./media/metrics-charts/segment-dimension.png)
   
3. Choisissez une limite sur le nombre de valeurs à afficher après le fractionnement par dimension sélectionnée. La limite par défaut est 10, comme indiqué dans le graphique ci-dessus. La plage de limites est compris de 1 à 50.
   
   ![Capture d’écran montrant la limite de fractionnement, qui limite le nombre de valeurs après le fractionnement.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Choisissez l’ordre de tri sur les segments : Croissant ou Décroissant. Décroissant est sélectionné par défaut.
   
   ![Capture d’écran montrant l’ordre de tri des valeurs de fractionnement.](./media/metrics-charts/segment-dimension-sort.png)

5. Cliquez à côté du **sélecteur de regroupement** pour fermer celui-ci.
   

   > [!NOTE]
   > Pour masquer les segments non pertinents pour votre scénario et faciliter la lecture des graphiques, utilisez un filtrage et un fractionnement sur la même dimension.

## <a name="locking-the-range-of-the-y-axis"></a>Verrouillage de la plage de l’axe Y

Le verrouillage de la plage de l’axe des valeurs (Y) devient important dans les graphiques qui montrent de petites variations de valeurs élevées. 

Par exemple, une chute du volume de requêtes réussies de 99,99 % à 99,5 % peut correspondre à une baisse significative de la qualité de service. Toutefois, il est très difficile, voire impossible, de remarquer une légère fluctuation de la valeur numérique si vous utilisez les paramètres par défaut du graphique. Dans ce cas, vous pouvez verrouiller la limite inférieure du graphique à 99 %. pour rendre une petite baisse plus évidente. 

Un autre exemple est une fluctuation de la mémoire disponible. Dans ce scénario, la valeur n’atteint techniquement jamais 0. Si la plage est définie sur une valeur plus élevée, vous pouvez repérer plus facilement les baisses de la mémoire disponible. 

Pour contrôler la plage de l’axe Y, ouvrez le menu du graphique ( **…** ). Sélectionnez ensuite **Paramètres du graphique** pour accéder aux paramètres avancés du graphique.

![Capture d’écran mettant en évidence la sélection des paramètres du graphique.](./media/metrics-charts/033.png)

Modifiez les valeurs de la section de la **plage de l’axe Y**, ou sélectionnez **Auto** pour rétablir les valeurs par défaut.
 
 ![Capture d’écran mettant en évidence la section de la plage de l’axe Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Si vous devez verrouiller les limites de l’axe Y pour les graphiques qui suivent des nombres ou des sommes sur une période donnée (en utilisant les agrégations Nombre, Somme, Min. ou Max.), vous devez généralement spécifier une granularité temporelle fixe. Dans ce cas, vous ne devez pas vous fier aux valeurs par défaut automatiques. 
>
> Vous choisissez une granularité temporelle fixe parce que les valeurs du graphique changent lorsque la granularité temporelle est automatiquement modifiée après qu’un utilisateur redimensionne une fenêtre de navigateur ou change la résolution de l’écran. La modification de la granularité temporelle qui en découle influe sur l’apparence du graphique et invalide la sélection actuelle de la plage de l’axe Y.

## <a name="line-colors"></a>Couleurs des courbes

Une fois les graphiques configurés, une couleur est automatiquement attribuée à leurs lignes à partir d'une palette par défaut. Vous pouvez modifier ces couleurs.

Pour changer la couleur d’une courbe, sélectionnez la barre de couleur dans la légende qui correspond au graphique. La boîte de dialogue du sélecteur de couleurs s’ouvre. Utilisez le sélecteur de couleurs pour configurer la couleur de la courbe.

![Capture d’écran montrant comment modifier la couleur.](./media/metrics-charts/035.png)

Vos couleurs personnalisées sont conservées lorsque vous épinglez le graphique à un tableau de bord. La section suivante explique comment épingler un graphique.

## <a name="pinning-to-dashboards"></a>Épinglage à des tableaux de bord 

Après avoir configuré un graphique, vous souhaiterez peut-être l’ajouter à un tableau de bord. En épinglant un graphique à un tableau de bord, vous pouvez le rendre accessible à votre équipe. Vous pouvez également obtenir des informations en le visualisant dans le contexte d’autres données de télémétrie de surveillance.

Pour épingler un graphique configuré à un tableau de bord, dans le coin supérieur droit du graphique, sélectionnez **Épingler au tableau de bord**.

![Capture d’écran montrant comment épingler un graphique à un tableau de bord.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Règles d'alerte

Vous pouvez utiliser vos critères de visualisation pour créer une règle d’alerte basée sur les métriques. La nouvelle règle d’alerte inclura les dimensions cibles de ressources, de métriques, de fractionnement et de filtres de votre graphique. Vous pouvez modifier ces paramètres à l’aide du volet de création de règles d’alerte.

Pour commencer, sélectionnez **Nouvelle règle d’alerte**.

![Capture d’écran montrant le bouton Nouvelle règle d’alerte encadré en rouge.](./media/metrics-charts/042.png)

Le volet de création de règles d’alerte s’ouvre. Dans le volet, vous voyez les dimensions de métrique du graphique. Les champs du volet sont préremplis pour vous aider à personnaliser la règle.

![Capture d’écran montrant le volet de création de règles.](./media/metrics-charts/041.png)

Pour plus d’informations, consultez [Créer, afficher et gérer des alertes de métrique](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Mettre en corrélation des métriques et des journaux
Pour aider les clients à diagnostiquer la cause racine d’anomalies dans leur graphique des métriques, nous avons créé une fonctionnalité d’exploration des journaux. L’exploration des journaux permet aux clients de mettre en corrélation des pics dans leur graphique des métriques avec des journaux et des requêtes. 

Avant de nous plonger dans l’expérience, nous souhaitons commencer par présenter les différents types de journaux et requêtes fournis. 

| Terme             | Définition  | 
|------------------|-------------|
| Journaux d’activité    | Fournit des informations sur les opérations effectuées sur chaque ressource Azure dans l’abonnement à partir de l’extérieur (plan de gestion) en plus des mises à jour sur des événements Service Health. Le journal d’activité vous permet de déterminer le quoi, le qui et le quand de toute opération d’écriture (PUT, POST, DELETE) effectuée sur les ressources dans votre abonnement. Il n’y qu’un seul journal d’activité par abonnement Azure.  |   
| Journal de diagnostic   | Fournissent des insights sur les opérations effectuées au sein d’une ressource Azure (le plan de données), par exemple l’obtention d’un secret à partir d’un Key Vault ou l’envoi d’une requête à une base de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource. **Remarque :** doit être fourni par le service et activé par le client  | 
| Journal recommandé | Requêtes basées sur un scénario que le client peut exploiter pour examiner des anomalies dans son explorateur de métriques.  |

Actuellement, la fonctionnalité d’exploration des journaux est disponible pour des fournisseurs de ressources sélectionnés. Les fournisseurs de ressources qui ont accès à l’expérience complète d’exploration des journaux sont les suivants : 

* Application Insights 
* Mise à l’échelle automatique 
* App Services  
* Stockage  

Voici un exemple d’expériences pour le fournisseur de ressources Application Insights.

![Pic d’échecs dans le panneau des métriques d’Application Insights](./media/metrics-charts/drill-into-log-ai.png)

Pour diagnostiquer le pic de demandes ayant échoué, cliquez sur « Drill into Logs » (Explorer les journaux).

![Capture d’écran de la liste déroulante d’exploration des journaux](./media/metrics-charts/drill-into-logs-dropdown.png)

En cliquant sur l’option d’échec, vous êtes dirigé vers un panneau d’échec personnalisé qui affiche les opérations qui ont échoué, les principaux types d’exceptions et les dépendances. 

![Capture d’écran du panneau d’échec d’Application Insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Problèmes courants liés à l’exploration des journaux

* Le journal et les requêtes sont désactivés : pour afficher les journaux et les requêtes recommandés, vous devez router vos journaux de diagnostic vers Log Analytics. Pour découvrir comment procéder, lisez [ce document](./diagnostic-settings.md). 
* Les journaux d’activité sont uniquement fournis : la fonctionnalité d’exploration des journaux n’est disponible que pour des fournisseurs de ressources sélectionnés. Par défaut, les journaux d’activité sont fournis. 

 
## <a name="troubleshooting"></a>Dépannage

Si vous ne voyez aucune donnée sur votre graphique, passez en revue les informations de dépannage suivantes :

* Les filtres s’appliquent à tous les graphiques du volet. Lorsque vous vous concentrez sur un graphique, vérifiez que vous n’avez pas défini un filtre qui exclut toutes les données d’un autre graphique.

* Pour définir des filtres différents sur différents graphiques, créez les graphiques dans différents panneaux. Enregistrez ensuite les graphiques en tant que favoris distincts. Si vous le souhaitez, vous pouvez épingler les graphiques au tableau de bord pour les voir ensemble.

* Si vous segmentez un graphique par une propriété que la métrique ne définit pas, le graphique n’affiche aucun contenu. Essayez d’effacer la segmentation (fractionnement) ou sélectionnez une autre propriété.

## <a name="next-steps"></a>Étapes suivantes

Pour créer des tableaux de bord exploitables à l’aide des métriques, consultez [Création de tableaux de bord d’indicateurs de performance clés personnalisés](../app/tutorial-app-dashboards.md).