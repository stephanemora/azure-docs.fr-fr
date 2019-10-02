---
title: Stratégie pour conserver des manifestes non étiquetés dans Azure Container Registry
description: Découvrez comment activer une stratégie de rétention dans votre registre de conteneurs Azure, pour la suppression automatique de manifestes non étiquetés après une période définie.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305920"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Stratégie de rétention pour les manifestes non étiquetés

Azure Container Registry vous donne la possibilité de définir une *stratégie de rétention* pour les manifestes d’image stockés qui n’ont pas d’étiquette associée (*manifestes non étiquetés*). Quand une stratégie de rétention est activée, les manifestes non étiquetés dans le registre sont automatiquement supprimés après un certain nombre de jours que vous définissez. Cette fonctionnalité empêche que le registre se remplisse d’artefacts superflus et vous permet d’économiser des coûts de stockage. Si l’attribut `delete-enabled` d’un manifeste non étiqueté a la valeur `false`, le manifeste ne peut pas être supprimé et la stratégie de rétention ne s’applique pas.

Pour exécuter les exemples de commandes fournis dans cet article, vous pouvez utiliser Azure Cloud Shell ou une installation locale d’Azure CLI. Si vous souhaitez l’utiliser en local, la version 2.0.74 ou une version ultérieure est requise. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli].

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

> [!WARNING]
> Définissez la stratégie de rétention avec soin car les données d’une image supprimée son IRRÉCUPÉRABLES. Si vous disposez de systèmes qui tirent (pull) des images en fonction du code de hachage du manifeste (et non en fonction du nom de l’image), vous ne devez pas définir de stratégie de rétention pour les manifestes non étiquetés. La suppression des images sans étiquette va empêcher ces systèmes de tirer les images à partir du registre. Au lieu de tirer en fonction du manifeste, envisagez d’adopter un schéma d’*étiquetage unique* (il s’agit là d’une [bonne pratique](container-registry-image-tag-version.md)).

Si vous souhaitez supprimer des étiquettes d’image unique ou des manifestes à l'aide des commandes Azure CLI, consultez [Supprimer des images conteneur dans Azure Container Registry](container-registry-delete.md).

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Seul un registre de conteneurs **Premium** peut être configuré avec une stratégie de rétention. Pour plus d’informations sur les niveaux de service de registre, consultez [Références SKU Azure Container Registry](container-registry-skus.md).
* Vous ne pouvez définir une stratégie de rétention que pour des manifestes non étiquetés.

## <a name="set-a-retention-policy---cli"></a>Définir une stratégie de rétention – CLI

L’exemple suivant montre comment utiliser Azure CLI pour définir une stratégie de rétention pour des manifestes non étiquetés dans un registre.

### <a name="enable-a-retention-policy"></a>Activer une stratégie de rétention

Par défaut, aucune stratégie de rétention n’est définie dans un registre de conteneurs. Pour définir ou mettre à jour une stratégie de rétention, exécutez la commande [az acr config retention update][az-acr-config-retention-update] dans Azure CLI. Vous pouvez spécifier un nombre de jours compris entre 0 et 365 pour la rétention des manifestes non étiquetés. Si vous ne spécifiez pas de nombre de jours, la commande définit une valeur par défaut de 7 jours. Après la période de rétention, tous les manifestes non étiquetés dans le registre sont automatiquement supprimés.

L’exemple suivant définit une stratégie de rétention de 30 jours pour les manifestes non étiquetés dans le registre *myregistry* :

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

L’exemple suivant définit une stratégie pour supprimer du registre tout manifeste non étiqueté. Créez cette stratégie en définissant une période de rétention de 0 jour :

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Désactiver une stratégie de rétention

Pour afficher la stratégie de rétention définie dans un registre, exécutez la commande [az acr config retention show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --name myregistry
```

Pour désactiver une stratégie de rétention dans un registre, exécutez la commande [az acr config retention update][az-acr-config-retention-update] et définissez `--status disabled` :

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Définir une stratégie de rétention – Portail

Vous pouvez également définir une stratégie de rétention du registre via le [portail Azure](https://portal.azure.com). L’exemple suivant montre comment utiliser le portail pour définir une stratégie de rétention de manifestes non étiquetés dans un registre.

### <a name="enable-a-retention-policy"></a>Activer une stratégie de rétention

1. Accédez à votre registre de conteneurs Azure. Sous **Stratégies**, sélectionnez **Rétention** (préversion).
1. Dans **État**, sélectionnez **Activé**.
1. Sélectionnez un nombre de jours compris entre 0 et 365 pour la rétention des manifestes non étiquetés. Sélectionnez **Enregistrer**.

![Activer une stratégie de rétention dans le portail Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Désactiver une stratégie de rétention

1. Accédez à votre registre de conteneurs Azure. Sous **Stratégies**, sélectionnez **Rétention** (préversion).
1. Dans **État**, sélectionnez **Désactivé**. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur les options de [suppression d’images et de référentiels](container-registry-delete.md) dans Azure Container Registry

* Découvrez comment [purger automatiquement](container-registry-auto-purge.md) des images et manifestes sélectionnés d’un registre

* Apprenez-en davantage sur les options de [verrouillage des images et manifestes](container-registry-image-lock.md) dans un registre

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
