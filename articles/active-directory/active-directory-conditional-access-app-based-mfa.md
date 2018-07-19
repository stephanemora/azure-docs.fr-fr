---
title: Démarrage rapide - Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory | Microsoft Docs
description: Ce démarrage rapide explique comment lier vos exigences d’authentification au type d’application cloud faisant l’objet d’un accès conditionnel Azure Active Directory (Azure AD).
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 0a2cddec1308b96b960cd0bbc5ccae9fd1a1a9a7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449143"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Démarrage rapide - Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory 

Pour simplifier l’expérience de connexion de vos utilisateurs, vous pouvez autoriser ceux-ci à se connecter à vos applications cloud à l’aide d’un nom d’utilisateur et d’un mot de passe. Cependant, de nombreux environnements englobent au moins quelques applications pour lesquelles il est souhaitable d’exiger une forme plus forte de vérification de compte, telle qu’une authentification multifacteur (MFA). Cela peut être vrai, par exemple, pour accéder au système de courrier de votre organisation ou à vos applications de gestion des ressources humaines. Dans Azure Active Directory (Azure AD), vous pouvez atteindre cet objectif avec une stratégie d’accès conditionnel.    

Ce démarrage rapide montre comment configurer une [stratégie d’accès conditionnel Azure AD](active-directory-conditional-access-azure-portal.md) qui exige une authentification multifacteur pour une application cloud sélectionnée dans votre environnement.

![Créer une stratégie](./media/active-directory-conditional-access-app-based-mfa/32.png)


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.



## <a name="prerequisites"></a>Prérequis 

Pour suivre le scénario décrit dans ce démarrage rapide, vous avez besoin de ce qui suit :

- **Accès à l’édition Azure AD Premium** : l’accès conditionnel Azure AD est une fonctionnalité d’Azure AD Premium. 

- **Un compte d’essai nommé Isabella Simonsen** : si vous ignorez comment créer un compte d’essai, voir [Ajouter des utilisateurs basés sur le cloud](fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Tester la connexion

L’objectif de cette étape consiste à obtenir une impression de l’expérience de connexion sans stratégie d’accès conditionnel.

**Pour initialiser votre environnement :**

1. Connectez-vous à votre portail Azure en tant que Isabella Simonsen.

2. Déconnectez-vous.


## <a name="create-your-conditional-access-policy"></a>Créer votre stratégie d’accès conditionnel 

Cette section montre comment créer la stratégie d’accès conditionnel requise. Le scénario de ce démarrage rapide utilise ce qui suit :

- le portail Azure en tant qu’espace réservé pour une application cloud qui exige une authentification multifacteur ; 
- votre exemple d’utilisateur pour tester la stratégie d’accès conditionnel.  

Dans votre stratégie, définissez :

|Paramètre |Valeur|
|---     | --- |
|Utilisateurs et groupes | Isabella Simonsen |
|Applications cloud | Gestion Microsoft Azure |
|Accorder l'accès | Exiger une authentification multifacteur |
 

![Créer une stratégie](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**Pour configurer votre stratégie d’accès conditionnel, procédez comme suit :**

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.

2. Dans la barre de navigation gauche du portail Azure, cliquez sur **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Dans la page **Accès conditionnel**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![Ajouter](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Dans la page **Nouveau**, dans la zone de texte **Nom**, tapez **Exiger une authentification multifacteur pour l’accès au portail Azure**.

    ![NOM](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Utilisateurs et groupes](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Dans la page **Utilisateurs et groupes**, effectuez les étapes suivantes :

    ![Utilisateurs et groupes](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**, puis choisissez **des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez **Isabella Simonsen**, puis cliquez sur **Sélectionner**.

    d. Dans la page **Utilisateurs et groupes**, cliquez sur **Terminé**.

8. Cliquez sur **Applications cloud**.

    ![Applications cloud](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Dans la page **Applications cloud**, procédez comme suit :

    ![Sélection des applications cloud](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. Cliquez sur **Sélectionner les applications**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, choisissez **Gestion Microsoft Azure**, puis cliquez sur **Sélectionner**.

    d. Dans la page **Applications cloud**, cliquez sur **Terminé**.


10. Dans la section **Contrôles d’accès**, cliquez sur **Accorder**.

    ![Contrôles d’accès](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. Dans la page **Octroyer**, effectuez les étapes suivantes :

    ![Grant (Autoriser)](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Sélectionner **Accorder l’accès**.

    a. Sélectionnez **Exiger une authentification multifacteur**.

    b. Cliquez sur **Sélectionner**.

12. Dans la section **Activer la stratégie**, cliquez sur **Activée**.

    ![Activer la stratégie](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Cliquez sur **Créer**.


## <a name="evaluate-a-simulated-sign-in"></a>Évaluer une connexion simulée

À présent que vous avez configuré votre stratégie d’accès conditionnel, vous souhaitez probablement savoir s’il fonctionne comme prévu. Dans un premier temps, utilisez l’outil de stratégie d’accès conditionnel What If pour simuler une connexion de votre utilisateur de test. La simulation évalue l’impact cette connexion sur vos stratégies et génère un rapport de simulation.  

Pour initialiser l’outil d’évaluation de stratégie What If, définissez ce qui suit :

- **Isabella Simonsen** en tant qu’utilisateur. 
- **Gestion Microsoft Azure** en tant qu’application cloud.

 Un clic sur **What If** a pour effet de créer un rapport de simulation indiquant ce qui suit :

- **Exiger une authentification multifacteur pour l’accès au portail Azure** sous **Stratégies qui vont s’appliquer**. 
- **Exiger une authentification multifacteur** en tant que **Contrôles d’octroi**.

![Outil de stratégie What If](./media/active-directory-conditional-access-app-based-mfa/23.png)



**Pour évaluer votre stratégie d’accès conditionnel :**

1. Dans la page [Accès conditionnel - Stratégies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), dans le menu en haut, cliquez sur **What If**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Cliquez sur **Utilisateurs**, sélectionnez **Isabella Simonsen**, puis cliquez sur **Sélectionner**.

    ![Utilisateur](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Pour sélectionner une application cloud, procédez comme suit :

    ![Applications cloud](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Cliquez sur **Applications cloud**.

    b. Dans la page **Applications cloud**, cliquez sur **Sélectionner les applications**.

    c. Cliquez sur **Sélectionner**.

    d. Dans la page **Sélectionner**, choisissez **Gestion Microsoft Azure**, puis cliquez sur **Sélectionner**.

    e. Dans la page Applications cloud, cliquez sur **Terminé**.

3. Cliquez sur **What If**.


## <a name="test-your-conditional-access-policy"></a>Tester votre stratégie d’accès conditionnel

Dans la section précédente, vous avez appris à évaluer une connexion simulée. En plus d’une simulation, vous devez tester votre stratégie d’accès conditionnel pour vous assurer qu’elle fonctionne comme prévu. 

Pour tester votre stratégie, essayez de vous connecter à votre [portail Azure](https://portal.azure.com) à l’aide de votre compte de test **Isabella Simonsen**. Vous devriez voir s’afficher une boîte de dialogue vous demandant de configurer votre compte pour une vérification de sécurité supplémentaire.

![Authentification multifacteur](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur de test et la stratégie d’accès conditionnel :

- Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pour supprimer votre stratégie, sélectionnez-la, puis cliquez sur **Supprimer** dans la barre d’outils Accès rapide.

    ![Authentification multifacteur](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exiger l’acceptation des conditions d’utilisation](./active-directory-conditional-access-tou.md)
> [Bloquer l’accès lorsqu’un risque de session est détecté](./active-directory-conditional-access-app-sign-in-risk.md)
