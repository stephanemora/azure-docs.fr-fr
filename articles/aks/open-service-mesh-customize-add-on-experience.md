---
title: Personnaliser l’expérience de l’interface CLI OSM
description: Personnaliser l’expérience de l’interface CLI OSM avec un fichier osmconfig.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
ms.openlocfilehash: 9a18db63f247ed3508f09426841e84ae65c34923
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994762"
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