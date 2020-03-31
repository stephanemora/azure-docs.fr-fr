---
title: Déployer une instance d’Azure Cache for Redis avec Azure Resource Manager
description: Découvrez comment utiliser un modèle Azure Resource Manager pour déployer une ressource Azure Cache for Redis. Des modèles sont fournis pour les scénarios les plus courants.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412405"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Créer un cache Azure pour Redis à l’aide d’un modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans cette rubrique, vous apprendrez à créer un modèle Azure Resource Manager qui déploie un cache Azure pour Redis. Le cache peut être utilisé avec un compte de stockage existant pour conserver les données de diagnostic. Vous allez également apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Actuellement, les paramètres de diagnostic sont partagés entre tous les caches de la même région d’un abonnement. La mise à jour d’un cache dans la région affecte tous les autres caches dans la région.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Pour en savoir plus sur les propriétés et la syntaxe JSON des types de ressources de cache, consultez [Types de ressources Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Pour obtenir l’intégralité du modèle, consultez la page [Modèle de cache Azure pour Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Des modèles Resource Manager pour le nouveau [niveau Premium](cache-premium-tier-intro.md) sont disponibles. 
> 
> * [Créer un cache Azure Premium pour Redis avec clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Créer un cache Azure Premium pour Redis avec persistance des données](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Créer un Cache Redis Premium déployé dans un réseau virtuel](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Pour connaître les derniers modèles, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) et recherchez `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer
Dans ce modèle, vous déploierez un cache Azure pour Redis qui utilise un compte de stockage existant pour les données de diagnostic.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déployer sur Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres.
Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Emplacement du cache Azure pour Redis. Pour de meilleures performances, utilisez le même emplacement que l’application à utiliser avec le cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nom du compte de stockage existant à utiliser pour les diagnostics. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Valeur booléenne qui indique s’il faut autoriser l’accès via des ports non-SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Valeur qui indique si les diagnostics sont activés. Utilisez ON ou OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Ressources à déployer
### <a name="azure-cache-for-redis"></a>Cache Azure pour Redis
Crée le cache Azure pour Redis.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Commandes pour l’exécution du déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
