---
title: Fonctions de modèle – any
description: Décrit la fonction any disponible dans Bicep pour convertir des types.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743225"
---
# <a name="any-function-for-bicep"></a>Fonction any pour Bicep

Bicep prend en charge une fonction appelée `any()` pour résoudre les erreurs de type dans le système de type Bicep. Vous utilisez cette fonction quand le format de la valeur que vous fournissez ne correspond pas à ce que le système de type attend. Par exemple, si la propriété requiert un nombre, mais que vous devez la fournir sous forme de chaîne, comme `'0.5'`. Utilisez la fonction `any()` pour supprimer l’erreur signalée par le système de type.

Cette fonction n’existe pas dans le runtime du modèle Azure Resource Manager. Elle est utilisée uniquement par Bicep et n’est pas émise dans le JSON pour le modèle généré.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>n'importe laquelle

`any(value)`

Retourne une valeur compatible avec tout type de données.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| value | Oui | tous les types | Valeur à convertir en type compatible. |

### <a name="return-value"></a>Valeur retournée

Valeur dans un formulaire, qui est compatible avec tout type de données.

### <a name="examples"></a>Exemples

L’exemple de modèle suivant montre comment utiliser la fonction `any()` pour fournir des valeurs numériques sous forme de chaînes.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

La fonction opère sur toute valeur attribuée dans Bicep. L’exemple suivant utilise `any()` avec une expression ternaire en tant qu’argument.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Étapes suivantes

Pour des utilisations plus complexes de la fonction `any()`, consultez les exemples suivants :

* [Ressources enfants qui requièrent des noms spécifiques](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Propriété de ressource non définie dans le type de la ressource, même si elle existe](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

