---
title: Impact sur l’utilisation d’Application Insights - Azure Monitor
description: Analysez comment les différentes propriétés peuvent impacter les taux de conversion de certaines parties de vos applications.
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: 0d09f93c5a0a541929169130feb921a8e65144be
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133231"
---
# <a name="impact-analysis-with-application-insights"></a>Analyse de l’impact avec Application Insights

Impact analyse comment la durée de chargement et d’autres propriétés influencent les taux de conversion de diverses parties de votre application. Plus précisément, il indique comment **n’importe quel élément** d’un **mode Page**, d’un **événement personnalisé** ou d’une **requête** affecte l’utilisation d’un autre **mode Page** ou d’un autre **événement personnalisé**. 

## <a name="still-not-sure-what-impact-does"></a>Vous ne savez toujours pas vraiment ce que fait Impact ?

Une manière d’envisager Impact est d’y voir l’outil parfait pour régler les litiges avec une personne de votre équipe à propos de la lenteur de certains aspects de votre site et de son impact sur la durée d’utilisation. Alors que les utilisateurs peuvent tolérer une certaine lenteur, Impact vous donne un aperçu de comment mieux équilibrer l’optimisation et les performances afin de maximiser la conversion des utilisateurs.

Mais l’analyse des performances n’est qu’une partie de ce que peut faire Impact. Étant donné qu’Impact prend en charge les événements personnalisés et les dimensions, répondre à des questions telles que « en quoi le choix du navigateur est corrélé au taux de conversion » se fait en seulement quelques clics.

> [!NOTE]
> Votre ressource Application Insights doit contenir des pages consultées ou des événements personnalisés pour pouvoir utiliser le classeur d’analyse d’impact. [Découvrez comment configurer votre application pour collecter des vues de page automatiquement à l’aide du Kit de développement logiciel (SDK) JavaScript Application Insights](./javascript.md). Gardez également à l’esprit que, dans la mesure où vous analysez une corrélation, la taille de l’échantillon est importante.

## <a name="impact-analytics-workbook"></a>Classeur d’analyse d’impact 

Pour utiliser le classeur d’analyse d’impact, dans vos ressources Application Insights, accédez à **Usage** > **Impact** et sélectionnez **Classeur d’analyse d’impact**. Ou dans l’onglet **Classeurs**, sélectionnez **Modèles publics**, puis **Analyse d’impact utilisateur** sous *Usage*.

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="Capture d’écran de la galerie de classeurs sur les modèles publics." lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>Utilisation du classeur

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="Capture d’écran montrant où choisir une page consultée, un événement personnalisé ou une requête." lightbox="./media/usage-impact/selected-event.png":::

1. Sélectionnez un événement dans la liste déroulante **Événement sélectionné**.
2. Sélectionnez une métrique dans la liste déroulante **analyser**.
3. Sélectionnez un événement dans la liste déroulante **Événement impactant**. 
1. Si vous voulez ajouter un filtre, faites-le dans **Ajouter les filtres d’événements sélectionnés** et/ou **Ajouter des filtres d’événements impactants**.


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Le temps de chargement des pages affecte-t-il le nombre de personnes qui deviennent clients de ma page ?

Pour commencer à répondre aux questions avec le classeur Impact, choisissez d’abord un mode Page, un événement personnalisé ou une requête.

1. Sélectionnez un événement dans la liste déroulante **Événement sélectionné**.
2. Laissez la liste déroulante d’**analyse** sur la sélection par défaut : **Durée** (dans ce contexte, la valeur **Durée** correspond au **temps de chargement de la page**).
3. Dans la liste déroulante indiquant l’**élément d’impact**, sélectionnez un événement personnalisé. Cet événement doit correspondre à un élément d’interface utilisateur du mode Page que vous avez sélectionné à l’étape 1.


:::image type="content" source="./media/usage-impact/impact.png" alt-text="La capture d’écran montre un exemple avec l’événement sélectionné comme page d’accueil analysée par durée." lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Que se passe-t-il si je suis les modes Page ou le temps de chargement de manière personnalisée ?

Impact prend en charge les propriétés et les mesures standard et personnalisées. Utilisez ce que vous souhaitez. Au lieu de la durée, utilisez des filtres sur les événements principaux et secondaires pour obtenir des données plus spécifiques.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Le taux de conversion des utilisateurs varie-t-il en fonction du pays ou de la région ?

1. Sélectionnez un événement dans la liste déroulante **Événement sélectionné**.
2. Choisissez « Pays ou région » dans la liste déroulante d’**analyse**.
3. Dans la liste déroulante indiquant l’**élément d’impact**, sélectionnez un événement personnalisé qui correspond à un élément d’interface utilisateur du mode Page que vous avez choisi à l’étape 1.

:::image type="content" source="./media/usage-impact/regions.png" alt-text="Capture d’écran d’un exemple avec l’événement sélectionné en tant que GET analysée par pays et région." lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>Comment le classeur d’analyse d’impact calcule-t-il ces taux de conversion ?

En coulisses, le classeur d’analyse d’impact repose sur le [coefficient de corrélation de Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Les résultats calculés sont compris entre -1 et 1, -1 correspondant à une corrélation linéaire négative, et 1 à une corrélation linéaire positive.

La répartition de base du fonctionnement de l’analyse d’Impact se présente comme suit :

Laissez _A_ = mode Page/événement personnalisé/requête principal(e) sélectionné(e) dans la première liste déroulante. (**Événement sélectionné**).

Laissez _B_ = mode Page/événement personnalisé secondaire sélectionné (**a un impact sur l’utilisation de**).

Impact examine un échantillon de toutes les sessions des utilisateurs sur la période sélectionnée. Pour chaque session, il recherche toutes les occurrences de _A_.

Les sessions sont ensuite réparties en deux types de _sous-sessions_ selon l’une des deux conditions suivantes :

- Une sous-session convertie se compose d’une session se terminant par un événement _B_ et englobe tous les événements _A_ qui se produisent avant _B_.
- Une sous-session non convertie est créée lorsque tous les _A_ se produisent sans un _B_ final.

Le calcul effectué par Impact dépend au final de l’analyse : par métrique ou par dimension. Pour les métriques, la moyenne de tous les _A_ d’une sous-session est calculée. Au contraire, pour les dimensions, la valeur de chaque _A_ contribue à hauteur de _1/N_ à la valeur attribuée à _B_, où _N_ est le nombre de _A_ dans la sous-session.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les classeurs, consultez [la vue d’ensemble des classeurs](../visualize/workbooks-overview.md).
- Pour activer les expériences d’utilisation, commencez à envoyer des [événements personnalisés](./api-custom-events-metrics.md#trackevent) ou des [affichages de page](./api-custom-events-metrics.md#page-views).
- Si vous envoyez déjà des événements personnalisés ou des affichages de page, explorez les outils d’utilisation pour savoir comment les utilisateurs emploient votre service.
    - [Entonnoirs](usage-funnels.md)
    - [Rétention](usage-retention.md)
    - [Flux d’utilisateurs](usage-flows.md)
    - [Classeurs](../visualize/workbooks-overview.md)
    - [Ajouter du contexte utilisateur](./usage-overview.md)