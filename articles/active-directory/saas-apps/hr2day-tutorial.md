---
title: 'Didacticiel : Intégration d’Azure Active Directory avec HR2day by Merces | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HR2day by Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e932fd59fb888d73fb2ad9aee3f440b4aca3e662
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180571"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutoriel : Intégration d’Azure Active Directory avec HR2day by Merces

Dans ce didacticiel, vous allez apprendre à intégrer HR2day by Merces à Azure Active Directory (Azure AD).

L’intégration de HR2day dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HR2day by Merces
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HR2day by Merces avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec HR2day by Merces, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement HR2day by Merces pour lequel l’authentification unique est activée

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Obtenez [un mois d’évaluation gratuite d’Azure AD](https://azure.microsoft.com/pricing/free-trial/) si vous n’y avez pas encore accès.  

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ici se compose des deux sections principales suivantes :

1. Ajout de HR2day by Merces à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Ajouter HR2day by Merces à partir de la galerie
Pour configurer l’intégration de HR2day by Merces avec Azure AD, vous devez ajouter HR2day by Merces à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HR2day by Merces à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le panneau de navigation de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **HR2day by Merces**.

    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. Dans le panneau de résultats, sélectionnez **HR2day by Merces**, puis sélectionnez le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec HR2day by Merces avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HR2day by Merces équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur HR2day by Merces associé.

Dans HR2day by Merces, affectez le **nom d’utilisateur** dans Azure AD comme **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec HR2day by Merces, vous devez suivre les indications des sections suivantes :

1. Configurer l'authentification unique Azure AD : Autorisez les utilisateurs à utiliser cette fonctionnalité.
1. Créer un utilisateur de test Azure AD : Testez l’authentification unique Azure AD avec Britta Simon.
1. Créer un utilisateur test HR2day by Merces : Créez un équivalent de Britta Simon dans HR2day by Merces lié à la représentation de l’utilisateur Azure AD.
1. Affecter l'utilisateur de test Azure AD : Activez Britta Simon afin qu’elle puisse utiliser l’authentification unique Azure AD.
1. Tester l'authentification unique : Vérifiez si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application HR2day by Merces.

**Pour configurer l’authentification unique Azure AD avec HR2day by Merces, effectuez les étapes suivantes :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **HR2day by Merces**, sélectionnez **Authentification unique**.

    ![Configurer l’authentification unique][4]

1. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, sélectionnez **Mode** comme **Authentification basée sur SAML**.
 
    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. Dans la section **Domaine et URL HR2day by Merces**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<tenantname>.force.com/<instancename>`.

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl). 
 


1. Dans la section **Certificat de signature SAML**, sélectionnez **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. Cette section décrit comment permettre aux utilisateurs de s’authentifier sur HR2day by Merces avec leur compte Azure AD. Pour cela, ils utilisent la fédération basée sur le protocole SAML.

    Votre application HR2day by Merces attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre jeton SAML. La capture d’écran suivante présente un exemple de cette opération. 

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Avant de pouvoir configurer votre assertion SAML, vous devez contacter [l’équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl) et lui demander d’affecter la valeur d’attribut d’identificateur unique à votre locataire. Vous avez besoin de cette valeur pour exécuter les étapes de la section suivante. 

1. Dans la boîte de dialogue **Authentification unique**, dans la section **Attributs utilisateur**, configurez l’attribut de jeton SAML comme illustré ci-dessous. Ensuite, effectuez les étapes suivantes.
    
      | Nom de l’attribut    |   Valeur de l’attribut |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Pour ouvrir la boîte de dialogue **Ajouter un attribut**, cliquez sur **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Nom**, tapez **ATTR_LOGINCLAIM**.

    c. Dans la liste **Valeur**, sélectionnez **Join()**.

    d. Dans la liste **String1**, sélectionnez **User.mail**.

    e. Dans **String2**, tapez l’identificateur unique fourni par votre équipe HR2day.

    f. Dans la zone **Séparateur**, tapez **\@**.
    
    g. Sélectionnez **OK**.

1. Sélectionnez le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_general_400.png)

1. Dans la section**Configuration de HR2day by Merces**, sélectionnez **Configurer HR2day by Merces** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL de déconnexion**, l’**ID d’entité SAML** et l’**URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Pour configurer l’authentification unique pour votre application, contactez l’[équipe du support client HR2day by Merces](mailTo:servicedesk@merces.nl). Joignez le fichier **Certificat (Base64)** téléchargé à votre e-mail. Indiquez également l’**URL de déconnexion**, l’**ID de l’entité SAML** et l’**URL du service d’authentification unique SAML**, afin de pouvoir les configurer pour l’intégration de l’authentification unique.

    > [!NOTE]
    >N’oubliez pas d’indiquer à l’équipe de Merces que cette intégration nécessite la définition de l’ID d’entité à l’aide du modèle **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**. Ensuite, accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation de gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur**, tapez l’**adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez le mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Créer un utilisateur test HR2day by Merces

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HR2day by Merces. Pour ajouter des utilisateurs dans le compte HR2day, collaborez avec l’[équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support client HR2day by Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HR2day by Merces.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à HR2day by Merces, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, puis accédez à **Applications d’entreprise**. Ensuite, sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **HR2day by Merces**.

    ![Configurer l’authentification unique](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste **Utilisateurs**, sélectionnez **Britta Simon**.

1. Cliquez sur le bouton **Sélectionner**.

1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Quand vous sélectionnez la vignette HR2day by Merces dans le volet d’accès, vous êtes connecté automatiquement à votre application HR2day by Merces.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur les procédures d’intégration des applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

