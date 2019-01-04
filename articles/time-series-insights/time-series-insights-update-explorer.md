---
title: Explorateur Azure Time Series Insights (préversion) - Visualiser des données dans l’Explorateur Azure Time Series Insights (préversion) | Microsoft Docs
description: Cet article décrit les fonctionnalités et options disponibles dans l’application web Explorateur Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 5372a36291ee13966d497bdae83a6e214dce99b9
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272335"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualiser des données dans l’explorateur (préversion)

Cet article décrit les fonctionnalités et options disponibles dans l’[application web Explorateur](https://insights.timeseries.azure.com/preview/samples) Azure Time Series Insights (préversion).

## <a name="prerequisites"></a>Prérequis

Avant que vous puissiez utiliser l’Explorateur Azure Time Series Insights (préversion), vous devez :

* Configurer un environnement Time Series Insights. Pour plus d’informations, consultez [Tutoriel : Préversion d’Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* Fournir l’accès aux données à l’environnement Time Series Insights que vous avez créé pour le compte. Vous pouvez octroyer l’accès à d’autres personnes ainsi qu’à vous-même.
* Ajouter une source d’événement à l’environnement Time Series Insights pour transmettre des données vers ce dernier.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>En savoir plus sur l’Explorateur Azure Time Series Insights (préversion)

  ![explorer-one][1]

L’Explorateur Azure Time Series Insights (préversion) est composé des éléments suivants :

* **Barre de navigation** : vous permet de basculer entre les pages d’analytique et de modèle.
* **Arborescence hiérarchique** : vous permet de sélectionner des éléments de données spécifiques à convertir en graphique.
* **Zone de séries chronologiques** : affiche vos éléments de données actuellement sélectionnés.
* **Panneau Graphique** : affiche votre graphique actif.
* **Chronologie** : vous permet de modifier votre intervalle de temps actif.
* **Barre de l’application** : contient des options de gestion d’utilisateur, tel que le locataire actuel, et vous permet de modifier les paramètres de thème et de langage.

## <a name="time-series-insights-preview-environment-panel"></a>Volet de l’environnement Time Series Insights en préversion

Le panneau de l’environnement affiche tous les environnements Time Series Insights auxquels vous avez accès. La liste inclut la préversion des environnements avec paiement à l’utilisation et les environnements S1/S2 (en disponibilité générale). Cliquez simplement sur l’environnement Time Series Insights que vous souhaitez utiliser.

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Menu de navigation Time Series Insights (préversion)

  ![explorer-three][3]

Avec le menu de navigation, vous pouvez basculer entre les applications Time Series Insights :

* **Analyser** : vous permet de créer des graphiques et d’effectuer des analytiques enrichies sur vos données de série chronologique basées ou non sur un modèle.

* **Modèle** : vous permet d’envoyer les nouveaux types, hiérarchies et instances de série chronologique Time Series Insights (préversion) à votre modèle Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>Création de modèle dans Time Series Insights en préversion

Avec cette application, vous pouvez effectuer les opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur votre modèle de série chronologique.  

* **Type de modèle de série chronologique** : les types Time Series Insights vous aident à définir des variables ou des formules permettant d’effectuer des calculs. Ils sont associés à une instance Time Series Insights spécifique. Un type peut avoir une ou plusieurs variables.
* **Hiérarchies de modèle de série temporelle** : les hiérarchies sont des organisations systématiques de vos données. Elles représentent les relations entre les différentes entités dans vos données Time Series Insights.
* **Instance de modèle de série chronologique** : les instances représentent la série chronologique elle-même. Dans la plupart des cas, deviceId ou assetId est l’identificateur unique de la ressource dans l’environnement.

Pour en savoir plus le modèle de série chronologique, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Panneau de recherche de modèles de Time Series Insights en préversion

Le panneau de recherche dans les modèles vous permet de facilement rechercher et parcourir votre hiérarchie de modèles de séries chronologiques pour rechercher les instances de séries chronologiques spécifiques que vous souhaitez afficher sur votre graphique. Lorsque vous sélectionnez vos instances, elles sont également ajoutées au graphique actuel et à la zone de données.

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>Zone Time Series Insights (préversion)

La zone affiche les champs d’instance et d’autres métadonnées associées aux instances de séries chronologiques sélectionnées. Les cases à cocher à droite vous permettent de masquer ou d’afficher des instances spécifiques du graphique actuel. Vous pouvez également supprimer des éléments de données spécifiques à partir de vos données en cours en cliquant sur le contrôle représentant une croix (x) rouge à droite de l’élément.

  ![explorer-five][5]

Vous pouvez également faire ressortir le panneau de télémétrie pour obtenir une meilleure vue verticale des éléments dans vos zone de données.

  ![explorer-six][6]

> [!NOTE]
> Si vous voyez le message suivant, l’instance n’a pas de données pendant l’intervalle de temps sélectionné. Pour résoudre ce problème, vous pouvez augmenter l’intervalle de temps ou vérifier que l’instance envoie des données.
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>Graphique de la préversion de Time Series Insights

Le graphique vous permet d’afficher les instances de séries chronologiques sous forme de lignes. Vous pouvez réduire le panneau d’environnement, le modèle de données et le panneau de contrôle des intervalles de temps en cliquant sur les contrôles web afin d’agrandir le graphique.

  ![explorer-eight][8]

1. **Plage de données sélectionnée** : Contrôle quels éléments de données sont disponibles pour la visualisation.

1. **Outil de curseur de plage de données interne** : utilisez les deux contrôles d’extrémité en les faisant glisser sur l’intervalle de temps souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : réduit et développe l’éditeur du panneau d’intervalle de temps.

1. **Contrôle de format de l’axe y** : parcourt les options de vue de l’axe y disponibles :

    * `Default`: Chaque ligne comporte un axe y individuel.
    * `Stacked`: Vous permet d’empiler plusieurs lignes sur le même axe y, celui-ci étant modifié en fonction de la ligne sélectionnée.
    * `Shared`: Toutes les données de l’axe y sont affichées ensemble.

1. **Élément de données actuel** : élément de données actuellement sélectionné et détails associés.

Vous pouvez affiner une tranche de données spécifique en cliquant sur le graphique actuel, un point de données, puis en faisant glisser la zone sélectionnée vers l’extrémité de votre choix. Cliquez avec le bouton droit sur la zone grisée sélectionnée, puis cliquez sur la fonction de zoom comme indiqué dans l’image suivante :

  ![explorer-nine][9]

Une fois que vous avez effectué l’action de zoom, vous voyez votre jeu de données sélectionné. Cliquez sur le contrôle de format de l’axe y pour faire défiler les représentations de l’axe y de vos données Time Series Insights.

  ![explorer-ten][10]

Ici, vous pouvez voir un exemple d’axes y partagés :

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Panneau de l’éditeur d’intervalle de temps Time Series Insights (préversion)

Lorsque vous utilisez la préversion d’Azure Time Series Insights, vous sélectionnez tout d’abord un intervalle de temps. L’intervalle de temps sélectionné détermine le jeu de données disponible pour la manipulation avec les widgets Time Series Insights (préversion). Les contrôles web suivants sont disponibles dans la préversion d’Azure Time Series Insights pour la sélection de votre intervalle de temps actif.

  ![explorer-twelve][12]

1. **Outil de curseur de plage de dates interne** : utilisez les deux contrôles d’extrémité en les faisant glisser sur l’intervalle de temps souhaité. Cette plage de dates interne est limitée par le contrôle de curseur de plage de dates externe.

1. **Boutons pour augmenter ou diminuer la plage de dates** : augmentez ou réduisez votre intervalle de temps en sélectionnant l’un des boutons pour l’intervalle souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : ce contrôle web vous permet de masquer tous les contrôles à l’exception de l’outil de curseur de plage de dates interne.

1. **Contrôle de curseur de plage de dates externe** : utilisez les contrôles d’extrémité pour sélectionner la plage de dates externe, qui sera disponible pour votre contrôle de plage de dates interne.

1. **Liste déroulante des plages de dates rapides** : vous permet de basculer rapidement entre des intervalles de temps prédéfinis, tels que les 30 dernières minutes, les 12 dernières heures ou une plage personnalisée. La modification de cette valeur a pour effet de changer également les plages d’intervalles disponibles présentées dans l’outil de curseur de taille d’intervalle.

1. **Outil de curseur de taille d’intervalle** : vous permet d’effectuer un zoom/zoom arrière sur les intervalles pour le même intervalle de temps. Cette action permet un contrôle plus précis du déplacement entre les tranches de temps volumineuses. Elle montre les tendances lissées, jusqu’à des tranches aussi petites que la milliseconde, ce qui vous permet de voir des morceaux granulaires, haute résolution de vos données. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection, avec un équilibre entre résolution, vitesse de requête et granularité.

1. **Contrôle web de plage de dates de début et de fin** : avec ce contrôle web, vous pouvez facilement sélectionner vos plages de dates et horaires souhaitées. Vous pouvez également utiliser le contrôle pour basculer entre les différents fuseaux horaires. Une fois que vous avez apporté des modifications, pour les appliquer à votre espace de travail actuel, sélectionnez **Enregistrer**.

  ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Panneau de navigation Time Series Insights (préversion)

Le panneau de navigation Time Series Insights (préversion) offre les fonctionnalités suivantes :

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>Contrôle de lien de partage de session en cours

  ![explorer-fifteen][15]

Sélectionnez le contrôle web de lien (mis en surbrillance) pour générer une URL afin d’enregistrer ou de partager votre session de travail actuelle Azure Time Series Insights, qui inclut :

* Intervalle de temps actuellement sélectionné
* Taille d’intervalle actuellement sélectionnée
* Zone de données actuellement sélectionnée

### <a name="tenant-section"></a>Section de locataire

  ![explorer-sixteen][16]

* Affiche vos informations de compte de connexion Time Series Insights en cours.
* Vous permet de basculer entre les thèmes Time Series Insights disponibles.

### <a name="theme-selection"></a>Sélection de thème

La préversion d’Azure Time Series Insights prend en charge deux thèmes :

* **Thème clair** : thème par défaut affiché dans ce document.
* **Thème foncé** :  restitue l’Explorateur comme illustré ici :

  ![explorer-seventeen][17]

Ici, vous pouvez également choisir une des langues prises en charge.

## <a name="s1s2-environment-controls"></a>Contrôles d’environnement S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Panneau des termes Time Series Insights (préversion)

Cette section s’applique uniquement aux environnements S1/S2 existants qui tentent d’utiliser l’Explorateur dans l’interface utilisateur mise à jour. Vous pouvez utiliser le produit de la disponibilité générale et de la préversion en même temps. Nous avons ajouté certaines fonctionnalités de l’interface utilisateur existante dans l’Explorateur mis à jour, mais vous pouvez accéder à l’expérience d’interface utilisateur complète pour l’environnement S1/S2 dans l’Explorateur Time Series Insights existant.  

À la place de la hiérarchie, vous voyez le panneau des termes Time Series Insights, où vous définissez des requêtes dans votre environnement. Celui-ci vous permet de filtrer vos données selon un prédicat.

  ![explorer-eighteen][18]

Le panneau de l’éditeur de termes Time Series Insights (préversion) accepte les paramètres suivants :

**Where** : la clause Where vous permet de filtrer rapidement les événements à l’aide de l’ensemble d’opérandes répertoriés dans le tableau ci-dessous. Si vous effectuez une recherche en sélectionnant un opérande, le prédicat est automatiquement mis à jour selon cette recherche. Les types d’opérandes pris en charge comprennent les suivants :

| Opération | Types pris en charge   | Notes |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL | Tous les opérandes doivent être du même type ou être la constante NULL. |
| `HAS` | Chaîne | Seuls les littéraux de chaîne constante sont autorisés à droite. Les chaînes vides et NULL ne sont pas autorisés. |

Pour en savoir plus sur les types d’opérations et de données de requête pris en charge, consultez [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemples de clauses Where

  ![explorer-nineteen][19]

**Mesure** : cette liste déroulante affiche toutes les colonnes numériques (**Doubles**) que vous pouvez utiliser en tant qu’éléments pour votre graphique actuel.

**Diviser par** : cette liste déroulante affiche toutes les colonnes de catégories disponibles (chaînes) dans votre modèle pour le regroupement de vos données. Vous pouvez ajouter jusqu'à cinq conditions à afficher sur l’axe des abscisses. Entrez les paramètres souhaités, puis sélectionnez **Ajouter** pour ajouter un nouveau terme.

  ![explorer-twenty][20]

Vous pouvez afficher et masquer des éléments dans le panneau de graphique en sélectionnant l’icône visible, comme illustré dans l’image suivante. Vous pouvez supprimer complètement des requêtes en cliquant sur la croix rouge **x**.

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :
* [Azure Time Series Insights (préversion) - Stockage et entrée](./time-series-insights-update-storage-ingress.md)
* [Modélisation des données](./time-series-insights-update-tsm.md)
* [Diagnostics et résolution des problèmes](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
