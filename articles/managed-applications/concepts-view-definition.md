---
title: Présentation de la définition de vue dans les applications managées Azure | Microsoft Docs
description: Décrit le concept de création d'une définition de vue pour les applications managées Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479054"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Artefact de définition de vue dans les applications managées Azure

La définition de vue est un artefact facultatif dans les applications managées Azure. Elle permet de personnaliser la page de présentation et d'ajouter plus de vues telles que Métriques et Ressources personnalisées.

Cet article présente l'artefact de définition de vue et ses fonctionnalités.

## <a name="view-definition-artifact"></a>Afficher un artefact de définition

L’artefact de définition de vue doit être nommé **viewDefinition.json** et placé au même niveau que **createUiDefinition.json** et **mainTemplate.json** dans le package .zip qui crée une définition d’application managée. Pour savoir comment créer le package .zip et publier une définition d'application managée, consultez [Publier une définition d’application managée](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Schéma de définition de vue

Le fichier **viewDefinition.json** présente une seule propriété `views` de niveau supérieur, qui est un tableau de vues. Chaque vue s'affiche dans l'interface utilisateur de l'application managée sous forme d'élément de menu distinct dans la table des matières. Chaque vue possède une propriété `kind` qui définit le type de vue. Elle doit être définie sur l’une des valeurs suivantes : [Vue d’ensemble](#overview), [Métriques](#metrics), [Ressources personnalisées](#custom-resources). Pour plus d’informations, consultez le [schéma JSON actuel pour viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exemple de schéma JSON pour la définition de vue :

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Vue d'ensemble

`"kind": "Overview"`

Lorsque vous fournissez cette vue dans **viewDefinition.json**, elle remplace la page Vue d'ensemble par défaut dans votre application managée.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|en-tête|Non|En-tête de la page Vue d'ensemble.|
|description|Non|Description de votre application managée.|
|commandes|Non|Pour le tableau de boutons de barre d’outils supplémentaires de la page Vue d’ensemble, consultez [commandes](#commands).|

## <a name="metrics"></a>Mesures

`"kind": "Metrics"`

La vue Métriques vous permet de collecter et d'agréger les données de vos ressources d’application managée dans [Métriques Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|displayName|Non|Titre affiché de la vue.|
|version|Non|Version de la plateforme utilisée pour afficher la vue.|
|graphiques|OUI|Tableau de graphiques de la page de métriques.|

### <a name="chart"></a>Graphique

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|displayName|OUI|Titre affiché du graphique.|
|chartType|Non|Visualisation à utiliser pour ce graphique. Graphique en courbes utilisé par défaut. Types de graphiques pris en charge : `Bar, Line, Area, Scatter`.|
|Mesures|OUI|Tableau de métriques à tracer sur ce graphique. Pour en savoir plus sur les métriques prises en charge dans le portail Azure, consultez [Métriques prises en charge avec Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Métrique

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|Nom|OUI|Nom de la mesure.|
|aggregationType|OUI|Type d’agrégation à utiliser pour cette métrique. Types d’agrégation pris en charge : `none, sum, min, max, avg, unique, percentile, count`|
|namespace|Non|Informations supplémentaires à utiliser pour déterminer le fournisseur de métriques qui convient.|
|resourceTagFilter|Non|Tableau de balises de ressource (seront séparées par le mot `or`) pour lesquelles les métriques s'affichent. S’applique en plus du filtre de type de ressource.|
|resourceType|OUI|Type de ressource pour laquelle les métriques s'affichent.|

## <a name="custom-resources"></a>Ressources personnalisées

`"kind": "CustomResources"`

Vous pouvez définir plusieurs vues de ce type. Chaque vue représente un type de ressource personnalisé **unique** dans le fournisseur personnalisé que vous avez défini dans **mainTemplate.json**. Pour une introduction aux fournisseurs personnalisés, consultez [Présentation de la préversion d’Azure Custom Providers](custom-providers-overview.md).

Dans cette vue, vous pouvez effectuer des opérations GET, PUT, DELETE et POST pour votre type de ressource personnalisé. Les opérations POST peuvent correspondre à des actions personnalisées globales ou actions personnalisées dans le contexte de votre type de ressource personnalisé.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|displayName|OUI|Titre affiché de la vue. Le titre doit être **unique** pour chaque vue CustomResources de votre fichier **viewDefinition.json**.|
|version|Non|Version de la plateforme utilisée pour afficher la vue.|
|resourceType|OUI|Type de ressource personnalisé. Doit correspondre à un type de ressource personnalisé **unique** de votre fournisseur personnalisé.|
|createUIDefinition|Non|Créez le schéma de définition d’interface utilisateur pour créer une commande de ressource personnalisée. Pour une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md)|
|commandes|Non|Pour le tableau de boutons de barre d’outils supplémentaires de la page Ressources personnalisées, consultez [commandes](#commands).|
|colonnes|Non|Tableau de colonnes de la ressource personnalisée. S'il n'est pas défini, la colonne `name` s'affiche par défaut. La colonne doit contenir `"key"` et `"displayName"`. Pour la clé, indiquez la clé de la propriété à afficher dans une vue. Si elle est imbriquée, utilisez un point en tant que séparateur, par exemple, `"key": "name"` ou `"key": "properties.property1"`. Pour le nom d’affichage, indiquez le nom complet de la propriété à afficher dans une vue. Vous pouvez également fournir une propriété `"optional"`. Lorsqu'elle est définie sur true, la colonne est masquée dans une vue par défaut.|

## <a name="commands"></a>Commandes

Les commandes correspondent à un tableau de boutons de barre d’outils supplémentaires qui s'affichent sur la page. Chaque commande représente une action POST de votre fournisseur personnalisé Azure défini dans **mainTemplate.json**. Pour une présentation des fournisseurs personnalisés, consultez [Vue d'ensemble des fournisseurs personnalisés Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Propriété|Obligatoire|Description|
|---------|---------|---------|
|displayName|OUI|Nom affiché du bouton de commande.|
|chemin d’accès|OUI|Nom de l'action du fournisseur personnalisé. L’action doit être définie dans **mainTemplate.json**.|
|icon|Non|Icône du bouton de commande. La liste des icônes prises en charge est définie dans [Schéma JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Non|Créez le schéma de définition d’interface utilisateur pour la commande. Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Étapes suivantes

- Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](overview.md).
- Pour une présentation des fournisseurs personnalisés, consultez [Vue d'ensemble des fournisseurs personnalisés Azure](custom-providers-overview.md).
