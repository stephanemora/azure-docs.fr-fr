---
title: Élément d’interface utilisateur DropDown
description: Décrit l’élément d’interface utilisateur Microsoft.Common.DropDown pour le Portail Azure. Permet de sélectionner les options disponibles lors du déploiement d’une application managée.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004444"
---
# <a name="microsoftcommondropdown-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.DropDown

Contrôle de sélection avec liste déroulante. Vous pouvez autoriser la sélection d’un seul élément ou celle de plusieurs éléments. Vous pouvez également joindre une description aux éléments.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

L’élément DropDown a différentes options qui déterminent son apparence dans le portail.

Quand un seul élément peut être sélectionné, le contrôle s’affiche comme suit :

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Sélection unique Microsoft.Common.DropDown":::

Lorsque des descriptions sont incluses, le contrôle s’affiche comme suit :

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Sélection unique Microsoft.Common.DropDown avec descriptions":::

Lorsque la sélection multiple est activée, le contrôle ajoute une option **Sélectionner tout** et des cases à cocher pour sélectionner plusieurs éléments :

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Sélection multiple Microsoft.Common.DropDown":::

Les descriptions peuvent être incluses lorsque la sélection multiple est activée.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Sélection multiple Microsoft.Common.DropDown avec descriptions":::

Lorsque le filtrage est activé, le contrôle comprend une zone de texte pour ajouter la valeur de filtrage.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Sélection multiple Microsoft.Common.DropDown avec descriptions":::

## <a name="schema"></a>schéma

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Exemple de sortie

```json
"two"
```

## <a name="remarks"></a>Notes

- Utilisez `multiselect` pour spécifier si les utilisateurs peuvent sélectionner plus d’un élément.
- Par défaut, `selectAll` est défini `true` lorsque la sélection multiple est activée.
- La propriété `filter` permet aux utilisateurs de rechercher parmi une longue liste d’options.
- L’étiquette de `constraints.allowedValues` est le texte qui s’affiche pour un élément, et sa valeur est la valeur de sortie de l’élément sélectionné lors de la sélection.
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné. La valeur par défaut est **null**.
- `constraints.allowedValues` doit avoir au moins un élément.
- Pour émuler une valeur qui n’est pas requise, ajoutez un élément avec une étiquette et la valeur de `""` (chaîne vide) à `constraints.allowedValues`.
- La propriété `defaultDescription` est utilisée pour les éléments qui n’ont pas de description.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
