---
title: Déploiement de  ressources avec le modèle et l’API REST | Microsoft Docs
description: Utilisez Azure Resource Manager et l’API REST Resource Manager pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: f49b8ed592422927288e24b164a04645e2e37744
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301381"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager

Cet article explique comment utiliser l’API REST Resource Manager avec les modèles Resource Manager pour déployer vos ressources dans Azure.  

> [!TIP]
> Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
> 
> * [View deployment operations (Afficher les opérations de déploiement)](resource-manager-deployment-operations.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> * [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes
> 
> 

Vous pouvez inclure votre modèle dans le corps de la requête ou un lien vers un fichier. Si vous utilisez un fichier, il peut s’agir d’un fichier local ou d’un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## <a name="deploy-with-the-rest-api"></a>Déployer avec l’API REST
1. Définissez [des en-têtes et des paramètres communs](/rest/api/azure/), y compris des jetons d’authentification.

1. Si vous n’avez pas de groupe de ressources, créez-en un. Fournissez votre ID abonnement, le nom du groupe de ressources et l’emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, consultez [Créer un groupe de ressources](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
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

1. Créez un déploiement. Fournissez votre ID abonnement, le nom du groupe de ressources, le nom du déploiement et un lien vers votre modèle. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](#parameter-file). Pour plus d’informations sur l’API REST pour créer un groupe de ressources, consultez [Créer un déploiement de modèle](/rest/api/resources/deployments/createorupdate). Notez que le **Mode** est défini sur **Incremental (Incrémentiel)**. Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete (Terminé)**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
  ```

  Avec un corps de requête comme suit :

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
      }
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
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
  }
  ```

    Vous pouvez configurer votre compte de stockage pour qu’il utilise un jeton de signature d’accès partagé (SAP). Pour plus d’informations, consultez [Délégation de l’accès avec une signature d’accès partagé](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Au lieu d’effectuer la liaison vers des fichiers pour le modèle et les paramètres, vous pouvez les inclure dans le corps de la requête.

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

5. Obtenez l’état du déploiement du modèle. Pour plus d’informations, consultez [Obtenir des informations sur le déploiement d’un modèle](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

En cas d’échec de déploiement, vous pouvez automatiquement redéployer un déploiement ayant réussi précédemment à partir de votre historique de déploiement. Pour spécifier le redéploiement, utilisez la propriété `onErrorDeployment` dans le corps de la requête.

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
* Pour spécifier comment gérer les ressources présentes dans le groupe de ressources, mais non définies dans le modèle, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
* Pour plus d’informations sur la gestion des opérations REST asynchrones, consultez [Track asynchronous Azure operations (Suivi des opérations asynchrones Azure)](resource-manager-async-operations.md).
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption-guide/subscription-governance).

