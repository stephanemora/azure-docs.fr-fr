---
title: Structure et syntaxe du modèle Azure Resource Manager | Microsoft Docs
description: Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2018
ms.author: tomfitz
ms.openlocfilehash: 8f1fc9eb5e7b19f25af2005cb3a99cb320cba640
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214543"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendre la structure et la syntaxe des modèles Azure Resource Manager
Cet article décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose d’un JSON et d’expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour obtenir un didacticiel étape par étape permettant de créer un modèle, voir [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="template-format"></a>Format de modèle

Dans sa structure la plus simple, un modèle a les éléments suivants :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| $schema |Oui |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle. Utilisez l’URL indiquée dans l’exemple précédent. |
| contentVersion |Oui |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Utilisez cette valeur pour documenter les modifications importantes dans votre modèle. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| parameters |Non  |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| variables |Non  |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| functions |Non  |Fonctions définies par l’utilisateur et disponibles dans le modèle. |
| les ressources |Oui |Types de ressource déployés ou mis à jour dans un groupe de ressources. |
| outputs |Non  |Valeurs retournées après le déploiement. |

Chaque élément a des propriétés que vous pouvez définir. L’exemple suivant montre la syntaxe complète d’un modèle :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Cet article décrit les sections du modèle de manière plus approfondie.

## <a name="syntax"></a>Syntaxe
La syntaxe de base du modèle est JSON. Toutefois, les expressions et fonctions étendent les valeurs JSON disponibles dans le modèle.  Les expressions sont écrites dans les littéraux de chaîne JSON dont le premier et dernier caractères sont les crochets : `[` et `]`, respectivement. La valeur de l’expression est évaluée lorsque le modèle est déployé. Bien qu’écrit sous la forme d’un littéral de chaîne, le résultat de l’évaluation de l’expression peut être d’un type différent de JSON, tel qu’un tableau ou un entier, en fonction de l’expression réelle.  Pour avoir une chaîne littérale qui commence par un crochet `[`, sans qu’elle soit interprétée comme une expression, ajoutez un crochet supplémentaire. La chaîne commence alors par `[[`.

En général, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme en JavaScript, les appels de fonction sont formatés comme suit : `functionName(arg1,arg2,arg3)`. Pour référencer des propriétés, vous utilisez les opérateurs point et [index].

L’exemple suivant montre comment utiliser plusieurs fonctions lors de la construction d’une valeur :

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Vous n’êtes pas obligé de fournir des paramètres dans votre modèle, mais sans paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

L’exemple suivant illustre une définition de paramètre simple :

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Pour plus d’informations sur la définition des paramètres, consultez [Section des paramètres des modèles Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>variables
Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. Vous n’êtes pas obligé de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

L’exemple suivant montre une définition de variable simple :

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Pour plus d’informations sur la définition des variables, consultez [Section Variables des modèles Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="functions"></a>Fonctions

Dans votre modèle, vous pouvez créer vos propres fonctions. Ces fonctions peuvent être utilisées dans votre modèle. En règle générale, vous définissez une expression complexe que vous ne voulez pas répéter tout au long de votre modèle. Vous créez les fonctions définies par l’utilisateur à partir d’expressions et de [fonctions](resource-group-template-functions.md) prises en charge dans les modèles.

La définition d’une fonction utilisateur est soumise à certaines restrictions :

* La fonction ne peut pas accéder aux variables.
* La fonction ne peut pas accéder aux paramètres du modèle. Autrement dit, la [fonction de paramètres](resource-group-template-functions-deployment.md#parameters) est limitée aux paramètres de fonction.
* La fonction ne peut pas appeler d’autres fonctions définies par l’utilisateur.
* La fonction ne peut pas utiliser la [fonction de référence](resource-group-template-functions-resource.md#reference).
* Les paramètres de la fonction ne peuvent pas avoir de valeur par défaut.

Vos fonctions requièrent une valeur pour l’espace de noms afin d’éviter tout conflit avec les fonctions de modèle. L’exemple suivant illustre une fonction qui renvoie un nom de compte de stockage :

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

Vous appelez la fonction avec :

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Ressources
Dans la section des ressources, vous définissez les ressources déployées ou mises à jour. Cette section peut devenir complexe, car vous devez connaître les types que vous déployez pour fournir les valeurs adéquates.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Pour inclure ou exclure conditionnellement une ressource pendant le déploiement, utilisez [l’élément Condition](resource-manager-templates-resources.md#condition). Pour plus d’informations sur la section des ressources, consultez [Section Ressources des modèles Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>Outputs
Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Pour plus d’informations, consultez [Section Sorties des modèles Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Limites de modèle

Limitez la taille de votre modèle à 1 Mo et celle de chaque fichier de paramètres à 64 ko. La limite de 1 Mo s’applique à l’état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres. 

Vous devez également respecter les limites suivantes :

* 256 paramètres
* 256 variables
* 800 ressources (incluant le nombre de copies)
* 64 valeurs de sortie
* 24 576 caractères dans une expression de modèle

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](resource-group-linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).

## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour obtenir des recommandations sur la création de modèles Resource Manager utilisables dans Azure partout dans le monde, les clouds souverains Azure et Azure Stack, consultez [Développer des modèles Azure Resource Manager pour la cohérence du cloud](templates-cloud-consistency.md).
