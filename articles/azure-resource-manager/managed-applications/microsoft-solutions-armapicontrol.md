---
title: Élément d’interface utilisateur ArmApiControl
description: Décrit l’élément d’interface utilisateur Microsoft.Solutions.ArmApiControl pour le portail Azure. Utilisé pour appeler des opérations d’API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313298"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Microsoft.Solutions.ArmApiControl UI element

ArmApiControl vous permet d’obtenir les résultats d’une opération d’API Azure Resource Manager. Utilisez les résultats pour alimenter du contenu dynamique dans d’autres contrôles.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

Il n’existe aucune interface utilisateur pour ce contrôle.

## <a name="schema"></a>schéma

L’exemple suivant montre le schéma de ce contrôle :

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Exemple de sortie

La sortie du contrôle n’est pas affichée à l’utilisateur. Au lieu de cela, le résultat de l’opération est utilisé dans d’autres contrôles.

## <a name="remarks"></a>Notes

- La propriété `request.method` spécifie la méthode HTTP. Seuls `GET` ou `POST` sont autorisés.
- La propriété `request.path` spécifie une URL qui doit être un chemin relatif à un point de terminaison ARM. Il peut s’agir d’un chemin d’accès statique ou être construit dynamiquement en faisant référence aux valeurs de sortie des autres contrôles.

  Par exemple, un appel ARM au fournisseur de ressources `Microsoft.Network/expressRouteCircuits` :

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- La propriété `request.body` est facultative. Utilisez-la pour spécifier un corps JSON envoyé avec la requête. Le corps peut être du contenu statique ou être construit dynamiquement en faisant référence aux valeurs de sortie d’autres contrôles.

## <a name="example"></a>Exemple

Dans l’exemple suivant, l’élément `providersApi` appelle une API pour récupérer un tableau d’objets fournisseur.

La propriété `allowedValues` de l’élément `providersDropDown` est configurée de façon à récupérer les noms des fournisseurs. Elle les affiche dans la liste déroulante.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Pour obtenir un exemple d’utilisation d’ArmApiControl pour vérifier la disponibilité d’un nom de ressource, consultez [Microsoft.Common.TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
- Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
