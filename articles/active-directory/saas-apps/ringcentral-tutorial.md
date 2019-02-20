---
title: 'Tutoriel : Intégration d’Azure Active Directory à RingCentral | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e01b06b2031da20ee9349588bd4a7ee757fbc22c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173298"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Tutoriel : Intégration d’Azure Active Directory à RingCentral

Dans ce didacticiel, vous allez apprendre à intégrer RingCentral à Azure Active Directory (Azure AD).

L’intégration de RingCentral dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à RingCentral.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à RingCentral (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à RingCentral, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement RingCentral pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de RingCentral depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ringcentral-from-the-gallery"></a>Ajout de RingCentral depuis la galerie
Pour configurer l’intégration de RingCentral à Azure AD, vous devez ajouter RingCentral à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter RingCentral à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **RingCentral**, sélectionnez **RingCentral** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RingCentral, en vous servant d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur RingCentral équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur associé dans RingCentral doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec RingCentral, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test RingCentral](#create-a-ringcentral-test-user)** pour avoir un équivalent de Britta Simon dans RingCentral, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application RingCentral.

**Pour configurer l’authentification unique Azure AD avec RingCentral, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **RingCentral**, cliquez sur **Authentification unique**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** en haut de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la zone de texte de la section **Configuration SAML de base**, comme indiqué ci-dessous :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. Dans la zone de texte **URL d’authentification**, entrez une URL :
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Sur la page de configuration de l’authentification unique de RingCentral, vous obtenez le **fichier de métadonnées du fournisseur de services**, qui sera expliqué plus tard dans le didacticiel.

6. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **URL d’authentification**, entrez une URL :
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Dans la zone de texte **Identificateur**, tapez une URL :
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant :
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le certificat en fonction des options définies, puis enregistrez-le sur votre ordinateur.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. Ouvrez une autre fenêtre de navigateur web et connectez-vous à RingCentral en tant qu’administrateur de la sécurité.

9. En haut de la page, cliquez sur **Outils**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Accédez à **Authentification unique**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Sur la page **Authentification unique**, dans la section **Configuration SSO**, cliquez sur **Modifier** dans l’**étape 1**, puis procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Sur la page **Configurer l’authentification unique**, effectuez les étapes suivantes :

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Cliquez sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Après avoir chargé les métadonnées, les valeurs sont automatiquement renseignées dans la section **SSO General Information** (Informations générales sur l’authentification unique).

    c. Dans la section **Mappage d’attributs**, définissez **Map Email Attribute to** (Mapper l’attribut d’e-mail à) sur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Cliquez sur **Enregistrer**.

    e. Dans l’**étape 2**, cliquez sur **Télécharger** pour télécharger le **fichier de métadonnées du fournisseur de services**. Ensuite, chargez-le dans la section **Configuration SAML de base** afin de renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** dans le portail Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Sur la même page, accédez à la section **Activer l’authentification unique** et procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Sélectionnez **Activer le service d’authentification unique**.
    
    b. Sélectionnez **Allow users to log in with SSO or RingCentral credential** (Autoriser les utilisateurs à se connecter avec des informations d’identification de l’authentification unique ou RingCentral).

    c. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-ringcentral-test-user"></a>Créer un utilisateur de test RingCentral

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RingCentral. Collaborez avec l’ [équipe de support technique RingCentral](https://success.ringcentral.com/RCContactSupp) pour ajouter les utilisateurs dans la plateforme RingCentral. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RingCentral.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque RingCentral dans le volet d’accès, vous devez être connecté automatiquement à votre application RingCentral.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

