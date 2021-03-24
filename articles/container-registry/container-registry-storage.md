---
title: Stockage des images conteneur
description: Plus d’informations sur la façon dont vos images de conteneur et autres artefacts sont stockés dans Azure Container Registry, y compris la sécurité, la redondance et la capacité.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183265"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Stockage des images conteneur dans Azure Container Registry

Chaque registre de conteneurs Azure [De base, Standard et Premium](container-registry-skus.md) bénéficie de fonctionnalités de stockage Azure avancées, comme le chiffrement au repos. Les sections suivantes décrivent les fonctionnalités et les limites du stockage des images dans Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Chiffrement des données au repos

Toutes les images conteneur et les autres artefacts dans votre registre sont chiffrés au repos. Azure chiffre automatiquement une image avant de la stocker et la déchiffre à la volée lorsque vous ou vos applications et services extraient l’image. Vous pouvez également appliquer une couche de chiffrement supplémentaire avec une [clé managée par le client](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Stockage régional

Azure Container Registry stocke les données dans la région où le registre est créé, afin d’aider les clients à répondre aux exigences de conformité et de résidence des données.

Pour vous protéger contre les pannes de centre de données, certaines régions offrent une [redondance de zone](zone-redundancy.md), où les données sont répliquées sur plusieurs centres de données dans une région particulière.

Les clients qui souhaitent disposer de leurs données stockées dans plusieurs régions pour obtenir de meilleures performances sur différentes zones géographiques ou qui souhaitent disposer d’une résilience en cas de panne régionale devraient activer la [géo-réplication](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Géoréplication

Pour les scénarios nécessitant une assurance de plus haute disponibilité, envisagez d’utiliser la fonctionnalité de [géo-réplication](container-registry-geo-replication.md) des registres Premium. La géo-réplication permet de se prémunir contre la perte d’accès à votre registre en cas d’échec régional. La géo-réplication offre également d’autres avantages, comme le stockage des images proches du réseau pour les envois et extraits les plus rapides dans des scénarios de développement ou de déploiement distribués.

## <a name="zone-redundancy"></a>Redondance de zone

Pour créer un registre de conteneurs Azure résilient et à haute disponibilité, vous pouvez éventuellement activer la [redondance des zones](zone-redundancy.md) dans Sélectionner des régions Azure. La redondance de zone, fonctionnalité du niveau de service Premium, utilise les [zones de disponibilité](../availability-zones/az-overview.md) Azure pour répliquer votre registre à un minimum de trois zones distinctes dans chaque région activée. Combinez la géo-réplication et la redondance de zone pour améliorer la fiabilité et les performances d’un registre. 

## <a name="scalable-storage"></a>Stockage évolutif

Azure Container Registry vous permet de créer autant de référentiels, d’images, de couches ou de balises que nécessaire, jusqu’à la [limite de stockage du Registre](container-registry-skus.md#service-tier-features-and-limits). 

Un très grand nombre de référentiels et de balises peuvent affecter les performances de votre registre. Supprimez régulièrement les référentiels, balises et images non utilisés dans le cadre de la routine de maintenance de votre registre, et définissez éventuellement une [stratégie de rétention](container-registry-retention-policy.md) pour les manifestes sans balise. Les ressources de registre supprimées telles que les référentiels, les images et les balises ne *peuvent pas* être récupérées après suppression. Pour plus d’informations sur la suppression de ressources du Registre, voir [Supprimer des images conteneur dans Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Coût de stockage

Pour plus d’informations sur la tarification, consultez [Tarification d’Azure Container Registry][pricing].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les registres de base, standard et Premium, consultez [Niveaux de service Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
