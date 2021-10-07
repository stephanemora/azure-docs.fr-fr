---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 10759b4d80b39b1fc9a04d513e75d98b7c691281
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441007"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>Télécharger et installer le fichier binaire client Open Service Mesh (OSM)

Dans l’interpréteur de commandes PowerShell sur Windows, utilisez `Invoke-WebRequest` pour télécharger la version d’OSM, puis extrayez-la avec `Expand-Archive` de la manière suivante :

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.9.1"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

Le binaire client `osm` s’exécute sur votre machine cliente et vous permet de gérer le contrôleur OSM dans votre cluster AKS. Utilisez les commandes suivantes pour installer le binaire client OSM `osm` dans un interpréteur de commandes PowerShell sur Windows. Ces commandes copient le binaire client `osm` vers un dossier OSM et le rendent accessible immédiatement (dans l’interpréteur de commandes actuel) et en permanence (entre les redémarrages de l’interpréteur de commandes) via votre `PATH`. Vous n’avez pas besoin de privilèges élevés (Admin) pour exécuter ces commandes et vous ne devez pas redémarrer votre interpréteur de commandes.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
