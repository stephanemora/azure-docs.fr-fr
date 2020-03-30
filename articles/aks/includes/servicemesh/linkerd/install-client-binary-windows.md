---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593713"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Télécharger et installer le fichier binaire client linkerd de Linkerd

Dans l’interpréteur de commandes PowerShell sur Windows, utilisez `Invoke-WebRequest` pour télécharger la version de Linkerd de la façon suivante :

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Le binaire client `linkerd` s’exécute sur votre machine cliente et vous permet d’interagir avec le maillage de services Linkerd. Utilisez les commandes suivantes pour installer le binaire client Linkerd `linkerd` dans un interpréteur de commandes Powershell sur Windows. Ces commandes copient le binaire client `linkerd` dans un dossier Linkerd et le rendent accessible immédiatement (dans l’interpréteur de commandes actuel) et en permanence (entre les redémarrages de l’interpréteur de commandes) via votre `PATH`. Vous n’avez pas besoin de privilèges élevés (Admin) pour exécuter ces commandes et vous ne devez pas redémarrer votre interpréteur de commandes.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```