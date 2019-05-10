---
title: 'Didacticiel : Intégration d’Azure Active Directory à Alibaba Cloud Service (Role-based SSO) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Alibaba Cloud Service (Role-based SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de2f7cb90e004673c59282a8023d55df364220a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140857"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Alibaba Cloud Service (Role-based SSO)

Dans ce tutoriel, vous allez apprendre à intégrer Alibaba Cloud Service (Role-based SSO) à Azure Active Directory (Azure AD).
L’intégration d’Alibaba Cloud Service (Role-based SSO) à Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Alibaba Cloud Service (Role-based SSO).
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Alibaba Cloud Service (Role-based SSO), par le biais de l’authentification unique, avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Alibaba Cloud Service (Role-based SSO), vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement Alibaba Cloud Service (Role-based SSO) avec l’authentification unique activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Alibaba Cloud Service (Role-based SSO) prend en charge l’authentification unique lancée par le **fournisseur d’identité**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Ajout d’Alibaba Cloud Service (Role-based SSO) à partir de la galerie

Pour configurer l’intégration d’Alibaba Cloud Service (Role-based SSO) à Azure AD, vous devez ajouter Alibaba Cloud Service (Role-based SSO), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Alibaba Cloud Service (Role-based SSO) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Alibaba Cloud Service (Role-based SSO)**, sélectionnez **Alibaba Cloud Service (Role-based SSO)** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Alibaba Cloud Service (Role-based SSO) dans la liste des résultats](common/search-new-app.png)

5. Sur la page **Alibaba Cloud Service (Role-based SSO)**, dans le volet de navigation de gauche, cliquez sur **Propriétés**, puis copiez l’**ID d’objet** et enregistrez-le sur votre ordinateur en vue d’une utilisation ultérieure.

    ![Configuration des propriétés](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Alibaba Cloud Service (Role-based SSO) à l’aide d’un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Alibaba Cloud Service (Role-based SSO) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Alibaba Cloud Service (Role-based SSO), vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique en fonction du rôle dans Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Alibaba Cloud Service (Role-based SSO)](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Alibaba Cloud Service (Role-based SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** pour avoir un équivalent de Britta Simon dans Alibaba Cloud Service (Role-based SSO) lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Alibaba Cloud Service (Role-based SSO), effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Alibaba Cloud Service (Role-based SSO)**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    >[!NOTE]
    >Vous obtiendrez les métadonnées du fournisseur de service via cette [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![image](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![image](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la zone de texte de la section Alibaba Cloud Service (Role-based SSO) :

    ![image](common/idp-intiated.png)

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

5. L’application Alibaba Cloud Service (Role-based SSO) s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![image](common/edit-attribute.png)

6. En plus de ce qui précède, l’application Alibaba Cloud Service (Role-based SSO) s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Nom | Espace de noms | Attribut source|
    | ---------------| ------------| --------------- |
    | Rôle | https://www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https://www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Cliquez [ici](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) pour savoir comment configurer un **rôle** dans Azure AD.

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer Alibaba Cloud Service (Role-based SSO)**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurer l’authentification unique en fonction du rôle dans Alibaba Cloud Service

1. Connectez-vous à la [RAM console](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) (console RAM) d’Alibaba Cloud en utilisant le compte Account1.

2. Dans le volet de navigation de gauche, sélectionnez **SSO**.

3. Sous l’onglet **Role-based SSO** (SSO en fonction du rôle), cliquez sur **Create IdP** (Créer un IdP).

4. Sur la page affichée, entrez `AAD` dans le champ Nom de l’IdP, saisissez une description dans le champ **Note**, cliquez sur **Upload** (Charger) pour charger le fichier de métadonnées de fédération que vous avez téléchargé précédemment, puis cliquez sur **OK**.

5. Une fois le fournisseur d’identité correctement créé, cliquez sur **Create RAM Role** (Créer un rôle de RAM).

6. Dans le champ **RAM Role Name** (Nom du rôle de RAM), entrez `AADrole`, sélectionnez `AAD` dans la liste déroulante **Select IdP** (Sélectionner un IdP), puis cliquez sur OK.

    >[!NOTE]
    >Vous pouvez accorder une autorisation au rôle en fonction des besoins. Après avoir créé le fournisseur d’identité et le rôle correspondant, nous vous recommandons d’enregistrer les ARN du fournisseur d’identité et du rôle en vue d’une utilisation ultérieure. Vous pouvez obtenir les ARNs sur la page d’informations sur le fournisseur d’identité et la page d’informations sur le rôle.

7. Associez le rôle de RAM d’Alibaba Cloud (AADrole) à l’utilisateur Azure AD (u2) : Pour associer le rôle de RAM à l’utilisateur Azure AD, vous devez créer un rôle dans Azure AD en procédant comme suit :

    a. Connectez-vous à l’[Afficheur Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Cliquez sur **Modify permissions** (Modifier les autorisations) pour obtenir les autorisations requises pour la création d’un rôle.

    ![Configuration de Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Sélectionnez les autorisations suivantes dans la liste, puis cliquez sur **Modify permissions** (Modifier les autorisations) , comme illustré dans la figure suivante.

    ![Configuration de Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Une fois les autorisations accordées, reconnectez-vous à l’Afficheur Graph.

    d. Dans la page de l’Afficheur Graph, sélectionnez **GET** (OBTENIR) dans la première liste déroulante, puis **beta** (bêta) dans la deuxième. Ensuite, entrez `https://graph.microsoft.com/beta/servicePrincipals` dans le champ en regard des listes déroulantes, puis cliquez sur **Run Query** (Exécuter la requête).

    ![Configuration de Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Si vous utilisez plusieurs répertoires, vous pouvez entrer `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` dans le champ de la requête.

    e. Dans la section **Response Preview** (Aperçu de la réponse), extrayez la propriété appRoles à partir de « Service Principal » (Principal du service) en vue d’un usage ultérieur.

    ![Configuration de Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Vous pouvez localiser la propriété appRoles en entrant `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` dans le champ de la requête. Notez que `objectID` est l’ID d’objet que vous avez copié à partir de la page **Propriétés** d’Azure AD.

    f. Revenez à l’Afficheur Graph, modifiez la méthode **GET** (OBTENIR) en **PATCH** (CORRIGER), collez le contenu suivant dans la section **Request Body** (Corps de la demande), puis cliquez sur **Run Query** (Exécuter la requête) :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` correspond aux ARNs du fournisseur d’identité et du rôle que vous avez créés dans la console RAM. Ici, vous pouvez ajouter plusieurs rôles en fonction des besoins. Azure AD enverra la valeur de ces rôles en tant que valeur de revendication dans la réponse SAML. Cependant, vous ne pouvez ajouter de nouveaux rôles qu’après la partie `msiam_access` de l’opération de correction. Pour faciliter le processus de création, nous vous recommandons d’utiliser un générateur d’ID tels que Générateur de GUID pour générer des ID en temps réel.

    g. Une fois le « Service Principal » (principal du service) corrigé avec le rôle requis, associez le rôle à l’utilisateur Azure AD (u2) en suivant la procédure décrite dans la section **Affecter l’utilisateur de test Azure AD** du didacticiel.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Configurer l’authentification unique d’Alibaba Cloud Service (Role-based SSO)

Pour configurer l’authentification unique côté **Alibaba Cloud Service (Role-based SSO)**, vous devez envoyer le fichier **XML des métadonnées de fédération** téléchargé et les URL copiées correspondantes du portail Azure à l’[équipe du support technique Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Alibaba Cloud Service (Role-based SSO).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Alibaba Cloud Service (Role-based SSO)**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Alibaba Cloud Service (Role-based SSO)**.

    ![Lien Alibaba Cloud Service (Role-based SSO) dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Sous l’onglet **Utilisateurs et groupes**, sélectionnez u2 dans la liste des utilisateurs, puis cliquez sur **Sélectionner**. Cliquez ensuite sur **Affecter**.

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Affichez le rôle affecté et testez Alibaba Cloud Service (Role-based SSO).

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Une fois l’utilisateur (u2) affecté, le rôle créé est automatiquement associé à l’utilisateur. Si vous avez créé plusieurs rôles, vous devez associer le rôle approprié à l’utilisateur en fonction des besoins. Si vous souhaitez implémenter l’authentification unique en fonction du rôle d’Azure AD pour plusieurs comptes Alibaba Cloud, répétez les étapes précédentes.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Créer un utilisateur de test Alibaba Cloud Service (Role-based SSO)

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Alibaba Cloud Service (Role-based SSO). Collaborez avec[l’équipe du support technique d’Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/) pour ajouter les utilisateurs à la plateforme Alibaba Cloud Service (Role-based SSO). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Une fois les configurations ci-dessus terminées, testez Alibaba Cloud Service (Role-based SSO) en procédant comme suit :

1. Dans le portail Azure, accédez à la page **Alibaba Cloud Service (Role-based SSO)**, sélectionnez **Single sign-on** (Authentification unique), puis cliquez sur **Test** (Tester).

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Cliquez sur **Se connecter en tant qu’utilisateur actuel**.

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Dans la page de sélection de compte, choisissez u2.

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. La page suivante s’affiche, indiquant que l’authentification unique en fonction du rôle a réussi.

    ![Configuration de test](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

