---
title: Préférence de routage non mesuré dans Azure
description: Découvrez comment vous pouvez configurer la préférence de routage de vos ressources sortant des données vers le fournisseur CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678422"
---
# <a name="what-is-routing-preference-unmetered"></a>Qu’est-ce qu’une préférence de routage non mesuré ?

La préférence de routage non mesuré est disponible pour les fournisseurs de réseau de distribution de contenu (CDN) dont les clients ont hébergé leur contenu d’origine dans Azure. Le service permet aux fournisseurs CDN d’établir une connexion de peering directe avec les routeurs Microsoft Global Network Edge à différents emplacements.

![Préférence de routage non mesuré](media/routing-preference-unmetered/unmetered.png)

Le trafic réseau sortant de l’origine dans Azure, et destiné au fournisseur CDN, bénéficie de la connectivité directe.
* Le transfert de données du trafic sortant de vos ressources Azure qui sont routées via ces liens directs n’est pas facturé.
* La connexion directe entre le fournisseur CDN et l’origine dans Azure offre des performances optimales, car il n’y a pas de sauts entre les deux. La charge de travail CDN qui extrait fréquemment des données de l’origine en tire profit.

## <a name="configuring-routing-preference-unmetered"></a>Configuration de la préférence de routage non mesuré

Pour tirer parti de la préférence de routage non mesuré, vos fournisseurs CDN doivent faire partie de ce programme. Si votre fournisseur CDN ne fait pas partie du programme, contactez-le.

Ensuite, configurez la préférence de routage pour vos ressources et définissez le type de préférence de routage sur **Internet**. Vous pouvez configurer la préférence de routage lors de la création d’une adresse IP publique, en associant ensuite cette IP publique à des ressources telles que des machines virtuelles, des équilibreurs de charge accessibles sur Internet, etc. [Apprenez à configurer la préférence de routage d’une adresse IP publique à l’aide du portail Azure](routing-preference-portal.md)

Vous pouvez également activer la préférence de routage de votre compte de stockage, et publier un deuxième point de terminaison à utiliser par le fournisseur CDN pour extraire des données de l’origine du stockage. Par exemple, la publication d’un point de terminaison propre à la route Internet pour le compte de stockage *StorageAccountA* publie le deuxième point de terminaison pour vos services de stockage, comme indiqué ci-dessous :

![Préférence de routage pour les comptes de stockage](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Étapes suivantes

* [Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure CLI](configure-routing-preference-virtual-machine-cli.md)
* [Configurer la préférence de routage de votre compte de stockage](/azure/storage/common/network-routing-preference)