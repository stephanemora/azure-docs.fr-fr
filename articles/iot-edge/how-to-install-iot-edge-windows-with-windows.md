---
title: Guide pratique pour installer Azure IoT Edge sur Windows avec des conteneurs Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows avec des conteneurs Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 3d34628a5a47788bca8cdafcb6e199a0c2cb3bcc
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437839"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Installer le runtime Azure IoT Edge sur Windows pour utiliser des conteneurs Windows

Le runtime Azure IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge sur votre système Windows x64 (AMD/Intel). 

La prise en charge de Windows est actuellement assurée en préversion.

## <a name="supported-windows-versions"></a>Versions de Windows prises en charge
Azure IoT Edge avec des conteneurs Windows peut être utilisé avec :
  * Windows 10/IoT Enterprise/IoT Core avec la mise à jour d’avril 2018 (Build 17134).
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur 

>[!NOTE]
>Pour installer le moteur de conteneur sur Windows IoT Core, suivez les étapes indiquées dans [l’article sur le provisionnement d’un appareil IoT Core][lnk-iot-core], puis poursuivez avec les instructions ci-dessous.

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). Vous pouvez utiliser [Docker pour Windows][lnk-docker-for-windows] pour le développement et les tests. 

**Vérifiez que Docker pour Windows est [configuré pour utiliser des conteneurs Windows][lnk-docker-config]**.

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

### <a name="download-the-edge-daemon-package-and-install"></a>Télécharger et installer le package de démon Edge

Dans une fenêtre PowerShell d’administrateur, exécutez les commandes suivantes :

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Installez le vcruntime (vous pouvez ignorer cette étape sur un appareil IoT Core Edge) :

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Créez et démarrez le service **iotedge** :

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Ajoutez des exceptions de pare-feu pour les ports utilisés par le service :

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Créez un fichier **iotedge.reg** avec le contenu suivant, puis importez-le dans le Registre Windows en double-cliquant dessus ou en utilisant la commande `reg import iotedge.reg` :

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurer le démon de sécurité Azure IoT Edge

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `C:\ProgramData\iotedge\config.yaml`.

L’appareil Edge peut être configuré manuellement à l’aide d’une [chaîne de connexion d’appareil][lnk-dcs] ou [automatiquement par le biais du service Device Provisioning][lnk-dps].

* Pour la configuration manuelle, supprimez les marques de commentaire du mode de provisionnement **manuel**. Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Pour la configuration automatique, supprimez les marques de commentaire du mode de provisionnement **dps**. Mettez à jour les valeurs de **scope_id** et de **registration_id** avec les valeurs de votre instance de service IoT Hub Device Provisioning et de votre appareil IoT Edge à l’aide du module de plateforme sécurisée. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Obtenez le nom de l’appareil Edge en utilisant la commande `hostname` dans PowerShell, puis définissez-le comme valeur de **hostname:** dans la configuration yaml. Par exemple : 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Indiquez ensuite le port et l’adresse IP pour **workload_uri** et **management_uri** dans les sections **connect:** et **listen:** de la configuration.

Pour récupérer l’adresse IP, entrez `ipconfig` dans la fenêtre PowerShell et copiez l’adresse IP de l’interface **vEthernet (nat)** comme indiqué dans l’exemple suivant (il se peut que l’adresse IP soit différente sur votre système) :  

![nat][img-nat]

Mettez à jour les valeurs de **workload_uri** et de **management_uri** dans la section **connect:** du fichier de configuration. Remplacez **\<GATEWAY_ADDRESS\>** par l’adresse IP que vous avez copiée. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Entrez les mêmes adresses dans la section **listen:** de la configuration, à l’aide de votre adresse IP comme adresse de passerelle.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Dans la fenêtre PowerShell, créez une variable d’environnement **IOTEDGE_HOST** avec l’adresse **management_uri**.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Conservez la variable d'environnement sur les redémarrages.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Enfin, vérifiez que le paramètre **network:** en dessous de **moby_runtime:** n’est pas commenté et qu’il est défini sur **nat**.

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

Enregistrez le fichier de configuration et redémarrez le service :

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Si vous avez utilisé la procédure de **configuration automatique**, vous devez suivre quelques étapes supplémentaires pour que le runtime puisse inscrire à votre place votre appareil avec votre hub IoT. Pour connaître les étapes suivantes, consultez [Créer et provisionner un appareil Edge avec TPM simulé sur Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).

Vous pouvez vérifier l’état du service IoT Edge comme suit : 

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [dépannage][lnk-trouble].


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

