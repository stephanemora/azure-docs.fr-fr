---
title: Visualiser les données dans l’explorateur Azure Time Series Insights en préversion | Microsoft Docs
description: Cet article décrit les fonctionnalités et options disponibles dans l’application web Explorateur Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442085"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualiser des données dans l’explorateur (préversion)

Ce document décrit les fonctionnalités d’interface/expérience utilisateur et l’interface de la version préliminaire de Azure temps série Insights [application web de démonstration](https://insights.timeseries.azure.com/preview/demo). Plus précisément, il aborde la mise en page de l’exemple hébergé, options de personnalisation d’interface et de navigation grâce à la démonstration fournie.

## <a name="prerequisites"></a>Conditions préalables

Pour vous familiariser avec l’Explorateur Azure temps série Insights Preview, vous devez :

* Configurer un environnement Time Series Insights. Pour en savoir plus sur l’approvisionnement d’une instance Essayez notre [Azure temps série Insights Preview](./time-series-insights-update-create-environment.md) didacticiel.
* [Fournir l’accès aux données](./time-series-insights-data-access.md) à l’environnement Time Series Insights que vous avez créé pour le compte. Vous pouvez octroyer l’accès à d’autres personnes ainsi qu’à vous-même.
* Ajouter une source d’événement à l’environnement Time Series Insights pour transmettre des données à l’environnement :
  * En savoir plus [comment se connecter à un concentrateur d’événements](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * En savoir plus [comment se connecter à un IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>En savoir plus sur l’Explorateur de l’aperçu

L’Explorateur Azure Time Series Insights (préversion) est composé des éléments suivants :

[![Le mode Explorateur](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Panneau d’environnement**</a>: Affiche vos environnements Azure TSI.
1. <a href="#navigation-menu">**Menu de navigation**</a>: Vous permet de passer le **analyser** et **modèle** pages.
1. <a href="#hierarchy-tree">**Arborescence hiérarchique**</a>: Vous permet de sélectionner le modèle spécifique et les éléments de données à tracer.
1. <a href="#preview-well">**Série chronologique bien**</a>: Affiche vos éléments de données actuellement sélectionné dans un tableau avec le codage en couleurs.
1. <a href="#preview-chart">**Panneau de configuration graphique**</a>:  affiche votre graphique actif.
1. <a href="#time-editor-panel">**Chronologie**</a>:  vous permet de modifier votre intervalle de temps actif.
1. <a href="#navigation-panel">**Barre de l’application**</a>:  contient des options de gestion d’utilisateur, tel que le locataire actuel, et vous permet de modifier les paramètres de thème et de langage.

## <a name="environment-dropdown"></a>Liste déroulante d’environnements

La liste déroulante environnement affiche tous les environnements Time Series Insights que vous avez accès. La liste inclut les environnements de paiement à l’utilisation (version préliminaire) et les environnements de S1/S2 (disponibilité générale ou la disponibilité générale). 

1. Cliquez simplement sur la flèche déroulante en regard de votre environnement affichée :

   [![Le panneau de configuration](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ensuite, sélectionnez l’environnement désiré.

## <a name="navigation-menu"></a>Menu de navigation

  [![Le menu de navigation](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Le menu de navigation vous permet de choisir entre deux vues :

* **Analyser** : vous permet de créer des graphiques et d’effectuer des analytiques enrichies sur vos données de série chronologique basées ou non sur un modèle.
* **Modèle** : vous permet d’envoyer les nouveaux types, hiérarchies et instances de série chronologique Time Series Insights (préversion) à votre modèle Time Series Insights.

## <a name="hierarchy-tree"></a>arborescence hiérarchique

L’arborescence hiérarchique affiche les éléments de données sélectionnés, y compris des modèles, des appareils spécifiques et les capteurs sur vos appareils.

### <a name="model-search-panel"></a>Volet de recherche de modèle

Le panneau de recherche dans les modèles vous permet de facilement rechercher et parcourir votre hiérarchie de modèles de séries chronologiques pour rechercher les instances de séries chronologiques spécifiques que vous souhaitez afficher sur votre graphique. Lorsque vous sélectionnez vos instances, elles sont également ajoutées au graphique actuel et à la zone de données.

  [![Le panneau de recherche de modèle](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Création de modèles

La version préliminaire d’Azure temps série Insights prend en charge des opérations de création, lecture, mise à jour et suppression (CRUD) complètes sur votre modèle de série chronologique.  

* **Type de modèle de série chronologique** : les types Time Series Insights vous aident à définir des variables ou des formules permettant d’effectuer des calculs. Ils sont associés à une instance Time Series Insights spécifique. Un type peut avoir une ou plusieurs variables.
* **Hiérarchies de modèle de série temporelle** : les hiérarchies sont des organisations systématiques de vos données. Elles représentent les relations entre les différentes entités dans vos données Time Series Insights.
* **Instance de modèle de série chronologique** : les instances représentent la série chronologique elle-même. Dans la plupart des cas, ils sont le **DeviceID** ou **AssetID**, qui est l’identificateur unique de la ressource dans l’environnement.

Pour en savoir plus le modèle de série chronologique, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Afficher un aperçu bien

La zone de configuration affiche les champs d’instance et d’autres métadonnées associées aux instances TSI sélectionnés. Les cases à cocher sur la droite vous permettent de masquer ou afficher des instances spécifiques du graphique actuel. Vous pouvez également supprimer des éléments de données spécifiques à partir de vos données en cours bien en cliquant sur la croix rouge **supprimer** (Corbeille) contrôle dans la partie gauche de l’élément.

  [![La version préliminaire bien](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Vous pouvez également reconfigurer la disposition de votre **analyser** page en sélectionnant l’icône de points de suspension dans l’angle supérieur droit du graphique :

  [![Options de mise en page de données de télémétrie](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Si vous voyez le message suivant, l’instance n’a pas de données pendant l’intervalle de temps sélectionné. Pour résoudre ce problème, vous pouvez augmenter l’intervalle de temps ou vérifier que l’instance envoie des données.
>
> ![Aucune notification de données](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Aperçu du graphique

Le graphique, vous pouvez afficher les instances TSI sous forme de lignes. Vous pouvez réduire le panneau d’environnement, le modèle de données et le panneau de contrôle des intervalles de temps en cliquant sur les contrôles web afin d’agrandir le graphique.

  [![Aperçu du graphique](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **Plage de données sélectionnée** : Contrôle quels éléments de données sont disponibles pour la visualisation.

1. **Outil de curseur de plage de données interne** : utilisez les deux contrôles d’extrémité en les faisant glisser sur l’intervalle de temps souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : réduit et développe l’éditeur du panneau d’intervalle de temps.

1. **Contrôle de format de l’axe y** : parcourt les options de vue de l’axe y disponibles :

    * `Default`: Chaque ligne comporte un axe y individuel.
    * `Stacked`: Vous permet d’empiler plusieurs lignes sur le même axe y, celui-ci étant modifié en fonction de la ligne sélectionnée.
    * `Shared`: Toutes les données de l’axe y sont affichées ensemble.

1. **Élément de données actuel** : élément de données actuellement sélectionné et détails associés.

Vous pouvez affiner une tranche de données spécifique en cliquant sur le graphique actuel, un point de données, puis en faisant glisser la zone sélectionnée vers l’extrémité de votre choix. Avec le bouton droit de la zone grisée, sélectionnée, puis cliquez sur **Zoom** comme indiqué dans cette image suivante :

  [![Zoom du graphique en version préliminaire](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Après avoir effectué la **Zoom** action, vous verrez votre jeu de données sélectionné. Cliquez sur le contrôle de format de l’axe y pour faire défiler les représentations de l’axe y de vos données Time Series Insights.

  [![Afficher un aperçu graphique de l’axe des y](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Ici, vous pouvez voir un exemple d’axes y partagés :

  [![Afficher un aperçu de l’axe des y partagé](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Volet de l’éditeur de temps

Lorsque vous utilisez la préversion d’Azure Time Series Insights, vous sélectionnez tout d’abord un intervalle de temps. L’intervalle de temps sélectionné détermine le jeu de données disponible pour la manipulation avec les widgets Time Series Insights (préversion). Les contrôles web suivants sont disponibles dans la préversion d’Azure Time Series Insights pour la sélection de votre intervalle de temps actif.

  [![Volet de sélection de temps](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Outil de curseur de plage de dates interne** : utilisez les deux contrôles d’extrémité en les faisant glisser sur l’intervalle de temps souhaité. Cette plage de dates interne est limitée par le contrôle de curseur de plage de dates externe.

1. **Boutons pour augmenter ou diminuer la plage de dates** : augmentez ou réduisez votre intervalle de temps en sélectionnant l’un des boutons pour l’intervalle souhaité.

1. **Contrôle de réduction de l’intervalle de temps** : ce contrôle web vous permet de masquer tous les contrôles à l’exception de l’outil de curseur de plage de dates interne.

1. **Contrôle de curseur de plage de dates externe** : utilisez les contrôles d’extrémité pour sélectionner la plage de dates externe, qui sera disponible pour votre contrôle de plage de dates interne.

1. **Durées rapides plage de dates déroulante**: Vous permet de passer rapidement des sélections span de temps prédéfinis, tels que la dernière **30 minutes**, le **12 dernières heures**, ou un **plage personnalisée**. La modification de cette valeur a pour effet de changer également les plages d’intervalles disponibles présentées dans l’outil de curseur de taille d’intervalle.

1. **Outil de curseur de taille d’intervalle** : vous permet d’effectuer un zoom/zoom arrière sur les intervalles pour le même intervalle de temps. Cette action permet un contrôle plus précis du déplacement entre les tranches de temps volumineuses. Elle montre les tendances lissées, jusqu’à des tranches aussi petites que la milliseconde, ce qui vous permet de voir des morceaux granulaires, haute résolution de vos données. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection, avec un équilibre entre résolution, vitesse de requête et granularité.

1. **Contrôle web de plage de dates de début et de fin** : Avec ce contrôle web, vous pouvez facilement cliquez sur et sélectionnez votre date souhaité et les intervalles de temps. Vous pouvez également utiliser le contrôle pour basculer entre les différents fuseaux horaires. Une fois que vous avez apporté des modifications, pour les appliquer à votre espace de travail actuel, sélectionnez **Enregistrer**.

   [![Vers et depuis le panneau de sélection](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Volet de navigation

Le volet de navigation de temps série Insights Preview s’affiche en haut de votre application TSI. Il fournit les fonctionnalités suivantes.

### <a name="current-session-share-link-control"></a>Contrôle de lien de partage de session en cours

  [![Icône de partage](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Sélectionnez la nouvelle **partager** icône partager un lien URL avec votre équipe.

  [![Partager votre URL de l’instance](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Section de locataire

  [![Sélection de client](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Affiche vos informations de compte de connexion Time Series Insights en cours.
* Vous permet de basculer entre les thèmes Time Series Insights disponibles.
* Vous permet d’afficher l’aperçu [application web de démonstration](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Sélection de thème

Pour sélectionner un nouveau thème, cliquez sur l’icône de profil situé dans l’angle supérieur droit. Ensuite, sélectionnez **modifier le thème**.

  [![Sélection du thème](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Sélection de la langue est également disponible en cliquant sur l’icône de votre profil.

La préversion d’Azure Time Series Insights prend en charge deux thèmes :

* **Thème clair** : thème par défaut affiché dans ce document.
* **Thème foncé** :  restitue l’Explorateur comme illustré ici :

  [![Thème sombre sélectionné](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Contrôles d’environnement S1/S2

### <a name="preview-terms-panel"></a>Panneau des termes de version préliminaire

Cette section s’applique uniquement aux environnements S1/S2 existants qui tentent d’utiliser l’Explorateur dans l’interface utilisateur mise à jour. Vous pouvez utiliser le produit de la disponibilité générale et de la préversion en même temps. Nous avons ajouté certaines fonctionnalités de l’interface utilisateur existante dans l’Explorateur mis à jour, mais vous pouvez accéder à l’expérience d’interface utilisateur complète pour l’environnement S1/S2 dans l’Explorateur Time Series Insights existant.  

À la place de la hiérarchie, vous voyez le panneau des termes Time Series Insights, où vous définissez des requêtes dans votre environnement. Celui-ci vous permet de filtrer vos données selon un prédicat.

  [![Panneau de configuration de la requête où](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

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

  [![Où les exemples de clause](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mesure** : Une liste déroulante qui affiche toutes les colonnes numériques (**Doubles**) vous pouvez utiliser en tant qu’éléments pour votre graphique actuel.

**Diviser par** : cette liste déroulante affiche toutes les colonnes de catégories disponibles (chaînes) dans votre modèle pour le regroupement de vos données. Vous pouvez ajouter jusqu'à cinq conditions à afficher sur l’axe des abscisses. Entrez les paramètres souhaités, puis sélectionnez **Ajouter** pour ajouter un nouveau terme.

  [![Vue interrogée filtrée et non une](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Vous pouvez afficher et masquer des éléments dans le panneau de graphique en sélectionnant l’icône visible, comme illustré dans l’image suivante. Vous pouvez supprimer complètement de requêtes en cliquant sur la croix rouge **X**.

  [![Vue interrogée et filtré deux](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [entrée et stockage](./time-series-insights-update-storage-ingress.md) dans la version préliminaire d’Azure temps série Insights.

- Consultez le document de temps série Insights Preview [modélisation des données](./time-series-insights-update-tsm.md).

- En savoir plus [comment diagnostiquer et résoudre les problèmes](./time-series-insights-update-how-to-troubleshoot.md) votre instance de Time Series Insights.
