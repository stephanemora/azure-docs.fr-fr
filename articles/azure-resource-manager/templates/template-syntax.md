---
title: Structure et syntaxe des modèles
description: Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 4cebe017793bc167f0a78c0be2f24154dc27b3c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476187"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendre la structure et la syntaxe des modèles Azure Resource Manager

Cet article décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections.

Cet article est destiné aux utilisateurs qui possèdent des connaissances sur les modèles Resource Manager. Il fournit des informations détaillées sur la structure du modèle. Pour obtenir un didacticiel pas à pas vous guidant tout au long du processus de création d’un modèle, consultez [Tutoriel : Créer et déployer votre premier modèle Azure Resource Manager](template-tutorial-create-first-template.md).

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
| $schema |Oui |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.<br><br> Pour des déploiements de groupes de ressources, utilisez : `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Pour des déploiements d’abonnements, utilisez : `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Oui |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Utilisez cette valeur pour documenter les modifications importantes dans votre modèle. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| apiProfile |Non | Une version d’API qui sert de collection de versions d’API pour les types de ressources. Utilisez cette valeur pour éviter d’avoir à spécifier les versions d’API pour chaque ressource dans le modèle. Lorsque vous spécifiez une version de profil d’API et que vous ne spécifiez pas une version d’API pour le type de ressource, Resource Manager utilise la version d’API pour ce type de ressource qui est définie dans le profil.<br><br>La propriété de profil d’API est particulièrement utile lorsque vous déployez un modèle dans différents environnements, comme Azure Stack et Azure global. Utilisez la version de profil d’API pour vous assurer que votre modèle utilise automatiquement des versions prises en charge dans les deux environnements. Pour une liste des versions de profil d’API actuelles et les versions d’API définies dans le profil de ressources, consultez [Profil d’API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Pour plus d’informations, consultez [Suivre les versions à l’aide de profils d’API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Non |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| [variables](#variables) |Non |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| [functions](#functions) |Non |Fonctions définies par l’utilisateur et disponibles dans le modèle. |
| [resources](#resources) |Oui |Types de ressource déployés ou mis à jour dans un groupe de ressources ou un abonnement. |
| [outputs](#outputs) |Non |Valeurs retournées après le déploiement. |

Chaque élément a des propriétés que vous pouvez définir. Cet article décrit les sections du modèle de manière plus approfondie.

## <a name="parameters"></a>Paramètres

C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Vous êtes limité à 256 paramètres dans un modèle. Vous pouvez réduire le nombre de paramètres en utilisant des objets contenant plusieurs propriétés.

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
| parameter-name |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur du paramètre. Les types et valeurs autorisés sont : **string**, **secureString**, **int**, **bool**, **object**, **secureObject** et **array**. Consultez [Types de données](#data-types). |
| defaultValue |Non |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non |Valeur minimale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| maxLength |Non |Valeur maximale pour les paramètres de type string, secure string et array. Cette valeur est inclusive. |
| description |Non |Description du paramètre qui apparaît aux utilisateurs dans le portail. Pour plus d’informations, consultez [Commentaires dans les modèles](#comments). |

Pour obtenir des exemples d’utilisation des paramètres, consultez [Paramètres dans les modèles Azure Resource Manager](template-parameters.md).

### <a name="data-types"></a>Types de données

Pour les entiers passés comme paramètres inclus, la plage de valeurs peut être limitée par le SDK ou l’outil en ligne de commande que vous utilisez pour le déploiement. Par exemple, si vous utilisez PowerShell pour déployer un modèle, les types d’entiers peuvent être compris entre -2147483648 et 2147483647. Pour éviter cette limite, spécifiez des valeurs entières élevées dans un [fichier de paramètres](parameter-files.md). Les types de ressources appliquent leurs propres limites aux propriétés d’entiers.

Quand vous spécifiez des valeurs booléennes et des valeurs entières dans votre modèle, ne les placez pas entre guillemets. Placez les valeurs de chaîne entre guillemets doubles.

Les objets commencent par une accolade ouvrante et se terminent par une accolade fermante. Les tableaux commencent par un crochet ouvrant et se terminent par un crochet fermant.

Les chaînes et objets sécurisés ne peuvent pas être lus après le déploiement des ressources.

Pour obtenir des exemples de mise en forme de types de données, consultez [Formats de types de paramètres](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variables

Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. Vous n’êtes pas obligé de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

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

Pour plus d’informations sur l’utilisation de `copy` pour créer plusieurs valeurs pour une variable, consultez [Itération de variable](create-multiple-instances.md#variable-iteration).

Pour obtenir des exemples d’utilisation des variables, consultez [Variables dans un modèle Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Fonctions

Dans votre modèle, vous pouvez créer vos propres fonctions. Ces fonctions peuvent être utilisées dans votre modèle. En règle générale, vous définissez des expressions complexes que vous ne voulez pas répéter tout au long de votre modèle. Vous créez les fonctions définies par l’utilisateur à partir d’expressions et de [fonctions](template-functions.md) prises en charge dans les modèles.

La définition d’une fonction utilisateur est soumise à certaines restrictions :

* La fonction ne peut pas accéder aux variables.
* La fonction ne peut utiliser que des paramètres définis dans l’autre fonction. Lorsque vous utilisez la [fonction parameters](template-functions-deployment.md#parameters) dans une fonction définie par l’utilisateur, vous êtes limité aux paramètres de cette fonction.
* La fonction ne peut pas appeler d’autres fonctions définies par l’utilisateur.
* La fonction ne peut pas utiliser la [fonction de référence](template-functions-resource.md#reference).
* Les paramètres de la fonction ne peuvent pas avoir de valeur par défaut.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
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
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| espace de noms |Oui |Espace de noms pour les fonctions personnalisées. Utilisez pour éviter les conflits de noms avec les fonctions de modèle. |
| function-name |Oui |Nom de la fonction personnalisée. Lors de l’appel de la fonction, associez le nom de la fonction à l’espace de noms. Par exemple, pour appeler une fonction nommée uniqueName dans l’espace de noms contoso, utilisez `"[contoso.uniqueName()]"`. |
| parameter-name |Non |Nom du paramètre à utiliser dans la fonction personnalisée. |
| parameter-value |Non |Type de la valeur du paramètre. Les types et valeurs autorisés sont : **string**, **secureString**, **int**, **bool**, **object**, **secureObject** et **array**. |
| output-type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée de la fonction. |
| output-value |Oui |Expression du langage du modèle évaluée et retournée à partir de la fonction. |

Pour obtenir des exemples d’utilisation des fonctions personnalisées, consultez [Fonctions définies par l’utilisateur dans un modèle Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Ressources

Dans la section des ressources, vous définissez les ressources déployées ou mises à jour.

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
| condition | Non | Valeur booléenne qui indique si la ressource sera provisionnée pendant ce déploiement. Quand la valeur est `true`, la ressource est créée pendant le déploiement. Quand la valeur est `false`, la ressource est ignorée pour ce déploiement. Voir [condition](conditional-resource-deployment.md). |
| apiVersion |Oui |La version de l'API REST à utiliser pour la création de la ressource. Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). |
| type |Oui |Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et du type de ressource (comme **Microsoft.Storage/storageAccounts**). Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). Pour une ressource enfant, le format du type dépend de si elle est imbriquée dans la ressource parente ou définie en dehors de la ressource parente. Consultez [Définition du nom et du type des ressources enfants](child-resource-name-type.md). |
| name |Oui |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. Les services Azure qui exposent le nom de la ressource à des parties externes valident le nom pour vérifier qu’il ne s’agit pas d’une tentative d’usurpation d’identité. Pour une ressource enfant, le format du nom dépend de si elle est imbriquée dans la ressource parente ou définie en dehors de la ressource parente. Consultez [Définition du nom et du type des ressources enfants](child-resource-name-type.md). |
| location |Variable |Emplacements géographiques de la ressource fournie pris en charge. Vous pouvez sélectionner l’un des emplacements disponibles, mais en général, il est judicieux de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer dans la même région les ressources qui interagissent entre elles. La plupart des types de ressources nécessitent un emplacement, mais certains types (comme une attribution de rôle) n’ont pas besoin d’emplacement. Voir [Définir l’emplacement des ressources](resource-location.md). |
| tags |Non |Balises associées à la ressource. Appliquer des balises pour organiser logiquement des ressources dans votre abonnement. |
| comments |Non |Vos commentaires pour documenter les ressources dans votre modèle. Pour plus d’informations, consultez [Commentaires dans les modèles](template-syntax.md#comments). |
| copy |Non |Si plusieurs instances sont nécessaires, le nombre de ressources à créer. Le mode par défaut est parallèle. Spécifiez le mode série si vous ne voulez pas que toutes les ressources soient déployées en même temps. Pour plus d’informations, consultez [Créer plusieurs instances de ressources dans Azure Resource Manager](create-multiple-instances.md). |
| dependsOn |Non |Les ressources qui doivent être déployées avant le déploiement de cette ressource. Resource Manager évalue les dépendances entre les ressources et les déploie dans le bon ordre. Quand les ressources ne dépendent pas les unes des autres, elles sont déployées en parallèle. La valeur peut être une liste séparée par des virgules de noms de ressource ou d’identificateurs de ressource uniques. Répertoriez uniquement les ressources qui sont déployées dans ce modèle. Les ressources qui ne sont pas définies dans ce modèle doivent déjà exister. Évitez d’ajouter des dépendances inutiles, car cela risque de ralentir votre déploiement et de créer des dépendances circulaires. Pour savoir comment définir des dépendances, consultez [Définition de dépendances dans les modèles Azure Resource Manager](define-resource-dependency.md). |
| properties |Non |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Vous pouvez aussi spécifier une copie en groupe pour créer plusieurs instances d’une propriété. Pour déterminer les valeurs disponibles, consultez [référence de modèle](/azure/templates/). |
| sku | Non | Certaines ressources autorisent les valeurs qui définissent la référence SKU à déployer. Par exemple, vous pouvez spécifier le type de redondance pour un compte de stockage. |
| kind | Non | Certaines ressources autorisent une valeur qui définit le type de ressource que vous déployez. Par exemple, vous pouvez spécifier le type Cosmos DB à créer. |
| Plan | Non | Certaines ressources autorisent les valeurs qui définissent le plan à déployer. Par exemple, vous pouvez spécifier l’image de marketplace pour une machine virtuelle. |
| les ressources |Non |Ressources enfants qui dépendent de la ressource qui est définie. Fournissez uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. La dépendance sur la ressource parente n’est pas induite. Vous devez la définir explicitement. Consultez [Définition du nom et du type des ressources enfants](child-resource-name-type.md). |

## <a name="outputs"></a>Outputs

Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. En règle générale, vous retournez des valeurs de ressources qui ont été déployées.

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| output-name |Oui |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| condition |Non | Valeur booléenne qui indique si cette valeur de sortie est retournée. Si elle est égale à `true`, cela signifie que la valeur est incluse dans la sortie pour le déploiement. Si elle est égale à `false`, la valeur de sortie est ignorée pour ce déploiement. Lorsqu’elle n’est pas spécifiée, la valeur par défaut est `true`. |
| type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. Si vous spécifiez **securestring** pour le type de sortie, la valeur n’est pas affichée dans l’historique de déploiement et ne peut pas être récupérée à partir d’un autre modèle. Pour utiliser une valeur secrète dans plusieurs modèles, stockez la clé secrète dans un coffre de clés et référencez la clé secrète dans le fichier de paramètres. Pour plus d’informations, consultez l’article [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](key-vault-parameter.md). |
| value |Oui |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

Pour obtenir des exemples d’utilisation des sorties, consultez [Sorties dans un modèle Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Commentaires et métadonnées

Vous avez quelques options permettant d’ajouter des commentaires et des métadonnées à votre modèle.

### <a name="comments"></a>Commentaires

Pour les commentaires inclus, vous pouvez utiliser `//` ou `/* ... */`, mais cette syntaxe ne fonctionne pas avec tous les outils. Vous ne pouvez pas utiliser l’éditeur de modèle du portail pour travailler sur des modèles avec des commentaires inclus. Si vous ajoutez ce style de commentaire, vérifiez que les outils que vous utilisez prennent en charge les commentaires JSON inclus.

> [!NOTE]
> Pour déployer des modèles avec des commentaires à l’aide d’Azure CLI, vous devez utiliser le commutateur `--handle-extended-json-format`.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Dans Visual Studio Code, l’[extension Azure Resource Manager Tools](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) peut détecter automatiquement un modèle Resource Manager et modifier le mode de langage en conséquence. Si **Modèle Azure Resource Manager** s’affiche dans le coin inférieur droit de VS Code, vous pouvez utiliser les commentaires inclus. Les commentaires inclus ne sont plus signalés comme étant non valides.

![Mode de modèle Azure Resource Manager Visual Studio Code](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Métadonnées

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

![Afficher le paramètre conseillé](./media/template-syntax/show-parameter-tip.png)

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

## <a name="multi-line-strings"></a>Chaînes à lignes multiples

Vous pouvez scinder une chaîne en plusieurs lignes. Par exemple, la propriété location et l’un des commentaires dans l’exemple JSON suivant.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Pour déployer des modèles avec des chaînes à plusieurs lignes à l’aide d’Azure CLI, vous devez utiliser le commutateur `--handle-extended-json-format`.

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](linked-templates.md).
* Pour obtenir des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives aux modèles Azure Resource Manager](template-best-practices.md).
* Pour obtenir des suggestions sur la création de modèles Resource Manager utilisables dans tous les environnements Azure et Azure Stack, consultez [Développer des modèles Azure Resource Manager pour la cohérence du cloud](templates-cloud-consistency.md).
