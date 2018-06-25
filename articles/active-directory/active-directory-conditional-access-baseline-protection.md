---
title: Qu’est ce qu’une protection de base dans l’accès conditionnel Azure Active Directory ? | Microsoft Docs
description: Découvrez comment la protection de base garantit qu’au moins le niveau de sécurité de base est activé dans votre environnement.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248934"
---
# <a name="what-is-baseline-protection"></a>Qu’est ce qu’une protection de base ?  

Au cours de l’année écoulée, les attaques d’identité ont augmenté de 300 %. Pour protéger votre environnement contre les attaques en augmentation constante, Azure Active Directory (Azure AD) introduit une nouvelle fonctionnalité appelée « protection de base ». La protection de base est un ensemble de stratégies d’accès conditionnel prédéfinies. L’objectif de ces stratégies est de garantir qu’au moins le niveau de sécurité de base est activé dans votre environnement. 

Pendant la préversion, vous devez activer les stratégies de base si vous souhaitez les rendre actives. Une fois la disponibilité générale validée, ces stratégies sont activées par défaut. 

La première stratégie de protection de base exige l’authentification multifacteur (MFA) pour les comptes privilégiés. Les attaquants qui prennent le contrôle de comptes privilégiés peuvent causer des dégâts considérables. Il est donc essentiel de protéger d’abord ces comptes. Les rôles privilégiés suivants sont concernés par cette stratégie : 

- Administrateur général  

- Administrateur SharePoint  

- Administrateur Exchange  

- Administrateur de l’accès conditionnel  

- Administrateur de sécurité  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Pour commencer 

Pour activer la stratégie de base :  

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.

2. Dans la barre de navigation gauche du **portail Azure**, cliquez sur **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/active-directory-conditional-access-baseline-protection/03.png)

4. Dans la liste des stratégies, cliquez sur **Stratégie de base : Exiger une authentification MFA pour les administrateurs (préversion)**. 

5. Pour activer la stratégie, cliquez sur **Utiliser la stratégie immédiatement**.

6. Cliquez sur **Enregistrer**. 
 

La stratégie de base vous offre la possibilité d’exclure des utilisateurs et des groupes. Vous pouvez vouloir exclure un *[compte d’administration de l’accès d’urgence](active-directory-admin-manage-emergency-access-accounts.md)* pour garantir que votre accès au locataire n’est pas verrouillé.
  
 

## <a name="what-you-should-know"></a>Ce que vous devez savoir 

Les rôles d’annuaire inclus dans la stratégie de base sont les rôles Azure AD les plus privilégiés. En fonction des commentaires, d’autres pourront être ajoutés par la suite. 

Si vous disposez de comptes avec des privilèges d’administrateur dans vos scripts, vous devez utiliser [Managed Service Identity (MSI)](managed-service-identity/overview.md) ou des [principaux de service (avec des certificats)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) à la place. Pour contourner provisoirement le problème, vous pouvez exclure des comptes d’utilisateurs spécifiques de votre stratégie de base. 

La stratégie s’applique à des flux d’authentification hérités comme POP, IMAP ou un client Office pour ordinateur de bureau plus ancien. 

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
