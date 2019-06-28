---
title: Références (SKU) Azure Container Registry
description: Comparez les différents niveaux de service disponibles dans Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: f36b206ff015511dea7369617febe9220282bbe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069037"
---
# <a name="azure-container-registry-skus"></a>Références (SKU) Azure Container Registry

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, appelés références SKU. Ces références SKU offrent une tarification prévisible et plusieurs options pour l’alignement en fonction de la capacité et des modèles d’utilisation de votre registre Docker privé dans Azure.

| SKU | Adresses IP gérées | Description |
| --- | :-------: | ----------- |
| **De base** | OUI | Point d’entrée au coût optimisé pour les développeurs apprenant Azure Container Registry. Les registres De base ont les mêmes fonctionnalités de programmation que les registres Standard et Premium (telle que l’[intégration de l’authentification](container-registry-authentication.md#individual-login-with-azure-ad) Azure Active Directory, la [suppression d’image][container-registry-delete] et les [webhooks][container-registry-webhook]). Toutefois, le stockage inclus et le débit d’image sont parfaitement appropriés pour des scénarios d’utilisation inférieure. |
| **Standard** | OUI | Les registres Standard offrent les mêmes fonctionnalités que la version De base, avec un stockage inclus et un débit d’image accrus. Les registres Standard devraient satisfaire les besoins de la plupart des scénarios de production. |
| **Premium** | OUI | Les registres Premium fournissent la quantité la plus élevée de stockage inclus et d’opérations simultanées, permettant des scénarios impliquant des volumes importants. En plus d’un débit d’image plus élevé, un registre Premium ajoute des fonctionnalités, dont la [géoréplication][container-registry-geo-replication] pour la gestion d’un Registre dans plusieurs régions, l’[approbation de contenu](container-registry-content-trust.md) pour la signature de balise image, et les [pare-feux et réseaux virtuels (préversion)](container-registry-vnet.md) pour restreindre l’accès au registre. |
|  Classique (*non disponible après avril 2019*) | Non | La référence SKU a permis la publication initiale du service Azure Container Registry dans Azure. Les registres classiques sont secondés par un compte de stockage crée par Azure dans votre abonnement, qui limite la capacité d’ACR à fournir des fonctionnalités de niveau supérieur telles que l’augmentation du débit et la géoréplication. |

> [!IMPORTANT]
> La référence SKU du registre Classique est **déconseillée** et ne sera plus disponible après le mois d’**avril 2019**. Nous recommandons d’utiliser les références De base, Standard ou Premium pour tous les nouveaux registres. Tous les registres Classiques existants doivent être mis à niveau avant avril 2019. Pour plus d’informations sur la mise à niveau, voir [Mise à niveau d’un registre Classique][container-registry-upgrade].

Les références SKU De base, Standard et Premium (collectivement appelées *registres gérés*) fournissent tous des mêmes fonctionnalités de programmation. Elles bénéficient également d’un [stockage des images][container-registry-storage] entièrement managé par Azure. Une référence de niveau supérieur fournit de meilleures performances et une meilleure mise à l’échelle. Avec plusieurs niveaux de service, vous pouvez commencer avec un niveau De base, puis passer aux niveaux Standard et Premium au fur et à mesure que votre utilisation du registre augmente.

## <a name="sku-feature-matrix"></a>Matrice de fonctionnalités des références SKU

Le tableau suivant décrit en détail les fonctionnalités et les limites des niveaux de service De base, Standard et Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Changement de référence

Vous pouvez changer la référence SKU d’un registre via l’interface de ligne de commande Azure ou dans le portail Azure. Vous pouvez changer librement de références SKU managées tant que la nouvelle référence SKU possède la capacité de stockage maximale requise. En revanche, une fois que vous êtes passé à l’une des références SKU de registre managé, il est impossible de revenir à la référence SKU Classique, car la conversion unidirectionnelle.

### <a name="azure-cli"></a>D’Azure CLI

Pour changer de références SKU dans l’interface de ligne de commande Azure, utilisez la commande [az acr update][az-acr-update]. Par exemple, pour basculer vers Premium :

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portail Azure

Dans la **vue d’ensemble** du registre de conteneur dans le portail Azure, sélectionnez **Mettre à jour**, puis choisissez une nouvelle **référence SKU** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure][update-registry-sku]

Si vous avez un registre Classique, vous ne pouvez pas sélectionner une référence SKU managée dans le portail Azure. Au lieu de cela, vous devez d’abord opérer une [mise à niveau][container-registry-upgrade] vers un registre managé.

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur la tarification de chacune des références SKU Azure Container Registry, voir [Tarification Container Registry][container-registry-pricing].

Pour plus d’informations sur la tarification des transferts de données, consultez [Détails sur la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Étapes suivantes

**Feuille de route Azure Container Registry**

Consultez la [feuille de route ACR][acr-roadmap] sur GitHub pour obtenir des informations sur les fonctionnalités à venir dans le service.

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
