---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079731"
---
## <a name="download-and-install-the-osm-client-binary"></a>Télécharger et installer la bibliothèque de client OSM

Dans un interpréteur de commandes bash sur Linux ou sur [Sous-système Windows pour Linux][install-wsl], utilisez `curl` pour télécharger la version d’OSM, puis extrayez-la avec `tar`, de la manière suivante :

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

Le binaire client `osm` s’exécute sur votre machine cliente et vous permet de gérer OSM dans votre cluster AKS. Utilisez les commandes suivantes pour installer le binaire client OSM `osm` dans un interpréteur de commandes bash sur Linux ou le [Sous-système Windows pour Linux][install-wsl]. Ces commandes copient le binaire client `osm` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Vous pouvez vérifier que la bibliothèque de client `osm` a été correctement ajoutée à votre chemin et son numéro de version à l’aide de la commande suivante.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
