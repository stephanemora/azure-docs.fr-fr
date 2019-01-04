---
title: 'Tutoriel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 6b2b5f72c9520498d4834bbbfaf6c56656a807e7
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015213"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutoriel : Intégration d’Azure Active Directory avec DocuSign

Dans ce didacticiel, vous allez apprendre à intégrer DocuSign à Azure Active Directory (Azure AD).

L’intégration de DocuSign à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à DocuSign.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à DocuSign (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec DocuSign, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement DocuSign pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de DocuSign à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-docusign-from-the-gallery"></a>Ajout de DocuSign à partir de la galerie

Pour configurer l’intégration de DocuSign à Azure AD, vous devez ajouter DocuSign à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter DocuSign à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **DocuSign**, sélectionnez **DocuSign** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![DocuSign dans la liste des résultats](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec DocuSign, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur DocuSign correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur associé dans DocuSign doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec DocuSign, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur test DocuSign](#creating-a-docusign-test-user)** pour avoir un équivalent de Britta Simon dans DocuSign lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application DocuSign.

**Pour configurer l’authentification unique Azure AD avec DocuSign, effectuez les étapes suivantes :**

1. Sur le portail Azure, à la page d’intégration de l’application **DocuSign**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations relatives à l’authentification unique des URL et du domaine DocuSign](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée ultérieurement dans le tutoriel, dans la section **Afficher les points de terminaison SAML 2.0**.

5. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Dans la section **Configurer DocuSign**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de DocuSign](common/configuresection.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration DocuSign** en tant qu’administrateur.

8. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Accéder à Admin**.
  
    ![Configuration de l'authentification unique][51]

9. Dans la page des solutions de votre domaine, cliquez sur **Domaines**.

    ![Configuration de l'authentification unique][50]

10. Dans la section **Domaines**, cliquez sur **REVENDIQUER LE DOMAINE**.

    ![Configuration de l'authentification unique][52]

11. Dans la boîte de dialogue de **revendication du domaine**, dans la zone de texte **Nom du domaine**, indiquez votre domaine d’entreprise, puis cliquez sur **REVENDIQUER**. Veillez à vérifier le domaine et assurez-vous que son état est actif.

    ![Configuration de l'authentification unique][53]

12. Dans la page des solutions de votre domaine, cliquez sur **Fournisseurs d'identité**.
  
    ![Configuration de l'authentification unique][54]

13. Dans la section **Fournisseurs d’identité**, cliquez sur **AJOUTER UN FOURNISSEUR D’IDENTITÉ**. 

    ![Configuration de l'authentification unique][55]

14. Dans la page **Identity Provider Settings** (Paramètres du fournisseur d’identité), effectuez les actions suivantes :

    ![Configuration de l'authentification unique][56]

    a. Dans la zone de texte **Nom** , entrez le nom de votre configuration. N’utilisez pas d’espaces.

    b. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**identificateur Azure AD** que vous avez copié dans le portail Azure.

    c. Dans la zone de texte **URL de connexion du fournisseur d'identité**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **URL de déconnexion du fournisseur d'identité**, collez la valeur de l’**URL de déconnexion** que vous avez copiée dans le portail Azure.

    e. Sélectionnez **Signer la demande d’authentification**.

    f. Sous **Send AuthN request by** (Envoyer la demande d’authentification par), sélectionnez **POST**.

    g. Sous **Send logout request by** (Envoyer la demande de déconnexion par), sélectionnez **GET**.

    h. Dans la section **Mappage des attributs personnalisés**, cliquez sur **AJOUTER UN NOUVEAU MAPPAGE**.

    ![Configuration de l'authentification unique][62]

    i. Choisissez le champ que vous voulez mapper avec la revendication Azure AD. Dans cet exemple, la revendication **emailaddress** est mappée sur la valeur de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Il s’agit du nom de revendication par défaut d’Azure AD pour la revendication e-mail. Ensuite, cliquez sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][57]

    > [!NOTE]
    > Utilisez l’**identificateur d’utilisateur** approprié pour mapper l’utilisateur d’Azure AD au mappage utilisateur DocuSign. Sélectionnez le champ correct et entrez la valeur appropriée en fonction des paramètres de votre organisation.

    j. Dans la section **Certificats du fournisseur d'identité**, cliquez sur **AJOUTER UN CERTIFICAT**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure AD avant de cliquer sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][58]

    k. Dans la section **Fournisseurs d'identité**, cliquez sur **ACTIONS**, puis sur **Points de terminaison**.

    ![Configuration de l'authentification unique][59]

    l. Dans la section **View SAML 2.0 Endpoints** (Afficher les points de terminaison SAML 2.0) du **portail d’administration DocuSign**, effectuez les étapes suivantes :

    ![Configuration de l'authentification unique][60]

    * Copiez la valeur **URL de l’émetteur du fournisseur de services**, puis collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL DocuSign** du portail Azure.

    * Copiez la valeur **URL de connexion du fournisseur de services**, puis collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL DocuSign** du portail Azure.

    * Cliquez sur **Fermer**

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-docusign-test-user"></a>Création d’un utilisateur de test DocuSign

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans DocuSign. DocuSign prend en charge l’approvisionnement juste-à-temps (activé par défaut). Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à DocuSign.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez  [l’équipe de support technique DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à DocuSign.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **DocuSign**.

    ![Configurer l'authentification unique](./media/docusign-tutorial/tutorial_docusign_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette DocuSign dans le volet d’accès, vous devez être connecté automatiquement à votre application DocuSign.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
