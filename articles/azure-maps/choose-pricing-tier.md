---
title: Choisir le bon niveau tarifaire pour Microsoft Azure Maps
description: En savoir plus sur les niveaux tarifaires de Azure Maps. Découvrez les fonctionnalités proposées aux niveaux et les principales considérations à prendre en compte pour le choix d’un niveau tarifaire.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 59ed6dea79e1ae827c35bc7ccb60fe1bb7373778
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110794394"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Choix du bon niveau tarifaire dans Azure Maps

Azure Maps propose maintenant deux niveaux tarifaires : Gen1 et Gen2. Le nouveau niveau tarifaire Gen2 contient toutes les fonctionnalités d’Azure Maps sans aucune restriction de RPS (requêtes par seconde). Il vous permet de réaliser des économies en cas d’augmentation du volume de transactions Azure Maps. Cet article a pour objectif de vous aider à choisir le niveau tarifaire qui répond le mieux à vos besoins.

## <a name="pricing-tier-targeted-customers"></a>Niveau tarifaire et clients ciblés

Pour mieux comprendre les niveaux tarifaires Gen1 et Gen2, consultez le tableau des **clients ciblés par les niveaux tarifaires** ci-dessous.  Pour plus d’informations, consultez la page [Tarification Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). Si vous êtes actuellement client Azure Maps, découvrez [ici](how-to-manage-pricing-tier.md) comment passer du tarif Gen1 au tarif Gen2.

| Niveau tarifaire  | SKU | Clients ciblés|
|-----------------|----| -----------------|
| **Gen1** | S0          |    Le niveau tarifaire S0 convient pout toutes les phases de production des applications : du développement « preuve de concept » à la phase de test initiale, en passant par la mise en production et le déploiement. Toutefois, ce niveau est conçu pour un développement à petite échelle, des clients avec peu d’utilisateurs simultanés, ou les deux.
|        |S1           |    Le niveau tarifaire S1 convient aux clients qui ont des applications d’entreprise à grande échelle, des applications stratégiques ou un grand nombre d’utilisateurs simultanés. Il convient également aux clients qui ont besoin de services géospatiaux avancés.
| **Gen2** | Maps/Location Insights | Le tarif Gen2 est destiné aux nouveaux clients et clients actuels Azure Maps. Il s’accompagne d’un niveau mensuel gratuit de transactions à utiliser pour tester et exploiter des cartes Azure Maps. Les références SKU Maps et Location Insights contiennent toutes les fonctionnalités d’Azure Maps. En outre, ils ne sont soumis à aucune restriction RPS (requêtes par seconde). Pour la plupart des services, cela représente des économies en cas d’augmentation du volume de transactions Azure Maps.  
|     |  |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’affichage et la modification des niveaux tarifaires :

> [!div class="nextstepaction"]
> [Gérer un niveau tarifaire](how-to-manage-pricing-tier.md)
