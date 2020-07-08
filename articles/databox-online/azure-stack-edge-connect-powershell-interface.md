---
title: Se connecter à un appareil Microsoft Azure Stack Edge et le gérer via l’interface Windows PowerShell | Microsoft Docs
description: Décrit comment se connecter à un appareil Azure Stack Edge et le gérer via l’interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 973c618b46d1b6be902d9629ca63ee120cae6855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85313196"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>Gérer un appareil Azure Stack Edge via Windows PowerShell

La solution Azure Stack Edge vous permet de traiter des données et de les envoyer à Azure via le réseau. Cet article décrit certaines des tâches de gestion et de configuration pour votre appareil Azure Stack Edge. Vous pouvez utiliser le portail Azure, l'interface utilisateur locale ou l'interface Windows PowerShell pour gérer votre appareil.

Cet article traite des tâches que vous effectuez à l’aide de l’interface PowerShell. 

Cet article inclut les procédures suivantes :

- Connexion à l’interface PowerShell
- Création d’un package de prise en charge
- Téléchargement d’un certificat
- Réinitialisation de l’appareil
- Affichage des informations sur l’appareil
- Récupération des journaux de calcul
- Supervision et dépannage des modules de calcul

## <a name="connect-to-the-powershell-interface"></a>Connexion à l’interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Téléchargement d’un certificat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Vous pouvez également charger des certificats IoT Edge pour permettre une connexion sécurisée entre votre appareil IoT Edge et les appareils en aval qui peuvent s’y connecter. Il existe trois certificats IoT Edge (format  *.pem*) que vous devez installer :

- Certificat d’autorité de certification racine ou de l’autorité de certification du propriétaire
- Certificat d’autorité de certification d’appareil
- Certificat de clé d’appareil

L’exemple suivant illustre l’utilisation de cette cmdlet pour installer des certificats IoT Edge :

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Lorsque vous exécutez cette cmdlet, vous devez fournir le mot de passe pour le partage réseau.

Pour plus d’informations sur les certificats, consultez [Certificats Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [Installation de certificats sur une passerelle](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Affichage des informations sur l’appareil
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Réinitialisation de votre appareil

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Récupération des journaux de calcul

Si le rôle de calcul est configuré sur votre appareil, vous pouvez également obtenir les journaux de calcul via l’interface PowerShell.

1. [Connectez-vous à l’interface PowerShell](#connect-to-the-powershell-interface).
2. Utilisez `Get-AzureDataBoxEdgeComputeRoleLogs` pour obtenir les journaux de calcul de votre appareil.

    L’exemple suivant montre comment utiliser cette cmdlet :

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Voici une description des paramètres utilisés pour la cmdlet :
    - `Path`: Fournir un chemin d’accès réseau au partage dans lequel vous souhaitez créer le package de journaux de calcul.
    - `Credential`: Indiquez le nom d’utilisateur pour le partage réseau. Lorsque vous exécutez cette cmdlet, vous devez fournir le mot de passe du partage.
    - `FullLogCollection`: Ce paramètre garantit que le package de journaux contiendra tous les journaux de calcul. Par défaut, le package de journaux contient uniquement un sous-ensemble des journaux.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Supervision et dépannage des modules de calcul

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Fermeture de la session à distance

Pour quitter la session PowerShell à distance, fermez la fenêtre PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- Déployez [Azure Stack Edge](azure-stack-edge-deploy-prep.md) sur le portail Azure.
