---
title: Références (SKU) Azure Container Registry
description: Comparez les différents niveaux de service disponibles dans Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582831"
---
# <a name="azure-container-registry-skus"></a>Références (SKU) Azure Container Registry

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, appelés références SKU. Ces références SKU offrent une tarification prévisible et plusieurs options pour l’alignement en fonction de la capacité et des modèles d’utilisation de votre registre Docker privé dans Azure.

| SKU | Adresses IP gérées | Description |
| --- | :-------: | ----------- |
| **De base** | Oui | Point d’entrée au coût optimisé pour les développeurs apprenant Azure Container Registry. Registres de base ont les mêmes fonctionnalités de programmation comme Standard et Premium (tels qu’Azure Active Directory [intégration de l’authentification](container-registry-authentication.md#individual-login-with-azure-ad), [suppression de l’image][container-registry-delete], et [webhooks][container-registry-webhook]). Toutefois, le stockage inclus et le débit d’image sont parfaitement appropriés pour des scénarios d’utilisation inférieure. |
| **Standard** | Oui | Les registres Standard offrent les mêmes fonctionnalités que la version De base, avec un stockage inclus et un débit d’image accrus. Les registres Standard devraient satisfaire les besoins de la plupart des scénarios de production. |
| **Premium** | Oui | Les registres Premium fournissent la quantité la plus élevée de stockage inclus et d’opérations simultanées, permettant des scénarios impliquant des volumes importants. En plus d’un débit plus élevé d’image, version Premium ajoute des fonctionnalités, notamment [géo-réplication] [ container-registry-geo-replication] pour la gestion d’un Registre dans plusieurs régions, [(version préliminaire)d’approbationdecontenu](container-registry-content-trust.md) pour la signature de balise image, et [les pare-feux et réseaux virtuels (version préliminaire)](container-registry-vnet.md) pour restreindre l’accès au Registre. |
|  Classique (*non disponible après avril 2019*) | Non  | La référence SKU a permis la publication initiale du service Azure Container Registry dans Azure. Les registres classiques sont secondés par un compte de stockage crée par Azure dans votre abonnement, qui limite la capacité d’ACR à fournir des fonctionnalités de niveau supérieur telles que l’augmentation du débit et la géoréplication. |

> [!IMPORTANT]
> La référence (SKU) est en cours de Registre classique **déconseillée**et ne sera pas disponible après **avril 2019**. Nous vous recommandons d’utiliser la base, Standard ou Premium pour tous les nouveaux registres. Tous les registres classique existants doivent être mis à niveau avant avril 2019. Pour plus d’informations de mise à niveau, consultez [mettre à niveau un Registre classique][container-registry-upgrade].

Les références de base, Standard et Premium (collectivement appelés *gérés registres*) fournissent tous des mêmes fonctionnalités de programmation. Ils bénéficient également des toutes [image stockage] [ container-registry-storage] entièrement géré par Azure. Une référence de niveau supérieur fournit de meilleures performances et une meilleure mise à l’échelle. Avec plusieurs niveaux de service, vous pouvez commencer avec un niveau De base, puis passer aux niveaux Standard et Premium au fur et à mesure que votre utilisation du registre augmente.

## <a name="sku-feature-matrix"></a>Matrice de fonctionnalités des références SKU

Le tableau suivant décrit en détail les fonctionnalités et les limites des niveaux de service De base, Standard et Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Changement de référence

Vous pouvez changer la référence SKU d’un registre via l’interface de ligne de commande Azure ou dans le portail Azure. Vous pouvez changer librement de références SKU managées tant que la nouvelle référence SKU possède la capacité de stockage maximale requise. Lorsque vous basculez vers une des références SKU managées de déploiement classique, vous ne pouvez pas revenir classique--il s’agit d’une conversion unidirectionnelle.

### <a name="azure-cli"></a>Azure CLI

Pour changer de références SKU dans l’interface de ligne de commande Azure, utilisez la commande [az acr update][az-acr-update]. Par exemple, pour basculer vers Premium :

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portail Azure

Dans la **vue d’ensemble** du registre de conteneur dans le portail Azure, sélectionnez **Mettre à jour**, puis choisissez une nouvelle **référence SKU** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure][update-registry-sku]

Si vous avez un registre Classique, vous ne pouvez pas sélectionner une référence SKU managée dans le portail Azure. Au lieu de cela, vous devez d’abord [mise à niveau] [ container-registry-upgrade] vers un Registre managé.

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
