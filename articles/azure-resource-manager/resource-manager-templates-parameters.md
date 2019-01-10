---
title: Section des paramètres des modèles Azure Resource Manager | Microsoft Docs
description: Décrit la section des paramètres des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: fd6fcff6ac556abe3b2d34c7e8b1b0290208f5b0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722140"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Section des paramètres des modèles Azure Resource Manager
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Vous n’êtes pas obligé de fournir des paramètres dans votre modèle, mais sans paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous êtes limité à 256 paramètres dans un modèle. Vous pouvez réduire le nombre de paramètres en utilisant des objets contenant plusieurs propriétés, comme indiqué dans cet article.

## <a name="define-and-use-a-parameter"></a>Définir et utiliser un paramètre

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

## <a name="available-properties"></a>Propriétés disponibles

L’exemple précédent a seulement illustré quelques-unes des propriétés que vous utilisez dans la section des paramètres. Les propriétés disponibles sont les suivantes :

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
| nom_paramètre |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| Type |Oui |Type de la valeur du paramètre. Les types et valeurs autorisés sont : **string**, **secureString**, **int**, **bool**, **object**, **secureObject** et **array**. |
| defaultValue |Non  |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non  |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non  |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non  |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non  |Valeur minimale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| maxLength |Non  |Valeur maximale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| description |Non  |Description du paramètre qui apparaît aux utilisateurs dans le portail. |

## <a name="template-functions-with-parameters"></a>Fonctions de modèle avec des paramètres

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

## <a name="objects-as-parameters"></a>Objets en tant que paramètres

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

## <a name="example-templates"></a>Exemples de modèles

Ces exemples de modèles montrent quelques scénarios d’utilisation de paramètres. Déployez-les pour tester la façon dont les paramètres sont gérés dans différents cas de figure.

|Modèle  |Description  |
|---------|---------|
|[Paramètres avec fonctions pour les valeurs par défaut](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Montre comment utiliser les fonctions de modèle durant la définition des valeurs par défaut des paramètres. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |
|[Objet de paramètre](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Montre comment utiliser un objet pour un paramètre. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur la saisie des valeurs de paramètre au cours du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md). 
* Pour obtenir des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives aux modèles Azure Resource Manager](template-best-practices.md).
* Pour plus d’informations sur l’utilisation d’un objet de paramètre, consultez [Utiliser un objet en tant que paramètre dans un modèle Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
