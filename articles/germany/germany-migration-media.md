---
title: Migrer des ressources média Azure depuis Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources média Azure depuis Azure Germany vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: ac7cd5ca55525c11d2b985d931be24d1780899fb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122523963"
---
# <a name="migrate-media-resources-to-global-azure"></a>Migrer des ressources média Azure vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des ressources média Azure depuis Azure Germany vers Azure global.

## <a name="media-services"></a>Media Services

Dans Azure Media Services, vous configurez votre propre compte de stockage et tous les éléments multimédias. Créez tout d’abord un compte Media Services dans Azure global. Recharge ensuite les artefacts média et effectuez l’encodage et la diffusion sur le nouveau compte Media Services.

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Media Service](../media-services/previous/index.yml).
- Consultez la [Vue d’ensemble d’Azure Media Services](../media-services/previous/media-services-overview.md).
- Découvrez [comment créer un compte Media Services](../media-services/previous/media-services-portal-create-account.md).

## <a name="media-player"></a>Lecteur multimédia

Vous pouvez sélectionner plusieurs points de terminaison dans le lecteur multimédia Azure. Vous pouvez diffuser votre contenu depuis les points de terminaison Azure Germany ou Azure global.

Pour plus d’informations, consultez [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)