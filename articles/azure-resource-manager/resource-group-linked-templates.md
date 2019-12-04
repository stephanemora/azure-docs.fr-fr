---
title: Lier des modèles pour déploiement
description: Décrit comment utiliser des modèles liés dans un modèle Azure Resource Manager afin de créer une solution de modèle modulaire. Indique comment transmettre des valeurs de paramètres, spécifier un fichier de paramètres et créer dynamiquement des URL.
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 985520963ba9fab1a7f71e1af76bba4390c751ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149708"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilisation de modèles liés et imbriqués durant le déploiement de ressources Azure

Pour déployer votre solution, vous pouvez utiliser un modèle unique ou un modèle principal avec de nombreux modèles associés. Les modèles associés peuvent être des fichiers distincts liés au modèle principal, ou des modèles imbriqués dans le modèle principal.

Pour les solutions petites et moyennes, un modèle unique est plus facile à comprendre et à gérer. Vous pouvez voir toutes les ressources et valeurs dans un même fichier. Pour des scénarios avancés, les modèles liés permettent de diviser la solution en composants ciblés. Vous pouvez facilement réutiliser ces modèles pour d’autres scénarios.

Lorsque vous utilisez des modèles liés, vous créez un modèle principal qui reçoit les valeurs de paramètre au cours du déploiement. Le modèle principal contient tous les modèles liés et transmet des valeurs à ces modèles en fonction des besoins.

Pour obtenir un tutoriel, consultez [Tutoriel : Créer des modèles Azure Resource Manager liés](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Pour les modèles liés ou imbriqués, vous pouvez uniquement utiliser le mode de déploiement [Incremental](deployment-modes.md).
>

## <a name="deployments-resource"></a>Ressources de déploiement

Pour créer un lien vers un autre modèle, ajoutez une ressource de **déploiement** à votre modèle principal.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Les propriétés que vous fournissez pour la ressource de déploiement varient selon que vous établissez la liaison à un modèle externe ou que vous imbriquez un modèle inclus dans le modèle principal.

## <a name="nested-template"></a>Modèle imbriqué

Pour imbriquer le modèle dans le modèle principal, utilisez la propriété **template** et spécifiez la syntaxe du modèle.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Pour les modèles imbriqués, vous ne pouvez pas utiliser les paramètres ou variables définis dans le modèle imbriqué. Vous pouvez en revanche utiliser les paramètres et variables du modèle principal. Dans l’exemple précédent, `[variables('storageName')]` récupère une valeur du modèle principal, et non du modèle imbriqué. Cette restriction ne s'applique pas aux modèles externes.
>
> Quand deux ressources sont définies dans un modèle imbriqué et qu’une ressource dépend de l’autre, la valeur de la dépendance est simplement le nom de la ressource dépendante :
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Vous ne pouvez pas utiliser la fonction `reference` dans la section Sorties d’un modèle imbriqué pour une ressource que vous avez déployée dans le modèle imbriqué. Pour renvoyer les valeurs d’une ressource déployée dans un modèle imbriqué, convertissez votre modèle imbriqué en modèle lié.

Le modèle imbriqué nécessite les [mêmes propriétés](resource-group-authoring-templates.md) qu’un modèle standard.

## <a name="external-template"></a>Modèle externe

Pour lier un modèle externe, utilisez la propriété **templateLink**. Vous ne pouvez pas spécifier un fichier local ni un fichier uniquement disponible sur votre réseau local. Vous pouvez seulement fournir une valeur URI qui inclut soit **http** soit **https**. Resource Manager doit être en mesure d’accéder au modèle.

Une possibilité consiste à placer votre modèle lié dans un compte de stockage et à utiliser l’URI de cet élément.

Vous pouvez fournir les paramètres de votre modèle externe dans un fichier externe ou inclus.

### <a name="external-parameters"></a>Paramètres externes

Lorsque vous fournissez un fichier de paramètres externe, utilisez la propriété **parametersLink** :

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
      },
      "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
      }
    }
  }
]
```

Vous ne devez pas fournir la propriété `contentVersion` pour le modèle ou les paramètres. Si vous ne fournissez pas une valeur de version du contenu, la version actuelle du modèle est déployée. Si vous fournissez une valeur pour la version du contenu, elle doit correspondre à la version du modèle lié ; sinon, le déploiement échoue avec une erreur.

### <a name="inline-parameters"></a>Paramètres inline

Ou bien, vous pouvez fournir le paramètre inclus. Vous ne pouvez pas utiliser à la fois des paramètres inline et un lien vers un fichier de paramètres. Si `parametersLink` et `parameters` sont spécifiés tous les deux, le déploiement échoue.

Pour transmettre une valeur du modèle principal au modèle lié, utilisez la propriété **parameters**.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Utilisation de la copie

Pour créer plusieurs instances d’une ressource avec un modèle imbriqué, ajoutez l’élément copy au niveau de la ressource **Microsoft.Resources/deployments**.

L’exemple de modèle suivant montre comment utiliser copy avec un modèle imbriqué.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Utilisation de variables pour lier des modèles

Les exemples précédents représentaient des valeurs d’URL codées en dur pour les liens de modèle. Cette approche peut fonctionner pour un modèle simple, mais elle ne fonctionne pas bien pour un grand ensemble de modèles modulaires. Au lieu de cela, vous pouvez créer une variable statique qui stocke une URL de base pour le modèle principal, avant de créer dynamiquement les URL pour les modèles liés à partir de cette URL de base. Cette approche permet notamment de déplacer ou de répliquer facilement le modèle. En effet, il vous suffit de modifier la variable statique dans le modèle principal. Le modèle principal transmet les URI appropriés au modèle décomposé.

L’exemple suivant indique comment utiliser une URL de base afin de créer deux URL pour des modèles liés (**sharedTemplateUrl** et **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Vous pouvez également utiliser [deployment()](resource-group-template-functions-deployment.md#deployment) pour obtenir l’URL de base pour le modèle actuel, qui permet d’obtenir l’URL d’autres modèles dans le même emplacement. Cette approche est pratique si l’emplacement des modèles change ou si vous voulez éviter de coder en dur les URL dans le fichier du modèle. La propriété templateLink n’est renvoyée qu’en cas de liaison à un modèle distant avec une URL. Si vous utilisez un modèle local, cette propriété n’est pas disponible.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obtenir des valeurs du modèle lié

Pour obtenir une valeur de sortie d’un modèle lié, récupérez la valeur de propriété à l’aide d’une syntaxe comme : `"[reference('deploymentName').outputs.propertyName.value]"`.

Lors de l’obtention d’une propriété de sortie à partir d’un modèle lié, le nom de propriété ne peut pas inclure de tiret.

Les exemples suivants montrent comment faire référence à un modèle lié pour récupérer une valeur de sortie. Le modèle lié retourne un message simple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Le modèle principal déploie le modèle lié et obtient la valeur retournée. Remarquez qu’il fait référence à la ressource de déploiement par son nom et qu’il utilise le nom de la propriété retournée par le modèle lié.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Comme pour d’autres types de ressources, vous pouvez définir des dépendances entre le modèle lié et d’autres ressources. Lorsque d’autres ressources requièrent une valeur de sortie provenant du modèle lié, veillez à ce que celui-ci soit déployé avant les ressources. Sinon, lorsque le modèle lié s’appuie sur d’autres ressources, vérifiez que celles-ci sont déployées avant le modèle lié.

L’exemple suivant montre un modèle qui déploie une adresse IP publique et retourne l’ID de ressource :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Pour utiliser l’adresse IP publique du modèle précédent lors du déploiement d’un équilibreur de charge, établissez un lien vers le modèle et ajoutez une dépendance vis-à-vis de la ressource de déploiement. L’adresse IP publique sur l’équilibreur de charge est définie sur la valeur de sortie du modèle lié.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Modèles liés et imbriqués dans l’historique de déploiement

Resource Manager traite chaque modèle comme un déploiement séparé dans l’historique de déploiement. Par conséquent, un modèle principal doté de trois modèles liés ou imbriqués s’affiche dans l’historique de déploiement en tant que :

![Historique de déploiement](./media/resource-group-linked-templates/deployment-history.png)

Vous pouvez utiliser ces entrées distinctes dans l’historique pour récupérer les valeurs de sortie après le déploiement. Le modèle suivant crée une adresse IP publique et renvoie l’adresse IP :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Le modèle suivant est lié au modèle précédent. Il crée trois adresses IP publiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Après le déploiement, vous pouvez récupérer les valeurs de sortie avec le script PowerShell suivant :

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Vous pouvez aussi utiliser un script Azure CLI dans un interpréteur de commandes Bash :

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Sécurisation d’un modèle externe

Même si le modèle lié doit être disponible en externe, il n’a pas besoin d’être accessible au public. Vous pouvez ajouter votre modèle dans un compte de stockage privé, uniquement accessible au propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature d’accès partagé (SAP) pour autoriser l’accès en cours de déploiement. Vous ajoutez ce jeton SAP à l’URI pour le modèle lié. Même si le jeton est transmis sous forme de chaîne sécurisée, l’URI du modèle lié, y compris le jeton SAP, est enregistré dans les opérations de déploiement. Pour limiter l’exposition, définissez un délai d’expiration pour le jeton.

Le fichier de paramètres peut également être limité à l’accès avec un jeton SAP.

Vous ne pouvez pas établir un lien vers un modèle dans un compte de stockage derrière un [pare-feu Stockage Azure](../storage/common/storage-network-security.md).

L’exemple suivant montre comment passer un jeton SAP lors de la liaison à un modèle :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Dans PowerShell, vous obtenez un jeton pour le conteneur et déployez les modèles avec les commandes suivantes. Notez que le paramètre **containerSasToken** est défini dans le modèle. Il ne s'agit pas d'un paramètre de la commande **New-AzResourceGroupDeployment**.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Avec un script Azure CLI dans un interpréteur de commandes Bash, vous obtenez un jeton pour le conteneur et vous déployez les modèles avec le code suivant :

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants montrent des utilisations courantes des modèles liés.

|Modèle principal  |Modèle lié |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retourne une chaîne du modèle lié. |
|[Équilibreur de charge avec adresse IP publique](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Retourne l’adresse IP publique du modèle lié et affecte cette valeur à l’équilibreur de charge. |
|[Plusieurs adresses IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Crée plusieurs adresses IP publiques dans le modèle lié.  |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un tutoriel, consultez [Tutoriel : Créer des modèles Azure Resource Manager liés](./resource-manager-tutorial-create-linked-templates.md).
* Pour obtenir des informations sur la définition de l’ordre de déploiement de vos ressources, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md).
* Pour savoir comment définir une seule ressource mais également créer de nombreuses instances de cette dernière, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour connaître les étapes de configuration d’un modèle dans un compte de stockage et de génération d’un jeton SAP, consultez [Déployer des ressources avec des modèles Resource Manager et Azure PowerShell](resource-group-template-deploy.md) ou [Déployer des ressources avec des modèles Resource Manager et l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md).
