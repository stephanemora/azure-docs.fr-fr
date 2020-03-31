---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593951"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Télécharger et installer le binaire client istioctl d’Istio

Dans l’interpréteur de commandes PowerShell sur Windows, utilisez `Invoke-WebRequest` pour télécharger la version d’Istio, puis extrayez-la avec `Expand-Archive` de la façon suivante :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Le binaire client `istioctl` s’exécute sur votre machine cliente et vous permet d’interagir avec le maillage de services Istio. Utilisez les commandes suivantes pour installer le binaire client Istio `istioctl` dans un interpréteur de commandes Powershell sur Windows. Ces commandes copient le binaire client `istioctl` vers un dossier Istio et le rendent accessible immédiatement (dans l’interpréteur de commandes actuel) et en permanence (entre les redémarrages de l’interpréteur de commandes) via votre `PATH`. Vous n’avez pas besoin de privilèges élevés (Admin) pour exécuter ces commandes et vous ne devez pas redémarrer votre interpréteur de commandes.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```