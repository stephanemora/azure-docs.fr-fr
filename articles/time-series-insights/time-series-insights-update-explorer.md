---
title: Visualiser les données dans l’explorateur Azure Time Series Insights en préversion | Microsoft Docs
description: Cet article décrit les fonctionnalités et options disponibles dans l’application web Explorateur Azure Time Series Insights (préversion).
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399873"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualiser des données dans l’explorateur (préversion)

Ce document décrit l’interface utilisateur, les fonctionnalités d’expérience utilisateur et l’interface la préversion de l’[application web de démonstration](https://insights.timeseries.azure.com/preview/demo) Azure Time Series Insights. Plus précisément, il aborde la disposition de l’exemple hébergé, les options de personnalisation de l’interface et la navigation dans la démonstration fournie.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer à utiliser la préversion de l’Explorateur Azure Time Series Insights, vous devez :

* Configurer un environnement Time Series Insights. Pour en savoir plus sur l’approvisionnement d’une instance, essayez le didacticiel [Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* [Fournir l’accès aux données](./time-series-insights-data-access.md) à l’environnement Time Series Insights que vous avez créé pour le compte. Vous pouvez octroyer l’accès à d’autres personnes ainsi qu’à vous-même.
* Ajouter une source d’événement à l’environnement Time Series Insights pour transmettre des données vers ce dernier :
  * Découvrez [comment vous connecter à un Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Découvrez [comment vous connecter à un Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>En savoir plus sur l’Explorateur en préversion

L’Explorateur Azure Time Series Insights (préversion) est composé des éléments suivants :

[![Le mode Explorateur](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panneau Environment</a> : Affiche vos environnements Azure Time Series Insights.
- <a href="#navigation-menu">Menu de navigation</a> : permet de basculer entre les pages **Analyser** et **Modèle**.
- <a href="#hierarchy-tree">Arborescence hiérarchique</a> : permet de sélectionner un modèle et des éléments de données spécifiques à tracer.
- <a href="#preview-well">Zone de séries chronologiques</a> : affiche vos éléments de données actuellement sélectionnés dans un tableau avec code couleur.
- <a href="#preview-chart">Panneau Graphique</a> : affiche votre graphique actif.
- <a href="#time-editor-panel">Chronologie</a> : permet de modifier votre intervalle de temps actif.
- <a href="#navigation-panel">Barre de l’application</a> : contient vos options de gestion des utilisateurs, telles que le locataire actuel. Permet de modifier les paramètres de thème et de langue.

## <a name="environment-drop-down-list"></a>Liste déroulante de l’environnement

La liste déroulante de l’environnement affiche tous les environnements Time Series Insights auxquels vous avez accès. La liste inclut les environnements faisant l’objet d’un paiement à l’utilisation, tels que la préversion de Time Series Insights. La liste inclut également les environnements S1/S2 généralement disponibles.

1. Sélectionnez la flèche déroulante en regard de votre environnement affiché.

   [![Le Panneau de configuration](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ensuite, sélectionnez l’environnement souhaité.

## <a name="navigation-menu"></a>Menu de navigation

  [![Le menu de navigation](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Utilisez le menu de navigation pour sélectionner entre deux vues :

* **Analyse** : permet de créer des graphiques et d’effectuer des analytiques enrichies de vos données de série chronologique basées ou non sur un modèle.
* **Modèle** : permet d’envoyer de nouveaux types, hiérarchies et instances de série chronologique de la préversion de Time Series Insights à votre modèle Time Series Insights.

## <a name="hierarchy-tree"></a>Arborescence hiérarchique

L’arborescence hiérarchique affiche les éléments de données sélectionnés qui incluent des modèles, des appareils spécifiques et des capteurs installés sur vos appareils.

### <a name="model-search-panel"></a>Panneau de recherche de modèle

Le panneau de recherche de modèle vous permet de facilement rechercher et parcourir votre hiérarchie de modèles de séries chronologiques pour rechercher les instances de séries chronologiques spécifiques que vous souhaitez afficher sur votre graphique. Une fois vos instances sélectionnées, elles sont ajoutées au graphique actuel et à la source de données.

  [![Le panneau de recherche de modèle](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Création de modèle

La préversion d’Azure Time Series prend en charge la totalité des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur votre modèle Time Series.

* **Type de modèle de série chronologique** : les types Time Series Insights vous permettent de définir des variables ou des formules pour l’exécution de calculs. Ils sont associés à une instance Time Series Insights spécifique. Un type peut avoir une ou plusieurs variables.
* **Hiérarchies de modèle de série temporelle** : les hiérarchies sont des organisations systématiques de vos données. Elles représentent les relations entre les différentes entités dans vos données Time Series Insights.
* **Instance de modèle de série chronologique** : les instances représentent la série chronologique elle-même. Dans la plupart des cas, ils s’agit de **DeviceID** ou d’**AssetID**, qui est l’identificateur unique de la ressource dans l’environnement.

Pour en savoir plus le modèle de série chronologique, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Source en préversion

La source affiche les champs d’instance et autres métadonnées associées aux instances Time Series Insights. Les cases à cocher à droite vous permettent de masquer ou d’afficher des instances spécifiques du graphique actuel. Vous pouvez également supprimer des éléments de données spécifiques à partir de vos données en cours en sélectionnant le contrôle **Supprimer** (corbeille) affiché en rouge à gauche de l’élément.

  [![La source en préversion](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Pour reconfigurer la disposition de la page de votre graphique **Analyse**, sélectionnez l’icône de points de suspension dans l’angle supérieur droit :

  [![Options de disposition de télémétrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Si vous voyez le message suivant, l’instance n’a pas de données pour l’intervalle de temps sélectionné. Pour résoudre ce problème, augmentez l’intervalle de temps ou vérifiez que l’instance envoie des données.
>
> ![Aucune notification de données](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Graphique en préversion

Le graphique vous permet d’afficher les instances Time Series Insights sous forme de lignes. Vous pouvez réduire le panneau d’environnement, le modèle de données et le panneau de contrôle des intervalles de temps en sélectionnant les contrôles web afin d’agrandir le graphique.

  [![Présentation du graphique en préversion](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Plage de données sélectionnée** : contrôle quels éléments de données sont disponibles pour la visualisation.

- **Outil de curseur de plage de dates interne** : utilisez les deux contrôles de point de terminaison en les faisant glisser sur l’intervalle de temps souhaité.

- **Contrôle de réduction de l’intervalle de temps** : réduit et développe l’éditeur du panneau d’intervalle de temps.

- **Contrôle de format de l’axe y** : parcourt les options d’affichage de l’axe Y disponibles :

    * `Default`: chaque ligne comporte un axe Y individuel.
    * `Stacked`: permet d’empiler plusieurs lignes sur le même axe y, celui-ci étant modifié en fonction de la ligne sélectionnée.
    * `Shared`: toutes les données de l’axe sont affichées ensemble.

- **Élément de données actuel** : élément de données actuellement sélectionné et détails associés.

Vous explorer une tranche de données spécifique en cliquant sur un point de données du graphique actuel, puis en faisant glisser la zone sélectionnée vers le point de terminaison de votre choix. Cliquez avec le bouton droit sur la zone grisée sélectionnée, puis sélectionnez **Zoom** comme illustré dans l’image suivante :

  [![Zoom sur graphique en préversion](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Une fois que vous avez effectué l’action **Zoom**, vous voyez votre jeu de données sélectionné. Sélectionnez le contrôle de format de l’axe Y pour parcourir les représentations de l’axe Y de vos données Time Series Insights.

  [![Axe Y de graphique en préversion](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Vous pouvez voir ici un exemple d’axes Y partagés :

  [![Axes Y partagés en préversion](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panneau de l’éditeur d’heure

Lorsque vous utilisez la préversion d’Azure Time Series Insights, vous sélectionnez tout d’abord un intervalle de temps. L’intervalle de temps sélectionné détermine le jeu de données disponible pour la manipulation avec les widgets Time Series Insights en préversion. Les contrôles web suivants sont disponibles dans la préversion d’Azure Time Series Insights pour la sélection de votre intervalle de temps actif :

  [![Volet de sélection d’heure](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Outil de curseur de plage de dates interne** : utilisez les deux contrôles de point de terminaison en les faisant glisser sur l’intervalle de temps souhaité. Cette plage de dates interne est limitée par le contrôle de curseur de plage de dates externe.

1. **Boutons pour augmenter ou diminuer la plage de dates** : augmentez ou réduisez votre intervalle de temps en sélectionnant l’un des boutons pour l’intervalle souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : ce contrôle web vous permet de masquer tous les contrôles à l’exception de l’outil de curseur de plage de dates interne.

1. **Contrôle de curseur de plage de dates externe** : utilisez les contrôles de point de terminaison pour sélectionner la plage de dates externe, qui sera disponible pour votre contrôle de plage de dates interne.

1. **Liste déroulante des plages de dates rapides** : permet de basculer rapidement entre des intervalles de temps prédéfinis, tels que les **30 dernières minutes**, les **12 dernières heures** ou une **plage personnalisée**. La modification de cette valeur a pour effet de changer également les plages d’intervalles disponibles présentées dans l’outil de curseur de taille d’intervalle.

1. **Outil de curseur de taille d’intervalle** : permet d’effectuer un zoom avant et arrière sur des intervalles d’un même intervalle de temps. Cette action permet un contrôle plus précis du déplacement entre les tranches de temps volumineuses. Elle affiche des tendances lissées jusqu’à des tranches de l’ordre de la milliseconde. Vous pouvez l’utiliser pour afficher des coupes granulaires de vos données en haute résolution. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection, avec un équilibre entre résolution, vitesse de requête et granularité.

1. **Contrôle web de début et de fin de plage de dates** : vous permet de sélectionner facilement les plages de dates et d’heures souhaitées. Vous pouvez également utiliser le contrôle pour basculer entre les différents fuseaux horaires. Une fois que vous avez apporté des modifications, pour les appliquer à votre espace de travail actuel, sélectionnez **Enregistrer**.

   [![Volet de sélection de début et de fin](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Volet de navigation

Le volet de navigation de la préversion de Time Series Insights s’affiche en haut de votre application de Time Series Insights. Il offre les fonctionnalités suivantes.

### <a name="current-session-share-link-control"></a>Contrôle de lien de partage de session en cours

  [![Icône Partager](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Sélectionnez la nouvelle icône **Partager** pour partager une URL de lien avec votre équipe.

  [![Partager l’URL de votre instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Section de locataire

  [![Sélection de locataire](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Affiche vos informations actuelles de compte de connexion Time Series Insights.
* Permet de basculer entre les thèmes Time Series Insights disponibles.
* Permet d’afficher la préversion de l’[application web de démonstration](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Sélection de thème

Pour choisir un nouveau thème, sélectionnez l’icône de votre profil située dans l’angle supérieur droit. Ensuite, sélectionnez **Modifier le thème**.

  [![Sélection de thème](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Vous pouvez également sélectionner la langue en sélectionnant l’icône de votre profil.

La préversion d’Azure Time Series Insights prend en charge deux thèmes :

* **Thème clair** : thème par défaut affiché dans ce document.
* **Thème foncé** : restitue l’Explorateur comme illustré ici :

  [![Thème sombre sélectionné](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Contrôles d’environnement S1/S2

### <a name="preview-terms-panel"></a>Volet des conditions d’utilisation de la préversion

Cette section s’applique uniquement aux environnements S1/S2 existants qui tentent d’utiliser l’Explorateur dans l’interface utilisateur mise à jour. Vous pouvez utiliser le produit généralement disponible et de la préversion en même temps. Nous avons ajouté certaines fonctionnalités de l’interface utilisateur existante dans l’Explorateur mis à jour, mais vous pouvez accéder à l’expérience d’interface utilisateur complète pour l’environnement S1/S2 dans l’Explorateur Time Series Insights existant. 

À la place de la hiérarchie, vous voyez le volet des conditions d’utilisation de Time Series Insights où vous définissez des requêtes dans votre environnement. Il permet de filtrer vos données selon un prédicat.

  [![Volet de requête Where](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Le panneau de l’éditeur de termes Time Series Insights (préversion) accepte les paramètres suivants :

**Where** : la clause Where vous permet de filtrer rapidement vos événements à l’aide de l’ensemble d’opérandes répertoriées dans le tableau ci-dessous. Si vous effectuez une recherche en sélectionnant un opérande, le prédicat est automatiquement mis à jour selon cette recherche. Les types d’opérandes pris en charge comprennent les suivants :

| Opération | Types pris en charge   | Notes |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL | Tous les opérandes doivent être du même type ou être la constante NULL. |
| `HAS` | Chaîne | Seuls les littéraux de chaîne constante sont autorisés à droite. Les chaînes vides et NULL ne sont pas autorisées. |

Pour en savoir plus sur les types d’opérations et de données de requête pris en charge, voir [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemples de clauses Where

  [![Exemples de clauses Where](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mesure** : liste déroulante affichant toutes les colonnes numériques (**Doubles**) que vous pouvez utiliser en tant qu’éléments pour votre graphique actuel.

**Diviser par** : cette liste déroulante affiche toutes les colonnes de catégories disponibles (chaînes) dans votre modèle pour le regroupement de vos données. Vous pouvez ajouter jusqu’à cinq conditions à afficher sur le même axe X. Entrez les paramètres souhaités, puis sélectionnez **Ajouter** pour ajouter un terme.

  [![Vue de requête et de filtre n°1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Vous pouvez afficher et masquer des éléments dans le panneau de graphique en sélectionnant l’icône visible, comme illustré dans l’image suivante. Pour supprimer complètement les requêtes, sélectionnez la croix rouge **X**.

  [![Vue de requête et de filtre n°2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez l’[entrée et le stockage](./time-series-insights-update-storage-ingress.md) dans Azure Time Series Insights en préversion.
- Lisez le document concernant la préversion d’Azure Time Series Insights sur [Modélisation des données](./time-series-insights-update-tsm.md).
- Découvrez [comment diagnostiquer et dépanner](./time-series-insights-update-how-to-troubleshoot.md) votre instance Time Series Insights.
