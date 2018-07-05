---
title: Guide pratique pour installer Azure IoT Edge sur Windows avec des conteneurs Linux | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows avec des conteneurs Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034433"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Installer le runtime Azure IoT Edge sur Windows pour l’utiliser avec des conteneurs Linux

Le runtime Azure IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant l’agent IoT Edge. L’**agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge sur votre système Windows x64 (AMD/Intel). La prise en charge de Windows est actuellement assurée en préversion.

>[!NOTE]
L’utilisation de conteneurs Linux sur les systèmes Windows n’est pas une configuration de production recommandée ou prise en charge pour Azure IoT Edge. Cependant, ils peuvent être utilisés à des fins de développement et de test.

## <a name="supported-windows-versions"></a>Versions de Windows prises en charge
Azure IoT Edge peut être utilisé à des fins de développement et de test sur les versions suivantes de Windows, quand des conteneurs Linux sont utilisés :
  * Windows 10 ou systèmes d’exploitation d’ordinateur plus récents.
  * Windows Server 2016 ou systèmes d’exploitation de serveur plus récents.

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur 

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). 

Vous pouvez utiliser [Docker pour Windows][lnk-docker-for-windows] pour le développement et les tests. Vérifiez que Docker pour Windows est [configuré pour utiliser des conteneurs Linux][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

### <a name="download-the-edge-daemon-package-and-install"></a>Télécharger et installer le package de démon Edge

Dans une fenêtre PowerShell d’administrateur, exécutez les commandes suivantes :

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Installez le vcruntime à l’aide de la commande suivante :

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Créez et démarrez le service *iotedge* :

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Ajoutez des exceptions de pare-feu pour les ports utilisés par le service :

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Créez un fichier **iotedge.reg** avec le contenu suivant, puis importez-le dans le Registre Windows en double-cliquant dessus ou en utilisant la commande `reg import iotedge.reg` :

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurer le démon de sécurité Azure IoT Edge

Vous pouvez configurer le démon à l’aide du fichier de configuration disponible à l’emplacement `C:\ProgramData\iotedge\config.yaml`. Vous pouvez configurer l’appareil Edge <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuellement en utilisant une [chaîne de connexion d’appareil][lnk-dcs].

Pour une configuration manuelle, entrez la chaîne de connexion d’appareil dans la section **provisioning:** de **config.yaml**.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
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

Vous devez ensuite indiquer l’adresse IP et le port pour **workload_uri** et **management_uri** dans la section **connect:** de la configuration.

Pour l’adresse IP, entrez `ipconfig` dans la fenêtre PowerShell et sélectionnez l’adresse IP de l’interface **vEthernet (DockerNAT)** comme indiqué dans l’exemple ci-dessous (il se peut que l’adresse IP soit différente sur votre système) :

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Entrez les mêmes adresses dans la section **listen:** de la configuration. Par exemple : 

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Dans la fenêtre PowerShell, créez une variable d’environnement **IOTEDGE_HOST** avec l’adresse **management_uri**, par exemple :

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

Enfin, vérifiez que le paramètre **network:** en dessous de **moby_runtime:** n’est pas commenté et qu’il est défini sur **azure iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Enregistrez le fichier de configuration et redémarrez le service :

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vous pouvez vérifier l’état du service IoT Edge comme suit : 

```powershell
Get-Service iotedge
```

Examinez les journaux du service des 5 dernières minutes à l’aide de la commande suivante :

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Enfin, répertoriez les modules en cours d’exécution avec la commande suivante :

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [dépannage][lnk-trouble].


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

