---
title: Migrer des ressources média Azure depuis Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources média Azure depuis Azure Germany vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 9c1426391edb42ecc3e74ee84649bb69196c45dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033657"
---
# <a name="migrate-media-resources-to-global-azure"></a>Migrer des ressources média Azure vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources média Azure depuis Azure Germany vers Azure global.

## <a name="media-services"></a>Media Services

Dans Azure Media Services, vous configurez votre propre compte de stockage et tous les éléments multimédias. Créez tout d’abord un compte Media Services dans Azure global. Recharge ensuite les artefacts média et effectuez l’encodage et la diffusion sur le nouveau compte Media Services.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Media Service](https://docs.microsoft.com/azure/media-services/#step-by-step-tutorials).
- Consultez la [Vue d’ensemble d’Azure Media Services](../media-services/previous/media-services-overview.md).
- Découvrez [comment créer un compte Media Services](../media-services/previous/media-services-portal-create-account.md).

## <a name="media-player"></a>Lecteur multimédia

Vous pouvez sélectionner plusieurs points de terminaison dans le lecteur multimédia Azure. Vous pouvez diffuser votre contenu depuis les points de terminaison Azure Germany ou Azure global.

Pour plus d’informations, consultez [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
