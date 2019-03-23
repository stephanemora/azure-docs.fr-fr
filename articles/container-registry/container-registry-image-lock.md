---
title: Verrouiller une image dans Azure Container Registry
description: Définir des attributs pour un référentiel ou une image de conteneur donc il ne peut pas être supprimé ou remplacé dans un Registre de conteneurs Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361283"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Verrouiller une image de conteneur dans un Registre de conteneurs Azure

Dans un Registre de conteneurs Azure, vous pouvez verrouiller une version de l’image ou un référentiel afin qu’il ne peut pas être supprimé ou mis à jour. Pour verrouiller une image ou un référentiel, mettre à jour ses attributs à l’aide de la commande CLI Azure [mise à jour du référentiel az acr][az-acr-repository-update]. 

Cet article suppose que vous exécutez Azure CLI dans Azure Cloud Shell ou localement (version 2.0.55 ou ultérieure recommandée). Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli].

## <a name="scenarios"></a>Scénarios

Par défaut, une image avec balises dans Azure Container Registry est *mutable*, de sorte que disposant des autorisations appropriées, vous pouvez à plusieurs reprises mettre à jour et envoyer une image avec la même balise à un Registre. Images de conteneur peuvent également être [supprimé](container-registry-delete.md) en fonction des besoins. Ce comportement est utile lorsque vous développez des images et que vous devez conserver une taille de votre Registre.

Toutefois, lorsque vous déployez une image de conteneur en production, vous devrez peut-être un *immuable* image de conteneur. Une image immuable est celle qui ne peut pas supprimer ou de remplacer accidentellement. Utilisez le [mise à jour du référentiel az acr] [ az-acr-repository-update] commande pour définir les attributs du référentiel afin de pouvoir :

* Verrouiller une version de l’image ou intégralité du dépôt

* Protéger une version de l’image ou le référentiel à partir de la suppression, mais autorise les mises à jour

* Empêcher les opérations de lecture (pull) sur une version de l’image ou intégralité du dépôt

Consultez les sections suivantes pour obtenir des exemples.

## <a name="lock-an-image-or-repository"></a>Verrouiller une image ou un référentiel 

### <a name="show-the-current-repository-attributes"></a>Afficher les attributs de référentiel actuel
Pour afficher les attributs actuels d’un référentiel, exécutez la commande suivante [show de référentiel az acr] [ az-acr-repository-show] commande :

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Afficher les attributs d’image actuelle
Pour afficher les attributs en cours d’une balise, exécutez la commande suivante [show de référentiel az acr] [ az-acr-repository-show] commande :

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Verrouiller une image par balise

Pour verrouiller le *monréf / myimage:tag* dans l’image *myregistry*, exécutez la commande suivante [mise à jour du référentiel az acr] [ az-acr-repository-update] commande :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Verrouiller une image par manifeste digest

Pour verrouiller un *monréf/myimage* image identifiée par le manifeste digest (hachage SHA-256, représenté sous la forme `sha256:...`), exécutez la commande suivante. (Pour connaître le manifeste digest associé à un ou plusieurs balises d’image, exécutez le [az acr référentiel show-manifestes] [ az-acr-repository-show-manifests] commande.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Verrouiller un référentiel

Pour verrouiller le *monréf/myimage* référentiel et toutes les images qu’il contient, exécutez la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Protéger une image ou un référentiel à partir de la suppression

### <a name="protect-an-image-from-deletion"></a>Protéger une image d’une suppression

Pour permettre la *monréf / myimage:tag* image à être mis à jour mais pas supprimé, exécutez la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Protéger un référentiel de la suppression

La commande suivante définit le *monréf/myimage* référentiel afin qu’il ne peut pas être supprimé. Des images individuelles peuvent toujours être mis à jour ou supprimés.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Empêcher les opérations de lecture sur une image ou d’un référentiel

Pour empêcher les opérations de lecture (pull) sur le *monréf / myimage:tag* image, exécutez la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Pour empêcher les opérations de lecture sur toutes les images dans le *monréf/myimage* référentiel, exécutez la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Déverrouiller une image ou un référentiel

Pour restaurer le comportement par défaut de la *monréf / myimage:tag* de l’image afin qu’il peut être supprimé et mis à jour, exécutez la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Pour restaurer le comportement par défaut de la *monréf/myimage* référentiel et toutes les images afin qu’ils peuvent être supprimés et mis à jour, exécutent la commande suivante :

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à l’aide de la [mise à jour du référentiel az acr] [ az-acr-repository-update] commande pour empêcher la suppression ou la mise à jour des versions d’image dans un référentiel. Pour définir des attributs supplémentaires, consultez le [mise à jour du référentiel az acr] [ az-acr-repository-update] référence de la commande.

Pour afficher les attributs définie pour une version de l’image ou le référentiel, utilisez le [show de référentiel az acr] [ az-acr-repository-show] commande.

Pour plus d’informations sur les opérations de suppression, consultez [supprimer des images de conteneur dans Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

