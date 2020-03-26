---
title: Configurer Azure IoT Hub Device Provisioning avec un modèle Azure Resource Manager
description: 'Démarrage rapide Azure : Configurer le service Azure IoT Hub Device Provisioning (DPS) avec un modèle'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 482401b75cadf44e2cef03cced8dd216d0980524
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74969579"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Démarrage rapide : Configurer le service IoT Hub Device Provisioning avec le modèle Azure Resource Manager

Vous pouvez utiliser [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour configurer par programme les ressources du cloud Azure nécessaires à l’approvisionnement de vos appareils. Ces étapes montrent comment créer un hub IoT et un service IoT Hub Device Provisioning et comment lier les deux avec un modèle Azure Resource Manager. Ce guide de démarrage rapide utilise [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) pour effectuer les étapes de programmation nécessaires pour créer un groupe de ressources et déployer le modèle. Cependant, vous pouvez facilement utiliser le [portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby ou d’autres langages de programmation pour effectuer ces étapes et déployer votre modèle. 


## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Ce démarrage rapide nécessite que vous exécutiez l’interface Azure CLI localement. Vous devez avoir installé Azure CLI 2.0 ou ultérieur. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Se connecter à Azure et créer un groupe de ressources

Vous connecter à votre compte Azure et sélectionner votre abonnement.

1. Dans l’invite de commande, exécutez la [commande login][lnk-login-command] :
    
    ```azurecli
    az login
    ```

    Suivez les instructions pour vous authentifier à l’aide du code et vous connecter à votre compte Azure via un navigateur web.

2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la [commande pour répertorier les comptes Azure][lnk-az-account-command] ci-dessous :
    
    ```azurecli
    az account list 
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour créer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Lorsque vous créez des ressources de cloud Azure comme des IoT Hubs et des services d’approvisionnement, vous les créez dans un groupe de ressources. Utilisez un groupe de ressources existant, ou exécutez la [commande suivante pour en créer un groupe de ressources][lnk-az-resource-command] :
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > L’exemple précédent crée le groupe de ressources dans l’emplacement USA Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant la commande `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Créer un modèle Resource Manager

Utilisez un modèle JSON pour créer un service d’approvisionnement et un IoT Hub lié dans votre groupe de ressources. Vous pouvez également utiliser un modèle Azure Resource Manager pour apporter des modifications à un IoT Hub ou un service d’approvisionnement existant.

1. Dans un éditeur de texte, créez un modèle Azure Resource Manager appelé **template.json** avec le contenu squelette suivant. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Remplacez la section **parameters** par ce qui suit. La section parameters définit les paramètres dont les valeurs peuvent être passées à partir d’un autre fichier. Cette section définit le nom du hub IoT et du service de provisionnement à créer. Elle définit également leur emplacement. Les valeurs sont limitées aux régions Azure qui prennent en charge les hubs IoT et les services de provisionnement. Pour obtenir la liste des emplacements pris en charge pour le service Device Provisioning, vous pouvez exécuter la commande suivante `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou accéder à la page [État d’Azure](https://azure.microsoft.com/status/) et rechercher « service Device Provisioning ».

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Remplacez la section **variables** par ce qui suit. Cette section définit les valeurs qui seront utilisées ultérieurement pour construire la chaîne de connexion de l’IoT Hub, qui est nécessaire pour lier le service d’approvisionnement et l’IoT Hub. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Pour créer un IoT Hub, ajoutez les lignes suivantes à la collection **ressources**. Le code JSON spécifie les propriétés minimales requises pour créer un hub IoT. Les valeurs **name** et **location** sont passées en tant que paramètres à partir d’un autre fichier. Pour en savoir plus sur les propriétés que vous pouvez spécifier pour un hub IoT dans un modèle, consultez les [informations de référence sur le modèle Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Pour créer le service d’approvisionnement, ajoutez les lignes suivantes après la spécification du IoT Hub dans la collection **ressources**. Les valeurs **name** et **location** du service de provisionnement sont passées en tant que paramètres. La collection **iotHubs** spécifie les hubs IoT à lier au service de provisionnement. Vous devez spécifier au moins les propriétés **connectionString** et **location** pour chaque IoT Hub lié. Vous pouvez également définir les propriétés telles que **allocationWeight** et **applyAllocationPolicy** sur chaque IoT Hub, ainsi que les propriétés comme **allocationPolicy** et  **authorizationPolicies** dans le service d’approvisionnement proprement dit. Pour plus d’informations, consultez l’article [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices) (Informations de référence sur le modèle Microsoft.Devices/provisioningServices).

   La propriété **dependsOn** permet de s’assurer que Resource Manager crée l’IoT Hub avant le service d’approvisionnement. Le modèle nécessite que la chaîne de connexion du IoT Hub spécifie sa liaison avec le service d’approvisionnement. Vous devez donc créer en premier lieu le Hub et ses clés. Le modèle utilise des fonctions telles que **concat** et **listKeys** pour créer la chaîne de connexion à partir de variables paramétrées. Pour en savoir plus, consultez l’article [Fonctions des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Enregistrez le fichier de modèle. Le fichier terminé doit se présenter comme suit :

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Créer un fichier de paramètres Resource Manager

Le modèle que vous avez défini à la dernière étape utilise des paramètres pour spécifier le nom du hub IoT, le nom du service de provisionnement et l’emplacement (région Azure) pour les créer. Vous passez ces paramètres au modèle à partir d’un fichier distinct. En procédant ainsi, vous pouvez réutiliser le même modèle pour plusieurs déploiements. Pour créer le fichier de paramètres, procédez comme suit :

1. Dans un éditeur de texte, créez un fichier de paramètres Azure Resource Manager appelé **parameters.json** avec le contenu squelette suivant : 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Ajoutez la valeur **iotHubName** à la section de paramètre.  Un nom de hub IoT doit être globalement unique dans Azure. Vous pouvez donc ajouter un préfixe ou un suffixe unique à l’exemple de nom ou choisir un nouveau nom. Vérifiez que votre nom respecte les conventions de nommage pour les hubs IoT, à savoir qu’il doit comprendre entre 3 et 50 caractères et contenir uniquement des caractères alphanumériques en minuscules ou majuscules ou des traits d’union (« - »). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Ajoutez la valeur **provisioningServiceName** à la section de paramètre. Vous devez également choisir un nom global unique pour votre service de provisionnement. Vérifiez qu’il respecte les conventions de nommage pour les services IoT Hub Device Provisioning, à savoir qu’il doit comprendre entre 3 et 64 caractères et contenir uniquement des caractères alphanumériques en minuscules ou majuscules ou des traits d’union (« - »).

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Ajoutez la valeur **hubLocation** à la section de paramètre. Cette valeur spécifie l’emplacement de l’IoT Hub et du service d’approvisionnement. La valeur doit correspondre à l’un des emplacements spécifiés dans la collection **allowedValues** de la définition de paramètre du modèle de fichier. Cette collection limite les valeurs aux emplacements Azure qui prennent en charge les IoT Hubs et les services d’approvisionnement. Pour obtenir la liste des emplacements pris en charge pour le service Device Provisioning, vous pouvez exécuter la commande `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou accéder à la page [État d’Azure](https://azure.microsoft.com/status/) et rechercher « service Device Provisioning ».

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Enregistrez le fichier . 


> [!IMPORTANT]
> Étant donné que l’IoT Hub et le service d’approvisionnement seront publiquement détectables en tant que points de terminaison DNS, assurez-vous de ne pas utiliser d’informations sensibles en leur attribuant un nom.
>

## <a name="deploy-the-template"></a>Déployer le modèle

Utilisez les commandes Azure CLI suivantes pour déployer vos modèles et vérifier le déploiement.

1. Pour déployer votre modèle, accédez au dossier contenant les fichiers de modèle et de paramètres, puis exécutez la [commande suivante pour démarrer un déploiement](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) :
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Cette opération peut prendre quelques minutes. Une fois l’opération terminée, recherchez la propriété **provisioningState** et vérifiez qu’elle indique « Succeeded » dans la sortie. 

   ![Sortie de l’approvisionnement](./media/quick-setup-auto-provision-rm/output.png) 


2. Pour vérifier votre déploiement, exécutez la [commande suivante pour répertorier les ressources](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) et recherchez le nouveau service d’approvisionnement et l’IoT Hub dans la sortie :

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées dans ce démarrage rapide. Si vous ne pensez pas continuer, vous pouvez utiliser Azure CLI pour [supprimer une ressource][lnk-az-resource-command], comme un hub IoT ou un service de provisionnement, ou bien un groupe de ressources et l’ensemble de ses ressources.

Pour supprimer le service d’approvisionnement, exécutez la commande suivante :

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Pour supprimer un IoT Hub, exécutez la commande suivante :

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Pour supprimer un groupe de ressources et toutes ses ressources, exécutez la commande suivante :

```azurecli
az group delete --name {your resource group name}
```

Vous pouvez également supprimer des groupes de ressources et des ressources individuelles à l’aide du portail Azure, de PowerShell, des API REST ou encore des kits SDK de plateforme prise en charge publiés pour Azure Resource Manager ou le service IoT Hub Device Provisioning.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un hub IoT et une instance de service Device Provisioning, puis vous avez lié ces deux ressources. Pour savoir comment utiliser cette configuration pour provisionner un appareil simulé, passez au guide de démarrage rapide relatif à la création d’un appareil simulé.

> [!div class="nextstepaction"]
> [Démarrage rapide pour créer un appareil simulé](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
