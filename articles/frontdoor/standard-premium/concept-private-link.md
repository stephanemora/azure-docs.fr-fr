---
title: Sécuriser votre origine avec Private Link dans Azure Front Door Standard/Premium (préversion)
description: Cette page fournit des informations sur la sécurisation de la connectivité à votre origine à l’aide de Private Link.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 88c000c96e632f970db075fafb13ea5eb4bbddfc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799929"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Sécuriser votre origine avec Private Link dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Consultez la [documentation Azure Front Door](../front-door-overview.md).

## <a name="overview"></a>Vue d’ensemble

[Azure Private Link](../../private-link/private-link-overview.md) vous permet d’accéder aux services Azure PaaS ainsi qu’aux services hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La référence (SKU) Azure Front Door Premium peut se connecter via le service de liaison privée. Vos applications peuvent être hébergées dans votre réseau virtuel privé ou derrière un service PaaS tel que Web App et Compte de stockage, éliminant la nécessité que votre origine soit accessible publiquement.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Architecture des points de terminaison privés de Front Door":::

Lorsque vous activez Private Link pour votre origine dans la configuration Azure Front Door Premium, Front Door crée un point de terminaison privé à partir de votre réseau privé régional Front Door. Ce point de terminaison est managé par Azure Front Door. Vous recevrez un message de demande d’approbation de point de terminaison privé Azure Front Door à votre origine. Une fois que vous avez approuvé la requête, une adresse IP privée est affectée à partir du réseau virtuel de Front Door, le trafic entre Azure Front Door et votre origine traverse la liaison privée établie avec l’infrastructure principale du réseau Azure. Le trafic entrant à votre origine est maintenant sécurisé en provenance d’Azure Front Door.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Activer un point de terminaison privé":::

> [!NOTE]
> Une fois que vous avez activé une origine Private Link et approuvé la connexion du point de terminaison privé, il faut quelques minutes pour que la connexion soit établie. Pendant ce temps, les requêtes adressées à l’origine recevront un message d’erreur d’Azure Front Door. Le message d’erreur disparaît une fois la connexion établie.

## <a name="limitations"></a>Limites

Les points de terminaison privés Azure Front Door sont disponibles dans les régions suivantes pendant la phase de préversion publique : USA Est, USA Ouest 2 et USA Centre Sud.

Pour une latence optimale, vous devez toujours choisir la région Azure la plus proche de votre origine lorsque vous choisissez d’activer le point de terminaison de liaison privée de Front Door.

Les points de terminaison privés Azure Front Door sont managés par la plateforme et sous l’abonnement d’Azure Front Door. Azure Front Door autorise les connexions de liaison privée vers le même abonnement client que celui utilisé pour créer le profil Front Door.

## <a name="next-steps"></a>Étapes suivantes

* Pour connecter Azure Front Door Premium à votre application web via un service Private Link, consultez [Se connecter à une application web à l’aide d’un point de terminaison privé](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Pour connecter Azure Front Door Premium à votre compte de stockage via un service de liaison privée, consultez [Se connecter à un compte de stockage avec un point de terminaison privé](../../private-link/tutorial-private-endpoint-storage-portal.md).
