---
title: Créer un IoT Hub Azure à l’aide d’un modèle (PowerShell) | Microsoft Docs
description: Comment utiliser un modèle Azure Resource Manager pour créer un IoT Hub avec Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609169"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Créer un IoT Hub avec un modèle Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Découvrez comment utiliser un modèle Azure Resource Manager pour créer un IoT Hub et un groupe de consommateurs. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour plus d’informations sur le développement de modèles Resource Manager, consultez [documentation Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Le modèle Resource Manager utilisé dans ce démarrage rapide provient [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Voici une copie du modèle :

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Le modèle crée un Azure Iot hub avec trois points de terminaison (eventhub, cloud-à-appareil et la messagerie) et un groupe de consommateurs. Pour d’autres exemples de modèle, consultez [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Vous pouvez trouver le schéma de modèle de Iot Hub [ici](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Il existe plusieurs méthodes pour déployer un modèle.  Vous utilisez Azure PowerShell dans ce didacticiel.

Pour exécuter le script PowerShell, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez Coller :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Comme vous pouvez le voir à partir du script PowerShell, le modèle utilisé est à partir de modèles de démarrage rapide Azure. Pour utiliser votre propre, vous devez tout d’abord charger le fichier de modèle dans Cloud shell et ensuite utiliser le `-TemplateFile` commutateur pour spécifier le nom de fichier.  Pour obtenir un exemple, consultez [déployer le modèle](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé un IoT hub à l’aide d’un modèle Azure Resource Manager, il pouvez que vous souhaitez aller encore plus loin :

* Découvrez les capacités de l’[API REST du fournisseur de ressources IoT Hub][lnk-rest-api].
* Pour plus d’informations sur les capacités d’Azure Resource Manager, voir [Vue d’ensemble d’Azure Resource Manager][lnk-azure-rm-overview].
* Pour connaître la syntaxe JSON et les propriétés à utiliser dans les modèles, consultez [Types de ressources Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Pour en savoir plus sur le développement pour IoT Hub, consultez les articles suivants :

* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
