---
title: Prise en charge intégrée d’Azure Policy dans Media Services
description: Cet article décrit la prise en charge intégrée d’Azure Policy pour les scénarios Azure Media Services.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527866"
---
# <a name="azure-policy-for-media-services"></a>Azure Policy pour Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services fournit plusieurs définitions [Azure Policy](../../governance/policy/overview.md) intégrées pour aider à appliquer les normes organisationnelles et la conformité à l’échelle.
Les cas d’usage courants pour Azure Policy incluent la mise en œuvre de la gouvernance pour la cohérence des ressources, la conformité réglementaire, la sécurité, le coût et la gestion.

Media Services fournit plusieurs définitions de cas d’usage courantes pour Azure Policy pour vous aider à démarrer.

## <a name="built-in-azure-policy-definitions-for-media-services"></a>Définitions Azure Policy intégrées pour Media Services

Plusieurs définitions de cas d’usage de stratégie intégrée peuvent être utilisées avec Media Services pour vous aider à démarrer et vous permettre de définir vos propres stratégies personnalisées.

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

La [liste des définitions de stratégie intégrées pour Media Services](../../governance/policy/samples/built-in-policies.md#media-services) fournit les dernières définitions, et lie les définitions de code et la façon d’y accéder dans le portail.

## <a name="common-scenarios-that-require-azure-policy"></a>Scénarios courants nécessitant Azure Policy

* Si la sécurité de votre entreprise vous oblige à vous assurer que tous les comptes Media Services sont créés avec des liens privés, vous pouvez utiliser une définition de stratégie pour vous assurer que les comptes sont créés uniquement avec l’API 2020-05-01 (ou version ultérieure) pour désactiver l’accès à l’API REST héritée v2 et accéder à la fonctionnalité Private Link.
* Si vous souhaitez appliquer des options spécifiques sur les jetons utilisés pour les stratégies de clés de contenu, une définition Azure Policy peut être conçue pour prendre en charge les exigences spécifiques.
* Si vos objectifs de sécurité vous obligent à limiter une source d’entrée de travail de façon à ce qu’elle provienne uniquement de vos comptes de stockage approuvés, et à limiter l’accès aux entrées HTTP(S) externes via l’utilisation de JobInputHttp, une stratégie Azure peut être conçue pour limiter le modèle d’URI d’entrée.

## <a name="example-policy-definitions"></a>Exemples de définitions de stratégie

Azure Media Services gère et publie un ensemble d’exemples de définitions d’Azure Policy dans le hub Git.
Consultez les [définitions de stratégie intégrées pour les exemples de Media Services](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services) dans le référentiel de hub Git azure-policy.

Pour plus d’informations, consultez les articles suivants :

- [Présentation d’Azure Policy](../../governance/policy/overview.md)
- [Démarrage rapide : créer une stratégie dans le portail](../../governance/policy/assign-policy-portal.md)
- [Liste des définitions de stratégie intégrées pour Media Services](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>Étapes suivantes

- [Développement avec des API Media Services v3](media-services-apis-overview.md)
- [Contrôle d’accès en fonction du rôle dans Media Services](security-rbac-concept.md)
- [Guide pratique des liens privés avec Media Services](security-private-link-how-to.md)