---
title: Guide pratique pour utiliser des valeurs nommées dans les stratégies Gestion des API Azure
description: Découvrez comment utiliser des valeurs nommées dans les stratégies Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 478b80b021b4df36e2eccc37ac9c74f75e43a5bb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791624"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Guide pratique pour utiliser des valeurs nommées dans les stratégies Gestion des API Azure
Les stratégies Gestion des API sont une fonctionnalité puissante du système qui permet au portail Azure de modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions de la stratégie peuvent être construites à l’aide de valeurs de texte littéral, d’expressions de stratégie et de valeurs nommées. 

Chaque instance du service Gestion des API possède une collection de propriétés de paires clé/valeur, appelées « valeurs nommées », qui s’appliquent de manière globale à l’instance du service. Ces valeurs nommées peuvent être utilisées pour gérer les valeurs de chaîne constantes dans l’ensemble des stratégies et de la configuration des API. Chaque propriété peut avoir les attributs suivants :

| Attribut | Type | Description |
| --- | --- | --- |
| `Display name` |string |Chaîne alphanumérique utilisée pour référencer la propriété dans les stratégies. |
| `Value` |string |Valeur de la propriété. Elle ne peut pas être vide ni se composer uniquement d’espaces blancs. |
| `Secret` |booléenne|Détermine si la valeur est un secret et doit être chiffrée.|
| `Tags` |tableau de chaînes |Balises facultatives qui, lorsqu’elles sont fournies, peuvent être utilisées pour filtrer la liste de propriétés. |

![Valeurs nommées](./media/api-management-howto-properties/named-values.png)

Les valeurs de propriété peuvent contenir des chaînes littérales et des [expressions de stratégie](/azure/api-management/api-management-policy-expressions). Par exemple, la valeur de `ExpressionProperty` est une expression de stratégie qui retourne une chaîne contenant la date et l’heure actuelles. La propriété `ContosoHeaderValue` est marquée en tant que secret. Sa valeur ne s’affiche donc pas.

| Nom | Valeur | Secret | Balises |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateHeure.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Pour ajouter et modifier une propriété

![Ajouter une propriété](./media/api-management-howto-properties/add-property.png)

1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **Valeurs nommées**.
3. Appuyez sur **+ Ajouter**.

   Nom et Valeur doivent être renseignés. Si la valeur de propriété est un secret, cochez la case Il s’agit d’une clé secrète. Entrez une ou plusieurs balises facultatives pour aider à organiser vos valeurs nommées, puis cliquez sur Enregistrer.
4. Cliquez sur **Créer**.

Une fois que la propriété est créée, vous pouvez le modifier en cliquant sur la propriété. Si vous modifiez le nom de propriété, toutes les stratégies qui font référence à cette propriété sont automatiquement mises à jour pour utiliser le nouveau nom.

Pour plus d’informations sur la modification d’une propriété à l’aide de l’API REST, consultez [Modifier une propriété à l’aide de l’API REST](/rest/api/apimanagement/property?Patch).

## <a name="to-delete-a-property"></a>Pour supprimer une propriété

Pour supprimer une propriété, cliquez sur **Supprimer** en regard de la propriété à supprimer.

> [!IMPORTANT]
> Si la propriété est référencée par des stratégies, vous ne pouvez pas la supprimer correctement tant que vous ne l’avez pas supprimée de toutes les stratégies qui l’utilisent.
> 
> 

Pour plus d’informations sur la suppression d’une propriété à l’aide de l’API REST, consultez [Supprimer une propriété à l’aide de l’API REST](/rest/api/apimanagement/property?Delete).

## <a name="to-search-and-filter-named-values"></a>Pour rechercher et filtrer des valeurs nommées

L’onglet **Valeurs nommées** inclut des fonctionnalités de recherche et de filtrage pour vous aider à gérer vos valeurs nommées. Pour filtrer la liste des propriétés par nom de propriété, entrez un terme à rechercher dans la zone de texte **Propriétés de recherche** . Pour afficher toutes les valeurs nommées, effacez la zone de texte **Propriétés de recherche**, puis appuyez sur Entrée.

Pour filtrer la liste des propriétés en fonction des valeurs de balise, entrez une ou plusieurs balises dans la zone de texte **Filtrer par balises** . Pour afficher toutes les valeurs nommées, effacez la zone de texte **Filtrer par balises**, puis appuyez sur Entrée.

## <a name="to-use-a-property"></a>Pour utiliser une propriété

Pour utiliser une propriété dans une stratégie, placez le nom de la propriété dans une paire d’accolades telle que `{{ContosoHeader}}`, comme illustré dans l’exemple suivant :

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Dans cet exemple, `ContosoHeader` est utilisé comme nom d’en-tête d’une stratégie `set-header`, et `ContosoHeaderValue` comme valeur de cet en-tête. Lorsque cette stratégie est évaluée lors d’une demande ou d’une réponse à la passerelle Gestion des API, `{{ContosoHeader}}` et `{{ContosoHeaderValue}}` sont remplacés par leurs valeurs de propriété respectives.

Les valeurs nommées peuvent être utilisées comme attribut complet ou valeurs d’élément, comme indiqué dans l’exemple précédent. Elles peuvent également être insérées dans ou combinés avec une partie d’une expression de texte littéral, comme illustré dans l’exemple suivant : `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Les valeurs nommées peuvent également contenir des expressions de stratégie. Dans l’exemple suivant, `ExpressionProperty` est utilisé.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Lorsque cette stratégie est évaluée, `{{ExpressionProperty}}` est remplacé par sa valeur : `@(DateTime.Now.ToString())`. Étant donné que la valeur est une expression de stratégie, l’expression est évaluée et la stratégie poursuit son exécution.

Vous pouvez tester cette opération dans le portail des développeurs en appelant une opération qui a une stratégie dont l’étendue inclut des valeurs nommées. Dans l’exemple suivant, une opération est appelée avec les deux stratégies `set-header` de l’exemple précédent incluant des valeurs nommées. Notez que la réponse contient deux en-têtes personnalisés configurés à l’aide de stratégies et de valeurs nommées.

![Portail des développeurs][api-management-send-results]

Si vous examinez le [suivi de l’inspecteur d’API](api-management-howto-api-inspector.md) pour un appel qui inclut les deux exemples de stratégies précédents incluant des valeurs nommées, vous pouvez voir les deux stratégies `set-header` avec les valeurs de propriété insérées, ainsi que l’évaluation de l’expression de stratégie pour la propriété contenant l’expression de stratégie.

![Suivi de l’inspecteur d’API][api-management-api-inspector-trace]

Alors que les valeurs de propriété peuvent contenir des expressions de stratégie, elles ne peuvent pas contenir d’autres valeurs nommées. Si le texte contenant une référence de propriété est utilisé pour une valeur de propriété, comme `Property value text {{MyProperty}}`, cette référence de propriété n’est pas remplacée et est incluse dans la valeur de propriété.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’utilisation des stratégies
  * [Stratégies dans Gestion des API](api-management-howto-policies.md)
  * [Référence de stratégie](/azure/api-management/api-management-policies)
  * [Expressions de stratégie](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

