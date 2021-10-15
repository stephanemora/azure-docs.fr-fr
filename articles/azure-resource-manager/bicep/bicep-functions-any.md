---
title: Fonctions Bicep - any
description: Décrit la fonction any disponible dans Bicep pour convertir des types.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 2826f6180bc8ba4c476fd067a19db25b52f5d65b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353701"
---
# <a name="any-function-for-bicep"></a>Fonction any pour Bicep

Bicep prend en charge une fonction appelée `any()` pour résoudre les erreurs de type dans le système de type Bicep. Vous utilisez cette fonction quand le format de la valeur que vous fournissez ne correspond pas à ce que le système de type attend. Par exemple, si la propriété requiert un nombre, mais que vous devez la fournir sous forme de chaîne, comme `'0.5'`. Utilisez la fonction `any()` pour supprimer l’erreur signalée par le système de type.

Cette fonction n’existe pas dans le runtime du modèle Azure Resource Manager. Elle est utilisée uniquement par Bicep et n’est pas émise dans le JSON pour le modèle généré.

> [!NOTE]
> Pour aider à résoudre les erreurs de type, faites-nous savoir quand des types manquants ou incorrects nécessitaient l’utilisation de la fonction `any()`. Ajoutez vos détails au problème de [validation/inexactitude de type manquant](https://github.com/Azure/bicep/issues/784) GitHub.

## <a name="any"></a>n'importe laquelle

`any(value)`

Retourne une valeur compatible avec tout type de données.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| value | Oui | tous les types | Valeur à convertir en type compatible. |

### <a name="return-value"></a>Valeur retournée

Valeur dans un formulaire, qui est compatible avec tout type de données.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser la fonction `any()` pour fournir des valeurs numériques sous forme de chaînes.

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

* [Ressources enfants qui requièrent des noms spécifiques](https://github.com/Azure/bicep/blob/62eb8109ae51d4ee4a509d8697ef9c0848f36fe4/docs/examples/201/api-management-create-all-resources/main.bicep#L247)
* [Propriété de ressource non définie dans le type de la ressource, même si elle existe](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

