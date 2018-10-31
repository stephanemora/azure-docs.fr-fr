---
title: Installer Azure IoT Edge sur IoT Standard | Microsoft Docs
description: Installer le runtime Azure IoT Edge sur un appareil Windows IoT Standard
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ac729963e63bd97c83719e21dad3ad2cfc9b4fee
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392792"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installer le runtime IoT Edge sur Windows IoT Standard - préversion

Utilisés conjointement, Azure IoT Edge et [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) permettent de mettre en place une architecture de type « edge computing » (traitement des données à la périphérie du réseau) même sur des petits appareils. Le runtime Azure IoT Edge peut s’exécuter même sur les tout petits appareils SBC (Single Board Computer) qui sont très répandus dans le domaine IoT. 

Cet article explique comment provisionner le runtime sur une carte de développement exécutant Windows IoT Standard. 

**Windows IoT Core prend uniquement en charge Azure IoT Edge sur les processeurs Intel x64.**

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

1. Configurer votre carte mère avec l’image IoT Core **Build 17134 (RS4)**. 
1. Allumez l’appareil, puis [connectez-vous à distance avec PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).
1. Dans la console PowerShell, installez le runtime de conteneurs : 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Le runtime de conteneurs provient du serveur de builds du projet Moby et est uniquement disponible à des fins d’évaluation. Il n’est ni testé, ni approuvé, ni pris en charge par Docker.

## <a name="finish-installing"></a>Terminer l’installation

Installez le démon de sécurité IoT Edge et configurez-le à l’aide des instructions indiquées dans [cet article](how-to-install-iot-edge-windows-with-windows.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez un appareil exécutant le runtime IoT Edge, découvrez comment [Déployer et superviser des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).