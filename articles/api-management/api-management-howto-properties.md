---
title: Guide pratique pour utiliser des valeurs nommées dans les stratégies Gestion des API Azure
description: Découvrez comment utiliser des valeurs nommées dans les stratégies Gestion des API Azure. Les valeurs nommées peuvent contenir des chaînes littérales, des expressions de stratégie et des secrets stockés dans Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 344500d5635f591b34a45130c7dd6b63659ad84d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491008"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Utiliser des valeurs nommées dans les stratégies Gestion des API Azure

Les [stratégies Gestion des API](api-management-howto-policies.md) sont une fonctionnalité puissante du système qui permet à l’éditeur de modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions de la stratégie peuvent être construites à l’aide de valeurs de texte littéral, d’expressions de stratégie et de valeurs nommées.

Les *valeurs nommées* représentent une collection globale de paires nom/valeur dans chaque instance Gestion des API. Il n’existe aucune limite imposée quant au nombre d’éléments que peut contenir une collection. Les valeurs nommées peuvent être utilisées pour gérer les valeurs de chaîne constantes et les secrets dans l’ensemble des stratégies et de la configuration des API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Valeurs nommées dans le portail Azure":::

## <a name="value-types"></a>Types de valeur

|Type  |Description  |
|---------|---------|
|Plain     |  Chaîne littérale ou expression de stratégie     |
|Secret     |   Chaîne littérale ou expression de stratégie chiffrée par Gestion des API      |
|[Key vault](#key-vault-secrets)     |  Identificateur d’un secret stocké dans un coffre de clés Azure.      |

Les valeurs brutes ou les secrets peuvent contenir des [expressions de stratégie](./api-management-policy-expressions.md). Par exemple, l’expression `@(DateTime.Now.ToString())` retourne une chaîne contenant la date et l’heure actuelles.

Pour plus d’informations sur les attributs de valeurs nommées, consultez les [informations de référence sur l’API REST](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate) dans Gestion des API.

## <a name="key-vault-secrets"></a>Secrets Key Vault

Les valeurs de secret peuvent être stockées en tant que chaînes chiffrées dans Gestion des API (secrets personnalisés) ou en référençant les secrets dans [Azure Key Vault](../key-vault/general/overview.md). 

L’utilisation de secrets Key Vault est recommandée car elle permet d’améliorer la sécurité de Gestion des API :

* Les secrets stockés dans les coffres de clés peuvent être réutilisés dans les services
* Des [stratégies d’accès](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) granulaires peuvent être appliquées aux secrets
* Les secrets mis à jour dans le coffre de clés sont automatiquement permutés dans Gestion des API. Après la mise à jour dans le coffre de clés, une valeur nommée dans Gestion des API est mise à jour dans les 4 heures. Vous pouvez également actualiser manuellement le secret à l’aide du portail Azure ou par le biais de l’API REST de gestion.

### <a name="prerequisites-for-key-vault-integration"></a>Conditions préalables à l’intégration d’un coffre de clés

1. Pour connaître la procédure de création d’un coffre de clés, consultez [Démarrage rapide : Créer un coffre de clés avec le portail Azure](../key-vault/general/quick-create-portal.md).
1. Activer une[ identité managée](api-management-howto-use-managed-service-identity.md) attribuée par le système ou par l’utilisateur dans l’instance Gestion des API.
1. Affectez une [stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md) à l’identité managée avec les autorisations pour récupérer et répertorier les secrets du coffre. Pour ajouter la stratégie :
    1. Dans le portail, accédez à votre coffre de clés.
    1. Sélectionnez **Paramètres > Stratégies d’accès > +Ajouter une stratégie d’accès**.
    1. Sélectionnez **Autorisations du secret**, puis **Get** (Obtenir) et **List** (Lister).
    1. Dans **Sélectionner le principal**, sélectionnez le nom de ressource de votre identité managée. Si vous utilisez une identité attribuée par le système, le principal est le nom de votre instance Gestion des API.
1. Créez ou importez un secret dans le coffre de clés. Consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../key-vault/secrets/quick-create-portal.md).

Pour utiliser le secret du coffre de clés, [ajoutez ou modifiez une valeur nommée](#add-or-edit-a-named-value), puis spécifiez un type de **coffre de clés**. Sélectionnez le secret dans le coffre de clés.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Ajouter ou modifier une valeur nommée

### <a name="add-a-key-vault-secret"></a>Ajouter un secret de coffre de clés

Voir [Conditions préalables à l’intégration d’un coffre de clés](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Lorsque vous utilisez un secret de coffre de clés dans Gestion des API, veillez à ne pas supprimer le secret, le coffre de clés ou l’identité managée utilisée pour accéder au coffre de clés.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **API**, sélectionnez **Valeurs nommées** > **Ajouter**.
1. Entrez un identificateur de **nom**, puis un **nom d’affichage** utilisé pour référencer la propriété dans les stratégies.
1. Dans **Type de valeur**, sélectionnez **Coffre de clés**.
1. Entrez l’identificateur d’un secret de coffre de clés (sans version), ou choisissez **Sélectionner** pour choisir un secret dans un coffre de clés.
    > [!IMPORTANT]
    > Si vous entrez vous-même un identificateur de secret de coffre de clés, assurez-vous qu’il ne contient pas d’informations de version. Sinon, le secret n’est pas automatiquement permuté dans Gestion des API après une mise à jour dans le coffre de clés.
1. Dans **Identité du client**, sélectionnez une entité managée affectée par le système ou une entité managée existante affectée par l’utilisateur. Découvrez comment [ajouter ou modifier des identités managées dans votre service Gestion des API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L’identité a besoin d’autorisations pour obtenir et lister les secrets du coffre de clés. Si vous n’avez pas encore configuré l’accès au coffre de clés, Gestion des API vous invite à configurer automatiquement l’identité avec les autorisations nécessaires.
1. Ajoutez une ou plusieurs balises facultatives pour faciliter l’organisation de vos valeurs nommées, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Ajouter une valeur de secret de coffre de clés":::

### <a name="add-a-plain-or-secret-value"></a>Ajouter une valeur brute ou secrète

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **API**, sélectionnez **Valeurs nommées** > **Ajouter**.
1. Entrez un identificateur de **nom**, puis un **nom d’affichage** utilisé pour référencer la propriété dans les stratégies.
1. Dans **Type de valeur**, sélectionnez **Plain** (Brute) ou **Secret** (Secrète).
1. Dans **Valeur**, entrez une chaîne ou une expression de stratégie.
1. Ajoutez une ou plusieurs balises facultatives pour faciliter l’organisation de vos valeurs nommées, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Create** (Créer).

Une fois la valeur nommée créée, vous pouvez la modifier en sélectionnant son nom. Si vous modifiez le nom d’affichage, toutes les stratégies qui font référence à cette valeur nommée sont automatiquement mises à jour pour utiliser le nouveau nom d’affichage.

## <a name="use-a-named-value"></a>Utiliser une valeur nommée

Les exemples de cette section utilisent les valeurs nommées affichées dans le tableau suivant.

| Nom               | Valeur                      | Secret | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Faux  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Faux  | 

Pour utiliser une valeur nommée dans une stratégie, placez son nom d’affichage dans une paire d’accolades telle que `{{ContosoHeader}}`, comme illustré dans l’exemple suivant :

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Dans cet exemple, `ContosoHeader` est utilisé comme nom d’en-tête d’une stratégie `set-header`, et `ContosoHeaderValue` comme valeur de cet en-tête. Lorsque cette stratégie est évaluée lors d’une demande ou d’une réponse à la passerelle Gestion des API, `{{ContosoHeader}}` et `{{ContosoHeaderValue}}` sont remplacés par leurs valeurs respectives.

Les valeurs nommées peuvent être utilisées comme attribut complet ou valeurs d’élément, comme indiqué dans l’exemple précédent. Elles peuvent également être insérées dans ou combinés avec une partie d’une expression de texte littéral, comme illustré dans l’exemple suivant :  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Les valeurs nommées peuvent également contenir des expressions de stratégie. Dans l’exemple suivant, l’expression `ExpressionProperty` est utilisée.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Lorsque cette stratégie est évaluée, `{{ExpressionProperty}}` est remplacé par sa valeur, `@(DateTime.Now.ToString())`. Étant donné que la valeur est une expression de stratégie, l’expression est évaluée et la stratégie poursuit son exécution.

Vous pouvez tester cette opération dans le portail Azure ou dans le [portail des développeurs](api-management-howto-developer-portal.md) en appelant une opération qui a une stratégie dont l’étendue inclut des valeurs nommées. Dans l’exemple suivant, une opération est appelée avec les deux stratégies `set-header` de l’exemple précédent incluant des valeurs nommées. Notez que la réponse contient deux en-têtes personnalisés configurés à l’aide de stratégies et de valeurs nommées.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Tester la réponse de l’API":::

Si vous examinez le [suivi de l’API](api-management-howto-api-inspector.md) sortant pour un appel qui inclut les deux exemples de stratégies précédents incluant des valeurs nommées, vous pouvez voir les deux stratégies `set-header` avec les valeurs nommées insérées, ainsi que l’évaluation de l’expression de stratégie pour la valeur nommée contenant l’expression de stratégie.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Suivi de l’inspecteur d’API":::

> [!CAUTION]
> Si une stratégie fait référence à un secret dans Azure Key Vault, la valeur du coffre de clés est visible pour les utilisateurs qui ont accès aux abonnements activés pour le [suivi des demandes API](api-management-howto-api-inspector.md).

Alors que les valeurs nommées peuvent contenir des expressions de stratégie, elles ne peuvent pas contenir d’autres valeurs nommées. Si le texte contenant une référence de valeur nommée est utilisé pour une valeur, telle que `Text: {{MyProperty}}`, cette référence ne sera pas résolue et remplacée.

## <a name="delete-a-named-value"></a>Supprimer une valeur nommée

Pour supprimer une valeur nommée, sélectionnez son nom, puis choisissez **Supprimer** dans le menu contextuel ( **...** ).

> [!IMPORTANT]
> Si la valeur nommée est référencée par des stratégies Gestion des API, vous ne pouvez pas la supprimer tant que vous ne l’avez pas supprimée de toutes les stratégies qui l’utilisent.

## <a name="next-steps"></a>Étapes suivantes

-   En savoir plus sur l’utilisation des stratégies
    -   [Stratégies dans Gestion des API](api-management-howto-policies.md)
    -   [Référence de stratégie](./api-management-policies.md)
    -   [Expressions de stratégie](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

