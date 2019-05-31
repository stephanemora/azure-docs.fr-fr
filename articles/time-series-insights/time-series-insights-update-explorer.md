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
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399873"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualiser des données dans l’explorateur (préversion)

Ce document décrit l’interface utilisateur et les fonctionnalités d’expérience utilisateur et de la version préliminaire de Azure temps série Insights [application web de démonstration](https://insights.timeseries.azure.com/preview/demo). Plus précisément, il aborde la mise en page de l’exemple hébergé, options de personnalisation d’interface et de navigation grâce à la démonstration fournie.

## <a name="prerequisites"></a>Conditions préalables

Pour vous familiariser avec l’Explorateur Azure temps série Insights Preview, vous devez :

* Configurer un environnement Time Series Insights. Pour en savoir plus sur la configuration d’une instance, essayez le [Azure temps série Insights Preview](./time-series-insights-update-create-environment.md) didacticiel.
* [Fournir l’accès aux données](./time-series-insights-data-access.md) à l’environnement Time Series Insights que vous avez créé pour le compte. Vous pouvez octroyer l’accès à d’autres personnes ainsi qu’à vous-même.
* Ajouter une source d’événement à l’environnement Time Series Insights pour transmettre des données à l’environnement :
  * En savoir plus [comment se connecter à un concentrateur d’événements](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * En savoir plus [comment se connecter à un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>En savoir plus sur l’Explorateur de l’aperçu

L’Explorateur Azure Time Series Insights (préversion) est composé des éléments suivants :

[![Le mode Explorateur](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panneau d’environnement</a>: Affiche vos environnements Azure Time Series Insights.
- <a href="#navigation-menu">Menu de navigation</a>: Il permet de basculer entre le **analyser** et **modèle** pages.
- <a href="#hierarchy-tree">Arborescence hiérarchique</a> : Il permet de sélectionner des éléments de modèle et les données spécifiques à tracer.
- <a href="#preview-well">Zone de séries chronologiques</a> : Affiche vos éléments de données actuellement sélectionné dans un tableau avec le codage en couleurs.
- <a href="#preview-chart">Panneau Graphique</a> : affiche votre graphique actif.
- <a href="#time-editor-panel">Chronologie</a> : Il permet de modifier votre intervalle de temps de travail.
- <a href="#navigation-panel">Barre de l’application</a> : Contient les options de gestion de votre utilisateur, telles que locataire actuel. Vous pouvez l’utiliser pour modifier les paramètres de thème et de langage.

## <a name="environment-drop-down-list"></a>Liste déroulante d’environnement

La liste déroulante environnement affiche tous les environnements Time Series Insights que vous avez accès. La liste inclut les environnements de paiement à l’utilisation, telles que le temps série Insights Preview. La liste inclut également des environnements de S1/S2, qui sont généralement disponibles.

1. Sélectionnez la flèche déroulante en regard de votre environnement affichée.

   [![Le panneau de configuration](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ensuite, sélectionnez l’environnement souhaité.

## <a name="navigation-menu"></a>Menu de navigation

  [![Le menu de navigation](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Utilisez le menu de navigation pour sélectionner entre deux vues :

* **Analyser** : Il permet de graphique et d’effectuer des analytique riches sur vos modélisé ou sans modèle données chronologiques.
* **Modèle** : Il permet d’envoyer les nouveaux types, hiérarchies et instances de temps série Insights Preview à votre modèle Time Series Insights.

## <a name="hierarchy-tree"></a>arborescence hiérarchique

L’arborescence hiérarchique affiche les éléments de données sélectionnés qui incluent des modèles, des appareils spécifiques et les capteurs sur vos appareils.

### <a name="model-search-panel"></a>Volet de recherche de modèle

Vous pouvez utiliser le panneau de recherche de modèle facilement rechercher et naviguer dans votre hiérarchie de modèle de série chronologique pour rechercher les instances de série heure spécifique que vous souhaitez afficher sur votre graphique. Après avoir sélectionné vos instances, elles sont ajoutées à la fois le graphique actuel et les données également.

  [![Le panneau de recherche de modèle](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Création de modèles

Le prend en charge de la version préliminaire d’Azure temps série Insights complète créer, lire, mettre à jour et supprimer des opérations sur votre modèle de série chronologique.

* **Type de modèle de série chronologique** : Vous pouvez utiliser des types de Time Series Insights pour définir des variables ou des formules qui permet d’effectuer des calculs. Elles sont associées à une instance donnée de Time Series Insights. Un type peut avoir une ou plusieurs variables.
* **Hiérarchies de modèle de série temporelle** : les hiérarchies sont des organisations systématiques de vos données. Elles représentent les relations entre les différentes entités dans vos données Time Series Insights.
* **Instance de modèle de série chronologique** : les instances représentent la série chronologique elle-même. Dans la plupart des cas, ils sont le **DeviceID** ou **AssetID**, qui est l’identificateur unique de la ressource dans l’environnement.

Pour en savoir plus le modèle de série chronologique, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Afficher un aperçu bien

La zone de configuration affiche les champs d’instance et d’autres métadonnées associées aux instances de Time Series Insights sélectionnés. En sélectionnant les cases à cocher située à droite, vous pouvez masquer ou afficher des instances spécifiques du graphique actuel. Vous pouvez également supprimer des éléments de données spécifiques à partir de vos données en cours bien en sélectionnant la croix rouge **supprimer** (Corbeille) contrôle sur le côté gauche de l’élément.

  [![La version préliminaire bien](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Pour reconfigurer la disposition de votre **analyser** page du graphique, sélectionnez l’icône de points de suspension dans le coin supérieur droit :

  [![Options de mise en page de données de télémétrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Si vous voyez le message suivant, l’instance ne comporte aucune donnée pendant l’intervalle de temps sélectionné. Pour résoudre ce problème, augmentez l’intervalle de temps ou vérifiez que l’instance/envoie des données.
>
> ![Aucune notification de données](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Aperçu du graphique

Le graphique, vous pouvez afficher les instances de Time Series Insights sous forme de lignes. Vous pouvez réduire le panneau d’environnement, le modèle de données et l’heure du panneau étendue en sélectionnant les contrôles web afin d’agrandir le graphique.

  [![Aperçu du graphique](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Plage de dates sélectionnée**: Contrôle quels éléments de données sont disponibles pour la visualisation.

- **Outil de curseur de plage de date interne**: Utilisez les deux contrôles de point de terminaison en les faisant glisser sur l’intervalle de temps souhaité.

- **Contrôle de réduction de l’intervalle de temps** : réduit et développe l’éditeur du panneau d’intervalle de temps.

- **Contrôle de format de l’axe y** : Parcourt les options d’affichage de l’axe des y disponibles :

    * `Default`: Chaque ligne comporte une axe des ordonnées individuelles.
    * `Stacked`: Il permet d’avoir plusieurs lignes sur la même axe des ordonnées, la pile avec la modification de données de l’axe des y en fonction de la ligne sélectionnée.
    * `Shared`: Toutes les données de l’axe des y affichées ensemble.

- **Élément de données actuel** : élément de données actuellement sélectionné et détails associés.

Pour explorer davantage dans une tranche de données spécifique, cliquez sur un point de données sur le graphique actuel, puis faites glisser la zone sélectionnée vers le point de terminaison de votre choix. Avec le bouton droit de la zone grise sélectionnée, puis sélectionnez **Zoom**, comme illustré dans cette image suivante :

  [![Zoom du graphique en version préliminaire](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Après avoir effectué la **Zoom** action, vous voyez vos données sélectionnées définie. Sélectionnez le contrôle de format de l’axe des y pour faire défiler les représentations sous forme de l’axe des y trois de vos données Time Series Insights.

  [![Axe des ordonnées du graphique de version préliminaire](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Ici, vous pouvez voir un exemple d’axes des Y partagés :

  [![Afficher un aperçu des axes Y partagés](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Volet de l’éditeur de temps

Lorsque vous utilisez la préversion d’Azure Time Series Insights, vous sélectionnez tout d’abord un intervalle de temps. L’intervalle de temps sélectionné détermine l’ensemble de données qui est disponible pour la manipulation avec les widgets de temps série Insights Preview. Les contrôles web suivants sont disponibles en version préliminaire de temps série Insights permettant de sélectionner votre intervalle de temps de travail :

  [![Volet de sélection d’heure](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Outil de curseur de plage de date interne**: Utilisez les deux contrôles de point de terminaison en les faisant glisser sur l’intervalle de temps souhaité. Cette plage de dates interne est limitée par le contrôle de curseur de plage de dates externe.

1. **Boutons pour augmenter ou diminuer la plage de dates** : augmentez ou réduisez votre intervalle de temps en sélectionnant l’un des boutons pour l’intervalle souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : Ce contrôle web vous permet de masquer tous les contrôles à l’exception de l’outil de curseur de plage de date interne.

1. **Contrôle de curseur de plage de date externe**: Utilisez les contrôles de point de terminaison pour sélectionner la plage de dates externe, qui sera disponible pour votre contrôle de plage de date interne.

1. **Liste déroulante de plage de dates durées rapides**: Il permet de basculer rapidement entre les sélections de span temps prédéfinis, tels que la dernière **30 minutes**, le **12 dernières heures**, ou un **plage personnalisée**. La modification de cette valeur a pour effet de changer également les plages d’intervalles disponibles présentées dans l’outil de curseur de taille d’intervalle.

1. **Outil de curseur de taille d’intervalle** : Il permet d’agrandir et réduire d’intervalles sur le même intervalle de temps. Cette action permet un contrôle plus précis du déplacement entre les tranches de temps volumineuses. Il affiche les tendances lissées jusqu'à tranches aussi petits qu’une milliseconde. Vous pouvez l’utiliser pour afficher des morceaux granulaires, haute résolution de vos données. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection, avec un équilibre entre résolution, vitesse de requête et granularité.

1. **Plage vers et depuis le web contrôle date**: Avec ce contrôle web, vous pouvez facilement sélectionner les plages de date et l’heure souhaitées. Vous pouvez également utiliser le contrôle pour basculer entre les différents fuseaux horaires. Après avoir apporté les modifications à appliquer à votre espace de travail actuel, sélectionnez **enregistrer**.

   [![Vers et depuis le panneau de sélection](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Volet de navigation

Le volet de navigation de temps série Insights Preview s’affiche en haut de votre application de Time Series Insights. Il fournit les fonctionnalités suivantes.

### <a name="current-session-share-link-control"></a>Contrôle de lien de partage de session en cours

  [![Icône de partage](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Sélectionnez la nouvelle **partager** icône partager un lien URL avec votre équipe.

  [![Partager votre URL de l’instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Section de locataire

  [![Sélection de client](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Affiche les informations de votre compte de connexion Time Series Insights en cours.
* Il permet de basculer entre les thèmes de Time Series Insights disponibles.
* Utilisez-le pour afficher l’aperçu [application web de démonstration](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Sélection de thème

Pour sélectionner un nouveau thème, sélectionnez l’icône de votre profil situé dans le coin supérieur droit. Ensuite, sélectionnez **modifier le thème**.

  [![Sélection du thème](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Sélection de la langue est également disponible en sélectionnant l’icône de votre profil.

La préversion d’Azure Time Series Insights prend en charge deux thèmes :

* **Le thème clair**: thème par défaut affiché dans ce document.
* **Thème foncé** : restitue l’Explorateur comme illustré ici :

  [![Thème sombre sélectionné](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Contrôles d’environnement S1/S2

### <a name="preview-terms-panel"></a>Panneau des termes de version préliminaire

Cette section s’applique uniquement aux environnements S1/S2 existants qui tentent d’utiliser l’Explorateur dans l’interface utilisateur mise à jour. Vous souhaiterez peut-être utiliser le produit à la disposition générale et la version préliminaire en combinaison. Nous avons ajouté certaines fonctionnalités de l’interface utilisateur existante dans l’Explorateur mis à jour, mais vous pouvez accéder à l’expérience d’interface utilisateur complète pour l’environnement S1/S2 dans l’Explorateur Time Series Insights existant. 

Au lieu de la hiérarchie, vous consultez le panneau des termes Time Series Insights, où vous définissez des requêtes dans votre environnement. Il permet de filtrer vos données selon un prédicat.

  [![Panneau de configuration de la requête où](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Le panneau de l’éditeur de termes Time Series Insights (préversion) accepte les paramètres suivants :

**Where** : Utilisez where clause pour filtrer rapidement les événements à l’aide de l’ensemble d’opérandes répertoriés dans le tableau suivant. Si vous effectuez une recherche en sélectionnant un opérande, le prédicat est automatiquement mis à jour selon cette recherche. Les types d’opérandes pris en charge comprennent les suivants :

| Opération | Types pris en charge   | Notes |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Chaîne, Bool, Double, DateTime, TimeSpan, NULL | Tous les opérandes doivent être du même type ou être la constante NULL. |
| `HAS` | String | Seuls les littéraux de chaîne constante sont autorisés sur le côté droit. Chaînes vides et NULL ne sont pas autorisés. |

Pour en savoir plus sur les types de données et les opérations de requête pris en charge, consultez [temps série Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemples de where clauses

  [![Où les exemples de clause](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mesure** : Une liste déroulante qui affiche toutes les colonnes numériques (**Doubles**) vous pouvez utiliser en tant qu’éléments pour votre graphique actuel.

**Diviser par** : Cette liste déroulante affiche toutes les catégorielles colonnes disponibles (chaînes) dans votre modèle que vous pouvez regrouper vos données par. Vous pouvez ajouter jusqu'à cinq conditions à afficher sur l’axe des abscisses. Entrez les paramètres de votre choix, puis sélectionnez **ajouter** pour ajouter un nouveau terme.

  [![Vue interrogée filtrée et non une](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Vous pouvez afficher et masquer des éléments dans le panneau graphique en sélectionnant l’icône visible, comme illustré dans l’image suivante. Pour supprimer complètement les requêtes, sélectionnez la croix rouge **X**.

  [![Vue interrogée et filtré deux](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [entrée et stockage](./time-series-insights-update-storage-ingress.md) dans la version préliminaire d’Azure temps série Insights.
- Consultez le document de temps série Insights Preview [modélisation des données](./time-series-insights-update-tsm.md).
- En savoir plus [comment diagnostiquer et résoudre les problèmes](./time-series-insights-update-how-to-troubleshoot.md) votre instance de Time Series Insights.
