---
titre : Modèle ARM de compte Media Services : Description d’Azure Media Services : Cet article explique comment utiliser un modèle ARM pour créer un compte Media Services.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: quickstart ms.date: 03/23/2021 ms.author: inhenkel ms.custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Démarrage rapide : Modèle ARM de compte Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment utiliser un modèle ARM (Azure Resource Manager) pour créer un compte Media Services.

## <a name="introduction"></a>Introduction

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Les lecteurs qui sont familiarisés avec les modèles ARM peuvent passer à la [section Déploiement](#deploy-the-template).

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.media%2Fmedia-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous n’avez jamais déployé de modèle ARM, nous vous conseillons de lire [cet article sur les modèles ARM Azure](../../azure-resource-manager/templates/index.yml) et de suivre le [tutoriel](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

La syntaxe de la clôture du code JSON est la suivante :

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.media/media-services-create/azuredeploy.json":::

Trois types de ressources Azure sont définis dans le modèle :

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) : créer un compte de stockage
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices) : créer un compte Media Services

## <a name="set-the-account"></a>Définir le compte

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Attribuer une variable à votre fichier de déploiement

Pour plus de commodité, créez une variable qui stocke le chemin d’accès au fichier du modèle. Cette variable vous permet d’exécuter plus facilement les commandes de déploiement car vous n’êtes pas obligé de retaper le chemin d’accès chaque fois que vous déployez.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Déployer le modèle

Vous serez invité à entrer le nom du compte Media Services.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous devez voir une réponse JSON similaire à la suivante :

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Dans le portail Azure, vérifiez que vos ressources ont été créées.

![ressources du guide de démarrage rapide créées](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas d’utiliser les ressources que vous venez de créer, vous pouvez supprimer le groupe de ressources.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation d’un modèle ARM en suivant le processus de création d’un modèle avec des paramètres, des variables, et ainsi de suite, essayez

> [!div class="nextstepaction"]
> [Tutoriel : Créer et déployer votre premier modèle Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)