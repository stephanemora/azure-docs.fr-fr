---
title: Bonnes pratiques relatives à l’implémentation de l’infrastructure Azure Service Fabric sous forme de code | Microsoft Docs
description: Bonnes pratiques relatives à la gestion de l’infrastructure Service Fabric sous forme de code
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 9224ecebed35a631514c5254703ad2694675d40e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793812"
---
# <a name="infrastructure-as-code"></a>Infrastructure as code

Dans un scénario de production, créez des clusters Azure Service Fabric à l’aide de modèles Resource Manager. Les modèles Resource Manager permettent un meilleur contrôle des propriétés de ressource et garantissent la cohérence de votre modèle de ressource.

Des exemples de modèles Resource Manager pour Windows et Linux sont disponibles dans les [exemples Azure sur GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster. Téléchargez `azuredeploy.json` et `azuredeploy.parameters.json`, puis modifiez-les selon vos besoins.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour déployer les modèles `azuredeploy.json` et `azuredeploy.parameters.json` que vous avez téléchargés précédemment, utilisez les commandes Azure CLI suivantes :

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Création d’une ressource à l’aide de PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Ressources Azure Service Fabric

Vous pouvez déployer des applications et services sur votre cluster Service Fabric par le biais d’Azure Resource Manager. Pour plus d’informations, consultez [Gérer des applications et services en tant que ressources Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource). Voici les bonnes pratiques relatives aux applications Service Fabric, qui concernent plus spécifiquement les ressources à inclure dans vos ressources de modèle Resource Manager.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Pour déployer votre application à l’aide d’Azure Resource Manager, vous devez d’abord [créer un package d’application sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric. Le script Python suivant montre comment créer un package sfpkg :

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)
