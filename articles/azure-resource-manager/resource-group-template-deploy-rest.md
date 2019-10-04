---
title: Déploiement de  ressources avec le modèle et l’API REST | Microsoft Docs
description: Utilisez Azure Resource Manager et l’API REST Resource Manager pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 42f6ce96cf339e90ed0a0dcdbdb3f1b6924430e9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206393"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager

Cet article explique comment utiliser l’API REST Resource Manager avec les modèles Resource Manager pour déployer vos ressources dans Azure.  

Vous pouvez inclure votre modèle dans le corps de la requête ou un lien vers un fichier. Si vous utilisez un fichier, il peut s’agir d’un fichier local ou d’un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## <a name="deployment-scope"></a>Étendue du déploiement

Vous pouvez cibler votre déploiement au niveau d’un groupe d’administration, d’un abonnement Azure ou d’un groupe de ressources. Dans la plupart des cas, les déploiements ciblent un groupe de ressources. Effectuez des déploiements au niveau du groupe d’administration ou de l’abonnement pour appliquer des stratégies et des attributions de rôles dans toute l’étendue spécifiée. Utilisez également ce type de déploiements pour créer un groupe de ressources et y déployer des ressources. Les commandes à utiliser diffèrent en fonction de l’étendue du déploiement.

Pour un déploiement dans un **groupe de ressources**, utilisez [Déploiements - Créer](/rest/api/resources/deployments/createorupdate). La requête est envoyée à :

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Pour un déploiement dans un **abonnement**, utilisez [Déploiements - Créer au niveau de l’abonnement](/rest/api/resources/deployments/createorupdateatsubscriptionscope). La requête est envoyée à :

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Pour un déploiement dans un **groupe d’administration**, utilisez [Déploiements - Créer au niveau du groupe d’administration](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). La requête est envoyée à :

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Les exemples de cet article illustrent des déploiements dans des groupes de ressources. Pour plus d’informations sur les déploiements dans des abonnements, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Déployer avec l’API REST

1. Définissez [des en-têtes et des paramètres communs](/rest/api/azure/), y compris des jetons d’authentification.

1. Si vous n’avez pas de groupe de ressources, créez-en un. Fournissez votre ID abonnement, le nom du groupe de ressources et l’emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, consultez [Créer un groupe de ressources](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Avec un corps de requête comme suit :

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Validez votre déploiement avant son exécution en exécutant l’opération [Valider un déploiement de modèle](/rest/api/resources/deployments/validate) . Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement (voir l'étape suivante).

1. Pour déployer un modèle, indiquez votre ID d’abonnement, le nom du groupe de ressources et le nom du déploiement dans l’URI de requête. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   Dans le corps de la requête, fournissez un lien vers votre modèle et le fichier de paramètres. Notez que le **Mode** est défini sur **Incremental (Incrémentiel)** . Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete (Terminé)** . Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Si vous souhaitez consigner le contenu de la réponse et/ou le contenu de la demande, incluez **debugSetting** dans la demande.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Vous pouvez configurer votre compte de stockage pour qu’il utilise un jeton de signature d’accès partagé (SAP). Pour plus d’informations, consultez [Délégation de l’accès avec une signature d’accès partagé](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Au lieu d’effectuer la liaison vers des fichiers pour le modèle et les paramètres, vous pouvez les inclure dans le corps de la requête. L’exemple suivant montre le corps de la requête où sont spécifiés le modèle et ses paramètres :

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
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
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
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
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Pour obtenir l’état du déploiement du modèle, utilisez [Déploiements - Obtenir](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

Cette fonctionnalité est également appelée *Annulation en cas d'erreur*. En cas d'échec du déploiement, vous pouvez automatiquement relancer un déploiement antérieur réussi à partir de votre historique de déploiement. Pour spécifier le redéploiement, utilisez la propriété `onErrorDeployment` dans le corps de la requête. Cette fonctionnalité est utile si le déploiement de votre infrastructure était dans un état correct connu et que vous souhaitez le restaurer à cet état. Il existe toutefois un certain nombre de restrictions et mises en garde :

- Le redéploiement est exécuté exactement comme le déploiement précédent, avec les mêmes paramètres. Vous ne pouvez pas changer les paramètres.
- Le déploiement précédent avait été exécuté en [mode complet](./deployment-modes.md#complete-mode). Les ressources qui n’étaient pas incluses dans le déploiement précédent sont supprimées, et les configurations de ressources sont définies à leur état précédent. Assurez-vous de bien comprendre les différents [modes de déploiement](./deployment-modes.md).
- Le redéploiement concerne uniquement les ressources ; il n’inclut pas les modifications de données.
- Cette fonctionnalité est prise en charge sur les déploiements dans des groupes de ressources, mais pas sur les déploiements au niveau de l’abonnement. Pour plus d’informations sur le déploiement au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](./deploy-to-subscription.md).

Pour utiliser cette option, vos déploiements doivent avoir des noms uniques afin de pouvoir être identifiés dans l’historique. Si les noms ne sont pas uniques, le déploiement actuellement en échec peut remplacer le déploiement réussi précédemment dans l’historique. Vous pouvez uniquement utiliser cette option avec les déploiements de niveau racine. Les déploiements à partir d’un modèle imbriqué ne sont pas disponibles pour le redéploiement.

Pour redéployer le dernier déploiement réussi en cas d’échec du déploiement actuel, utilisez :

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Pour redéployer un déploiement spécifique en cas d’échec du déploiement actuel, utilisez :

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Le déploiement spécifié doit avoir réussi.

## <a name="parameter-file"></a>Fichier de paramètres

Si vous utilisez un fichier de paramètres pour transmettre des valeurs lors du déploiement, vous devez créer un fichier JSON avec un format similaire à l’exemple suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

La taille du fichier de paramètres ne peut pas être supérieure à 64 Ko.

Pour fournir une valeur sensible pour un paramètre (par exemple, un mot de passe), ajoutez cette valeur à un coffre de clés. Récupérez le coffre de clés pendant le déploiement comme indiqué dans l’exemple précédent. Pour plus d’informations, consultez [Passage de valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Étapes suivantes

- Pour spécifier comment gérer les ressources présentes dans le groupe de ressources, mais non définies dans le modèle, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
- Pour plus d’informations sur la gestion des opérations REST asynchrones, consultez [Track asynchronous Azure operations (Suivi des opérations asynchrones Azure)](resource-manager-async-operations.md).
- Pour apprendre à créer des modèles, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).

