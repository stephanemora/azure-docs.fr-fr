---
title: Se connecter à et gérer des appareils Microsoft Azure données boîte Edge via l’interface Windows PowerShell | Microsoft Docs
description: Décrit comment se connecter à et à gérer Edge de zone de données via l’interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/29/2019
ms.author: alkohli
ms.openlocfilehash: a3096729b2430adf0fd884fc03e3b051b17f5b51
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660458"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gérer un appareil Edge de zone de données Azure via Windows PowerShell

Solution de données boîte Edge Azure vous permet de traiter les données et les envoyer sur le réseau à Azure. Cet article décrit certaines des tâches de configuration et la gestion de votre appareil Edge de zone de données. Vous pouvez utiliser le portail Azure, interface utilisateur web locale ou l’interface Windows PowerShell pour gérer votre appareil.

Cet article se concentre sur les tâches que vous effectuez à l’aide de l’interface de PowerShell.

Cet article inclut les procédures suivantes :

- Se connecter à l’interface de PowerShell
- Démarrer une session de support
- Création d’un package de prise en charge
- Téléchargement d’un certificat
- Réinitialise l’appareil
- Afficher les informations de périphérique
- Les journaux de calcul
- Surveiller et résoudre les modules de calcul

## <a name="connect-to-the-powershell-interface"></a>Se connecter à l’interface de PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Téléchargement d’un certificat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Vous pouvez également charger des certificats de IoT Edge pour activer une connexion sécurisée entre votre appareil IoT Edge et les appareils en aval qui peuvent s’y connecter. Il existe trois certificats IoT Edge (*.pem* format) que vous devez installer :

- Certificat d’autorité de certification racine ou de l’autorité de certification du propriétaire
- Certificat d’autorité de certification d’appareil
- Certificat de clé d’appareil

L’exemple suivant illustre l’utilisation de cette applet de commande pour installer des certificats de IoT Edge :

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Pour plus d’informations sur les certificats, accédez à [les certificats Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [installer des certificats sur une passerelle](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Afficher les informations de périphérique
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Réinitialisez votre appareil

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Les journaux de calcul

Si le rôle de calcul est configuré sur votre appareil, vous pouvez également obtenir les journaux de calcul via l’interface de PowerShell.

1. [Se connecter à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez le `Get-AzureDataBoxEdgeComputeRoleLogs` pour obtenir les journaux de calcul pour votre appareil.

    L’exemple suivant illustre l’utilisation de cette applet de commande :

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Voici une description des paramètres utilisés pour l’applet de commande :
    - `Path`: Fournir un chemin d’accès réseau au partage dans lequel vous souhaitez créer le package de journaux de calcul.
    - `Credential`: Fournir le nom d’utilisateur et le mot de passe pour le partage réseau.
    - `RoleInstanceName`: Fournissez cette chaîne `IotRole` pour ce paramètre.
    - `FullLogCollection`: Ce paramètre garantit que le package de journaux contiendra tous les journaux de calcul. Par défaut, le package de journaux contient uniquement un sous-ensemble des journaux.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Surveiller et résoudre les modules de calcul

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>Étapes suivantes

- Déployer [Azure Data Box Edge](data-box-edge-deploy-prep.md) sur le portail Azure.
