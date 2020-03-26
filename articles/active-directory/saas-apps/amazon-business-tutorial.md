---
title: 'Tutoriel : Intégration d’Azure Active Directory à Amazon Business | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496564"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutoriel : Intégrer Amazon Business à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer Amazon Business à Azure Active Directory (Azure AD). Quand vous intégrez [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Amazon Business.
* Permettre à vos utilisateurs de se connecter automatiquement à Amazon Business avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un essai gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Amazon Business pour lequel l’authentification unique (SSO) est activée Accédez à la page [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) pour créer un compte Amazon Business.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un compte Amazon Business existant.

* Amazon Business prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* Amazon Business prend en charge le provisionnement d’utilisateurs **Juste-à-temps**

## <a name="adding-amazon-business-from-the-gallery"></a>Ajout d’Amazon Business à partir de la galerie

Pour configurer l’intégration d’Amazon Business à Azure AD, vous devez ajouter Amazon Business depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Amazon Business** dans la zone de recherche.
1. Sélectionnez **Amazon Business** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Amazon Business pour un utilisateur de test nommé **B.Simon**.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Business, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Amazon Business](#configure-amazon-business-sso)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Amazon Business](#create-amazon-business-test-user)** pour avoir un équivalent de B.Simon dans Amazon Business lié à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Amazon Business**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer en mode lancé par le **fournisseur d’identité**, suivez les étapes ci-dessous :

    1. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une URL en utilisant un des modèles suivants :
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant un des modèles suivants :
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Vous obtenez la valeur `<idpid>` de la section de configuration de l’authentification unique Amazon Business, qui est expliquée plus loin dans le tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL : `https://www.amazon.com/`

1. La capture d’écran suivante montre la liste des attributs par défaut. Modifiez les attributs en cliquant sur l’icône **Modifier** dans la section **Attributs et revendications de l’utilisateur**.

    ![Attributs](media/amazon-business-tutorial/map-attribute3.png)

1. Éditez les attributs et copiez la valeur de **Espace de noms** de ces attributs dans le Bloc-notes.

    ![Attributs](media/amazon-business-tutorial/map-attribute4.png)

1. En plus de ce qui précède, l’application Amazon Business s’attend à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Dans la section **Attributs et revendications de l’utilisateur** de la boîte de dialogue **Revendications de groupe** , effectuez les étapes suivantes :

    a. Cliquez sur le **stylo** en regard de **Groupes renvoyés dans la revendication**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Sélectionnez **Tous les groupes** dans la liste d’options.

    c. Sélectionnez **ID de groupe** comme **Attribut de la source**.

    d. Cochez la case **Personnaliser le nom de la revendication de groupe** et entrez le nom du groupe en fonction des besoins de votre organisation.

    e. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML des métadonnées**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Amazon Business**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configurer l’authentification unique Amazon Business

1. Dans une autre fenêtre de navigateur web, connectez-vous au site Amazon Business de votre entreprise en tant qu’administrateur.

1. Cliquez sur **User profile** (Profil utilisateur) et sélectionnez **Business Settings** (Paramètres de l’entreprise).

    ![Profil utilisateur](media/amazon-business-tutorial/user-profile.png)

1. Dans l’Assistant **System integrations** (Intégrations système), sélectionnez **Single Sign-On (SSO)** (Authentification unique).

    ![Authentification unique (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Dans l’Assistant **Set up SSO** (Configurer l’authentification unique), sélectionnez le fournisseur en fonction des exigences de votre organisation, puis cliquez sur **Next** (Suivant).

    ![Groupe par défaut](media/amazon-business-tutorial/default-group1.png)

1. Dans l’Assistant **New user account defaults** (Valeurs par défaut pour les nouveaux comptes d’utilisateur), sélectionnez le **Default Group** (Groupe par défaut), puis sélectionnez **Default Buying Role** (Rôle d’achat par défaut) en fonction du rôle de l’utilisateur dans votre organisation, puis cliquez sur **Next** (Suivant).

    ![Groupe par défaut](media/amazon-business-tutorial/dafault-group2.png)

1. Dans l’Assistant **Upload your metadata file** (Charger votre fichier de métadonnées), cliquez sur **Browse** (Parcourir) pour charger le fichier **Metadata XML** (XML de métadonnées) que vous avez téléchargé à partir du portail Azure, puis cliquez sur **Upload** (Télécharger).

    ![Données de connexion](media/amazon-business-tutorial/connection-data1.png)

1. Une fois que vous avez chargé le fichier de métadonnées téléchargé, les champs de la section **Connection data** (Données de connexion) sont renseignés automatiquement. Après cela, cliquez sur **Next** (Suivant).

    ![Données de connexion](media/amazon-business-tutorial/connection-data2.png)

1. Dans l’Assistant **Upload your Attribute statement** (Charger votre déclaration d’attributs), cliquez sur **Skip** (Ignorer).

    ![Attributs](media/amazon-business-tutorial/map-attribute1.png)

1. Dans l’Assistant **Attribute mapping** (Mappage d’attributs), ajoutez les champs des spécifications en cliquant sur l’option **+ Add a field** (+ Ajouter un champ). Ajoutez les valeurs des attributs en y incluant l’espace de noms que vous avez copié depuis la section **Attributs et revendications de l’utilisateur** du portail Azure dans le champ **SAML AttributeName** (Nom d’attribut SAML), puis cliquez sur **Next** (Suivant).

    ![Attributs](media/amazon-business-tutorial/map-attribute2.png)

1. Dans l’Assistant **Amazon connection data** (Données de connexion Amazon), cliquez sur **Next** (Suivant).

    ![Connexion](media/amazon-business-tutorial/amazon-connect.png)

1. Vérifiez l’**État** des étapes qui ont été configurées, puis cliquez **Start testing** (Démarrer le test).

    ![Connexion](media/amazon-business-tutorial/sso-connection1.png)

1. Dans l’Assistant **Test SSO Connection** (Tester la connexion d’authentification unique), cliquez sur **Test** (Tester).

    ![Connexion](media/amazon-business-tutorial/sso-connection2.png)

1. Dans l’Assistant **IDP initiated URL** (URL lancée par le fournisseur d’identité), avant de cliquer sur **Activate** (Activer), copiez la valeur qui est affectée à **idpid** et collez-la dans le paramètre **idpid** de **URL de réponse** dans la section **Configuration SAML de base** du portail Azure.

    ![Connexion](media/amazon-business-tutorial/sso-connection3.png)

1. Dans l’Assistant **Are you ready to switch to active SSO?** (Êtes-vous prêt à passer à l’authentification unique ?), cochez la case **I have fully tested SSO and am ready to go live** (J’ai entièrement testé l’authentification unique et je suis prêt à l’activer), puis cliquez sur **Switch to active** (Passer à l’activation).

    ![Connexion](media/amazon-business-tutorial/sso-connection4.png)

1. Enfin, dans la section **SSO Connection details** (Détails de la connexion SSO), l’**État** apparaît comme étant **Active** (Actif).

    ![Connexion](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

> [!NOTE]
> Si nécessaire, les administrateurs doivent créer les utilisateurs de test dans leur locataire. Les étapes suivantes montrent comment créer un utilisateur de test.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Créer un groupe de sécurité Azure Active Directory dans le portail Azure

1. Cliquez sur **Azure Active Directory > Tous les groupes**.

    ![Créer un groupe de sécurité Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Cliquez sur **Nouveau groupe**.

    ![Créer un groupe de sécurité Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Complétez les champs **Type de groupe**, **Nom du groupe**, **Description du groupe**, **Type d’appartenance**. Cliquez sur la flèche pour sélectionner des membres, puis recherchez le membre que vous souhaitez ajouter au groupe ou cliquez dessus. Cliquez sur **Sélectionner** pour ajouter les membres sélectionnés, puis cliquez sur **Créer**.

    ![Créer un groupe de sécurité Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Amazon Business.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Amazon Business**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

    >[!NOTE]
    > Si vous n’affectez pas les utilisateurs dans Azure AD, vous recevez l’erreur suivante.

    ![Lien Ajouter un utilisateur](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Affecter un groupe de sécurité Azure Active Directory dans le portail Azure

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Amazon Business**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Amazon Business**.

    ![Lien Amazon Business dans la liste Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le **Ajouter un utilisateur**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Recherchez le groupe de sécurité à utiliser, puis cliquez sur le groupe pour l’ajouter à la section Sélectionner des membres. Cliquez sur **Sélectionner**, puis sur **Affecter**.

    ![Recherche de groupe de sécurité](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Cochez les notifications dans la barre de menus pour être informé que le groupe a été correctement affecté à l’Application d’entreprise dans le portail Azure.

### <a name="create-amazon-business-test-user"></a>Créer un utilisateur de test Amazon Business

Dans cette section, un utilisateur appelé B.Simon est créé dans Amazon Business. Amazon Business prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Amazon Business, un utilisateur est créé après l’authentification.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Amazon Business dans le panneau d’accès doit vous connecter automatiquement à l’application Amazon Business pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
