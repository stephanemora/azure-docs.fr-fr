---
title: Guide pratique pour installer Azure IoT Edge sur Windows avec des conteneurs Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows avec des conteneurs Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: e92adc5dbd0da6ab4f60f8cc7bf6dbe7a58694c6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128416"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Installer le runtime Azure IoT Edge sur Windows pour utiliser des conteneurs Windows

Le runtime Azure IoT Edge est ce qui transforme un appareil en un appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. 

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge avec les conteneurs Windows sur votre système Windows x64 (AMD/Intel). 

La prise en charge de Windows est actuellement assurée en préversion.

## <a name="supported-windows-versions"></a>Versions de Windows prises en charge
Azure IoT Edge avec des conteneurs Windows peut être utilisé avec :
  * Windows 10/IoT Enterprise/IoT Core avec la mise à jour d’avril 2018 (Build 17134).
  * Windows Server 1803

Pour plus d’informations sur les systèmes d’exploitation actuellement pris en charge, consultez [Prise en charge d’Azure IoT Edge](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur 

>[!NOTE]
>Pour installer le moteur de conteneur sur Windows IoT Core, suivez les étapes indiquées dans [l’article sur le provisionnement d’un appareil IoT Core][lnk-iot-core], puis poursuivez avec les instructions ci-dessous.

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). Vous pouvez utiliser [Docker pour Windows][lnk-docker-for-windows] pour le développement et les tests. 

Configurez Docker pour Windows [pour utiliser des conteneurs Windows][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour approvisionner automatiquement des appareils. Ce service s’avère particulièrement utile lorsque vous devez approvisionner de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement. 

### <a name="install-and-manually-provision"></a>Installer et approvisionner manuellement

1. Suivez les étapes de la rubrique [Inscrire un nouvel appareil Azure IoT Edge][lnk-dcs] pour inscrire votre appareil et récupérer la chaîne de connexion d’appareil. 

2. Sur votre appareil IoT Edge, exécutez PowerShell en tant qu’administrateur. 

3. Téléchargez et installez le runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Lorsque **DeviceConnectionString** vous est demandé, fournissez la chaîne de connexion récupérée dans IoT Hub. Ne mettez pas de guillemets autour de la chaîne de connexion. 

### <a name="install-and-automatically-provision"></a>Installer et approvisionner automatiquement

1. Suivez les étapes de la rubrique [Créer et provisionner un appareil Edge avec TPM simulé sur Windows][lnk-dps] pour configurer le service Device Provisioning et récupérer son **ID d’étendue**, simuler un appareil TPM et récupérer son **ID d’inscription**, puis créez une inscription individuelle. Une fois l’appareil inscrit dans votre IoT Hub, passez à l’installation.  

   >[!TIP]
   >Gardez ouverte la fenêtre dans laquelle s’exécute le simulateur TPM durant l’installation et les tests. 

2. Sur votre appareil IoT Edge, exécutez PowerShell en tant qu’administrateur. 

3. Téléchargez et installez le runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Lorsque vous y êtes invité, fournissez l’**ID d’étendue** et l’**ID d’inscription** pour votre service d’approvisionnement et votre appareil. 

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vous pouvez vérifier l’état du service IoT Edge come suit : 

```powershell
Get-Service iotedge
```

Examinez les journaux du service des 5 dernières minutes comme suit :

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge][lnk-modules].

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [résolution des problèmes][lnk-trouble].


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md
[lnk-modules]: how-to-deploy-modules-portal.md
