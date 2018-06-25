---
title: Configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1c21c915bc0a83cdafb221a2cd592890577437ee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849523"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés peuvent accéder à vos ressources. Par exemple, vous pouvez limiter aux appareils gérés l’accès à certaines ressources. Une stratégie d’accès conditionnel qui exige un appareil géré est aussi appelée stratégie d’accès conditionnel basé sur les appareils.

Cette rubrique explique comment vous pouvez configurer des stratégies d’accès conditionnel basé sur les appareils pour des applications connectées à Azure AD. 


## <a name="before-you-begin"></a>Avant de commencer

L’accès conditionnel basé sur les appareils fait le lien entre l’**accès conditionnel Azure AD** et la **gestion des appareils Azure AD**. Si l’un de ces aspects vous est inconnu, consultez d’abord les rubriques suivantes :

- **[Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)** - Cette rubrique fournit une vue d’ensemble conceptuelle de l’accès conditionnel et explique la terminologie associée.

- **[Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)** - Cette rubrique vous donne une vue d’ensemble des différentes options à votre disposition pour connecter des appareils avec Azure AD. 



## <a name="managed-devices"></a>Appareils gérés  

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Pour certaines ressources de votre environnement, accorder l’accès aux utilisateurs appropriés n’est parfois pas suffisant. Outre les utilisateurs appropriés, vous pouvez également exiger que les tentatives d’accès soient uniquement effectuées à l’aide d’un appareil géré.

Un appareil géré est un appareil qui respecte vos critères de conformité et de sécurité. En termes simples, les appareils gérés sont ceux que l’organisation peut contrôler *d’une manière ou d’une autre*. Dans Azure AD, les prérequis pour un appareil géré implique son inscription auprès d’Azure AD. L’inscription d’un appareil crée une identité pour lui sous la forme d’un objet appareil. Cet objet est utilisé par Azure pour effectuer le suivi des informations d’état relatives à un appareil. En tant qu’administrateur Azure AD, vous pouvez déjà utiliser cet objet pour permuter (activer/désactiver) l’état d’un appareil.
  
![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Pour inscrire un appareil auprès d’Azure AD, vous avez trois possibilités :

- **[Appareils inscrits sur Azure AD](device-management-introduction.md#azure-ad-registered-devices)**  : pour inscrire un appareil personnel auprès d’Azure AD

- **[Appareils joints à Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  : pour inscrire auprès d’Azure AD un appareil Windows 10 professionnel qui n’est pas joint à une version locale d’AD. 

- **[Appareils joints à une version hybride d’Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  : pour inscrire auprès d’Azure AD un appareil Windows 10 joint à un AD local.

Pour devenir un appareil géré, un appareil inscrit peut être un appareil joint à une version hybride d’Azure AD ou un appareil marqué comme conforme.  

![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Exiger un appareil joint à une version hybride d’Azure AD

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Exiger un appareil joint à une version hybride d’Azure AD** pour indiquer que les applications cloud sélectionnées sont uniquement accessibles à l’aide d’un appareil géré. 

![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Ce paramètre s’applique uniquement aux appareils Windows 10 joints à une version locale d’AD. Vous pouvez uniquement inscrire ces appareils auprès d’Azure AD en utilisant une jointure à une version hybride d’Azure AD, ce qui correspond à un [processus automatisé](device-management-hybrid-azuread-joined-devices-setup.md) pour inscrire un appareil Windows 10. 

![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Qu’est-ce qui transforme un appareil joint à une version hybride d’Azure AD en appareil géré ?  Pour les appareils joints à une version locale d’Active Directory, le contrôle est supposé être appliqué à l’aide de solutions de gestion comme **System Center Configuration Manager (SCCM)** ou une **stratégie de groupe**. Étant donné qu’il n’existe aucune méthode permettant à Azure AD de déterminer si une de ces méthodes a été appliquée à un appareil, exiger un appareil joint à une version hybride d’Azure AD constitue un mécanisme relativement faible pour exiger un appareil géré. Il vous appartient en tant qu’administrateur de déterminer si les méthodes appliquées à vos appareils joints au domaine local sont suffisamment fortes pour constituer un appareil géré si ces appareils sont également joints à une version hybride d’Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Exiger que l’appareil soit marqué comme conforme

L’option *Exiger que l’appareil soit marqué comme conforme* est la façon la plus forte de demander un appareil géré.

![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Cette option exige qu’un appareil soit inscrit auprès d’Azure AD et qu’il soit marqué comme conforme par :
         
- Intune 
- Un système géré par un appareil mobile tiers qui gère les appareils Windows 10 via l’intégration d’Azure AD 
 
![Conditions basées sur l’appareil](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Pour un appareil marqué comme conforme, vous pouvez partir du principe que : 

- Les appareils mobiles que votre personnel utilise pour accéder aux données d’entreprise sont gérés.
- Les appareils mobiles que votre personnel utilise sont gérés.
- Les informations de votre entreprise sont protégées grâce au contrôle de la manière dont votre personnel y accède et les partage.
- L’appareil et ses applications sont conformes aux exigences de sécurité de l’entreprise.




## <a name="next-steps"></a>Étapes suivantes

Avant de configurer une stratégie d’accès conditionnel basé sur les appareils dans votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md).

