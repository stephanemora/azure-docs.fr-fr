---
title: 'Tutoriel : Intégration d’Azure Active Directory à Meta Networks Connector| Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e27ba7d5b245d8857f0c07bfe2923afe9d7e3a0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267044"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutoriel : Intégration d’Azure Active Directory à Meta Networks Connector

Dans ce tutoriel, vous apprenez à intégrer Meta Networks Connector à Azure Active Directory (Azure AD).

L’intégration de Meta Networks Connector à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Meta Networks Connector.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Meta Networks Connector (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Meta Networks Connector, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à Meta Networks Connector pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Meta Networks Connector à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Ajout de Meta Networks Connector à partir de la galerie
Pour configurer l’intégration de Meta Networks Connector à Azure AD, vous devez ajouter Meta Networks Connector à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Meta Networks Connector à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Meta Networks Connector**, sélectionnez **Meta Networks Connector** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Meta Networks Connector dans la liste des résultats](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Meta Networks Connector, au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Meta Networks Connector équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Meta Networks Connector qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Meta Networks Connector, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Meta Networks Connector](#create-a-meta-networks-connector-test-user)** pour avoir un équivalent de Britta Simon dans Meta Networks Connector lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure, et vous configurez l’authentification unique dans votre application Meta Networks Connector.

**Pour configurer l’authentification unique Azure AD avec Meta Networks Connector, effectuez les étapes suivantes :**

1. Dans la page d’intégration de l’application **Meta Networks Connector** du portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

3. Dans la section **Domaines et URL Meta Networks Connector**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le tutoriel.

5. L’application Meta Networks Connector attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut | ESPACE DE NOMS|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Nom | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.

    e. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)

8. Dans la section **Configuration de Meta Networks Connector**, cliquez sur **Configurer Meta Networks Connector** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)

9. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/metanetworksconnector-tutorial/tutorial_general_400.png)

10. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Meta Networks Connector.

    > [!NOTE]
    > Meta Networks Connector est un système sécurisé. Par conséquent, avant d’accéder à son portail, vous devez faire approuver votre adresse IP publique pour qu’elle figure dans la liste verte de ce système. Pour obtenir votre adresse IP publique, suivez le lien spécifié [ici](https://whatismyipaddress.com/). Envoyez votre adresse IP à l’[équipe du support technique de Meta Networks Connector](mailto:support@metanetworks.com) pour son approbation.

11. Accédez à **Administrator** (Administrateur) et sélectionnez **Settings** (Paramètres).

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/configure3.png)

12. Assurez-vous que les options **Log Internet Traffic** (Journaliser le trafic Internet) et **Force VPN MFA** (Forcer l’authentification multifacteur VPN) sont désactivées.

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/configure1.png)

13. Accédez à **Administrator** (Administrateur) et sélectionnez **SAML** (SAML).

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/configure4.png)

14. Effectuez les étapes suivantes dans la page **DETAILS** (DÉTAILS) :

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/configure2.png)

    a. Copiez la valeur de **SSO URL** (URL SSO) et collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL Meta Networks Connector**.

    b. Copiez la valeur de **Recipient URL** (URL du destinataire) et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Meta Networks Connector**.

    c. Copiez la valeur de **Audience URI (SP Entity ID)** (URI d’audience - ID d’entité SP) et collez-la dans la zone de texte **Identificateur (ID d’entité)** de la section **Domaine et URL Meta Networks Connector**.

    d. Activer SAML

15. Sous l’onglet **GENERAL**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/metanetworksconnector-tutorial/configure5.png)

    a. Dans la zone **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez la valeur de l’**URL du service d’authentification unique SAML** copiée à partir du portail Azure.

    b. Dans la zone **Identity Provider Issuer** (Émetteur du fournisseur d’identité), collez la valeur de l’**ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    c. Dans le bloc-notes, ouvrez le certificat téléchargé à partir du portail Azure et collez-le dans la zone de texte **X.509 Certificate** (Certificat X.509).

    d. Activez l’option **Just-in-Time Provisioning** (Provisionnement juste-à-temps).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/metanetworksconnector-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/metanetworksconnector-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/metanetworksconnector-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/metanetworksconnector-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-meta-networks-connector-test-user"></a>Créer un utilisateur de test Meta Networks Connector

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Meta Networks Connector. Meta Networks Connector prend en charge le provisionnement juste-à-temps, option qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Meta Networks Connector.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique du client Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Meta Networks Connector.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Meta Networks Connector, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Meta Networks Connector**.

    ![Lien Meta Networks Connector dans la liste des applications](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Meta Networks Connector dans le Panneau d’accès doit vous connecter automatiquement à votre application Meta Networks Connector.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

