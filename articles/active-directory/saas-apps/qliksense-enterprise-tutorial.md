---
title: 'Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d5824708f6a99d0222ef5e236758b78a7eedafb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882176"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Qlik Sense Enterprise à Azure Active Directory (Azure AD).
L’intégration du logiciel Qlik Sense Enterprise à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Qlik Sense Enterprise.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Qlik Sense Enterprise (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Qlik Sense Enterprise, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Qlik Sense Enterprise pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Qlik Sense Enterprise prend en charge l’authentification unique initiée par le **fournisseur de services**.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Ajout de Qlik Sense Enterprise à partir de la galerie

Pour configurer l’intégration de Qlik Sense Enterprise à Azure AD, vous devez ajouter Qlik Sense Enterprise depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Qlik Sense Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Qlik Sense Enterprise**, sélectionnez **Qlik Sense Enterprise** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Qlik Sense Enterprise dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Qlik Sense Enterprise associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Qlik Sense Enterprise](#configure-qlik-sense-enterprise-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** pour avoir un équivalent de Britta Simon dans Qlik Sense Enterprise, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Qlik Sense Enterprise, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Qlik Sense Enterprise**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL Qlik Sense Enterprise](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL d’authentification, l’identificateur et l’URL de réponse réels décrits plus loin dans ce tutoriel, ou contactez le [support technique de Qlik Sense Enterprise](https://www.qlik.com/us/services/support) pour obtenir ces valeurs.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Configurer l’authentification unique pour Qlik Sense Enterprise

1. Préparez le fichier XML de métadonnées de fédération afin de le charger vers le serveur Qlik Sense.

    > [!NOTE]
    > Avant de charger les métadonnées IdP vers le serveur Qlik Sense, vous devez modifier le fichier et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense.

    ![QlikSense][qs24]

    a. Ouvrez le fichier FederationMetaData.xml téléchargé à partir du portail Azure dans un éditeur de texte.

    b. Recherchez la valeur **RoleDescriptor**.  Il en existe quatre entrées (deux paires de balises d’élément d’ouverture et de fermeture).

    c. Dans le fichier, supprimez les balises RoleDescriptor et toutes les informations insérées entre celles-ci.

    d. Enregistrez le fichier et conservez-le pour l’utiliser ultérieurement dans ce document.

2. Accédez à la console de gestion Qlik (QCM) de Qlik Sense en tant qu’utilisateur pouvant créer des configurations de proxy virtuel.

3. Dans la console QMC, cliquez sur l’élément de menu **Proxys virtuels**.

    ![QlikSense][qs6]

4. En bas de l’écran, cliquez sur le bouton **Créer nouveau**.

    ![QlikSense][qs7]

5. L’écran Virtual proxy edit (Modification du proxy virtuel) s’affiche.  Un menu permettant de rendre les options de configuration visibles est affiché sur le côté droit de l’écran.

    ![QlikSense][qs9]

6. L’option de menu Identification étant sélectionnée, entrez les informations d’identification pour la configuration du proxy virtuel Azure.

    ![QlikSense][qs8]  

    a. Le champ **Description** contient un nom convivial pour la configuration du proxy virtuel.  Entrez une valeur pour la description.

    b. Le champ **Préfixe** identifie le point de terminaison proxy virtuel pour la connexion à Qlik Sense avec l’authentification unique Azure AD.  Entrez un nom de préfixe unique pour ce proxy virtuel.

    c. La valeur **Session inactivity timeout** (Délai d’inactivité de session) (minutes) représente le délai d’expiration des connexions via ce proxy virtuel.

    d. La zone **Session cookie header name** (Nom d’en-tête de cookie de session) est le nom du cookie stockant l’identificateur de session pour la session Qlik Sense qu’un utilisateur reçoit après une authentification réussie.  Ce nom doit être unique.

7. Cliquez sur l’option de menu Authentification pour la rendre visible.  L’écran Authentification s’affiche.

    ![QlikSense][qs10]

    a. La liste déroulante **Anonymous access mode (Mode d’accès anonyme)** détermine si des utilisateurs anonymes peuvent accéder à Qlik Sense via le proxy virtuel.  L’option par défaut est No anonymous user (Pas d’utilisateur anonyme).

    b. La liste déroulante **Méthode d’authentification** détermine le schéma d’authentification qui sera utilisé par le proxy virtuel.  Dans la liste déroulante, sélectionnez SAML.  À la suite de cette sélection, d’autres options s’affichent.

    c. Dans le champ **SAML host URI**(URI d’hôte SAML), indiquez le nom d’hôte que les utilisateurs doivent entrer pour accéder à Qlik Sense via ce proxy virtuel SAML.  Le nom d’hôte est l’URI du serveur Qlik Sense.

    d. Dans le champ **SAML entity ID (ID d’entité SAML)**, entrez la valeur indiquée dans le champ SAML host URI (URI d’hôte SAML).

    e. La zone **SAML IdP metadata** (Métadonnées IdP SAML) indique le fichier modifié précédemment dans la section **Modifier les métadonnées de fédération dans la configuration d’Azure AD**.  **Avant de charger les métadonnées IdP, vous devez modifier le fichier** et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense.  **Reportez-vous aux instructions ci-dessus si le fichier doit encore être modifié.**  Si le fichier a été modifié, cliquez sur le bouton Parcourir et sélectionnez le fichier de métadonnées modifié pour le charger vers la configuration du proxy virtuel.

    f. Entrez le nom d’attribut ou la référence de schéma pour l’attribut SAML représentant **l’ID utilisateur** qu’Azure AD envoie au serveur Qlik Sense.  Les informations de référence de schéma sont disponibles dans les écrans de l’application Azure post-configuration.  Pour utiliser le nom d’attribut, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Entrez la valeur du **répertoire utilisateur** qui est attaché aux utilisateurs lorsqu’ils s’authentifient sur le serveur Qlik Sense via Azure AD.  Les valeurs codées en dur doivent être entourées de **crochets []**.  Pour utiliser un attribut envoyé dans l’assertion SAML Azure AD, entrez son nom dans cette zone de texte **sans** crochets.

    h. **L’algorithme de signature SAML** définit la signature du certificat du fournisseur de service (dans ce cas le serveur Qlik Sense) pour la configuration du proxy virtuel.  Si le serveur Qlik Sense utilise un certificat approuvé généré à l’aide de Microsoft Enhanced RSA and AES Cryptographic Provider, définissez l’algorithme de signature SAML sur **SHA-256**.

    i. La section SAML attribute mapping (Mappage d’attributs SAML) permet d’envoyer des attributs supplémentaires, comme des groupes, vers Qlik Sense pour les utiliser dans les règles de sécurité.

8. Cliquez sur l’option de menu **ÉQUILIBRAGE DE CHARGE** pour la rendre visible.  L’écran Équilibrage de charge s’affiche.

    ![QlikSense][qs11]

9. Cliquez sur le bouton **Add new server node** (Ajouter un nouveau nœud serveur), sélectionnez un ou plusieurs nœuds de moteur auxquels Qlik Sense enverra des sessions à des fins d’équilibrage de charge, puis cliquez sur le bouton **Ajouter**.

    ![QlikSense][qs12]

10. Cliquez sur l’option de menu Avancé pour la rendre visible. L’écran Avancé s’affiche.

    ![QlikSense][qs13]

    La liste des hôtes approuvés identifie les noms d’hôte qui sont acceptés lors de la connexion au serveur Qlik Sense.  **Entrez le nom d’hôte spécifié par les utilisateurs pour se connecter au serveur Qlik Sense.**  Le nom d’hôte est défini sur la même valeur que l’URI d’hôte SAML sans https://.

11. Cliquez sur le bouton **Appliquer**.

    ![QlikSense][qs14]

12. Cliquez sur OK pour accepter le message d’avertissement indiquant que les proxys liés au proxy virtuel vont être redémarrés.

    ![QlikSense][qs15]

13. Sur le côté droit de l’écran, le menu Éléments associés s’affiche.  Cliquez sur l’option de menu **Proxys**.

    ![QlikSense][qs16]

14. L’écran Proxy s’affiche.  Cliquez sur le bouton **Lier** en bas de l’écran pour lier un proxy au proxy virtuel.

    ![QlikSense][qs17]

15. Sélectionnez le nœud proxy qui prendra en charge cette connexion de proxy virtuel et cliquez sur le bouton **Lier**.  Après l’établissement du lien, le proxy est répertorié dans les proxys associés.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Après environ cinq à dix secondes, le message Refresh QMC (Actualiser la console QMC) s’affiche.  Cliquez sur le bouton **Refresh QMC** (Actualiser la console QMC).

    ![QlikSense][qs20]

17. Lors de l’actualisation de la console QMC, cliquez sur l’élément de menu **Proxys virtuels**. La nouvelle entrée de proxy virtuel SAML est indiquée dans le tableau affiché à l’écran.  Cliquez sur l’entrée de proxy virtuel.

    ![QlikSense][qs51]

18. En bas de l’écran, le bouton Download SP metadata (Télécharger les métadonnées du fournisseur de service) est activé.  Cliquez sur le bouton **Download SP metadata** (Télécharger les métadonnées du fournisseur de service) pour enregistrer les métadonnées dans un fichier.

    ![QlikSense][qs52]

19. Ouvrez le fichier de métadonnées du fournisseur de service.  Observez les entrées **entityID** et **AssertionConsumerService**.  Ces valeurs sont équivalentes à celles des champs **Identificateur**, **URL de connexion** et **URL de réponse** dans la configuration d’application Azure AD. Collez ces valeurs dans la section **Domaine et URL Qlik Sense Enterprise** de la configuration de l’application Azure AD si elles ne sont pas identiques. Vous devez ensuite les remplacer dans l’assistant de configuration de l’application Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Qlik Sense Enterprise.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Qlik Sense Enterprise**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, saisissez et sélectionnez **Qlik Sense Enterprise**.

    ![Dans la liste des applications, sélectionnez Qlik Sense Enterprise.](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-qlik-sense-enterprise-test-user"></a>Créer un utilisateur de test Qlik Sense Enterprise

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Qlik Sense Enterprise. Collaborez avec l’ [équipe du support technique Qlik Sense Enterprise](https://www.qlik.com/us/services/support) pour ajouter les utilisateurs à la plateforme Qlik Sense Enterprise. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Qlik Sense Enterprise dans le volet d’accès, vous devez être connecté automatiquement à votre application Qlik Sense Enterprise pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

