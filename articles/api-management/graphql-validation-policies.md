---
title: Stratégie de validation Gestion des API Azure pour les demandes GraphQL | Microsoft Docs
description: Découvrez une nouvelle stratégie que vous pouvez utiliser dans Gestion des API Azure pour valider et autoriser des demandes GraphQL.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: 02225e0a07ef6567b8b190d9d31a957a69adbc4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096937"
---
# <a name="api-management-policy-to-validate-and-authorize-graphql-requests-preview"></a>Stratégie Gestion des API pour valider et autoriser des demandes GraphQL (préversion)

Cet article fournit des informations de référence sur une nouvelle stratégie Gestion des API permettant de valider et d’autoriser des demandes à une [API GraphQL](graphql-api.md) importée dans Gestion des API.

Pour plus d’informations sur l’ajout et la configuration de stratégies, consultez [Stratégies dans Gestion des API](./api-management-policies.md).

## <a name="validation-policy"></a>Stratégie de validation

| Stratégie | Description |
| ------ | ----------- |
| [Valider la demande GraphQL](#validate-graphql-request) | Valide et autorise une demande à une API GraphQL. |


## <a name="validate-graphql-request"></a>Valider une demande GraphQL

La stratégie `validate-graphql-request` valide la requête GraphQL et autorise l’accès à des chemins de requête spécifiques. Une requête non valide est une « erreur de demande ». L’autorisation n’est effectuée que pour les demandes valides. 

**autorisations**  
Étant donné que les requêtes GraphQL utilisent un schéma aplati :
* Des autorisations peuvent être appliquées à n’importe quel nœud terminal d’un type de sortie : 
    * Mutation, requête ou abonnement
    * Champ individuel dans une déclaration de type. 
* Les autorisations ne peuvent pas être appliquées aux éléments suivants : 
    * Types d’entrée
    * Fragments
    * Unions
    * Interfaces
    * Élément de schéma   

**Éléments d’autorisation**  
Vous pouvez utiliser plusieurs éléments d’autorisation. Le chemin le plus spécifique est utilisé pour sélectionner la règle d’autorisation appropriée pour chaque nœud terminal dans la requête. 
* Chaque autorisation peut éventuellement fournir une autre action.
* Les clauses `if` permettent à l’administrateur de spécifier des actions conditionnelles. 

**Système d’introspection**   
La stratégie pour path=`/__*` est le système d’[introspection](https://graphql.org/learn/introspection/). Vous pouvez l’utiliser pour rejeter les demandes d’introspection (`__schema`, `__type`, etc.).   

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<validate-graphql-request error-variable-name="variable name" max-size="size in bytes" max-depth="query depth">
    <authorize-path="query path, for example: /Query/list Users or /__*" action="allow|remove|reject" />
        <if condition="policy expression" action="allow|remove|reject" />
</validate-graphql-request>
```

### <a name="example"></a>Exemple

Dans l’exemple suivant, nous validons une requête GraphQL et rejetons ce qui suit :
* Demandes supérieures à 100 Ko ou avec une profondeur de requête supérieure à 4. 
* Accès au système d’introspection et à la requête `list Users`. 

```xml
<validate-graphql-request error-variable-name="name" max-size="102400" max-depth="4"> 
    <authorize path="/" action="allow" /> 
    <authorize path="/__*" action="reject" /> 
    <authorize path="/Query/list Users" action="reject" /> 
</validate-graphql-request> 
```

### <a name="elements"></a>Éléments

| Nom         | Description                                                                                                                                   | Obligatoire |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-graphql-request` | Élément racine.                                                                                                                               | Oui      |
| `authorize` | Ajoutez un ou plusieurs de ces éléments pour fournir une autorisation au niveau du champ avec des erreurs au niveau de la demande et du champ.   | Oui |
| `if` | Ajoutez un ou plusieurs de ces éléments pour des modifications conditionnelles de l’action pour une autorisation au niveau du champ. | Non |

### <a name="attributes"></a>Attributs

| Nom                       | Description                                                                                                                                                            | Obligatoire | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `error-variable-name` | Nom de la variable dans `context.Variables` dans laquelle enregistrer les erreurs de validation.  |   Non    | N/A   |
| `max-size` | Taille maximale de la charge utile de la demande, en octets. Valeur maximale autorisée : 102 400 octets (100 Ko). (Contactez le [support](https://azure.microsoft.com/support/options/) si vous devez augmenter cette limite). | Oui       | N/A   |
| `max-depth` | Entier. Profondeur de requête maximale. | Non | 6 |
| `path` | Chemin de requête sur lequel exécuter la validation d’autorisation. | Oui | N/A |
| `action` | [Action](#request-actions) à effectuer pour le champ correspondant. Peut être modifié si une condition de correspondance est spécifiée. |  Oui     | N/A   |
| `condition` | Valeur booléenne qui détermine si l’[expression de stratégie](api-management-policy-expressions.md) correspond. La première condition de correspondance est utilisée. | Non | N/A |

### <a name="request-actions"></a>Actions de demande

Les actions disponibles sont décrites dans le tableau suivant.

|Action |Description  |
|---------|---------|
|`reject`     | Une erreur de demande se produit et la demande n’est pas envoyée au back-end.     |
|`remove`     | Une erreur de champ se produit et le champ est supprimé de la demande.         |
|`allow`     | Le champ est passé au back-end.        |

### <a name="usage"></a>Usage

Cette stratégie peut être utilisée dans les [sections](./api-management-howto-policies.md#sections) et [étendues](./api-management-howto-policies.md#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** inbound

-   **Étendues de la stratégie :** toutes les étendues

## <a name="error-handling"></a>Gestion des erreurs

Un échec de validation par rapport au schéma GraphQL ou un échec lié à la taille ou à la profondeur de la demande constitue une erreur de demande et entraîne son échec avec un bloc d’erreurs (mais pas de bloc de données). 

Comme pour la propriété [`Context.LastError`](api-management-error-handling-policies.md#lasterror), toutes les erreurs de validation GraphQL sont automatiquement propagées dans la variable `GraphQLErrors`. Si les erreurs doivent être propagées séparément, vous pouvez spécifier un nom de variable d’erreur. Les erreurs font l’objet d’un push sur la variable `error` et la variable `GraphQLErrors`. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des stratégies, consultez :

-   [Stratégies dans Gestion des API](api-management-howto-policies.md)
-   [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
-   [Référence de stratégie](./api-management-policies.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
-   [Exemples de stratégie](./policy-reference.md)
-   [Gestion des erreurs](./api-management-error-handling-policies.md)
