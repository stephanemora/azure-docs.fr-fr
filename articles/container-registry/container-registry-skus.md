---
title: Niveaux de service et références SKU
description: Découvrez les fonctionnalités et les limites des niveaux de service (SKU) De base, Standard et Premium d’Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456273"
---
# <a name="azure-container-registry-skus"></a>Références (SKU) Azure Container Registry

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, appelés références SKU. Ces références SKU offrent une tarification prévisible et plusieurs options pour l’alignement en fonction de la capacité et des modèles d’utilisation de votre registre Docker privé dans Azure.

| SKU | Description |
| --- | ----------- |
| **De base** | Point d’entrée au coût optimisé pour les développeurs apprenant Azure Container Registry. Les registres De base ont les mêmes fonctionnalités de programmation que les registres Standard et Premium (telle que l’[intégration de l’authentification](container-registry-authentication.md#individual-login-with-azure-ad) Azure Active Directory, la [suppression d’image][container-registry-delete] et les [webhooks][container-registry-webhook]). Toutefois, le stockage inclus et le débit d’image sont parfaitement appropriés pour des scénarios d’utilisation inférieure. |
| **Standard** | Les registres Standard offrent les mêmes fonctionnalités que la version De base, avec un stockage inclus et un débit d’image accrus. Les registres Standard devraient satisfaire les besoins de la plupart des scénarios de production. |
| **Premium** | Les registres Premium fournissent la quantité la plus élevée de stockage inclus et d’opérations simultanées, permettant des scénarios impliquant des volumes importants. En plus d’un débit d’image plus élevé, le niveau Premium ajoute des fonctionnalités comme la [géoréplication][container-registry-geo-replication] pour gérer un seul registre dans plusieurs régions, [l’approbation de contenu](container-registry-content-trust.md) pour la signature d’étiquettes d’image et les [pare-feu et réseaux virtuels (préversion)](container-registry-vnet.md) pour restreindre l’accès au registre. |

Les références SKU De base, Standard et Premium fournissent toutes les mêmes fonctionnalités de programmation. Elles bénéficient également d’un [stockage des images][container-registry-storage] complètement managé par Azure. Une référence de niveau supérieur fournit de meilleures performances et une meilleure mise à l’échelle. Avec plusieurs niveaux de service, vous pouvez commencer avec un niveau De base, puis passer aux niveaux Standard et Premium au fur et à mesure que votre utilisation du registre augmente.

## <a name="sku-features-and-limits"></a>Fonctionnalités et limites des références SKU

Le tableau suivant décrit en détail les fonctionnalités et les limites des niveaux de service De base, Standard et Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Changement de référence

Vous pouvez changer la référence SKU d’un registre via l’interface de ligne de commande Azure ou dans le portail Azure. Vous pouvez changer librement de références SKU, tant que la nouvelle possède la capacité de stockage maximale requise. 

### <a name="azure-cli"></a>D’Azure CLI

Pour changer de références SKU dans Azure CLI, utilisez la commande [az acr update][az-acr-update]. Par exemple, pour basculer vers Premium :

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portail Azure

Dans la **vue d’ensemble** du registre de conteneur dans le portail Azure, sélectionnez **Mettre à jour**, puis choisissez une nouvelle **référence SKU** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure][update-registry-sku]

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur la tarification de chacune des références Azure Container Registry, consultez [Tarification Container Registry][container-registry-pricing].

Pour plus d’informations sur la tarification des transferts de données, consultez [Détails sur la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Étapes suivantes

**Feuille de route Azure Container Registry**

Consultez la [Feuille de route ACR][acr-roadmap] sur GitHub pour obtenir des informations sur les fonctionnalités à venir dans le service.

**Azure Container Registry UserVoice**

Proposez et votez pour des suggestions de fonctionnalités dans [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
