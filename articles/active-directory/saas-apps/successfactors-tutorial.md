---
title: 'Didacticiel : Intégration d’Azure Active Directory avec SuccessFactors | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 467db4046c0600142338dcfa39e136f45255caba
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976747"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Didacticiel : Intégration d’Azure Active Directory avec SuccessFactors

L’objectif de ce didacticiel est de vous apprendre à intégrer SuccessFactors à Azure Active Directory (Azure AD).

L’intégration de SuccessFactors dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SuccessFactors.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SuccessFactors (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SuccessFactors, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SuccessFactors pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SuccessFactors à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-successfactors-from-the-gallery"></a>Ajout de SuccessFactors à partir de la galerie

Pour configurer l’intégration de SuccessFactors à Azure AD, vous devez ajouter SuccessFactors à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SuccessFactors à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SuccessFactors**, sélectionnez **SuccessFactors** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SuccessFactors dans la liste des résultats](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD à SuccessFactors, avec un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SuccessFactors équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SuccessFactors associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SuccessFactors, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SuccessFactors](#creating-a-successfactors-test-user)** pour avoir un équivalent de Britta Simon dans SuccessFactors lié à la représentation Azure AD de l'utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application SuccessFactors.

**Pour configurer l’authentification unique Azure AD avec SuccessFactors, procédez comme suit :**

1. Sur la page d’intégration d’applications **SuccessFactors** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse, l’identificateur et l’URL de connexion réels. Contactez [l’équipe de support client de SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) pour obtenir ces valeurs. 

5. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Dans la section **Configurer SuccessFactors**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de SuccessFactors](common/configuresection.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre **Portail d’administration SuccessFactors** en tant qu’administrateur.
    
8. Dans **Application Security** (Sécurité des applications), accédez à la **fonctionnalité d’authentification unique**. 

9. Indiquez une valeur dans le champ **Reset Token** (Réinitialiser le jeton), puis cliquez sur **Save Token** (Enregistrer le jeton) pour activer l’authentification unique SAML.
   
    ![Configuration de l’authentification unique côté application][11]

    > [!NOTE] 
    > Cette valeur est utilisée comme commutateur activé/désactivé. Si une valeur est enregistrée, l’authentification unique SAML est activée. Si aucune valeur n’est enregistrée, l’authentification unique SAML est désactivée.

10. Accédez à la capture d’écran ci-dessous et effectuez les actions suivantes :
   
    ![Configuration de l’authentification unique côté application][12]
   
    a. Sélectionnez la case d’option **SAML v2 SSO** .
   
    b. Définissez le **Nom de la partie de confiance SAML** (par exemple, émetteur SAML + nom de l’entreprise).
   
    c. Dans la zone de texte **URL de l’émetteur**, collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.
   
    d. Sélectionnez **Assertion** pour **Require Mandatory Signature** (Exiger une signature obligatoire).
   
    e. Sélectionnez **Enabled** (Activé) dans le champ **Enable SAML Flag** (Activer l’indicateur SAML).
   
    f. Sélectionnez **No** (Non) dans le champ **Login Request Signature(SF Generated/SP/RP)** (Signature de la demande de connexion (générée par SF/fournisseur de services/fournisseur de ressources)).
   
    g. Sélectionnez **Browser/Post Profile** (Navigateur/Post-profilage) en tant que **profil SAML**.
   
    h. Sélectionnez **No** (Non) dans le champ **Enforce Certificate Valid Period** (Appliquer la période valide du certificat).
   
    i. Copiez le contenu du fichier de certificat téléchargé sur le Portail Azure, puis collez-le dans la zone de texte **Certificat de vérification SAML**.

    > [!NOTE] 
    > Le contenu du certificat doit comporter des balises de début et de fin de certificat.

11. Accédez à SAML V2, puis procédez comme suit :
   
    ![Configuration de l’authentification unique côté application][13]
   
    a. Sélectionnez **Yes** (Oui) dans **Support SP-initiated Global Logout** (Prendre en charge la déconnexion globale initiée par le fournisseur de services).
   
    b. Dans la zone de texte **URL du service de déconnexion global (destination LogoutRequest)**, collez la valeur **URL de déconnexion** que vous avez copiée sur le Portail Azure.
   
    c. Sélectionnez **No** (Non) dans **Require sp must encrypt all NameID element** (Exiger que le fournisseur de services chiffre tous les éléments NameID).
   
    d. Sélectionnez **unspecified** (non spécifié) dans **NameID Format** (Format NameID).
   
    e. Sélectionnez **Yes** (Oui) dans **Enable sp initiated login (AuthnRequest)** (Activer la connexion initiée par le fournisseur de services (AuthnRequest)).
   
    f. Dan la zone de texte **Envoyer la requête à l’émetteur couvrant l'ensemble de l'entreprise**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée sur le Portail Azure.

12. Suivez ces étapes si vous souhaitez que les noms d’utilisateur de connexion ne respectent pas la casse.
   
    ![Configurer l'authentification unique][29]
    
    a. Accédez à **Company Settings** (Paramètres de l’entreprise) (en bas).
   
    b. Sélectionnez la case à cocher près de **Enable Non-Case-Sensitive Username**(Activer le non-respect de la casse pour les noms d’utilisateur).
   
    c. Cliquez sur **Enregistrer**.
   
    > [!NOTE] 
    > Si vous essayez d’activer cette option, le système vérifie si cela crée un nom de connexion SAML en double. Par exemple, si le client possède les noms d’utilisateur User1 et user1. Si vous désactivez le respect de la casse, ces noms deviennent des doublons. Le système vous transmet un message d’erreur et n’active pas la fonctionnalité. Le client devra modifier l’orthographe d’un des noms d’utilisateur.

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

### <a name="creating-a-successfactors-test-user"></a>Création d’un utilisateur de test SuccessFactors

Pour pouvoir se connecter à SuccessFactors, les utilisateurs d’Azure AD doivent être approvisionnés dans SuccessFactors.  
Dans le cas de SuccessFactors, l’approvisionnement est une tâche manuelle.

Pour créer des utilisateurs dans SuccessFactors, vous devez contacter [l’équipe de support technique SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SuccessFactors.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **SuccessFactors**.

    ![Configurer l'authentification unique](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque SuccessFactors dans le volet d’accès, vous devez être connecté automatiquement à votre application SuccessFactors.
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
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
