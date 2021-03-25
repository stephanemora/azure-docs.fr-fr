---
title: Déterminer votre état de sécurité pour la collaboration externe avec Azure Active Directory
description: Avant de pouvoir exécuter un plan de sécurité d’accès externe, vous devez déterminer ce que vous essayez d’obtenir.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725439"
---
# <a name="determine-your-security-posture-for-external-access"></a>Déterminer votre posture de sécurité pour l’accès externe 

Lorsque vous envisagez d’établir un accès externe, vous devez évaluer les besoins de sécurité et de collaboration dans l’ensemble de votre organisation, et dans chaque scénario. Au niveau de l’organisation, prenez en compte la quantité de contrôle dont vous avez besoin pour que votre équipe informatique bénéficie d’une collaboration quotidienne. Les organisations dans des secteurs réglementés peuvent nécessiter davantage de contrôle informatique. Par exemple, un entrepreneur de défense peut être obligé d’identifier et de documenter de manière positive chaque utilisateur externe, son accès et la suppression de l’accès. Cette exigence peut porter sur tous les accès, ou sur des scénarios ou des charges de travail spécifiques. À l’autre extrémité du spectre, une société de conseil peut généralement permettre aux utilisateurs finaux de déterminer les utilisateurs externes avec lesquels ils doivent collaborer, avec certains garde-fous informatiques. 

![Contrôle de la collaboration par l’informatique ou par l’utilisateur final](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Un contrôle trop strict de la collaboration peut entraîner une augmentation des budgets informatiques, une réduction de la productivité et un retard dans les résultats commerciaux. Lorsque les canaux de collaboration officiels sont perçus comme trop onéreux, les utilisateurs finaux ont tendance à contourner les systèmes informatiques pour faire leur travail, par exemple en envoyant des documents non sécurisés par courrier électronique.

## <a name="think-in-terms-of-scenarios"></a>Penser en termes de scénarios

Dans de nombreux cas, les TI peuvent déléguer les accès partenaires, du moins dans certains scénarios, tout en assurant la sécurité par des garde-fous. Les garde-fous des TI permettent de garantir la sécurité de la propriété intellectuelle, tout en permettant aux employés de collaborer avec leurs partenaires pour effectuer leurs tâches.

En examinant les scénarios au sein de votre organisation, évaluez le besoin d’accès aux ressources pour les employés par rapport aux partenaires commerciaux. Une banque peut avoir des besoins de conformité qui limitent l’accès à certaines ressources, comme les informations sur les comptes utilisateurs, à un petit groupe d’employés internes. À l’inverse, la même banque peut activer l’accès délégué pour les partenaires qui travaillent sur une campagne marketing.

![continuum de gouvernance par scénario](media\secure-external-access\1-scenarios.png)

Dans chaque scénario, prenez en compte : 

* la sensibilité des informations à risque ;

* si vous devez restreindre ce que les partenaires peuvent voir sur d’autres utilisateurs ;

* le coût d’une violation par rapport au poids du contrôle centralisé et aux frictions des utilisateurs finaux.

 Vous pouvez également commencer par des contrôles gérés de manière centralisée pour atteindre les objectifs de conformité et déléguer le contrôle aux utilisateurs finaux au fil du temps. Tous les modèles d’administration des accès peuvent coexister simultanément au sein d’une organisation. 

L’utilisation d’[identifiants gérés par les partenaires](../external-identities/what-is-b2b.md) fournit à votre organisation un signal essentiel qui met fin à l’accès à vos ressources une fois que l’utilisateur externe a perdu l’accès aux ressources de sa propre entreprise.

## <a name="goals-of-securing-external-access"></a>Objectifs de la sécurisation de l’accès externe

Les objectifs de l’accès régi par informatique et de l'accès délégué diffèrent.

**Les principaux objectifs de l’accès régi par l’informatique sont les suivants :**

* Respectez les cibles de gouvernance, de réglementation et de conformité (GRC). 

* Contrôler étroitement l’accès des partenaires et ce que les partenaires peuvent voir sur les utilisateurs membres, les groupes et les autres partenaires.

**Les principaux objectifs de la délégation de l’accès sont les suivants :**

* Permettre aux chefs d’entreprise de décider avec qui ils collaborent, dans le respect des contraintes informatiques.

* Permettre aux partenaires commerciaux de demander l’accès sur la base de règles définies par les propriétaires d’entreprises.

Quel que soit celui que vous mettez en œuvre pour votre organisation et vos scénarios, vous devez : 

* **Contrôler l’accès aux applications, aux données et au contenu**. Cela peut être accompli par le biais de différentes méthodes, en fonction de vos versions d’[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) et de [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Réduire la surface d’attaque**. [Privileged identity management](../privileged-identity-management/pim-configure.md), [prévention contre la perte de données (DLP),](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) et les [fonctionnalités de chiffrement](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) réduisent la surface d’attaque.

* **Examiner régulièrement les activités et le journal d’audit pour confirmer la conformité**. Les TI peuvent déléguer les décisions d’accès aux propriétaires d’entreprises par le biais de la gestion des droits d’utilisation, tandis que les révisions d’accès permettent de confirmer périodiquement le maintien de l’accès. La classification automatisée des données avec des étiquettes de confidentialité contribue à automatiser le chiffrement des contenus sensibles, ce qui permet aux utilisateurs finaux de s’y conformer facilement.

## <a name="next-steps"></a>Étapes suivantes 

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md) (le présent article).

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)
 

