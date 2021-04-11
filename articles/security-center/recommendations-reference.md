---
title: Table de référence pour toutes les recommandations Azure Security Center
description: Cet article liste les recommandations de sécurité d’Azure Security Center qui vous permettent de durcir la sécurité et de protéger vos ressources.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 2d9f76bd3c6ad0060a90f52abba1c7378b310e67
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801460"
---
# <a name="security-recommendations---a-reference-guide"></a>Recommandations de sécurité - Guide de référence

Cet article liste les recommandations que vous pouvez voir dans Azure Security Center. Les recommandations présentées dans votre environnement dépendent des ressources que vous protégez et de votre configuration personnalisée.

Les recommandations de Security Center sont basées sur le [Benchmark de sécurité Azure](../security/benchmarks/introduction.md). Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. Ce point de référence, largement respecté et centré sur le cloud, est basé sur les contrôles du [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) et du [National Institute of Standards and Technology (NIST)](https://www.nist.gov/).

Pour en savoir plus sur la façon de répondre à ces recommandations, consultez [Appliquer les recommandations d’Azure Security Center](security-center-remediate-recommendations.md).

Votre degré de sécurisation est basé sur le nombre de recommandations Security Center que vous avez appliquées. Pour déterminer quelles recommandations doivent être suivies en premier, examinez la gravité de chacune d’entre elles et son impact potentiel sur votre degré de sécurisation.

> [!TIP]
> Si la description d’une recommandation indique « aucune stratégie associée », cela est généralement dû au fait que cette recommandation est dépendante d’une autre recommandation et de _sa_ stratégie. Par exemple, la recommandation « Les échecs d’intégrité d’Endpoint Protection doivent être corrigés... », s’appuie sur la recommandation qui vérifie si une solution Endpoint Protection est _installée_ en premier lieu (« La solution Endpoint Protection doit être installée... »). La recommandation sous-jacente _est associée_ à une stratégie.
> Limiter les stratégies à la recommandation de base simplifie la gestion des stratégies.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Recommandations AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Recommandations relatives au calcul

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Recommandations relatives aux conteneurs

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Recommandations relatives aux données

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Recommandations relatives à IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Suggestions IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Recommandations pour la mise en réseau

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Recommandations dépréciées

|Recommandation|Description et stratégie associée|severity|Correction rapide activée ? ([en savoir plus](security-center-remediate-recommendations.md#quick-fix-remediation))|Type de ressource|
|----|----|----|----|----|
|**L’accès à App Services doit être limité**|Limitez l'accès à App Services en modifiant la configuration du réseau pour empêcher le trafic entrant provenant de plages trop larges.<br>(Stratégie associée : [Préversion] : L’accès à App Services doit être limité)|Élevé|N|App Service|
|**Les règles relatives aux applications web doivent être renforcées sur les groupes de sécurité réseau IaaS**|Renforcez le groupe de sécurité réseau (NSG) des machines virtuelles qui exécutent des applications web lorsque les règles NSG sont trop permissives en ce qui concerne les ports des applications web.<br>(Stratégie associée : Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées)|Élevé|N|Machine virtuelle|
|**Des stratégies de sécurité de pod doivent être définies pour réduire le vecteur d’attaque en supprimant les privilèges d’application inutiles (préversion)**|Définissez des stratégies de sécurité de pod pour réduire le vecteur d’attaque en supprimant les privilèges d’application inutiles. Il est recommandé de configurer des stratégies de sécurité de pod afin que les pods ne puissent accéder qu’aux ressources auxquelles ils sont autorisés à accéder.<br>(Stratégie associée : [Préversion] : Des stratégies de sécurité de pods doivent être définies sur les services Kubernetes)|Moyenne|N|Ressources de calcul (conteneurs)|
|**Installer Azure Security Center pour le module de sécurité IoT afin d’obtenir une meilleure visibilité sur vos appareils IoT**|Installez Azure Security Center pour le module de sécurité IoT afin d’obtenir une meilleure visibilité sur vos appareils IoT.|Faible|N|Appareil IoT|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations, consultez les articles suivants :

- [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).
- [Consulter vos recommandations de sécurité](security-center-recommendations.md)
