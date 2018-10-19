---
title: Paramètres de stratégie de sécurité Azure Security Center | Microsoft Docs
description: Configurez les paramètres de stratégie de sécurité Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ab8a289ea0de263871b76788514052c09a6bf4da
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295735"
---
# <a name="security-policy-settings"></a>Paramètres de la politique de sécurité
Cet article offre une vue d’ensemble des stratégies de stratégie de sécurité dans Security Center.

## <a name="what-are-security-policies"></a>Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Azure Security Center, vous pouvez définir les stratégies de vos abonnements Azure, et les adapter à votre type de charge de travail ou à la sensibilité de vos données. Par exemple, les applications qui utilisent des données réglementées, telles que les informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé que d’autres charges de travail.

Vous pouvez définir ce qui suit sous Stratégie de sécurité :

- **Collecte de données**, pour déterminer le provisionnement d’agent et les paramètres de la [collecte de données](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Stratégie de sécurité**, pour déterminer quels contrôles Security Center surveille et recommande. Vous pouvez modifier la [stratégie de sécurité](security-center-policies.md) dans Security Center. Vous pouvez également utiliser [Azure Policy](security-center-azure-policy.md) pour créer de nouvelles définitions, définir des stratégies supplémentaires et affecter les stratégies dans des groupes d’administration. 
- **Notifications par e-mail**, pour déterminer des contacts de sécurité et des paramètres de [notification par e-mail](security-center-provide-security-contact-details.md).
- **Niveau tarifaire**, pour définir la [sélection d’un tarif](security-center-pricing.md) gratuit ou standard. Le niveau que vous choisissez détermine les fonctionnalités de Security Center qui sont disponibles pour les ressources visées. Vous pouvez spécifier un niveau pour les abonnements, les groupes de ressources et les espaces de travail.

> [!NOTE]
> Vous pouvez définir tout cela par abonnement. Pour les espaces de travail, vous pouvez définir uniquement Collecte de données et Niveau tarifaire. Pour les groupes de ressources, vous pouvez définir uniquement Niveau tarifaire.
>


## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?
Security Center utilise le contrôle d’accès en fonction du rôle (RBAC) qui fournit des rôles intégrés susceptibles d’être assignés à des utilisateurs, des groupes et des services dans Azure. Lorsque les utilisateurs ouvrent Security Center, ils ne voient que les informations associées aux ressources auxquelles ils ont accès. Autrement dit, les utilisateurs se voient attribuer le rôle de *propriétaire*, *collaborateur* ou *lecteur*, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- **Lecteur Sécurité**, pour bénéficier des droits d’affichage dans Security Center, ce qui inclut les recommandations, les alertes, la stratégie et l’intégrité, mais sans pouvoir effectuer de modification.
- **Administration de sécurité**, pour bénéficier des mêmes droits d’affichage que le rôle *Lecteur Sécurité*, tout en étant en mesure de mettre à jour la stratégie de sécurité et d’ignorer les alertes et les recommandations.


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert les stratégies de sécurité dans Azure Security Center. Pour plus d’informations sur Azure Security Center, consultez les articles suivants :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez de quelle façon les recommandations Security Center peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment Security Center gère et protège les données.
* [FAQ Azure Security Center](security-center-faq.md) : obtenez des réponses aux questions concernant l’utilisation de ce service.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) : tenez-vous informé au sujet de la sécurité Azure.
