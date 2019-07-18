---
title: Structure et syntaxe du modèle Azure Resource Manager | Microsoft Docs
description: Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: ab8e4f5f6506f80b62c112298f73f95bc7fedeaf
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204361"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendre la structure et la syntaxe des modèles Azure Resource Manager

Cet article décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose d’un JSON et d’expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Cet article est destiné aux utilisateurs qui possèdent des connaissances sur les modèles Resource Manager. Il fournit des informations détaillées sur la structure et la syntaxe du modèle. Pour une introduction à la création d’un modèle, voir [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format de modèle

Dans sa structure la plus simple, un modèle a les éléments suivants :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| $schema |OUI |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.<br><br> Pour des déploiements de groupes de ressources, utilisez : `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pour des déploiements d’abonnements, utilisez : `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |OUI |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Utilisez cette valeur pour documenter les modifications importantes dans votre modèle. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| apiProfile |Non | Une version d’API qui sert de collection de versions d’API pour les types de ressources. Utilisez cette valeur pour éviter d’avoir à spécifier les versions d’API pour chaque ressource dans le modèle. Lorsque vous spécifiez une version de profil d’API et que vous ne spécifiez pas une version d’API pour le type de ressource, Resource Manager utilise la version d’API pour ce type de ressource qui est définie dans le profil.<br><br>La propriété de profil d’API est particulièrement utile lorsque vous déployez un modèle dans différents environnements, comme Azure Stack et Azure global. Utilisez la version de profil d’API pour vous assurer que votre modèle utilise automatiquement des versions prises en charge dans les deux environnements. Pour une liste des versions de profil d’API actuelles et les versions d’API définies dans le profil de ressources, consultez [Profil d’API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Pour plus d’informations, consultez [Suivre les versions à l’aide de profils d’API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Non |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| [variables](#variables) |Non |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| [functions](#functions) |Non |Fonctions définies par l’utilisateur et disponibles dans le modèle. |
| [resources](#resources) |OUI |Types de ressource déployés ou mis à jour dans un groupe de ressources ou un abonnement. |
| [outputs](#outputs) |Non |Valeurs retournées après le déploiement. |

Chaque élément a des propriétés que vous pouvez définir. Cet article décrit les sections du modèle de manière plus approfondie.

## <a name="syntax"></a>Syntaxe

La syntaxe de base du modèle est JSON. Toutefois, vous pouvez utiliser des expressions et fonctions pour étendre les valeurs JSON disponibles dans le modèle.  Les expressions commencent et se terminent avec des crochets : `[` et `]`, respectivement. La valeur de l’expression est évaluée lorsque le modèle est déployé. Une expression peut retourner une chaîne, un entier, un booléen, un tableau ou un objet. L’exemple suivant montre une expression dans la valeur par défaut d’un paramètre :

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dans l’expression, la syntaxe `resourceGroup()` appelle une des fonctions fournies par Resource Manager pour une utilisation dans un modèle. Comme en JavaScript, les appels de fonction sont formatés comme suit : `functionName(arg1,arg2,arg3)`. La syntaxe `.location` récupère une propriété de l’objet retourné par cette fonction.

Les fonctions des modèles et leurs paramètres ne respectent pas la casse. Par exemple, Resource Manager résout **variables('var1')** et **VARIABLES('VAR1')** de la même manière. Lors de l’évaluation, la fonction préserve la casse sauf si elle la modifie expressément (toUpper ou toLower, par exemple). Certains types de ressources peuvent avoir des exigences de casse, quelle que soit la manière dont les fonctions sont évaluées.

Pour avoir une chaîne littérale qui commence par un crochet gauche `[` et se termine par un crochet droit `]`, sans qu’elle soit interprétée comme une expression, ajoutez un crochet supplémentaire. La chaîne commence alors par `[[`. Par exemple, la variable :

```json
"demoVar1": "[[test value]"
```

Est résolu en `[test value]`.

Toutefois, si la chaîne littérale ne se termine par un crochet, n’utilisez pas le caractère d’échappement pour le premier crochet. Par exemple, la variable :

```json
"demoVar2": "[test] value"
```

Est résolu en `[test] value`.

Pour passer une valeur de chaîne en tant que paramètre à une fonction, utilisez des guillemets simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Pour échapper les guillemets doubles dans une expression, comme l’ajout d’un objet JSON dans le modèle, utilisez la barre oblique inverse.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Une expression de modèle ne peut pas dépasser 24 576 caractères.

Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters

C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Vous n’êtes pas obligé de fournir des paramètres dans votre modèle, mais sans paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous êtes limité à 256 paramètres dans un modèle. Vous pouvez réduire le nombre de paramètres en utilisant des objets contenant plusieurs propriétés, comme indiqué dans cet article.

### <a name="available-properties"></a>Propriétés disponibles

Les propriétés disponibles pour un paramètre sont :

```json
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
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| parameterName |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur du paramètre. Les types et valeurs autorisés sont : **string**, **secureString**, **int**, **bool**, **object**, **secureObject** et **array**. |
| defaultValue |Non |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non |Valeur minimale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| maxLength |Non |Valeur maximale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| description |Non |Description du paramètre qui apparaît aux utilisateurs dans le portail. Pour plus d’informations, consultez [Commentaires dans les modèles](#comments). |

### <a name="define-and-use-a-parameter"></a>Définir et utiliser un paramètre

L’exemple suivant illustre une définition de paramètre simple. Il définit le nom du paramètre et indique qu’il nécessite une valeur de chaîne. Le paramètre accepte uniquement des valeurs qui ont un sens pour son usage prévu. Il indique une valeur par défaut quand aucune valeur n’est fournie au cours du déploiement. Enfin, le paramètre inclut une description de son utilisation.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

Dans le modèle, vous référencez la valeur du paramètre à l’aide de la syntaxe suivante :

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Fonctions de modèle avec des paramètres

Quand vous spécifiez la valeur par défaut d’un paramètre, vous pouvez utiliser la plupart des fonctions de modèle. Vous pouvez utiliser une autre valeur de paramètre pour générer une valeur par défaut. Le modèle suivant illustre l’utilisation de fonctions avec les valeurs par défaut :

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section des paramètres. Les paramètres étant évalués avant le déploiement, la fonction `reference` ne peut pas obtenir l’état d’exécution d’une ressource. 

### <a name="objects-as-parameters"></a>Objets en tant que paramètres

Il peut s’avérer plus facile d’organiser des valeurs connexes en les transmettant en tant qu’objets. Cette approche réduit également le nombre de paramètres dans le modèle.

Définissez le paramètre dans votre modèle, puis spécifiez un objet JSON au lieu d’une valeur unique au cours du déploiement. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Référencez ensuite les sous-propriétés du paramètre en utilisant l’opérateur point.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Exemples de modèles de paramètre

Ces exemples de modèles montrent quelques scénarios d’utilisation de paramètres. Déployez-les pour tester la façon dont les paramètres sont gérés dans différents cas de figure.

|Modèle  |Description  |
|---------|---------|
|[Paramètres avec fonctions pour les valeurs par défaut](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Montre comment utiliser les fonctions de modèle durant la définition des valeurs par défaut des paramètres. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |
|[Objet de paramètre](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Montre comment utiliser un objet pour un paramètre. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |

## <a name="variables"></a>variables

Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. Vous n’êtes pas obligé de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

### <a name="available-definitions"></a>Définitions disponibles

L’exemple suivant montre les options disponibles pour la définition d’une variable :

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Pour plus d’informations sur l’utilisation de `copy` pour créer plusieurs valeurs pour une variable, consultez [Itération de variable](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Définir et utiliser une variable

L’exemple suivant montre une définition de variable. Il crée une valeur de chaîne pour le nom d’un compte de stockage. Il utilise plusieurs fonctions de modèle pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

La variable est utilisée lors de la définition de la ressource.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variables de configuration

Vous pouvez utiliser les types JSON complexes pour définir les valeurs associées d’un environnement.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Dans Paramètres, créez une valeur qui indique les valeurs de configuration à utiliser.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Récupérez les paramètres actuels ainsi :

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Exemples de modèles de variable

Ces exemples de modèles montrent quelques scénarios d’utilisation de variables. Déployez-les pour tester la façon dont les variables sont gérées dans différents cas de figure. 

|Modèle  |Description  |
|---------|---------|
| [définitions de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Montre les différents types de variables. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [variable de configuration](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustre l’utilisation d’une variable qui définit des valeurs de configuration. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [règles de sécurité réseau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) et [fichier de paramètres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crée un tableau au bon format pour attribuer des règles de sécurité à un groupe de sécurité réseau. |


## <a name="functions"></a>Fonctions

Dans votre modèle, vous pouvez créer vos propres fonctions. Ces fonctions peuvent être utilisées dans votre modèle. En règle générale, vous définissez une expression complexe que vous ne voulez pas répéter tout au long de votre modèle. Vous créez les fonctions définies par l’utilisateur à partir d’expressions et de [fonctions](resource-group-template-functions.md) prises en charge dans les modèles.

La définition d’une fonction utilisateur est soumise à certaines restrictions :

* La fonction ne peut pas accéder aux variables.
* La fonction ne peut utiliser que des paramètres définis dans l’autre fonction. Lorsque vous utilisez la [fonction parameters](resource-group-template-functions-deployment.md#parameters) dans une fonction définie par l’utilisateur, vous êtes limité aux paramètres de cette fonction.
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
Dans la section des ressources, vous définissez les ressources déployées ou mises à jour.

### <a name="available-properties"></a>Propriétés disponibles

Vous définissez des ressources avec la structure suivante :

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| condition | Non | Valeur booléenne qui indique si la ressource sera provisionnée pendant ce déploiement. Quand la valeur est `true`, la ressource est créée pendant le déploiement. Quand la valeur est `false`, la ressource est ignorée pour ce déploiement. Voir [condition](#condition). |
| apiVersion |OUI |La version de l'API REST à utiliser pour la création de la ressource. Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). |
| Type |OUI |Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et du type de ressource (comme **Microsoft.Storage/storageAccounts**). Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). Pour une ressource enfant, le format du type dépend de si elle est imbriquée dans la ressource parente ou définie en dehors de la ressource parente. Voir [Ressources enfants](#child-resources). |
| Nom |OUI |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. Par ailleurs, les services Azure qui exposent le nom de la ressource à des parties externes valident le nom pour vérifier qu’il ne s’agit pas d’une tentative d’usurpation d’identité. Pour une ressource enfant, le format du nom dépend de si elle est imbriquée dans la ressource parente ou définie en dehors de la ressource parente. Voir [Ressources enfants](#child-resources). |
| location |Varie |Emplacements géographiques de la ressource fournie pris en charge. Vous pouvez sélectionner l’un des emplacements disponibles, mais en général, il est judicieux de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer dans la même région les ressources qui interagissent entre elles. La plupart des types de ressources nécessitent un emplacement, mais certains types (comme une attribution de rôle) n’ont pas besoin d’emplacement. |
| tags |Non |Balises associées à la ressource. Appliquer des balises pour organiser logiquement des ressources dans votre abonnement. |
| commentaires |Non |Vos commentaires pour documenter les ressources dans votre modèle. Pour plus d’informations, consultez [Commentaires dans les modèles](resource-group-authoring-templates.md#comments). |
| copy |Non |Si plusieurs instances sont nécessaires, le nombre de ressources à créer. Le mode par défaut est parallèle. Spécifiez le mode série si vous ne voulez pas que toutes les ressources soient déployées en même temps. Pour plus d’informations, consultez [Créer plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Non |Les ressources qui doivent être déployées avant le déploiement de cette ressource. Resource Manager évalue les dépendances entre les ressources et les déploie dans le bon ordre. Quand les ressources ne dépendent pas les unes des autres, elles sont déployées en parallèle. La valeur peut être une liste séparée par des virgules de noms de ressource ou d’identificateurs de ressource uniques. Répertoriez uniquement les ressources qui sont déployées dans ce modèle. Les ressources qui ne sont pas définies dans ce modèle doivent déjà exister. Évitez d’ajouter des dépendances inutiles, car cela risque de ralentir votre déploiement et de créer des dépendances circulaires. Pour savoir comment définir des dépendances, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Non |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Vous pouvez aussi spécifier une copie en groupe pour créer plusieurs instances d’une propriété. Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). |
| sku | Non | Certaines ressources autorisent les valeurs qui définissent la référence SKU à déployer. Par exemple, vous pouvez spécifier le type de redondance pour un compte de stockage. |
| kind | Non | Certaines ressources autorisent une valeur qui définit le type de ressource que vous déployez. Par exemple, vous pouvez spécifier le type Cosmos DB à créer. |
| Plan | Non | Certaines ressources autorisent les valeurs qui définissent le plan à déployer. Par exemple, vous pouvez spécifier l’image de marketplace pour une machine virtuelle. | 
| les ressources |Non |Ressources enfants qui dépendent de la ressource qui est définie. Fournissez uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. La dépendance sur la ressource parente n’est pas induite. Vous devez la définir explicitement. Voir [Ressources enfants](#child-resources). |

### <a name="condition"></a>Condition

Quand vous devez décider pendant le déploiement s’il faut créer une ressource, utilisez l’élément `condition`. La valeur de cet élément est résolue en true ou false. Lorsque la valeur est true, la ressource est créée. Lorsque la valeur est false, la ressource n’est pas créée. La valeur ne peut être appliquée qu’à l’ensemble de la ressource.

En règle générale, vous utilisez cette valeur quand vous voulez créer une ressource ou utiliser une ressource existante. Par exemple, pour spécifier si un nouveau compte de stockage est déployé ou si un compte de stockage existant est utilisé, utilisez :

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Pour un exemple de modèle complet qui utilise l’élément `condition`, consultez [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Machine virtuelle avec un réseau virtuel, un stockage et une adresse IP publique nouveaux ou existants).

Si vous utilisez une fonction de [référence](resource-group-template-functions-resource.md#reference) ou de [liste](resource-group-template-functions-resource.md#list) avec une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée. Vous obtenez une erreur si la fonction fait référence à une ressource qui n’existe pas. Utilisez la fonction [if](resource-group-template-functions-logical.md#if) pour vous assurer que la fonction est évaluée uniquement pour les conditions lorsque la ressource est déployée. Consultez la [fonction if](resource-group-template-functions-logical.md#if) pour un exemple de modèle qui utilise if et une référence avec une ressource déployée de manière conditionnelle.

### <a name="resource-names"></a>Noms de ressource

Il existe généralement trois types de noms de ressource avec lesquels vous travaillez dans Resource Manager :

* des noms de ressource qui doivent être uniques ;
* Des noms de ressources qui ne doivent pas obligatoirement être uniques, mais pour lesquels vous choisissez un nom qui vous permet d’identifier la ressource.
* des noms de ressources qui peuvent être génériques.

Fournissez un **nom de ressource unique** pour tout type de ressource ayant un point de terminaison d’accès aux données. Certains types de ressource courants nécessitent un nom unique, notamment :

* Azure Storage<sup>1</sup> 
* Fonctionnalité Web Apps d’Azure App Service
* SQL Server
* Azure Key Vault
* Cache Azure pour Redis
* Azure Batch
* Azure Traffic Manager
* Recherche Azure
* Azure HDInsight

<sup>1</sup> Les noms de compte de stockage doivent être en minuscules, comporter 24 caractères au maximum et ne pas comprendre de traits d’union.

Quand vous définissez le nom, vous pouvez soit créer manuellement un nom unique, soit utiliser la fonction [uniqueString()](resource-group-template-functions-string.md#uniquestring) pour générer un nom. Vous pouvez également ajouter un préfixe ou un suffixe au résultat.**uniqueString**. La modification du nom unique peut vous aider à identifier plus facilement le type de ressource à partir du nom. Par exemple, vous pouvez générer un nom unique pour un compte de stockage avec la variable suivante :

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Pour certains types de ressources, vous souhaiterez peut-être fournir un **nom pour l’identification**, mais le nom n’a pas à être unique. Pour ces types de ressources, fournissez un nom qui décrit l’utilisation ou les caractéristiques.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Pour les types de ressources qui sont accessibles en grande partie par le biais d’une autre ressource, vous pouvez utiliser un **nom générique** codé en dur dans le modèle. Par exemple, vous pouvez définir un nom générique standard pour les règles de pare-feu sur un serveur SQL :

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Emplacement des ressources

Lorsque vous déployez un modèle, vous devez fournir un emplacement pour chaque ressource. Différents types de ressources sont pris en charge à différents emplacements. Pour obtenir les emplacements pris en charge pour un type de ressource, consultez [Fournisseurs et types de ressources Azure](resource-manager-supported-services.md).

Utilisez un paramètre pour spécifier l’emplacement des ressources, et définissez la valeur par défaut sur `resourceGroup().location`.

L’exemple suivant illustre le déploiement d’un compte de stockage dans un emplacement spécifié en tant que paramètre :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Ressources enfants

Dans certains types de ressources, vous pouvez également définir un tableau de ressources enfants. Les ressources enfants sont des ressources qui n’existent que dans le contexte d’une autre ressource. Par exemple, une base de données SQL ne peut pas exister sans serveur SQL. Elle est donc un enfant du serveur. Vous pouvez définir la base de données dans la définition du serveur.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

Toutefois, vous n’êtes pas obligé de définir la base de données dans le serveur. Vous pouvez définir la ressource enfant au niveau supérieur. Vous pouvez utiliser cette approche si la ressource parente n’est pas déployée dans le même modèle ou si voulez utiliser `copy` pour créer plusieurs ressources enfants. Dans le cadre de cette approche, vous devez fournir le type de ressource complet et inclure le nom de la ressource parent dans le nom de la ressource enfant.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

Les valeurs que vous fournissez pour le type et le nom varient selon que la ressource enfant est définie dans la ressource parente ou en dehors de la ressource parente.

Si elle est imbriquée dans la ressource parente, utilisez :

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Si elle est définie en dehors de la ressource parente, utilisez :

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

En cas d’imbrication, le type est défini sur `databases`, mais son type de ressource complet est toujours `Microsoft.Sql/servers/databases`. Vous ne fournissez pas `Microsoft.Sql/servers/`, car il est déduit du type de ressource parent. Le nom de la ressource enfant est défini sur `exampledatabase`, mais le nom complet inclut le nom parent. Vous ne fournissez pas `exampleserver`, car il est déduit de la ressource parente.

Quand vous créez une référence complète à une ressource, l’ordre utilisé pour combiner les segments de type et de nom n’est pas une simple concaténation des deux. Au lieu de cela, utilisez après l’espace de noms une séquence de paires *type/nom* du moins spécifique au plus spécifique :

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Par exemple :

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` est correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` n’est pas correct

## <a name="outputs"></a>Outputs

Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. En règle générale, vous retournez des valeurs de ressources qui ont été déployées.

### <a name="available-properties"></a>Propriétés disponibles

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| outputName |OUI |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| condition |Non | Valeur booléenne qui indique si cette valeur de sortie est retournée. Si elle est égale à `true`, cela signifie que la valeur est incluse dans la sortie pour le déploiement. Si elle est égale à `false`, la valeur de sortie est ignorée pour ce déploiement. Lorsqu’elle n’est pas spécifiée, la valeur par défaut est `true`. |
| Type |OUI |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. Si vous spécifiez **securestring** pour le type de sortie, la valeur n’est pas affichée dans l’historique de déploiement et ne peut pas être récupérée à partir d’un autre modèle. Pour utiliser une valeur secrète dans plusieurs modèles, stockez la clé secrète dans un coffre de clés et référencez la clé secrète dans le fichier de paramètres. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](resource-manager-keyvault-parameter.md). |
| value |OUI |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

### <a name="define-and-use-output-values"></a>Définir et utiliser des valeurs de sortie

L’exemple suivant montre comment retourner l’ID de ressource d’une adresse IP publique :

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

L’exemple suivant montre comment retourner conditionnellement l’ID de ressource pour une adresse IP publique si une nouvelle a été déployée :

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Pour obtenir un exemple simple de sortie conditionnelle, consultez [Modèle de sortie conditionnelle](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Après le déploiement, vous pouvez récupérer la valeur à l’aide d’un script. Pour PowerShell, utilisez la commande suivante :

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Vous pouvez récupérer la valeur de sortie à partir d’un modèle lié à l’aide de la fonction [reference](resource-group-template-functions-resource.md#reference). Pour obtenir une valeur de sortie d’un modèle lié, récupérez la valeur de propriété à l’aide d’une syntaxe comme : `"[reference('deploymentName').outputs.propertyName.value]"`.

Lors de l’obtention d’une propriété de sortie à partir d’un modèle lié, le nom de propriété ne peut pas inclure de tiret.

L’exemple suivant montre comment définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un modèle lié.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section de sortie d’un [modèle imbriqué](resource-group-linked-templates.md#link-or-nest-a-template). Pour renvoyer les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

### <a name="output-example-templates"></a>Exemples de modèles de sortie

|Modèle  |Description  |
|---------|---------|
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crée des variables complexes et génère ces valeurs. Ne déploie aucune ressource. |
|[Adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crée une adresse IP publique et génère l’ID de ressource. |
|[Équilibreur de charge](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Est lié au modèle précédent. Utilise l’ID de ressource dans la sortie durant la création de l’équilibreur de charge. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Commentaires et métadonnées

Vous avez quelques options permettant d’ajouter des commentaires et des métadonnées à votre modèle.

Vous pouvez ajouter un objet `metadata` presque n’importe où dans votre modèle. Resource Manager ignore l’objet, mais votre éditeur JSON peut vous avertir que la propriété n’est pas valide. Dans l’objet, définissez les propriétés dont vous avez besoin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Pour **parameters**, ajoutez un objet `metadata` avec une propriété `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Lorsque vous déployez le modèle par le biais du portail, le texte que vous fournissez dans la description est automatiquement utilisé comme une info-bulle pour ce paramètre.

![Afficher le paramètre conseillé](./media/resource-group-authoring-templates/show-parameter-tip.png)

Pour **resources**, ajoutez un élément `comments` ou un objet de métadonnées. L’exemple suivant montre à la fois un élément de commentaires et un objet de métadonnées.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Pour **outputs**, ajoutez un objet de métadonnées à la valeur de sortie.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Vous ne pouvez pas ajouter un objet de métadonnées aux fonctions définies par l’utilisateur.

Pour les commentaires inclus, vous pouvez utiliser `//`, mais cette syntaxe ne fonctionne pas avec tous les outils. Vous ne pouvez pas utiliser Azure CLI pour déployer le modèle avec les commentaires inclus. Et, vous ne pouvez pas utiliser l’éditeur de modèle du portail pour travailler sur des modèles avec des commentaires inclus. Si vous ajoutez ce style de commentaire, vérifiez que les outils que vous utilisez prennent en charge les commentaires JSON inclus.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Dans VS Code, vous pouvez définir le mode de langage sur JSON avec des commentaires. Les commentaires inclus ne sont plus signalés comme étant non valides. Pour modifier le mode :

1. Ouvrez la sélection du mode de langage (Ctrl + K M)

1. Sélectionnez **JSON avec des commentaires**.

   ![Sélectionner un mode de langage](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour obtenir des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives aux modèles Azure Resource Manager](template-best-practices.md).
* Pour obtenir des suggestions sur la création de modèles Resource Manager utilisables dans tous les environnements Azure et Azure Stack, consultez [Développer des modèles Azure Resource Manager pour la cohérence du cloud](templates-cloud-consistency.md).
