---
title: Créer des vues pour analyser les données de journal dans Azure Monitor | Microsoft Docs
description: En utilisant le Concepteur de vues dans Azure Monitor, vous pouvez créer des vues personnalisées qui sont affichés dans le portail Azure et contiennent une variété de visualisations de données dans l’espace de travail Analytique de journal. Cet article contient une présentation du Concepteur de vues et présente des procédures de création et de modification des vues personnalisées.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: f07fc2f03ad72e7ee0fd408782b8fe845c88e780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342007"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Créer des vues personnalisées à l’aide du Concepteur de vues dans Azure Monitor
En utilisant le Concepteur de vues dans Azure Monitor, vous pouvez créer une variété de vues personnalisées dans le portail Azure qui peut vous aider à visualiser les données dans votre espace de travail Analytique de journal. Cet article fournit une présentation du Concepteur de vues et des procédures de création et de modification des vues personnalisées.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pour plus d’informations sur le Concepteur de vues, consultez :

* [Référence de vignette](view-designer-tiles.md) : guide de référence pour les paramètres de chacune des vignettes disponibles dans vos vues personnalisées.
* [Référence des composants de visualisation](view-designer-parts.md) : guide de référence pour les paramètres des composants de visualisation disponibles dans vos vues personnalisées.


## <a name="concepts"></a>Concepts
Les vues sont affichées dans le moniteur Azure **vue d’ensemble** page dans le portail Azure. Ouvrez cette page à partir du menu **Azure Monitor** en cliquant sur **Plus** sous la section **Insights**. Les vignettes de chaque vue personnalisée sont affichées par ordre alphabétique, et les vignettes pour les solutions de surveillance sont installés le même espace de travail.

![Page Vue d’ensemble](media/view-designer/overview-page.png)

Les vues que vous créez avec le Concepteur de vues contiennent les éléments décrits dans le tableau suivant :

| Partie | Description |
|:--- |:--- |
| Vignettes | Sont affichés sur votre Azure Monitor **vue d’ensemble** page. Chaque vignette affiche une synthèse visuelle de la vue personnalisée qu’elle représente. Chaque type de vignette fournit une visualisation différente de vos enregistrements. Vous sélectionnez une vignette pour afficher une vue personnalisée. |
| Vue personnalisée | Affichée quand vous sélectionnez une vignette. Chaque vue contient un ou plusieurs composants de visualisation. |
| Composants de visualisation | Présente une visualisation de données dans l’espace de travail Analytique de journal basé sur un ou plusieurs [enregistrer des requêtes](../log-query/log-query-overview.md). La plupart des composants incluent un en-tête, qui fournit une visualisation d’ensemble, et une liste, qui montre les premiers résultats. Chaque type de composant produit différentes visualisations des enregistrements dans l’espace de travail Log Analytics. Sélectionner des éléments dans le composant pour effectuer une requête de journal qui fournit des enregistrements détaillés. |

## <a name="required-permissions"></a>Autorisations requises
Vous avez besoin au moins [les autorisations de niveau contributeur](manage-access.md#manage-accounts-and-users) dans l’espace de travail Analytique de journal pour créer ou modifier des vues. Si vous n’avez pas cette autorisation, puis l’option du Concepteur de vue ne s’afficheront dans le menu.


## <a name="work-with-an-existing-view"></a>Utiliser une vue existante
Les vues créées avec le Concepteur de vues affichent les options suivantes :

![Menu Vue d’ensemble](media/view-designer/overview-menu.png)

Les options sont décrites dans le tableau suivant :

| Option | Description |
|:--|:--|
| Actualiser   | Actualise la vue avec les données les plus récentes. | 
| Journaux d’activité      | Ouvre le [Analytique de journal](../log-query/portals.md) pour analyser les données avec des requêtes de journal. |
| Modifier       | Ouvre la vue dans le Concepteur de vues pour modifier son contenu et sa configuration.  |
| Cloner      | Crée une vue et l’ouvre dans le Concepteur de vues. Le nom de la nouvelle vue est identique à celui de la vue d’origine, avec le mot *Copy* ajouté à la fin. |
| Plage de dates | Définissez un filtre de date et d’heure pour les données incluses dans la vue. Cette plage de dates est appliquée avant les plages de dates définies dans les requêtes dans la vue.  |
| +          | Définissez un filtre personnalisé qui est défini pour la vue. |


## <a name="create-a-new-view"></a>Créer une vue
Vous pouvez créer une nouvelle vue dans le Concepteur de vues en sélectionnant **Concepteur de vues** dans le menu de votre espace de travail Log Analytics.

![Vignette Concepteur de vues](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Utiliser le Concepteur de vues
Vous utilisez le Concepteur de vues pour créer des vues ou les modifier. 

Le Concepteur de vues comporte trois volets : 
* **Conception** : contient la vue personnalisée que vous créez ou modifiez. 
* **Contrôles** : contient les vignettes et les composants que vous ajoutez au volet **Conception**. 
* **Propriétés** : affiche les propriétés des vignettes ou des composants sélectionnés.

![Concepteur de vues](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurer la vignette de vue
Un vue personnalisée ne peut avoir qu’une seule vignette. Pour afficher la vignette active ou en sélectionner une autre, dans le volet **Contrôle** sélectionnez l’onglet **Vignette**. Le volet **Propriétés** affiche les propriétés de la vignette active. 

Vous pouvez configurer les propriétés de la vignette conformément aux informations fournies dans [Référence de la vignette](view-designer-tiles.md), puis cliquer sur **Appliquer** pour enregistrer les modifications.

### <a name="configure-the-visualization-parts"></a>Configurer les composants de visualisation
Une vue peut inclure une nombre quelconque de composants de visualisation. Pour ajouter des composants à une vue, sélectionnez l’onglet **Vue**, puis sélectionnez un composant de visualisation. Le volet **Propriétés** affiche les propriétés du composant sélectionné. 

Vous pouvez configurer les propriétés de la vue conformément aux informations fournies dans la [Référence du composant de visualisation](view-designer-parts.md), puis cliquer sur **Appliquer** pour enregistrer les modifications.

Les vues ne comprennent qu’une seule ligne de composants de visualisation. Vous pouvez réorganiser les composants existants en les faisant glisser vers un nouvel emplacement.

Vous pouvez supprimer un composant de visualisation de la vue en sélectionnant le bouton **X** dans l’angle supérieur droit du composant.


### <a name="menu-options"></a>Options de menu
Les options pour l’utilisation des vues en mode d’édition sont décrites dans le tableau suivant.

![Menu Edition](media/view-designer/edit-menu.png)

| Option | Description |
|:--|:--|
| Enregistrer        | Enregistre les modifications et ferme la vue. |
| Annuler      | Ignore les modifications et ferme la vue. |
| Supprimer la vue | Supprime la vue. |
| Exportation      | Exporte la vue vers un [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) que vous pouvez importer dans un autre espace de travail. Le nom du fichier est identique à celui de la vue, avec une extension *omsview*. |
| Importer      | Importe le fichier *omsview* que vous avez exporté à partir d’un autre espace de travail. Cette opération remplace la configuration de la vue existante. |
| Cloner       | Crée une vue et l’ouvre dans le Concepteur de vues. Le nom de la nouvelle vue est identique à celui de la vue d’origine, avec le mot *Copy* ajouté à la fin. |

## <a name="next-steps"></a>Étapes suivantes
* Ajouter des [vignettes](view-designer-tiles.md) à votre vue personnalisée.
* Ajouter des [composants de visualisation](view-designer-parts.md) à votre vue personnalisée.
