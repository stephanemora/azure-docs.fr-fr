---
title: Personnaliser l’expérience de l’interface CLI OSM
description: Personnaliser l’expérience de l’interface CLI OSM avec un fichier osmconfig.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1e5b39424b8e162feda65783eecc3a1a95fdaa7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535484"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Configurer les variables CLI OSM avec un fichier OSM_CONFIG

Les utilisateurs peuvent remplacer la configuration CLI OSM par défaut pour améliorer l’expérience du module complémentaire. Pour ce faire, vous pouvez créer un fichier de configuration, semblable à `kubeconfig`. Le fichier de configuration peut être créé au niveau de `$HOME/.osm/config.yaml` ou dans un chemin différent qui est exporté à l’aide de la variable d’environnement `OSM_CONFIG`.

Le fichier doit contenir le contenu au format YAML suivant :

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

Si le fichier n’est pas créé dans `$HOME/.osm/config.yaml`, n’oubliez pas de définir la variable d’environnement `OSM_CONFIG` pour qu’elle pointe vers le chemin où le fichier de configuration est créé.

Après avoir défini OSM_CONFIG, la sortie de la commande `osm env` doit être la suivante : 

```console
$ osm env
--- 
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```