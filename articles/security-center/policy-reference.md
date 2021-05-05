---
title: Définitions de stratégie intégrées pour Azure Security Center
description: Liste les définitions de stratégie intégrées d’Azure Policy pour Azure Security Center. Ces définitions de stratégie intégrées fournissent des approches courantes pour la gestion de vos ressources Azure.
ms.date: 04/28/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: fb23fb24ca0376d84cb498049d60e1859769943a
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108179036"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Définitions intégrées d’Azure Policy pour Azure Security Center

Cette page est un index des définitions de stratégie intégrées d’[Azure Policy](../governance/policy/overview.md) relatives à Azure Security Center. Les regroupements suivants de définitions de stratégie sont disponibles :

- Le groupe [Initiatives](#azure-security-center-initiatives) liste les définitions d’initiative d’Azure Policy dans la catégorie « Security Center ».
- Le groupe [Initiative par défaut](#azure-security-center-initiatives) répertorie toutes les définitions d’Azure Policy qui font partie de l’initiative par défaut de Security Center,[Benchmark de sécurité Azure](/security/benchmark/azure/introduction). Ce benchmark largement respecté créé par Microsoft s’appuie sur les contrôles du [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) et du [National Institute of Standards and Technology (NIST)](https://www.nist.gov/), et se focalise sur la sécurité du cloud.
- Le groupe [Catégorie](#azure-security-center-category) liste toutes les définitions d’Azure Policy dans la catégorie « Security Center ».

Pour plus d’informations sur les stratégies de sécurité, consultez [Utilisation des stratégies de sécurité](./tutorial-security-policy.md). Pour obtenir des éléments intégrés supplémentaires d’Azure Policy pour d’autres services, consultez [Définitions intégrées d’Azure Policy](../governance/policy/samples/built-in-policies.md).

Le nom de chaque définition de stratégie intégrée est un lien vers la définition de la stratégie dans le portail Azure. Utilisez le lien figurant dans la colonne **Version** pour voir la source dans le [dépôt GitHub Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Initiatives d’Azure Security Center

Pour plus d’informations sur les initiatives intégrées supervisées par Security Center, consultez le tableau suivant :

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Initiative par défaut de Security Center (benchmark de sécurité Azure)

Pour plus d’informations sur les stratégies intégrées supervisées par Security Center, consultez le tableau suivant :

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Catégorie d’Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les définitions de stratégies de sécurité Azure Policy dans Security Center. Pour en savoir plus sur les initiatives, les stratégies et la façon dont elles sont liées aux recommandations de Security Center, consultez [Que sont les stratégies de sécurité, les initiatives et les recommandations ?](security-policy-concept.md).
