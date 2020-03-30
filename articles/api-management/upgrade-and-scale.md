---
title: Mettre à niveau une instance du service Gestion des API Azure et la mettre à l’échelle | Microsoft Docs
description: Cette rubrique explique comment mettre à niveau et mettre à l’échelle une instance du service Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018236"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Mettre à niveau une instance du service Gestion des API Azure et la mettre à l’échelle  

Les clients peuvent mettre à une instance du service Gestion des API Azure (APIM) en ajoutant et en supprimant des unités. Une **unité** est constituée de ressources Azure dédiées et inclut une capacité de gestion de charge définie, exprimée sous la forme d’un certain nombre d’appels d’API par mois. Ce nombre ne représente pas une limite d’appel, mais plutôt une valeur de débit maximal pour permettre une planification approximative de la capacité. Le débit et la latence réels dépendent de nombreux facteurs, tels que le nombre et le taux de connexions simultanées, le type et le nombre de stratégies configurées, les tailles des requêtes et des réponses et la latence du backend.

La capacité et le prix de chaque unité dépendent du **niveau** auquel se trouve l’unité. Vous pouvez choisir entre quatre niveaux : **Développeur**, **De base**, **Standard** et **Premium**. Si vous avez besoin d’augmenter la capacité d’un service au sein d’un niveau, vous devez ajouter une unité. Si le niveau actuellement sélectionné dans votre instance APIM n’autorise pas l’ajout d’unités supplémentaires, vous devez effectuer la mise à niveau vers une couche de niveau supérieur.

Le prix de chaque unité et les fonctionnalités disponibles (par exemple, le déploiement de plusieurs régions) dépendent de la couche que vous avez choisie pour votre instance APIM. L’article relatif aux [informations de tarification](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explique le prix unitaire et les fonctionnalités que vous obtenez dans chaque couche. 

>[!NOTE]
>L’article relatif aux [informations de tarification](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) indique la valeur de capacité unitaire approximative dans chaque couche. Pour obtenir des chiffres plus précis, vous devez consulter un scénario réaliste pour vos API. Consultez l’article [Capacité d’une instance du service Gestion des API Azure](api-management-capacity.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre les étapes décrites dans cet article, vous devez :

+ Avoir un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Avoir une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

+ Comprendre le concept de [Capacité d’une instance du service Gestion des API Azure](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Mise à niveau et mise à l’échelle  

Vous pouvez choisir entre quatre niveaux : **Développeur**, **De base**, **Standard** et **Premium**. Le niveau **Développeur** doit être utilisé pour évaluer le service ; il ne doit pas être utilisé pour un environnement de production. Le niveau **Développeur** n’est associé à aucun SLA, et vous ne pouvez pas le mettre à l’échelle (ajouter/supprimer des unités). 

Les niveaux **De base**, **Standard** et **Premium** sont des niveaux de production, associés à des contrats SLA. Vous pouvez les mettre à l’échelle. Le niveau **De base** est, parmi les niveaux assortis d’un contrat SLA, le plus abordable : il peut monter en puissance jusqu’à 2 unités, tandis que le niveau **Standard** peut évoluer jusqu’à quatre unités. Vous pouvez ajouter n’importe quel nombre d’unités au niveau **Premium**.

Le niveau **Premium** vous permet de distribuer une seule instance du service Gestion des API Azure sur n’importe quel nombre de régions Azure. Quand vous créez un service Gestion des API Azure, l’instance contient une seule unité et se trouve dans une seule région Azure. La région initiale est désignée comme la région **principale**. D’autres régions peuvent être facilement ajoutées. Lorsque vous ajoutez une région, vous spécifiez le nombre d’unités que vous souhaitez allouer. Par exemple, vous pouvez avoir une unité dans la région **principale** et cinq unités, dans une autre région. Vous pouvez adapter le nombre d’unités au trafic dans chaque région. Pour en savoir plus, découvrez [comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md).

Vous pouvez effectuer la mise à niveau et la rétrogradation vers n’importe quel niveau. Notez que la mise à niveau et le passage à une version antérieure sont susceptibles de supprimer certaines fonctionnalités, par exemple, les réseaux virtuels ou les déploiements sur plusieurs régions lors du passage du niveau Premium au niveau Standard ou au niveau De base.

> [!NOTE]
> Le processus de mise à niveau ou de mise à l’échelle peut durer entre 15 et 45 minutes. Vous recevez une notification lorsque le processus est terminé.

> [!NOTE]
> Le service Gestion des API dans le niveau **Consommation** est automatiquement mis à l’échelle en fonction du trafic.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Utiliser le portail Azure pour effectuer la mise à niveau et la mise à l’échelle

![Mettre à l’échelle une instance APIM dans le Portail Azure](./media/upgrade-and-scale/portal-scale.png)

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Dans le menu, sélectionnez **Échelle et tarification**.
3. Sélectionnez le niveau souhaité.
4. Spécifiez le nombre d’**unités** à ajouter. Vous pouvez utiliser le curseur ou saisir le nombre d’unités.  
    Si vous choisissez le niveau **Premium**, vous devez d’abord sélectionner une région.
5. Appuyez sur **Enregistrer**.

## <a name="downtime-during-scaling-up-and-down"></a>Temps d’arrêt pendant la montée et la descente en puissance
Si vous effectuez une mise à l’échelle à partir de ou vers le niveau développeur, il y aura des temps d’arrêt. Dans le cas contraire, il n’y a aucun temps d’arrêt. 


## <a name="next-steps"></a>Étapes suivantes

- [Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md)
- [Mettre à l’échelle automatiquement une instance du service Gestion des API Azure](api-management-howto-autoscale.md)
