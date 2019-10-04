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
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146601"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Créez un IoT Hub à l’aide de la cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

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

Pour créer un groupe de ressources pour votre hub IoT, utilisez la commande [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup). Cet exemple crée un groupe de ressources appelé **MyIoTRG1** dans la région **USA Est** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Pour créer un hub IoT dans le groupe de ressources que vous avez créé à l’étape précédente, utilisez la commande [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub). Cet exemple crée un hub **S1** appelé **MyTestIoTHub** dans la région **USA Est** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Le nom du hub IoT doit être globalement unique.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Vous pouvez afficher tous les hubs IoT de votre abonnement à l’aide de la commande [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) :

```azurepowershell-interactive
Get-AzIotHub
```

Cet exemple montre le hub IoT Standard S1 que vous avez créé à l’étape précédente.

Vous pouvez supprimer le hub IoT à l’aide de la commande [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Vous pouvez également supprimer un groupe de ressources et toutes les ressources qu’il contient à l’aide de la commande [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) :

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
