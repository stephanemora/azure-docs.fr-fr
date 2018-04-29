---
title: Azure Application Insights Usage Cohorts | Microsoft docs
description: Analyser différents jeux d’utilisateurs, sessions, événements ou opérations qui partagent un point commun
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights Cohorts

Une cohorte est un ensemble d’utilisateurs, de sessions, d’événements ou d’opérations qui partagent un point commun. Dans Azure Application Insights, les cohortes sont définies par une requête Analytics. Si vous analysez un ensemble d’utilisateurs ou d’événements à plusieurs reprises, les cohortes vous offrent davantage de souplesse pour exprimer précisément ce qui vous intéresse.

![Volet des cohortes](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Cohortes ou filtres de base

Si les cohortes sont utilisées comme les filtres, leur création à partir de requêtes Analytics personnalisées les rend beaucoup plus adaptables et complexes. Contrairement aux filtres, vous pouvez enregistrer des cohortes afin d’autres membres de votre équipe puissent les réutiliser.

Vous pouvez définir une cohorte d’utilisateurs qui ont tous testé une nouvelle fonctionnalité de votre application. En enregistrant cette cohorte dans votre ressource Application Insights, un clic suffit pour analyser ce groupe d’utilisateurs dans le futur.

> [!NOTE]
> Une fois créées, les cohortes sont disponibles dans les outils Utilisateurs, Sessions, Événements et Flux d’utilisateurs.

## <a name="example-engaged-users"></a>Exemple : utilisateurs engagés

Votre équipe définit un utilisateur engagé comme une personne qui utilise votre application au moins cinq fois pendant un mois donné. Nous allons définir une cohorte de ces utilisateurs engagés.

1. Ouvrez l’outil **Cohorts**.

2. Cliquez sur l’onglet **Galerie des modèles**. Vous y trouverez des modèles pour différentes cohortes.

3. Choisissez **Engaged Users** – by Days Used (Utilisateurs engagés – par jours utilisés).

    Cette cohorte a trois paramètres :
      * **Activities (Activités)** : permet de choisir les événements et les pages consultées qui doivent être comptabilisés dans l’utilisation.
      * **Period (Période)** : définition d’un mois.
      * **UsedAtleastCustom** : nombre de fois où il doit utiliser un élément pendant une période pour être comptabilisé comme utilisateur engagé.

4. Sélectionnez « 5+ days (+ de 5 jours) » dans **UsedAtleastCustom** et conservez la valeur par défaut de 28 jours dans **Period (Période)**.

    ![Image](.\media\app-insights-usage-cohorts\003.png)

    Maintenant, cette cohorte représente tous les ID d’utilisateur qui ont été envoyés avec un événement personnalisé ou une page consultée, pendant cinq jours durant les 28 derniers jours.

5. Cliquez sur **Enregistrer**.

   > [!TIP]
   >  Attribuez à votre cohorte un nom du type « Utilisateurs engagés (+ de 5 jours) et enregistrez-la dans « Mes rapports » ou « Rapports partagés » selon que vous souhaitiez ou non autoriser d’autres personnes ayant accès à cette ressource Application Insights à voir cette cohorte.

6. Cliquez sur **Back to Gallery (Retour à la galerie)**.

### <a name="what-can-you-do-with-this-cohort"></a>Que pouvez-vous faire avec cette cohorte ?

Ouvrez l’outil **Utilisateurs**. Dans le menu déroulant **Afficher**, puis choisissez la cohorte que vous avez créée sous **Users who belong to… (Utilisateurs appartenant à...)**.

Maintenant, l’outil Utilisateurs est filtré sur cette cohorte d’utilisateurs :

![Volet Utilisateurs filtré sur une cohorte particulière](.\media\app-insights-usage-cohorts\004.png)

Voici quelques points importants à prendre en considération :
   * Vous n’auriez jamais pu créer un tel ensemble avec des filtres normaux. La logique de date est plus perfectionnée.
   * Vous pouvez filtrer davantage cette cohorte à l’aide des filtres normaux dans l’outil Utilisateurs. Même si la cohorte est définie sur des périodes de 28 jours, vous pouvez régler l’intervalle sur 30, 60 ou 90 jours dans l’outil Utilisateurs. 

Cela vous permet de poser des questions plus complexes, du type _Comment les personnes engagées dans les 28 derniers jours se sont-elles comportées au cours des 60 derniers jours ?_, qui seraient impossibles à exprimer avec le générateur de requêtes.

## <a name="example-events-cohort"></a>Exemple : cohorte d’événements

Vous pouvez également créer des cohortes d’événements. Nous allons définir une cohorte des événements et des pages consultées, puis voir comment les utiliser dans les autres outils. Elle peut être utile pour définir un ensemble d’événements que votre équipe considère comme une _utilisation active_, ou des événements liés à une nouvelle fonctionnalité.

1. Ouvrez l’outil **Cohorts**.

2. Cliquez sur l’onglet **Galerie des modèles**. Vous y trouverez des modèles pour différentes cohortes.

3. Choisissez **Events Picker (Sélecteur d’événements)**.

    ![Capture d’écran d’Events Picker (Sélecteur d’événements)](.\media\app-insights-usage-cohorts\006.png)

4. Dans le menu déroulant **Activities (Activités)**, sélectionnez les événements que vous souhaitez inclure dans la cohorte.

5. Enregistrez la cohorte et attribuez-lui un nom.

## <a name="example-active-users-where-you-modify-query"></a>Exemple : utilisateurs actifs dont vous modifiez la requête

Les deux cohortes précédentes ont été définies à l’aide des menus déroulants. Mais nous pouvons également définir des cohortes avec des requêtes Analytics pour une flexibilité totale. Voyons comment faire en créant une cohorte d’utilisateurs du Royaume-Uni.

![Présentation animée de l’utilisation de l’outil Cohorts](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Ouvrez l’outil **Cohorts**, cliquez sur l’onglet **Galerie des modèles**, puis choisissez **Cohorte d’utilisateurs vide**.

    ![Cohorte d’utilisateurs vide](.\media\app-insights-usage-cohorts\001.png)

    Les sections sont au nombre de trois :
       * Une section de texte Markdown, où vous pouvez décrire la cohorte plus en détail pour d’autres personnes de votre équipe.

       * Une section de paramètres que vous pouvez utiliser pour créer vos propres paramètres, tels que les **activités** et les autres menus déroulants des deux exemples précédents.

       * Une section de requête que vous utilisez pour définir la cohorte à l’aide d’une requête Analytics.

    Dans la section de la requête, vous [rédigez une requête Analytics](https://docs.loganalytics.io/index) qui sélectionne les lignes qui décrivent la cohorte que vous souhaitez définir. Ensuite, l’outil Cohorts ajoute implicitement une clause « | summarize by user_Id » à la requête. Cette information s’affiche sous la requête dans un tableau, pour vous assurer que votre requête renvoie des résultats.

    > [!NOTE]
    > Si la requête n’apparaît pas, essayez de redimensionner la section pour l’agrandir et afficher la requête. Le fichier GIF animé, au début de cette section, illustre le comportement de redimensionnement.

2. Copiez-collez le texte suivant dans l’éditeur de requête :

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Cliquez sur **Exécuter la requête**. L’ID utilisateur doit s’afficher dans le tableau. Si tel n’est pas le cas, sélectionnez un pays où votre application a des utilisateurs.

4. Enregistrez la cohorte et attribuez-lui un nom.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

_J’ai défini une cohorte d’utilisateurs d’un certain pays. Lorsque je la compare dans l’outil Utilisateurs à la définition d’un filtre sur ce pays dans ce même outil, j’obtiens des résultats différents. Pourquoi ?_

Les cohortes et les filtres sont différents. Supposons que vous ayez une cohorte d’utilisateurs du Royaume-Uni (définie comme dans l’exemple ci-dessus) et que vous compariez ses résultats à ceux obtenus avec le filtre « Country or region = United Kingdom (Pays ou région = Royaume-Uni) ».

* La version cohorte affiche tous les événements des utilisateurs qui ont envoyé au moins un événement du Royaume-Uni pendant la période. Si vous fractionnez les données par pays ou région, vous verrez probablement de nombreux pays et régions.
* La version filtre n’affiche que les événements du Royaume-Uni. Si vous fractionnez les données par pays ou région, vous ne verrez que le Royaume-Uni.

## <a name="learn-more"></a>En savoir plus
- [Langage de requête Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
- [Utilisateurs, sessions, événements](app-insights-usage-segmentation.md)
- [Flux d’utilisateurs](app-insights-usage-flows.md)
- [Vue d’ensemble de l’utilisation](app-insights-usage-overview.md)