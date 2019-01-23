---
title: Gérer IoT Central à partir d’Azure PowerShell | Microsoft Docs
description: Gérez IoT Central à partir d’Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 04726249809656344c8f81164d5deed5af321e71
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355076"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gérer IoT Central à partir d’Azure PowerShell

Au lieu de créer et de gérer des applications IoT Central à partir de la page [Application Manager](https://aka.ms/iotcentral) d’IoT Central, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) pour gérer vos applications.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous préférez exécuter Azure PowerShell sur votre ordinateur local, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Lorsque vous exécutez Azure PowerShell localement, utilisez l’applet de commande **Connect-AzAccount** pour vous connecter à Azure avant d’essayer les applets de commande de cet article.

## <a name="install-the-iot-central-module"></a>Installer le module IoT Central

Exécutez la commande suivante pour vérifier que le [module IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) est bien installé dans votre environnement PowerShell :

```PowerShell
Get-InstalledModule -name Az.I*
```

Si la liste des modules installés n’inclut pas **Az.IotCentral**, exécutez la commande suivante :

```PowerShell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Création d'une application

Utilisez l’applet de commande [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) pour créer une application IoT Central dans votre abonnement Azure. Par exemple : 

```PowerShell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```PowerShell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Le script crée d’abord un groupe de ressources dans la région USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **New-AzIotCentralApp** :

|Paramètre         |Description |
|------------------|------------|
|ResourceGroupName |Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
|Lieu |Par défaut, cette applet de commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les régions **USA Est**, **USA Ouest**, **Europe Nord** et **Europe Ouest**. |
|NOM              |Nom de l’application dans le portail Azure. |
|Sous-domaine         |Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est https://mysubdomain.azureiotcentral.com. |
|Sku               |Actuellement, la seule valeur est **S1** (niveau Standard). Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modèle          | Modèle d’application à utiliser. Pour plus d’informations, voir le tableau suivant : |
|DisplayName       |Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

**Modèles d’application**

|Nom du modèle  |Description |
|---------------|------------|
|iotc-default@1.0.0 |Crée une application vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
|iotc-demo@1.0.0    |Crée une application qui comprend un modèle d’appareil déjà créé pour une machine de distribution réfrigérée. Utilisez ce modèle pour commencer à explorer Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Crée une application avec des modèles d’appareil tout prêts qui vous permettent de connecter un appareil MXChip ou Raspberry Pi. Utilisez ce modèle si vous êtes développeur d’appareils et que vous testez l’un de ces appareils. |

## <a name="view-your-iot-central-applications"></a>Afficher les applications IoT Central

Utilisez l’applet de commande [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) pour lister vos applications IoT Central et afficher les métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez l’applet de commande [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```PowerShell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez l’applet de commande [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) pour supprimer les métadonnées d’une application IoT Central. Par exemple : 

```PowerShell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure PowerShell, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
