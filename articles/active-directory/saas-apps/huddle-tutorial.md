---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Huddle | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 3ec78c7dc3ac3ce9747a09218e1e35068c60b894
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187723"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Didacticiel : Intégration d’Azure Active Directory avec Huddle

Dans ce didacticiel, vous allez apprendre à intégrer Huddle à Azure Active Directory (Azure AD).

L’intégration de Huddle dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Huddle.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Huddle (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Huddle, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Huddle pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Huddle depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-huddle-from-the-gallery"></a>Ajout de Huddle depuis la galerie

Pour configurer l’intégration de Huddle avec Azure AD, vous devez ajouter Huddle à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Huddle à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, entrez **Huddle**. Dans le panneau des résultats, sélectionnez **Huddle**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Huddle avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Huddle équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Huddle associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Huddle, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Huddle](#creating-a-huddle-test-user)** pour obtenir un équivalent de Britta Simon dans Huddle lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Huddle.

**Pour configurer l’authentification unique Azure AD avec Huddle, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Huddle**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_general_300.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_general_301.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_general_302.png)

5. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode initié par **IDP** :

    > [!NOTE]
    > Votre instance Huddle est automatiquement détectée à partir du domaine que vous entrez ci-dessous.

    ![Informations d’authentification unique dans Domaine et URL Huddle](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL :

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant :

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Huddle](./media/huddle-tutorial/tutorial_huddle_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support technique Huddle](https://huddle.zendesk.com) pour obtenir cette valeur.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le certificat approprié en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. Dans la section **Configurer Huddle**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. Pour configurer l’authentification unique côté **Huddle**, vous devez envoyer le certificat que vous avez téléchargé ainsi que les URL que vous avez copiées à partir de la section **Configurer** **Huddle** du portail Azure à [l’équipe du support technique Huddle](https://huddle.zendesk.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

    >[!NOTE]
    > L’authentification unique doit être activée par l’équipe du support technique Huddle. Vous recevrez une notification une fois la configuration terminée.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-huddle-test-user"></a>Création d’un utilisateur de test Huddle

Pour se connecter à Huddle, les utilisateurs d’Azure AD doivent être configurés dans Huddle. Dans le cas de Huddle, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Huddle** en tant qu’administrateur.

2. Cliquez sur **Workspace**.

3. Cliquez sur **People \> Invite People**.

    ![Personnes](./media/huddle-tutorial/IC787838.png "Personnes")

4. Dans la section **Create a new invitation** , procédez comme suit :
  
    ![New Invitation](./media/huddle-tutorial/IC787839.png "New Invitation")
  
    a. Dans la liste **Choose a team to invite people to join**, sélectionnez **team**.

    b. Entrez l’**adresse e-mail** associée au compte Azure AD valide que vous souhaitez configurer dans la zone de texte **Entrer l’adresse e-mail des personnes que vous souhaitez inviter**.

    c. Cliquez sur **Invite**.

    >[!NOTE]
    > Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[!NOTE]
>Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur Huddle fourni par ce site pour configurer des comptes d’utilisateurs Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Huddle.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Huddle**.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Huddle dans le panneau d’accès, vous accédez automatiquement à la page de connexion Huddle.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
