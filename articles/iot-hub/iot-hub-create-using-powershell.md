---
title: Créer un IoT Hub Azure à l’aide d’une cmdlet PowerShell | Microsoft Docs
description: Comment utiliser une cmdlet PowerShell pour créer un IoT Hub.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 6462673f6c3992aacbaee168eafc6bdb1b2fa944
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733468"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Créer un IoT hub à l’aide de l’applet de commande New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Présentation

Vous pouvez utiliser les cmdlets Azure PowerShell pour créer et gérer des IoT Hubs Azure. Ce didacticiel vous montre comment créer un IoT Hub à l’aide de PowerShell.

Pour suivre ce guide pratique, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure

Si vous utilisez Cloud Shell, vous êtes déjà connecté à votre abonnement. Par contre, si vous exécutez PowerShell localement, entrez la commande suivante pour vous connecter à votre abonnement Azure :

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Vous avez besoin d’un groupe de ressources pour déployer un IoT Hub. Vous pouvez utiliser un groupe de ressources existant ou en créer un.

Pour créer un groupe de ressources pour votre IoT hub, utilisez le [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) commande. Cet exemple crée un groupe de ressources appelé **MyIoTRG1** dans la région **USA Est** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Pour créer un IoT hub dans le groupe de ressources que vous avez créé à l’étape précédente, utilisez le [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) commande. Cet exemple crée un hub **S1** appelé **MyTestIoTHub** dans la région **USA Est** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Le nom du hub IoT doit être globalement unique.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Vous pouvez répertorier tous les hubs IoT dans votre abonnement à l’aide de la [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) commande :

```azurepowershell-interactive
Get-AzIotHub
```

Cet exemple montre le hub IoT Standard S1 que vous avez créé à l’étape précédente.

Vous pouvez supprimer le hub IoT à l’aide du [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) commande :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Ou bien, vous pouvez supprimer un groupe de ressources et toutes les ressources qu’il contient à l’aide de la [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) commande :

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Étapes suivantes

Ayant déployé un hub IoT à l’aide d’une applet de commande PowerShell, vous pouvez aller encore plus loin en consultant les articles suivants :

* [Applets de commande PowerShell pour utiliser votre hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [API REST de fournisseur de ressources IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Pour en savoir plus sur le développement pour IoT Hub, consultez les articles suivants :

* [Présentation du SDK C](iot-hub-device-sdk-c-intro.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
