---
title: 'Tutoriel : Intégration d’Azure Active Directory à SanSan | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SanSan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 6bd84e42bf46885a9dced363724538cbd4d2066a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815002"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Tutoriel : Intégration d’Azure Active Directory à SanSan

Dans ce didacticiel, vous allez apprendre à intégrer SanSan avec Azure Active Directory (Azure AD).

L’intégration de SanSan avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à SanSan.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SanSan (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SanSan, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SanSan pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SanSan à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sansan-from-the-gallery"></a>Ajout de SanSan à partir de la galerie
Pour configurer l’intégration de SanSan à Azure AD, vous devez ajouter SanSan à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter SanSan à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **SanSan**.

    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. Dans le volet de résultats, sélectionnez **SanSan**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SanSan sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SanSan équivalent à l’utilisateur dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur SanSan associé.

Dans SanSan, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec SanSan, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SanSan](#creating-a-sansan-test-user)** pour avoir un équivalent de Britta Simon dans SanSan, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SanSan.

**Pour configurer l’authentification unique Azure AD avec SanSan, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **SanSan**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Dans la section **Domaine et URL SanSan**, procédez comme suit :

    ![Configurer l'authentification unique](./media/sansan-tutorial/tutorial_sansan_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : 
    
    | Environnement | URL |
    |:--- |:--- |
    | Web PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Application mobile native |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Paramètres de navigateur mobile |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Remplacez ces valeurs par l’URL de connexion réelle. Pour obtenir ces valeurs, contactez l’[équipe de support technique SanSan](https://www.sansan.com/form/contact). 
     
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/sansan-tutorial/tutorial_general_400.png)

6. L’application Sansan attend plusieurs **Identificateurs** et **URL de réponse** pour prendre en charge des environnements multiples (web pour PC, application mobile native, paramètres de navigateur mobile), configurables au moyen de scripts PowerShell. Les étapes à suivre sont détaillées ci-dessous.

7. Pour configurer plusieurs **Identificateurs** et **URL de réponse** pour l’application Sansan avec un script PowerShell, suivez les étapes ci-dessous :

    ![Configurer l’obj d’authentification unique](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Accédez à la page **Propriétés** de l’application **Sansan**, puis copiez **l’ID objet** à l’aide du bouton **Copier** et collez-le dans le Bloc-notes.

    b. **L’ID objet** que vous avez copié sur le Portail Azure servira de **ServicePrincipalObjectId** dans le script PowerShell utilisé dans la suite de ce tutoriel. 

    c. Ouvrez maintenant une invite de commandes Windows PowerShell avec élévation de privilèges.
    
    >[!NOTE] 
    > Vous devez installer le module AzureAD (utilisez la commande `Install-Module -Name AzureAD`). Si vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

    d. Exécutez `Connect-AzureAD` et connectez-vous avec un compte d’utilisateur Administrateur général.

    e. Pour mettre à jour plusieurs URL sur une application, utilisez le script suivant :

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Une fois l’exécution du script PowerShell terminée, le résultat se présentera de la façon suivante. Les valeurs d’URL seront mises à jour, mais ne seront pas répercutées sur le Portail Azure. 

    ![Configurer le script d’authentification unique](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Dans la section **Configuration de SanSan**, cliquez sur **Configurer SanSan** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Pour configurer l’authentification unique côté **SanSan**, vous devez envoyer le **Certificat**, l’**URL de déconnexion**, l’**ID d’entité SAML** et l’**URL du service d’authentification unique SAML** téléchargés à l’[équipe de support technique SanSan](https://www.sansan.com/form/contact). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

>[!NOTE]
>Les paramètres de navigateur PC fonctionnent également avec l’application mobile et le navigateur mobile avec Web PC. 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-sansan-test-user"></a>Création d’un utilisateur de test SanSan

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Sansan. L’utilisateur doit faire l’objet d’une attribution dans l’application Sansan pour pouvoir bénéficier de l’authentification unique. 

>[!NOTE]
>Si vous avez besoin de créer un utilisateur manuellement ou un groupe d’utilisateurs, vous devez contacter l’[équipe de support technique SanSan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SanSan.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SanSan, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SanSan**.

    ![Configurer l'authentification unique](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SanSan dans le volet d’accès, vous devez être connecté automatiquement à votre application SanSan.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

