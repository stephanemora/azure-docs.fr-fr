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
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237430"
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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuration de mise à niveau automatique du système d’exploitation Machine virtuelle Azure 
La mise à niveau de vos machines virtuelles est une opération initiée par l’utilisateur, et il est recommandé d’utiliser [mise à niveau de la Machine virtuelle mise à l’échelle définie automatique Système_exploitation](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) pour gestion des correctifs hôte ; les clusters Azure Service Fabric Application d’Orchestration des correctifs est une solution alternative qui est destinée à quand ils sont hébergés en dehors d’Azure, bien que POA peut être utilisé dans Azure, avec une surcharge de l’hébergement de POA dans Azure en cours d’une raison courante de préférer la mise à niveau automatique du système d’exploitation de Machine virtuelle au fil de POA. Les propriétés du modèle de calcul Machine virtuelle mise à l’échelle définie Resource Manager pour activer la mise à niveau automatique du système d’exploitation sont les suivantes :

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Lorsque vous utilisez des mises à niveau automatiques de système d’exploitation avec Service Fabric, la nouvelle image du système d’exploitation est déployée à la fois pour assurer une haute disponibilité des services en cours d’exécution dans Service Fabric un seul domaine de mise à jour. Pour utiliser les mises à niveau automatiques du système d’exploitation dans Service Fabric, votre cluster doit être configuré pour utiliser le niveau de durabilité Silver ou une version supérieure.

Vérifiez que la clé de Registre suivante est définie sur false pour empêcher le lancement des mises à jour non coordonnés de vos ordinateurs hôtes de windows : HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Les propriétés du modèle de calcul Machine virtuelle mise à l’échelle définie Resource Manager pour définir la clé de Registre de Windows Update sur false sont les suivantes :
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuration mise à niveau du Cluster Azure Service Fabric
La propriété de modèle Resource Manager pour activer la mise à niveau automatique du cluster Service Fabric est fourni ci-dessous :
```json
"upgradeMode": "Automatic",
```
Pour mettre à niveau manuellement votre cluster, téléchargez la distribution de cab/deb à une machine virtuelle de cluster, puis appeler la commande PowerShell suivante :
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)
