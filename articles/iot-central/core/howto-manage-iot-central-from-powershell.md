---
title: Gérer IoT Central à partir d’Azure PowerShell | Microsoft Docs
description: Cet article explique comment créer et gérer des applications IoT Central sur Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480292"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gérer IoT Central à partir d’Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central dans le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) pour gérer vos applications.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez exécuter Azure PowerShell sur votre ordinateur local, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Lorsque vous exécutez Azure PowerShell localement, utilisez l’applet de commande **Connect-AzAccount** pour vous connecter à Azure avant d’essayer les applets de commande de cet article.

## <a name="install-the-iot-central-module"></a>Installer le module IoT Central

Exécutez la commande suivante pour vérifier que le [module IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) est bien installé dans votre environnement PowerShell :

```powershell
Get-InstalledModule -name Az.I*
```

Si la liste des modules installés n’inclut pas **Az.IotCentral**, exécutez la commande suivante :

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Création d'une application

Utilisez l’applet de commande [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) pour créer une application IoT Central dans votre abonnement Azure. Par exemple :

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Le script crée d’abord un groupe de ressources dans l’emplacement USA Est pour l’application. Le tableau suivant décrit les paramètres utilisés avec la commande **New-AzIotCentralApp** :

|Paramètre         |Description |
|------------------|------------|
|ResourceGroupName |Groupe de ressources qui contient l’application. Ce groupe de ressources doit déjà exister dans votre abonnement. |
|Location |Par défaut, cette applet de commande utilise l’emplacement du groupe de ressources. Actuellement, vous pouvez créer une application IoT Central dans les emplacements **États-Unis**, **Australie**, **Asie-Pacifique** ou **Europe**.  |
|Nom              |Nom de l’application dans le portail Azure. |
|Sous-domaine         |Sous-domaine dans l’URL de l’application. Dans l’exemple, l’URL de l’application est https://mysubdomain.azureiotcentral.com. |
|Sku               |Actuellement, la seule valeur est **S1** (niveau Standard). Consultez [Tarifs Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modèle          | Modèle d’application à utiliser. Pour plus d’informations, voir le tableau suivant : |
|DisplayName       |Nom de l’application tel qu’il est affiché dans l’interface utilisateur. |

**Modèles d’application avec les fonctionnalités généralement disponibles**

| Nom du modèle            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crée une application vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
| iotc-demo@1.0.0          | Crée une application qui comprend un modèle d’appareil déjà créé pour une machine de distribution réfrigérée. Utilisez ce modèle pour commencer à explorer Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Crée une application avec des modèles d’appareil tout prêts qui vous permettent de connecter un appareil MXChip ou Raspberry Pi. Utilisez ce modèle si vous êtes développeur d’appareils et que vous testez l’un de ces appareils. |


**Modèles d’application avec des fonctionnalités en préversion publique**

| Nom du modèle            | Description |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Crée une application Plug-and-Play en préversion vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
| iotc-condition@1.0.0     | Crée une application avec un modèle Analytique dans le magasin - Supervision des conditions. Utilisez ce modèle pour connecter et superviser l’environnement de magasin. |
| iotc-consumption@1.0.0   | Créez une application avec le modèle de supervision de la consommation d’eau. Utilisez ce modèle pour superviser et contrôler le débit d’eau. |
| iotc-distribution@1.0.0  | Crée une application avec un modèle de distribution numérique. Utilisez ce modèle pour améliorer l’efficacité des sorties d’entrepôt en numérisant les ressources et les actions clés. |
| iotc-inventory@1.0.0     | Crée une application avec un modèle de gestion intelligente des stocks. Utilisez ce modèle pour automatiser la réception, le déplacement de produits, l’inventaire tournant et le suivi des capteurs. |
| iotc-logistics@1.0.0     | Crée une application avec un modèle Logistique connectée. Utilisez ce modèle pour suivre votre expédition en temps réel par voie aérienne, maritime et terrestre grâce à la supervision de la localisation et des conditions. |
| iotc-meter@1.0.0         | Crée une application avec le modèle de supervision des compteurs intelligents. Utilisez ce modèle pour superviser la consommation énergétique et l’état du réseau, ainsi que pour identifier les tendances afin d’améliorer le service client et la gestion des compteurs intelligents.  |
| iotc-patient@1.0.0       | Crée une application avec un modèle de supervision continue des patients. Utilisez ce modèle pour organiser les soins des patients, réduire le taux de réadmission et gérer les maladies. |
| iotc-power@1.0.0         | Crée une application avec un modèle de supervision des panneaux solaires. Utilisez ce modèle pour superviser l’état des panneaux solaires et les tendances de la génération d’énergie. |
| iotc-quality@1.0.0       | Créez une application avec le modèle de supervision de la qualité de l’eau. Utilisez ce modèle pour superviser numériquement la qualité de l’eau.|
| iotc-store@1.0.0         | Crée une application avec un modèle Analytique dans le magasin - Paiement. Utilisez ce modèle pour superviser et gérer le passage en caisse dans votre magasin. |
| iotc-waste@1.0.0         | Crée une application avec un modèle Gestion des déchets connectée. Utilisez ce modèle pour superviser les bennes à ordures et répartir les agents. |

> [!NOTE]
> Les modèles d’application en préversion sont actuellement disponibles uniquement dans les emplacements en **Europe** et **États-Unis**.

## <a name="view-your-iot-central-applications"></a>Afficher les applications IoT Central

Utilisez l’applet de commande [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) pour lister vos applications IoT Central et afficher les métadonnées.

## <a name="modify-an-application"></a>Modifier une application

Utilisez l’applet de commande [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) pour mettre à jour les métadonnées d’une application IoT Central. Par exemple, pour modifier le nom complet de votre application :

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Supprimer une application

Utilisez l’applet de commande [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) pour supprimer les métadonnées d’une application IoT Central. Par exemple :

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure PowerShell, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
