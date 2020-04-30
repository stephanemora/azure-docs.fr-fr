---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735486"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```