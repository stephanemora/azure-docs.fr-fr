---
title: Comprendre les stratégies, les initiatives et les recommandations de sécurité dans Azure Security Center
description: Découvrez les stratégies, les initiatives et les recommandations de sécurité dans Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738969"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Que sont les stratégies, les initiatives et les recommandations de sécurité ?

Security Center applique des initiatives de sécurité à vos abonnements. Ces initiatives contiennent une ou plusieurs stratégies de sécurité. Chacune de ces stratégies entraîne une recommandation de sécurité pour améliorer votre position de sécurité. Cette page explique en détail chacun de ces concepts.


## <a name="what-is-a-security-policy"></a>Qu’est-ce qu’une stratégie de sécurité ?

Une définition de stratégie Azure, créée dans Azure Policy, est une règle relative à des conditions de sécurité spécifiques que vous souhaitez contrôler. Les définitions intégrées incluent des éléments tels que le contrôle du type de ressources qui peut être déployé ou le respect des balises sur toutes les ressources. Vous pouvez également créer vos propres définitions de stratégies personnalisées.

Pour implémenter ces définitions de stratégie (définitions intégrées et personnalisées), vous devez les attribuer. Vous pouvez affecter ces stratégies via le portail Azure, PowerShell ou Azure CLI.

Il existe différents types de stratégies dans Azure Policy. Security Center utilise principalement des stratégies « d’audit » qui vérifient les conditions et configurations spécifiques, puis rendent compte de la conformité. Il existe également des stratégies de « respect » qui peuvent être utilisées pour appliquer des paramètres sécurisés.

## <a name="what-is-a-security-initiative"></a>Qu’est-ce qu’une initiative de sécurité ?

Une initiative Azure est une collection de définitions ou de règles de stratégie Azure regroupées pour atteindre un objectif. Les initiatives Azure simplifient la gestion de vos stratégies en regroupant un ensemble de stratégies, de manière logique, en tant qu’élément unique.

Une initiative de sécurité définit la configuration souhaitée de vos charges de travail, et vous permet de vous assurer que vous êtes en conformité avec les exigences de sécurité de votre organisation ou des régulateurs.

À l’instar des stratégies de sécurité, les initiatives de Security Center sont également créées dans Azure Policy. Vous pouvez utiliser [Azure Policy](../governance/policy/overview.md) pour gérer vos stratégies, créer des initiatives et attribuer des initiatives à plusieurs abonnements ou à des groupes d’administration complets.

L’initiative par défaut attribuée automatiquement à chaque abonnement dans Azure Security Center est Azure Security Benchmark. Ce benchmark de sécurité Azure constitue l’ensemble des directives propres à Azure et créées par Microsoft, qui contient les bonnes pratiques de sécurité et de conformité s’inscrivant dans les cadres de conformité courants. Ce point de référence, largement respecté et centré sur le cloud, est basé sur les contrôles du [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) et du [National Institute of Standards and Technology (NIST)](https://www.nist.gov/). Découvrez le [Benchmark de sécurité Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).

Security Center offre les options suivantes pour l’utilisation des initiatives de sécurité :

- **Afficher et modifier l’initiative par défaut intégrée** : lorsque vous activez Security Center, l’initiative nommée « Benchmark de sécurité Azure » est automatiquement attribuée à tous les abonnements inscrits auprès de Security Center. Pour personnaliser cette initiative, vous pouvez activer ou désactiver des stratégies individuelles au sein des paramètres de stratégies. Pour comprendre les options disponibles prêtes à l’emploi, consultez la liste des [stratégies de sécurité intégrées](./policy-reference.md).

- **Ajouter vos propres initiatives personnalisées** : si vous souhaitez personnaliser les initiatives de sécurité appliquées à votre abonnement, vous pouvez le faire dans Security Center. Vous recevez ensuite des recommandations si vos machines ne suivent pas les stratégies que vous créez. Pour obtenir des instructions sur la création et l’attribution de stratégies personnalisées, consultez [Utilisation de stratégies et d’initiatives de sécurité personnalisées](custom-security-policies.md).

- **Ajouter des initiayives de conformité réglementaire** : le tableau de bord de conformité réglementaire du Security Center affiche l’état de toutes les évaluations au sein de votre environnement dans le contexte d’une norme ou d’une réglementation particulières (par exemple, Azure CIS, NIST SP 800-53 R4, CSP CSCF-v2020). Pour plus d’informations, voir [Améliorer votre conformité aux normes](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Qu’est-ce qu’une recommandation de sécurité ?

À l’aide des stratégies, Security Center analyse régulièrement l’état de conformité de vos ressources pour identifier les problèmes de configuration de sécurité potentiels et les faiblesses. Il fournit ensuite des recommandations sur la façon de résoudre ces problèmes. Les recommandations sont le résultat de l’évaluation de vos ressources par rapport aux stratégies pertinentes et de l’identification des ressources qui ne répondent pas à vos exigences définies.

Security Center émet ses recommandations de sécurité en fonction des initiatives que vous avez choisies. Lorsqu’une stratégie de votre initiative est comparée à vos ressources et en trouve une ou plusieurs non conformes, elle est présentée comme recommandation dans Security Center.

Les recommandations sont des actions à effectuer pour sécuriser et renforcer vos ressources. Chaque recommandation vous fournit les informations suivantes :

- Courte description du problème
- Étapes de correction à effectuer pour implémenter la recommandation
- Ressources concernées

Dans la pratique, cela fonctionne comme suit :

1. Azure Security Benchmark est une ***initiative***
1. Elle comprend une ***stratégie*** qui oblige tous les comptes de stockage Azure à limiter l’accès au réseau, afin de réduire la surface d’attaque. Cette stratégie est appelée « les comptes de stockage doivent restreindre l’accès au réseau à l’aide de règles de réseau virtuel ». Elle peut être désactivée ou activée à partir d’Azure Policy
1. Si Azure Security Center trouve un compte de stockage Azure sur l’un de vos abonnements protégés, il évalue ces comptes pour déterminer s’ils sont protégés par des règles de réseau virtuel. Si ce n’est pas le cas, il affiche une ***recommandation*** pour corriger cette situation et renforcer la sécurité de ces ressources. 

Par conséquent, une initiative (1) comprend des stratégies (2) qui génèrent des recommandations le cas échéant (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Affichage de la relation entre une recommandation et une stratégie

Comme indiqué ci-dessus, les recommandations intégrées de Security Center sont basées sur Azure Security Benchmark. Presque toutes les recommandations ont une stratégie sous-jacente qui est dérivée d’une exigence dans le point de référence.

Lorsque vous examinez les détails d’une recommandation, il est souvent utile de pouvoir consulter la stratégie sous-jacente. Pour chaque recommandation soutenue par une stratégie, utilisez le lien **Afficher la définition de stratégie** à partir de la page des détails de la recommandation pour accéder directement à l’entrée d’Azure Policy pour la stratégie correspondante :

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Lien vers la page Azure Policy de la stratégie spécifique prenant en charge une recommandation":::

Utilisez ce lien pour afficher la définition de stratégie et passer en revue la logique d’évaluation. 

Si vous examinez la liste des recommandations de notre [Guide de référence des recommandations de sécurité](recommendations-reference.md), vous remarquerez également des liens vers les pages de définition de stratégie :

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accès à la page Azure Policy d’une stratégie particulière, directement à partir de la page de référence des recommandations d’Azure Security Center":::


## <a name="next-steps"></a>Étapes suivantes

Cette page a expliqué, à un niveau élevé, les concepts de base et les relations entre les stratégies, les initiatives et les recommandations. Pour plus d’informations, consultez :

- [Créer des initiatives personnalisées](custom-security-policies.md)
- [Désactiver des stratégies de sécurité pour désactiver des recommandations](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Découvrez comment modifier une stratégie de sécurité dans Azure Policy](../governance/policy/tutorials/create-and-manage.md)