---
title: Fonctions définies par l’utilisateur dans les modèles
description: Explique comment définir et utiliser des fonctions définies par l'utilisateur dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 802ac6e1c70db8caa58aecde3efe814430c79682
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307773"
---
# <a name="user-defined-functions-in-arm-template"></a>Fonctions définies par l'utilisateur dans un modèle Azure Resource Manager

Dans votre modèle, vous pouvez créer vos propres fonctions. Ces fonctions peuvent être utilisées dans votre modèle. Les fonctions définies par l’utilisateur sont distinctes des [fonctions de modèles standard](template-functions.md) qui sont automatiquement disponibles dans votre modèle. Créez vos propres fonctions quand vous avez des expressions complexes qui sont utilisées de manière répétitive dans votre modèle.

Cet article vous explique comment ajouter des fonctions définies par l'utilisateur dans votre modèle Azure Resource Manager.

## <a name="define-the-function"></a>Définir la fonction

Vos fonctions requièrent une valeur pour l’espace de noms afin d’éviter tout conflit avec les fonctions de modèle. L'exemple suivant illustre une fonction qui renvoie un nom unique :

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Utiliser la fonction

L’exemple suivant montre un modèle qui comprend une fonction définie par l’utilisateur pour obtenir un nom unique pour un compte de stockage. Le modèle comprend un paramètre `storageNamePrefix` qu’il transmet à la fonction sous forme de paramètre.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

Pendant le déploiement, le paramètre `storageNamePrefix` est transmis à la fonction :

* Le modèle définit un paramètre `storageNamePrefix`.
* La fonction utilise `namePrefix` parce que vous ne pouvez utiliser que des paramètres définis dans la fonction. Pour plus d’informations, consultez [Limitations](#limitations).
* Dans la section `resources` du modèle, l’élément `name` utilise la fonction et transmet la valeur `storageNamePrefix` au paramètre `namePrefix` de la fonction.

## <a name="limitations"></a>Limites

La définition d’une fonction utilisateur est soumise à certaines restrictions :

* La fonction ne peut pas accéder aux variables.
* La fonction ne peut utiliser que des paramètres définis dans l’autre fonction. Quand vous utilisez la fonction [parameters](template-functions-deployment.md#parameters) dans une fonction définie par l’utilisateur, vous êtes limité aux paramètres de cette fonction.
* La fonction ne peut pas appeler d’autres fonctions définies par l’utilisateur.
* La fonction ne peut pas utiliser la fonction [reference](template-functions-resource.md#reference) ni aucune des fonctions [list](template-functions-resource.md#list).
* La fonction ne peut pas utiliser la fonction [dateTimeAdd](template-functions-date.md#datetimeadd).
* Les paramètres de la fonction ne peuvent pas avoir de valeur par défaut.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les propriétés disponibles pour les fonctions définies par l'utilisateur, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
* Pour obtenir la liste des fonctions de modèle disponibles, consultez [Fonctions de modèle Azure Resource Manager](template-functions.md).
