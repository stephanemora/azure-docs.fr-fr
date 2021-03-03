---
title: Classeurs Azure Monitor - Actions des liens
description: Utiliser les actions des liens dans les classeurs Azure Monitor
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100598945"
---
# <a name="link-actions"></a>Actions du lien

Les actions des liens sont accessibles via les étapes de création de liens dans les classeurs ou via les paramètres de colonne des [grilles](../visualize/workbooks-grid-visualizations.md), [titres](../visualize/workbooks-tile-visualizations.md) ou [graphiques](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Actions générales des liens

| Action de lien | Action en cas de clic |
|:------------- |:-------------|
| `Generic Details` | Présente les valeurs de la ligne dans un affichage contextuel de la grille des propriétés. |
| `Cell Details` | Présente la valeur de la cellule dans un affichage contextuel de la grille des propriétés. Utile lorsque la cellule contient un type dynamique avec des informations (par exemple, JSON avec des propriétés de requête telles que l’emplacement, l’instance de rôle, etc.). |
| `Url` | La valeur de la cellule doit être une URL HTTP valide, et la cellule sera un lien permettant d'ouvrir cette URL dans un nouvel onglet.|

## <a name="application-insights"></a>Application Insights

| Action de lien | Action en cas de clic |
|:------------- |:-------------|
| `Custom Event Details` | Ouvre les détails de la recherche d'Application Insights avec l'ID d'événement personnalisé (`itemId`) dans la cellule. |
| `* Details` | Similaire à Détails d’événement personnalisés, sauf pour les dépendances, les exceptions, les affichages de page, les requêtes et les traces. |
| `Custom Event User Flows` | Ouvre l'expérience Flux d'utilisateurs d'Application Insights avec le nom de l'événement personnalisé de la cellule. |
| `* User Flows` | Semblable aux Flux d'utilisateurs des événements personnalisés, sauf pour les exceptions, les consultations de pages et les requêtes. |
| `User Timeline` | Ouvre la chronologie de l'utilisateur avec l'identifiant utilisateur (`user_Id`) de la cellule. |
| `Session Timeline` | Ouvre l'expérience de recherche d'Application Insights pour la valeur figurant dans la cellule (par exemple, recherche du texte « abc », abc correspondant à la valeur figurant dans la cellule). |

`*` correspond à un caractère générique dans le tableau ci-dessus

## <a name="azure-resource"></a>Ressource Azure

| Action de lien | Action en cas de clic |
|:------------- |:-------------|
| `ARM Deployment` | Déploie un modèle Azure Resource Manager.  Lorsque cet élément est sélectionné, des champs supplémentaires sont affichés pour permettre à l'auteur de configurer le modèle Azure Resource Manager à ouvrir, les paramètres du modèle, etc. [Voir Paramètres des liens de déploiement Azure Resource Manager](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Crée une règle d'alerte pour une ressource.  |
| `Custom View` | Ouvre un affichage personnalisé. Lorsque cet élément est sélectionné, des champs supplémentaires sont affichés pour permettre à l'auteur de configurer l'extension de l'affichage, le nom de l'affichage et tous les paramètres utilisés pour ouvrir l'affichage. [Voir Affichages personnalisés](#custom-view-link-settings). |
| `Metrics` | Ouvre un affichage dédié aux métriques.  |
| `Resource overview` | Ouvre l'affichage de la ressource sur le portail en fonction de la valeur de l'ID de ressource figurant dans la cellule. L'auteur a également la possibilité de définir une valeur `submenu` permettant d'ouvrir un élément de menu spécifique dans l'affichage de la ressource. |
| `Workbook (template)` | Ouvre un modèle de classeur.  Lorsque cet élément est sélectionné, des champs supplémentaires sont affichés pour permettre à l'auteur de configurer le modèle à ouvrir, etc.  |

## <a name="link-settings"></a>Paramètres des liens

Lorsque vous utilisez le renderer de liens, les paramètres suivants sont disponibles :

![Capture d'écran de la fenêtre Paramètres des liens](./media/workbooks-link-actions/link-settings.png)

| Paramètre | Explication |
|:------------- |:-------------|
| `View to open` | Permet à l'auteur de sélectionner une des actions énumérées ci-dessus. |
| `Menu item` | Si « Vue d'ensemble de la ressource » est sélectionné, il s'agit de l'élément de menu à ouvrir à partir de la vue d'ensemble de la ressource. Cet élément peut être utilisé pour ouvrir des alertes ou des journaux d'activité au lieu de la « vue d'ensemble » de la ressource. Les valeurs des éléments de menu varient d'un `Resourcetype` Azure à l'autre.|
| `Link label` | Lorsqu'elle est spécifiée, cette valeur est affichée dans la colonne de la grille. Sinon, la valeur de la cellule est affichée. Si vous souhaitez qu'une autre valeur soit affichée, comme une carte thermique ou une icône, n'utilisez pas le renderer `Link` ; utilisez plutôt le renderer approprié et sélectionnez l'option `Make this item a link`. |
| `Open link in Context Blade` | Si spécifié, le lien s'ouvre sous forme d'affichage contextuel sur le côté droit de la fenêtre au lieu de s'ouvrir sous forme d'affichage complet. |

Lorsque vous utilisez l'option `Make this item a link`, les paramètres suivants sont disponibles :

| Paramètre | Explication |
|:------------- |:-------------|
| `Link value comes from` | Lors de l'affichage d'une cellule en tant que renderer avec un lien, ce champ indique d'où vient la valeur de « lien » à utiliser dans le lien, ce qui permet à l'auteur de faire son choix dans la liste déroulante des autres colonnes de la grille. Par exemple, la cellule peut être une valeur de carte thermique, alors que vous souhaitez que le lien ouvre la vue d'ensemble correspondant à l'ID de ressource figurant sur la ligne. Dans ce cas, vous devez définir la valeur de lien provenant du champ `Resource Id`.
| `View to open` | identique à ce qui précède. |
| `Menu item` | identique à ce qui précède. |
| `Open link in Context Blade` | identique à ce qui précède. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Paramètres des liens de déploiement Azure Resource Manager

Si le type de lien sélectionné est `ARM Deployment`, l'auteur doit spécifier des paramètres supplémentaires pour ouvrir un déploiement Azure Resource Manager. Deux onglets principaux sont disponibles pour les configurations.

### <a name="template-settings"></a>Paramètres de modèle

Cette section permet de définir la provenance du modèle et les paramètres utilisés pour exécuter le déploiement Azure Resource Manager.

| Source | Explication |
|:------------- |:-------------|
|`Resource group id comes from` | L'ID de ressource est utilisé pour gérer les ressources déployées. L'abonnement est utilisé pour gérer les ressources déployées et les coûts. Les groupes de ressources sont utilisés comme des dossiers pour organiser et gérer toutes vos ressources. Si cette valeur n'est pas spécifiée, le déploiement échouera. Sélectionnez `Cell`, `Column`, `Static Value` ou `Parameter` dans [Sources de liens](#link-sources).|
|`ARM template URI from` | URI du modèle Azure Resource Manager. L'URI du modèle doit être accessible aux utilisateurs qui déploieront le modèle. Sélectionnez `Cell`, `Column`, `Parameter` ou `Static Value` dans [Sources de liens](#link-sources). Les débutants sont invités à consulter nos [modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Cette section définit les paramètres de modèle utilisés pour l'URI du modèle défini ci-dessus. Ces paramètres seront utilisés pour déployer le modèle sur la page d'exécution. La grille contient un bouton de développement de la barre d'outils pour configurer les paramètres avec les noms définis dans l'URI du modèle et définir celui-ci sur des valeurs statiques vides. Cette option ne peut être utilisée que si aucun paramètre ne figure dans la grille et que l'URI du modèle a été défini. La section inférieure offre un aperçu de la sortie des paramètres. Sélectionnez Actualiser pour mettre à jour l'aperçu avec les modifications actuelles. Les paramètres sont généralement des valeurs, tandis que les références peuvent pointer vers des secrets de coffre de clés auxquels l'utilisateur a accès. <br/><br/> **Limitation relative au panneau Visionneuse de modèles** : n'affiche pas correctement les paramètres de référence et se présente sous forme de valeur/null, ce qui empêche les utilisateurs de déployer correctement les paramètres de référence à partir de l'onglet Visionneuse de modèles.|

![Capture d'écran de la fenêtre Paramètres des modèles d'Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Paramètres de l'expérience utilisateur

Cette section permet de configurer ce que les utilisateurs verront avant de lancer le déploiement d'Azure Resource Manager.

| Source | Explication |
|:------------- |:-------------|
|`Title from` | Titre utilisé dans l'affichage d'exécution. Sélectionnez `Cell`, `Column`, `Parameter` ou `Static Value` dans [Sources de liens](#link-sources).|
|`Description from` | Il s'agit du texte Markdown utilisé pour fournir une description utile aux utilisateurs lorsqu'ils souhaitent déployer le modèle. Sélectionnez `Cell`, `Column`, `Parameter` ou `Static Value` dans [Sources de liens](#link-sources). <br/><br/> **REMARQUE :** si `Static Value` est sélectionné, une zone de texte de plusieurs lignes apparaît. Cette zone de texte vous permet de résoudre les paramètres en utilisant `{paramName}`. Vous pouvez également traiter les colonnes comme des paramètres en ajoutant `_column` après leur nom (par exemple, `{columnName_column}`). Dans l'illustration ci-dessous, nous pouvons référencer la colonne `VMName` en écrivant `{VMName_column}`. La valeur qui suit le signe deux-points est le [formateur de paramètres](../visualize/workbooks-parameters.md#parameter-options) ; dans ce cas, il s'agit de `value`.|
|`Run button text from` | Étiquette utilisée sur le bouton Exécuter pour déployer le modèle Azure Resource Manager. C'est ce que les utilisateurs sélectionneront pour commencer à déployer le modèle Azure Resource Manager.|

![Capture d'écran de la fenêtre Paramètres de l'expérience utilisateur d'Azure Resource Manager](./media/workbooks-link-actions/ux-settings.png)

Une fois ces configurations définies, lorsque les utilisateurs sélectionnent le lien, l'affichage contenant l'expérience utilisateur décrite dans les paramètres de l'expérience utilisateur s'ouvre. Si l'utilisateur sélectionne `Run button text from`, un modèle Azure Resource Manager contenant les valeurs définies dans les [Paramètres des modèles](#template-settings) est déployé. L'option Afficher le modèle ouvrira l'onglet Visionneuse de modèles pour permettre à l'utilisateur d'examiner le modèle et les paramètres avant le déploiement.

![Capture d'écran de l'affichage d'exécution d'Azure Resource Manager](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Paramètres des liens des affichages personnalisés

Utilisez ceci pour ouvrir les affichages personnalisés sur le portail Azure. Vérifiez l'ensemble de la configuration et des paramètres. Des valeurs incorrectes entraîneront des erreurs sur le portail ou empêcheront d'ouvrir correctement les affichages. Les paramètres peuvent être configurés de deux façons : via le `Form` ou via l'`URL`.

> [!NOTE]
> Les affichages comportant un menu ne peuvent pas être ouverts dans un onglet contextuel. Si un affichage comportant un menu est configuré pour s'ouvrir dans un onglet contextuel, aucun onglet contextuel ne s'affichera lorsque le lien sera sélectionné.

### <a name="form"></a>Formulaire

| Source | Explication |
|:------------- |:-------------|
|`Extension name` | Nom de l'extension qui héberge le nom de l'affichage.|
|`View name` | Nom de l'affichage à ouvrir.|

#### <a name="view-inputs"></a>Entrées des affichages

Il existe deux types d'entrées : les grilles et les entrées JSON. Utilisez `Grid` pour les entrées simples de type clé et valeur, ou sélectionnez `JSON` pour spécifier une entrée JSON imbriquée.

- Grille
    - `Parameter Name` : nom du paramètre d'entrée de l'affichage.
    - `Parameter Comes From` : provenance de la valeur du paramètre de l'affichage. Sélectionnez `Cell`, `Column`, `Parameter` ou `Static Value` dans [Sources de liens](#link-sources).
    > [!NOTE]
    > Si `Static Value` est sélectionné, les paramètres peuvent être résolus en utilisant un lien entre crochets `{paramName}` dans la zone de texte. Les colonnes peuvent être traitées comme des colonnes de paramètres en ajoutant `_column` après leur nom (par exemple, `{columnName_column}`).

    - `Parameter Value` : en fonction de `Parameter Comes From`, il s'agira d'une liste déroulante de paramètres disponibles, de colonnes, ou d'une valeur statique.

    ![Capture d'écran de la fenêtre Modifier les paramètres de colonne dans laquelle les paramètres d'affichage personnalisé sont définis sur Formulaire.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Spécifiez votre entrée d'onglet au format JSON dans l'éditeur. Comme pour le mode `Grid`, les paramètres et les colonnes peuvent être référencés en utilisant `{paramName}` pour les paramètres et `{columnName_column}` pour les colonnes. En sélectionnant `Show JSON Sample`, vous obtiendrez la sortie attendue de tous les paramètres et colonnes résolus pour l'entrée de l'affichage.

    ![Capture d'écran de la fenêtre Ouvrir les paramètres des affichages personnalisés dans laquelle l'entrée de l'affichage est définie sur JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Collez une URL de portail contenant l'extension, le nom de l'affichage et toutes les entrées nécessaires pour ouvrir l'affichage. Dès que `Initialize Settings` sera sélectionné, le `Form` de l'auteur sera renseigné pour ajouter/modifier/supprimer les entrées de l'affichage.

![Capture d'écran de la fenêtre Modifier les paramètres de colonne dans laquelle les paramètres d'affichage personnalisé sont définis sur URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Paramètres des liens des classeurs (modèles)

Si le type de lien sélectionné est `Workbook (Template)`, l'auteur doit spécifier des paramètres supplémentaires pour ouvrir le modèle de classeur approprié. Les paramètres ci-dessous comportent des options pour déterminer la façon dont la grille trouvera la valeur appropriée pour chacun des paramètres.

| Paramètre | Explication |
|:------------- |:-------------|
| `Workbook owner Resource Id` | ID de la ressource Azure qui « possède » le classeur. Il s'agit généralement d'une ressource Application Insights ou d'un espace de travail Log Analytics. Dans Azure Monitor, il peut également s'agir de la chaîne littérale `"Azure Monitor"`. Il s'agit de ce à quoi le classeur sera lié lors de son enregistrement. |
| `Workbook resources` | Tableau d'ID de ressources Azure spécifiant la ressource par défaut utilisée dans le classeur. Par exemple, si le modèle ouvert affiche des métriques de machines virtuelles, les valeurs disponibles ici seront des ID de ressources de machines virtuelles.  Souvent, le propriétaire et les ressources sont définis sur les mêmes paramètres. |
| `Template Id` | Spécifiez l'ID du modèle à ouvrir. S'il s'agit d'un modèle de communauté provenant de la galerie (le cas le plus courant), ajoutez le préfixe `Community-` au chemin d'accès de celui-ci (par exemple, `Community-Workbooks/Performance/Apdex` pour le modèle `Workbooks/Performance/Apdex`). Dans le cas d'un lien d'accès à un classeur/modèle enregistré, il s'agit de l'ID de ressource Azure complet de cet élément. |
| `Workbook Type` | Spécifiez le type de modèle de classeur à ouvrir. Les cas les plus courants ont recours à l'option `Default` ou `Workbook` pour utiliser la valeur figurant dans le classeur actuel. |
| `Gallery Type` | Spécifie le type de galerie qui sera affiché dans l'affichage « Galerie » du modèle qui s'ouvre. Les cas les plus courants ont recours à l'option `Default` ou `Workbook` pour utiliser la valeur figurant dans le classeur actuel. |
|`Location comes from` | Le champ d'emplacement doit être spécifié si vous ouvrez une ressource spécifique du classeur. Si l'emplacement n'est pas spécifié, la recherche du contenu du classeur est beaucoup plus lente. Si vous connaissez l'emplacement, spécifiez-le. Si vous ne le connaissez ou si vous ouvrez un modèle sans emplacement spécifique, conservez la valeur « Par défaut » de ce champ.|
|`Pass specific parameters to template` | Sélectionnez cet élément pour transmettre des paramètres spécifiques au modèle. Si cet élément est sélectionné, seuls les paramètres spécifiés sont transmis au modèle. Sinon, tous les paramètres du classeur sont transmis. Dans ce cas, les *noms* des paramètres doivent être identiques dans les deux classeurs pour que cette valeur fonctionne.|
|`Workbook Template Parameters` | Cette section définit les paramètres qui sont transmis au modèle cible. Le nom du paramètre doit correspondre à celui qui figure dans le modèle cible. Sélectionnez une valeur dans `Cell`, `Column`, `Parameter` et `Static Value`. Le nom et la valeur ne doivent pas être vides pour transmettre ce paramètre au modèle cible.|

Pour chacun des paramètres ci-dessus, l'auteur doit choisir d'où viendra la valeur du classeur associé au lien. Voir [Sources de liens](#link-sources)

Lorsque le lien du classeur est ouvert, toutes les valeurs configurées à partir des paramètres ci-dessus sont transmises au nouvel affichage du classeur.

![Capture d'écran de la fenêtre Paramètres des liens des classeurs](./media/workbooks-link-actions/workbook-link-settings.png)

![Capture d'écran des paramètres des modèles de classeurs](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Sources de liens

| Source | Explication |
|:------------- |:-------------|
| `Cell` | La valeur figurant dans cette cellule de la grille sera utilisée comme valeur de lien. |
| `Column` | Lorsque cet élément est sélectionné, un nouveau champ apparaît pour permettre à l'auteur de sélectionner une autre colonne de la grille.  La valeur de cette colonne pour la ligne sera utilisée dans la valeur du lien. Cette méthode est généralement utilisée pour permettre à chaque ligne d'une grille d'ouvrir un modèle différent, en définissant le champ `Template Id` sur `column`, ou pour ouvrir le même modèle de classeur pour différentes ressources, si le champ `Workbook resources` est défini sur une colonne qui contient un ID de ressource Azure. |
| `Parameter` | Lorsque cet élément est sélectionné, un nouveau champ apparaît pour permettre à l'auteur de sélectionner un paramètre. La valeur de ce paramètre sera utilisée lorsque le lien sera sélectionné. |
| `Static value` | Lorsque cet élément est sélectionné, un nouveau champ apparaît pour permettre à l'auteur d'entrer une valeur statique qui sera utilisée dans le classeur associé au lien. Cette méthode est généralement employée lorsque toutes les lignes de la grille utilisent la même valeur pour un champ. |
| `Step` | Utilisez la valeur définie dans l'étape actuelle du classeur. Cette méthode est couramment utilisée dans les étapes de requête et de métriques pour définir les ressources du classeur associé au lien avec celles utilisées *dans l'étape de requête/métriques*, et non dans le classeur actuel. |
| `Workbook` | Utilisez la valeur définie dans le classeur actuel. |
| `Default` | Utilisez la valeur par défaut qui serait utilisée si aucune valeur n'était spécifiée. Cette méthode est couramment utilisée pour le Type de galerie, où la galerie par défaut est définie par le type de la ressource propriétaire. |

## <a name="next-steps"></a>Étapes suivantes

- [Contrôlez](../visualize/workbooks-access-control.md) et partagez l’accès à vos ressources de classeur.
- Découvrez comment utiliser des [groupes dans des classeurs](../visualize/workbooks-groups.md).