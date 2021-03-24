---
title: 'Azure Front Door : Endpoint Manager'
description: Cet article fournit une vue d’ensemble d’Azure Front Door Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097808"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Qu’est-ce qu’Azure Front Door Standard/Premium (préversion) Endpoint Manager ?

> [!NOTE]
> * Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Consultez la [documentation Azure Front Door](../front-door-overview.md).

Endpoint Manager fournit une vue d’ensemble des points de terminaison que vous avez configurés pour Azure Front Door. Un point de terminaison est un regroupement logique de domaines et de leurs configurations associées. Endpoint Manager vous aide à gérer votre collection de points de terminaison pour les opérations CRUD (création, lecture, mise à jour et suppression). Vous pouvez gérer les éléments suivants pour vos points de terminaison par le biais d’Endpoint Manager :

* Domaines
* Groupes d’origines
* Itinéraires
* Sécurité

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Capture d’écran d’Endpoint Manager sans configurations" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Endpoint Manager liste le nombre d’instances de chaque élément qui sont créées dans un point de terminaison. L’état de l’association pour chaque élément est également affiché. Par exemple, vous pouvez créer plusieurs domaines et groupes d’origines et attribuer l’association entre eux avec différentes routes.

> [!IMPORTANT]
> * Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Vue liée

Avec la vue liée dans Endpoint Manager, vous pouvez facilement identifier l’association entre les éléments Azure Front Door, par exemple :

* Quels sont les domaines associés au point de terminaison actuel ?
* Quel groupe d’origines est associé à quel domaine ?
* Quelle stratégie WAF est associée à quel domaine ?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Capture d’écran d’Endpoint Manager avec des configurations" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
