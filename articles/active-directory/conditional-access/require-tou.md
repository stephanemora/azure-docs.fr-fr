---
title: Démarrage rapide – Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud Azure Active Directory protégées par accès conditionnel | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous découvrirez comment exiger que vos conditions d’utilisation soient acceptées avant d’accorder l’accès à une sélection d’applications cloud grâce à l’accès conditionnel Azure Active Directory.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel avec Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel, conditions d’utilisation
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: a7aeecc84a3629b43f2c1eb40030866a941d0d3b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627982"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Démarrage rapide : exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud 

Si vous souhaitez obtenir le consentement des utilisateurs avant qu’ils ne puissent accéder à certaines applications cloud de votre environnement, vous pouvez demander à ce qu’ils acceptent vos conditions d’utilisation. L’accès conditionnel Azure Active Directory (Azure AD) vous offre les avantages suivants : 

- Une méthode simple pour configurer les conditions d’utilisation
- La possibilité d’exiger l’acceptation de vos conditions d’utilisation à l’aide d’une stratégie d’accès conditionnel  

Ce démarrage rapide vous montre comment configurer une [stratégie d’accès conditionnel Azure AD](../active-directory-conditional-access-azure-portal.md) qui vous permet de demander aux utilisateurs d’accepter vos conditions d’utilisation pour accéder à une certaine application cloud de votre environnement.

![Créer une stratégie](./media/require-tou/5555.png)


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.



## <a name="prerequisites"></a>Prérequis 

Pour suivre le scénario décrit dans ce démarrage rapide, vous avez besoin de ce qui suit :

- **Accès à l’édition Azure AD Premium** : l’accès conditionnel Azure AD est une fonctionnalité d’Azure AD Premium. 

- **Un compte d’essai nommé Isabella Simonsen** : si vous ignorez comment créer un compte d’essai, voir [Ajouter des utilisateurs basés sur le cloud](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Tester la connexion

L’objectif de cette étape consiste à obtenir une impression de l’expérience de connexion sans stratégie d’accès conditionnel.

**Pour tester la connexion :**

1. [Connectez-vous](https://portal.azure.com/) à votre portail Azure en tant que Isabella Simonsen.

2. Déconnectez-vous.




## <a name="create-your-terms-of-use"></a>Créer vos conditions d’utilisation

Cette section explique comment configurer des conditions d’utilisation. Lorsque vous créez des conditions d’utilisation, vous sélectionnez une valeur pour **Appliquer avec des modèles de stratégie d’accès conditionnel**. Sélectionnez **Stratégie personnalisée** pour ouvrir la boîte de dialogue permettant de créer une nouvelle stratégie d’accès conditionnel. Cette étape est possible dès que vos conditions d’utilisation sont créées.


**Pour créer vos conditions d’utilisation :**

1. Dans Microsoft Word, créez un nouveau document.

2. Saisissez **My terms of use**, puis enregistrez le document sur votre ordinateur sous le nom **mytou.pdf**.

3. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.

4. Dans la barre de navigation gauche du portail Azure, cliquez sur **Azure Active Directory**. 

    ![Azure Active Directory](./media/require-tou/02.png)

5. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/require-tou/03.png) 

6. Dans la section **Gérer**, cliquez sur **Conditions d’utilisation**.

    ![Conditions d’utilisation](./media/require-tou/04.png) 

7. Dans le menu supérieur, cliquez sur **Nouvelles conditions**.

    ![Conditions d’utilisation](./media/require-tou/05.png) 

8. Sur la page **Nouvelles conditions d’utilisation** :

    ![Conditions d’utilisation](./media/require-tou/112.png) 

    a. Dans la zone de texte **Nom**, saisissez **My TOU**.

    b. Dans la zone de texte **Nom d’affichage** saisissez **My TOU**.

    c. Chargez vos conditions d’utilisation sous forme de fichier PDF.

    d. Dans la zone **langue**, sélectionnez **anglais**.

    e. Activez l’option permettant de **demander aux utilisateurs de développer les conditions d’utilisation****.**

    f. Pour l’option **Appliquer avec des modèles de stratégie d’accès conditionnel**, sélectionnez **Stratégie personnalisée**.

    g. Cliquez sur **Créer**.
 


## <a name="create-your-conditional-access-policy"></a>Créer votre stratégie d’accès conditionnel 

Cette section montre comment créer la stratégie d’accès conditionnel requise. Le scénario de ce démarrage rapide utilise ce qui suit :

- le portail Azure en tant qu’espace réservé pour une application cloud qui exige que vos conditions d’utilisation soient acceptées ; 
- votre exemple d’utilisateur pour tester la stratégie d’accès conditionnel.  

Dans votre stratégie, définissez :

|Paramètre |Valeur|
|---     | --- |
|Utilisateurs et groupes | Isabella Simonsen |
|Applications cloud | Gestion Microsoft Azure |
|Accorder l'accès | My TOU |
 

![Créer une stratégie](./media/require-tou/1234.png)

 


**Pour configurer votre stratégie d’accès conditionnel, procédez comme suit :**

1. Sur la page **Nouveau**, dans la zone de texte **Nom**, saisissez **Require TOU for Isabella**.

    ![NOM](./media/require-tou/71.png)

2. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Utilisateurs et groupes](./media/require-tou/06.png)

3. Sur la page **Utilisateurs et groupes** :

    ![Utilisateurs et groupes](./media/require-tou/24.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**, puis choisissez **des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez **Isabella Simonsen**, puis cliquez sur **Sélectionner**.

    d. Dans la page **Utilisateurs et groupes**, cliquez sur **Terminé**.

4. Cliquez sur **Applications cloud**.

    ![Applications cloud](./media/require-tou/08.png)

5. Sur la page **Applications cloud** :

    ![Sélection des applications cloud](./media/require-tou/26.png)

    a. Cliquez sur **Sélectionner les applications**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, choisissez **Gestion Microsoft Azure**, puis cliquez sur **Sélectionner**.

    d. Dans la page **Applications cloud**, cliquez sur **Terminé**.


6. Dans la section **Contrôles d’accès**, cliquez sur **Accorder**.

    ![Contrôles d’accès](./media/require-tou/10.png)

7. Sur la page des **octrois** :

    ![Grant (Autoriser)](./media/require-tou/111.png)

    a. Sélectionner **Accorder l’accès**.

    a. Sélectionnez **My TOU**.

    b. Cliquez sur **Sélectionner**.

8. Dans la section **Activer la stratégie**, cliquez sur **Activée**.

    ![Activer la stratégie](./media/require-tou/18.png)

9. Cliquez sur **Créer**.


## <a name="evaluate-a-simulated-sign-in"></a>Évaluer une connexion simulée

À présent que vous avez configuré votre stratégie d’accès conditionnel, vous souhaitez probablement savoir s’il fonctionne comme prévu. Dans un premier temps, utilisez l’outil de stratégie d’accès conditionnel What If pour simuler une connexion de votre utilisateur de test. La simulation évalue l’impact cette connexion sur vos stratégies et génère un rapport de simulation.  

Pour initialiser l’outil d’évaluation de stratégie What If, définissez ce qui suit :

- **Isabella Simonsen** en tant qu’utilisateur. 
- **Gestion Microsoft Azure** en tant qu’application cloud.


Un clic sur **What If** a pour effet de créer un rapport de simulation indiquant ce qui suit :

- **Require TOU for Isabella** sous **Stratégies qui vont s’appliquer** 
- **My TOU** en tant que **contrôles des octrois**.

![Outil de stratégie What If](./media/require-tou/79.png)



**Pour évaluer votre stratégie d’accès conditionnel :**

1. Dans la page [Accès conditionnel - Stratégies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), dans le menu en haut, cliquez sur **What If**.  
 
    ![What If](./media/require-tou/14.png)

2. Cliquez sur **Utilisateurs**, sélectionnez **Isabella Simonsen**, puis cliquez sur **Sélectionner**.

    ![Utilisateur](./media/require-tou/15.png)

2. Pour sélectionner une application cloud :

    ![Applications cloud](./media/require-tou/16.png)

    a. Cliquez sur **Applications cloud**.

    b. Dans la page **Applications cloud**, cliquez sur **Sélectionner les applications**.

    c. Cliquez sur **Sélectionner**.

    d. Dans la page **Sélectionner**, choisissez **Gestion Microsoft Azure**, puis cliquez sur **Sélectionner**.

    e. Dans la page Applications cloud, cliquez sur **Terminé**.

3. Cliquez sur **What If**.


## <a name="test-your-conditional-access-policy"></a>Tester votre stratégie d’accès conditionnel

Dans la section précédente, vous avez appris à évaluer une connexion simulée. En plus d’une simulation, vous devez tester votre stratégie d’accès conditionnel pour vous assurer qu’elle fonctionne comme prévu. 

Pour tester votre stratégie, essayez de vous connecter à votre [portail Azure](https://portal.azure.com) à l’aide de votre compte de test **Isabella Simonsen**. Une boîte de dialogue doit s’afficher, exigeant que vous acceptiez vos conditions d’utilisation.

![Conditions d’utilisation](./media/require-tou/57.png)


## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur de test et la stratégie d’accès conditionnel :

- Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pour supprimer votre stratégie, sélectionnez-la, puis cliquez sur **Supprimer** dans la barre d’outils Accès rapide.

    ![Authentification multifacteur](./media/require-tou/33.png)

- Pour supprimer vos conditions d’utilisation, sélectionnez-les, puis cliquez sur **Supprimer les conditions** dans la barre d’outils supérieure. 

    ![Authentification multifacteur](./media/require-tou/29.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exiger une authentification multifacteur pour des applications spécifiques](app-based-mfa.md)
> [Bloquer l’accès lorsqu’un risque de session est détecté](app-sign-in-risk.md)

