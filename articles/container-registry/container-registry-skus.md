---
title: Niveaux de service et fonctionnalités du registre
description: Découvrez les fonctionnalités et les limites (quotas) des niveaux de service (SKU) De base, Standard et Premium d’Azure Container Registry.
ms.topic: article
ms.date: 08/12/2021
ms.openlocfilehash: 7f9fe5d461dede4510d3fc8069f42e7950803984
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582330"
---
# <a name="azure-container-registry-service-tiers"></a>Niveaux de service Azure Container Registry

Azure Container Registry est disponible dans plusieurs niveaux de service (également appelés références SKU). Ces niveaux offrent une tarification prévisible et plusieurs options pour l’alignement en fonction de la capacité et des modèles d’utilisation de votre registre Docker privé dans Azure.

| Niveau | Description |
| --- | ----------- |
| **De base** | Point d’entrée au coût optimisé pour les développeurs apprenant Azure Container Registry. Les registres De base ont les mêmes fonctionnalités de programmation que les registres Standard et Premium (telle que l’[intégration de l’authentification](container-registry-authentication.md#individual-login-with-azure-ad) Azure Active Directory, la [suppression d’image][container-registry-delete] et les [webhooks][container-registry-webhook]). Toutefois, le stockage inclus et le débit d’image sont parfaitement appropriés pour des scénarios d’utilisation inférieure. |
| **Standard** | Les registres Standard offrent les mêmes fonctionnalités que la version De base, avec un stockage inclus et un débit d’image accrus. Les registres Standard devraient satisfaire les besoins de la plupart des scénarios de production. |
| **Premium** | Les registres Premium fournissent la quantité la plus élevée de stockage inclus et d’opérations simultanées, permettant des scénarios impliquant des volumes importants. En plus d’un débit d’image plus élevé, le niveau Premium ajoute des fonctionnalités comme la [géoréplication][container-registry-geo-replication] pour gérer un seul registre dans plusieurs régions, [l’approbation de contenu](container-registry-content-trust.md) pour la signature d’étiquettes d’image et la [liaison privée avec points de terminaison privés](container-registry-private-link.md) pour restreindre l’accès au registre. |

Les niveaux De base, Standard et Premium fournissent toutes les mêmes fonctionnalités de programmation. Elles bénéficient également d’un [stockage des images][container-registry-storage] complètement managé par Azure. Choisir un niveau supérieur permet d’obtenir de meilleures performances et une meilleure mise à l’échelle. Avec plusieurs niveaux de service, vous pouvez commencer avec un niveau De base, puis passer aux niveaux Standard et Premium au fur et à mesure que votre utilisation du registre augmente.

## <a name="service-tier-features-and-limits"></a>Fonctionnalités et limites de niveau de service

Le tableau suivant détaille les fonctionnalités et les limites de registre des niveaux de service De base, Standard et Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>Débit et limitation du registre

### <a name="throughput"></a>Débit 

Quand vous générez un taux élevé d’opérations de registre, utilisez les limites d’opérations de lecture et écriture et de bande passante du niveau de service comme guide pour le débit maximal attendu. Ces limites affectent les opérations de plan de données, y compris la génération de liste, la suppression, l’envoi (push) et le tirage (pull) d’images et d’autres artefacts.

Pour estimer le débit spécifique de tirages et d’envois d’images, tenez compte des limites du registre et de ces facteurs : 

* Nombre et taille des couches d’images
* Réutilisation des couches ou des images de base entre les images
* Appels d’API supplémentaires qui peuvent être nécessaires pour chaque tirage ou envoi

Pour obtenir des informations détaillées, consultez la documentation relative à l’[API Docker HTTP V2](https://docs.docker.com/registry/spec/api/).

Pour évaluer le débit du registre ou résoudre les problèmes correspondants, tenez compte également de la configuration de votre environnement client :

* La configuration de votre démon Docker pour les opérations simultanées
* Votre connexion réseau au point de terminaison (ou aux points de terminaison si votre registre est [géorépliqué](container-registry-geo-replication.md)) des données du registre.

Si vous rencontrez des problèmes de débit avec votre registre, consultez [Résoudre les problèmes de performances de registre](container-registry-troubleshoot-performance.md). 

#### <a name="example"></a>Exemple

L’envoi d’une seule image `nginx:latest` de 133 Mo sur un registre de conteneurs Azure nécessite plusieurs opérations de lecture et d’écriture pour les cinq couches de l’image : 

* Opérations de lecture pour lire le manifeste de l’image s’il existe dans le registre
* Opérations d’écriture pour écrire le blob de configuration de l’image
* Opérations d’écriture pour écrire le manifeste de l’image

### <a name="throttling"></a>Limitation

Vous pouvez être confronté à une limitation des opérations de tirage et d’envoi quand le registre détermine que le taux de requêtes dépasse les limites autorisées pour le niveau de service du registre. Vous pouvez alors voir une erreur HTTP 429 de type `Too many requests`.

La limitation peut se produire temporairement quand vous générez une rafale d’opérations de tirage et d’envoi d’images sur une période très brève, même si le taux moyen d’opérations de lecture et d’écriture est compris dans les limites du registre. Vous devrez peut-être implémenter une logique de nouvelle tentative avec un backoff dans votre code ou réduire le taux maximal de requêtes adressées au registre.

## <a name="show-registry-usage"></a>Afficher l'utilisation du registre

Utilisez la commande [AZ ACR Show-usage](/cli/az/acr#az_acr_show_usage) ou l’API REST [liste des utilisations](/rest/api/containerregstry/registries/list-usages) pour obtenir un instantané de la consommation actuelle du stockage et d’autres ressources du Registre, en comparaison avec les limites du niveau de service de ce registre. L’utilisation du stockage s’affiche également sur la page **Vue d’ensemble** du registre dans le portail.

Les informations d’utilisation vous aident à prendre des décisions sur [la modification du niveau de service](#changing-tiers) lorsque votre registre est proche d’une limite. Ces informations vous permettent également de [gérer la consommation](container-registry-best-practices.md#manage-registry-size). 

> [!NOTE]
> L'utilisation du stockage du registre ne doit être utilisée qu'à titre indicatif et peut ne pas refléter les opérations récentes du registre. Surveillez la [mesure StorageUsed](monitor-service-reference.md#container-registry-metrics) du Registre pour obtenir des données à jour. 

En fonction du niveau de service de votre registre, les informations sur l'utilisation comprennent tout ou partie des éléments suivants, ainsi que la limite de ce niveau :

* Stockage consommé en octets<sup>1</sup>
* Nombre de [webhooks](container-registry-webhook.md)
* Nombre de [géo-réplications](container-registry-geo-replication.md) (y compris le réplica de démarrage)
* Nombre de [points de terminaison privés](container-registry-private-link.md)
* Nombre de [règles d’accès IP](container-registry-access-selected-networks.md)
* Nombre de [liaisons de réseau virtuel](container-registry-vnet.md)

<sup>1</sup>Dans un registre géo-répliqué, l’utilisation du stockage est indiquée pour la région d’hébergement. Multiplier par le nombre de réplications pour le stockage de registre total consommé.

## <a name="changing-tiers"></a>Changer de niveau

Vous pouvez changer le niveau de service d’un registre via l’interface de ligne de commande Azure ou dans le Portail Azure. Vous pouvez changer librement de niveau, tant que le nouveau niveau possède la capacité de stockage maximale requise. 

Il n’y a pas de temps d’arrêt du registre ni d’impact sur les opérations de registre lorsque vous passez d’un niveau de service à un autre.

### <a name="azure-cli"></a>Azure CLI

Pour changer de niveau de service dans Azure CLI, utilisez la commande [az acr update][az-acr-update]. Par exemple, pour basculer vers Premium :

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portail Azure

Dans la **vue d’ensemble** du registre de conteneur dans le portail Azure, sélectionnez **Mettre à jour**, puis choisissez une nouvelle **référence SKU** dans la liste déroulante.

![Mettre à jour la référence de registre de conteneurs dans le portail Azure][update-registry-sku]

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur la tarification de chacun des niveaux de service Azure Container Registry, voir [Tarification Container Registry][container-registry-pricing].

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
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
