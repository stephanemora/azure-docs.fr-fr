---
title: Paramètres Azure Security Center | Microsoft Docs
description: Configurez les paramètres d’Azure Security Center.
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
ms.openlocfilehash: fa9b6821e1e35f7631907a029a2576d5949ac6d3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339635"
---
# <a name="security-center-settings"></a>Paramètres Security Center
Cet article offre une vue d’ensemble des paramètres de Security Center.

Les paramètres suivants sont accessibles sous Stratégie de sécurité :

- **Collecte de données** : détermine le provisionnement d’agent et les paramètres de la [collecte de données](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Security policy** : détermine les contrôles supervisés et recommandés par Security Center. Vous pouvez modifier la [stratégie de sécurité](tutorial-security-policy.md) dans Security Center. Vous pouvez également utiliser [Azure Policy](tutorial-security-policy.md) pour créer de nouvelles définitions, définir des stratégies supplémentaires et affecter les stratégies dans des groupes d’administration. 
- **Notifications par e-mail** : détermine les contacts de sécurité et les paramètres de [notification par e-mail](security-center-provide-security-contact-details.md).
- **Niveau tarifaire** : définit la [sélection d’un tarif](security-center-pricing.md) gratuit ou standard. Le niveau que vous choisissez détermine les fonctionnalités de Security Center qui sont disponibles pour les ressources visées. Vous pouvez spécifier un niveau pour les abonnements, les groupes de ressources et les espaces de travail.

> [!NOTE]
> Vous pouvez définir tout cela par abonnement. Pour les espaces de travail, vous pouvez définir uniquement Collecte de données et Niveau tarifaire. Pour les groupes de ressources, vous pouvez définir uniquement Niveau tarifaire.
>


## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?
Security Center utilise le contrôle d’accès en fonction du rôle (RBAC) qui fournit des rôles intégrés susceptibles d’être assignés à des utilisateurs, des groupes et des services dans Azure. Lorsque les utilisateurs ouvrent Security Center, ils ne voient que les informations associées aux ressources auxquelles ils ont accès. Autrement dit, les utilisateurs se voient attribuer le rôle de *propriétaire*, *collaborateur* ou *lecteur*, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- **Lecteur Sécurité** : l’utilisateur ayant ce rôle a des droits d’affichage dans Security Center. Il peut afficher les suggestions, les alertes, la stratégie et l’intégrité, mais il ne peut pas effectuer de changements.
- **Administrateur Sécurité** : l’utilisateur ayant ce rôle a les mêmes droits d’affichage que le *lecteur Sécurité*, mais il peut en plus mettre à jour la stratégie de sécurité, et ignorer les alertes et les suggestions.


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert les stratégies de sécurité dans Azure Security Center. Pour plus d’informations sur Azure Security Center, consultez les articles suivants :

* [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des suggestions de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les suggestions Security Center peuvent vous aider à protéger vos ressources Azure.
* [Supervision de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Supervision des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : Découvrez comment Security Center gère et protège les données.
* [FAQ sur Azure Security Center](security-center-faq.md) : Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : tenez-vous informé au sujet de la sécurité Azure.
