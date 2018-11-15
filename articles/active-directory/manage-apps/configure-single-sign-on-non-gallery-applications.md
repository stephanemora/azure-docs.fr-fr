---
title: Configurer l’authentification unique Azure AD pour les applications | Microsoft Docs
description: Découvrez comment connecter les applications à Azure Active Directory à l'aide de SAML et de l'authentification unique basée sur le mot de passe en libre-service
services: active-directory
author: barbkess
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2018
ms.author: barbkess
ms.reviewer: asmalser,luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29a2be1735561d31f934f1353a336b5e4d198227
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622191"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurer l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory

Cet article concerne une fonctionnalité permettant aux administrateurs de configurer l’authentification unique pour les applications qui ne figurent pas dans la galerie d’applications Azure Active Directory, *sans écrire de code*. Cette fonctionnalité a été publiée à partir de la version d’évaluation technique le 18 novembre 2015 et est incluse dans [Azure Active Directory Premium](../fundamentals/active-directory-whatis.md). Si vous recherchez plutôt des instructions destinées aux développeurs sur l’intégration d’applications personnalisées avec Azure AD grâce au code, consultez [Scénarios d’authentification pour Azure AD](../develop/authentication-scenarios.md).

La galerie d'applications Azure Active Directory contient une liste d'applications qui prennent en charge une forme d'authentification unique avec Azure Active Directory, conformément à la description dans [cet article](what-is-single-sign-on.md). Une fois que vous (spécialiste informatique ou intégrateur système de votre organisation) avez trouvé l’application que vous voulez connecter, vous pouvez commencer par suivre les instructions détaillées présentées dans le portail Azure pour activer l’authentification unique.

Les clients disposant d’une licence [Azure Active Directory Premium](../fundamentals/active-directory-whatis.md) obtiennent également ces fonctionnalités supplémentaires :

* Intégration libre-service de toute application prenant en charge les fournisseurs d’identité SAML 2.0 (Initiée par le fournisseur de services ou par le fournisseur d’identité fédérée)
* Intégration libre-service de toute application Web dont la page de connexion est basée sur le HTML et utilise une [authentification unique par mot de passe](what-is-single-sign-on.md#password-based-sso)
* Connexion libre-service des applications qui utilisent le protocole SCIM pour l'affectation d'utilisateurs ([description ici](use-scim-to-provision-users-and-groups.md))
* Possibilité d'ajouter des liens à n'importe quelle application dans le [Lanceur d'application Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](what-is-single-sign-on.md#linked-sso)

Ceci peut inclure non seulement les applications SaaS que vous utilisez, mais qui n'ont pas encore été embarquées dans la galerie d'applications Azure AD, mais encore les applications Web tierces que votre organisation a déployées sur les serveurs que vous contrôlez, dans le cloud ou localement.

Ces fonctionnalités, également appelées *modèles d’intégration d’application*, fournissent des points de connexion basés sur des normes pour les applications prenant en charge l’authentification SCIM, SAML ou par formulaire, et incluent des paramètres et options flexibles pour la compatibilité avec un grand nombre d’applications. 

## <a name="adding-an-unlisted-application"></a>Ajout d’une application non répertoriée
Pour connecter une application à l’aide d’un modèle d’intégration d’application, connectez-vous au Portail Azure avec votre compte Administrateur Azure Active Directory. Accédez à la section **Active Directory > Applications d’entreprise > Nouvelle application > Application extérieure à la galerie**, sélectionnez **Ajouter**, puis **Ajouter une application à partir de la galerie**.

  ![Ajouter l’application](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

Dans la galerie d’applications, vous pouvez ajouter une application non répertoriée en sélectionnant la vignette **Application ne figurant pas dans la galerie** affichée dans les résultats de recherche si vous n’avez pas trouvé l’application souhaitée. Après avoir entré un nom pour votre application, vous pouvez configurer les options d'authentification unique et le comportement. 

**Astuce** : comme meilleure pratique, utilisez la fonction de recherche pour vérifier si l'application existe déjà dans la galerie d'applications. Si elle est trouvée et que sa description mentionne l’authentification unique, c’est qu’elle prend déjà en charge l’authentification unique fédérée.

  ![Recherche](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Une application ajoutée de cette manière offre une expérience similaire à des applications préintégrées. Pour commencer, sélectionnez **Configurer l’authentification unique** ou cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application. L’écran ci-dessous présente les options de configuration de l’authentification unique. Celles-ci sont décrites dans les sections suivantes de cet article.
  
![Options de configuration](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Authentification unique SAML
Sélectionnez cette option pour configurer l'authentification basée SAML pour l'application. Il est nécessaire que l’application prenne en charge SAML 2.0. Il est recommandé de recueillir des informations sur la façon d’utiliser les fonctionnalités SAML de l’application avant de continuer. Lisez les sections suivantes pour configurer l’authentification unique entre l’application et Azure AD.

### <a name="enter-basic-saml-configuration"></a>Entrer la configuration SAML de base

Pour configurer Azure AD, entrez la configuration SAML de base. Vous pouvez entrer manuellement les valeurs ou charger un fichier de métadonnées pour extraire la valeur des champs.

  ![URL et domaine Litware](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **URL de connexion (initiée par le fournisseur de services)** : où l’utilisateur se connecte à cette application. Si l’application est configurée pour effectuer une authentification unique initiée par le fournisseur de services, lorsque l’utilisateur accède à cette URL, le fournisseur de services effectue la redirection nécessaire vers Azure AD pour effectuer l’authentification et connecter l’utilisateur. Si ce champ est renseigné, Azure AD utilise cette URL pour lancer l’application à partir d’Office 365 et du panneau d’accès Azure AD. Si ce champ est omis, Azure AD effectuera une authentification initiée par le fournisseur d’identité lorsque l’application sera lancée à partir d’Office 365, du panneau d’accès Azure AD ou de l’URL d’authentification unique d’Azure AD (qui peut être copiée dans l’onglet Tableau de bord).
- **Identificateur** : doit identifier de façon unique l’application pour laquelle l’authentification unique est en cours de configuration. Cette valeur correspond à l’élément Émetteur dans la demande SAML AuthRequest envoyée par l’application. Cette valeur apparaît également comme **l’ID d’entité** dans les métadonnées SAML fournies par l’application. Consultez la documentation SAML de l’application pour plus d’informations sur ce que sont les valeurs d’ID d’entité ou d’audience. 

    Voici un exemple d’identificateur ou d’émetteur apparaissant dans la demande SAML envoyée par l’application à Azure AD :

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **URL de réponse** : l’URL de réponse est l’adresse où l’application attend le jeton SAML. Elle est aussi appelée URL ACS (Assertion Consumer Service). Consultez la documentation SAML de l’application pour plus d’informations sur ce que sont les jetons de réponse SAML de l’URL ou de l’URL ACS. 

    Pour configurer plusieurs replyURL, vous pouvez utiliser le script PowerShell suivant.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Pour plus d’informations, voir [Demandes et réponses d’authentification SAML 2.0 prises en charge par Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Examiner ou personnaliser les revendications émises dans le jeton SAML

Lorsqu'un utilisateur s'authentifie auprès de l'application, Azure AD émet un jeton SAML à l'application qui contient des informations (ou des revendications) sur l'utilisateur qui l'identifient de façon unique. Par défaut, ceci inclut le nom d'utilisateur, l'adresse e-mail, le prénom et nom de l'utilisateur. 

Vous pouvez afficher ou modifier les revendications envoyées à l'application dans le jeton SAML sous l'onglet **Attributs** .

  ![Attributs](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Vous pourrez avoir à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :

- L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication.
- Votre application a été déployée d’une manière qui nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur (nom d’utilisateur principal) stocké dans Azure Active Directory. 

Pour plus d’informations, voir [Personnaliser les revendications émises dans le jeton SAML pour les applications d’entreprise](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Examiner les données d’expiration du certificat, l’état et la notification par e-mail

Lorsque vous créez une application, qu’elle fasse partie ou non de la galerie, Azure AD crée un certificat propre à l’application et assorti d’une date d’expiration de trois ans à compter de la date de création. Vous aurez besoin de ce certificat pour configurer l’approbation entre Azure AD et l’application. Pour plus d’informations sur le format du certificat, consultez la documentation SAML de l’application. 

Dans Azure AD, vous pouvez télécharger le certificat au format Base64 ou au format brut. Par ailleurs, vous pouvez le récupérer en téléchargeant le fichier XML de métadonnées de l’application ou en utilisant l’URL des métadonnées de fédération de l’application.

  ![Certificat](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Vérifiez que le certificat comporte :

- La date d’expiration souhaitée. Le maximum est de trois ans.
- Un statut actif. Si l’état est inactif, modifiez-le. Pour cela, cochez **Actif**, puis enregistrez la configuration. 
- L’adresse e-mail de notification correcte. Lorsque le certificat actif est proche de la date d’expiration, Azure AD envoie une notification à l’adresse e-mail configurée dans ce champ.  

Pour plus d’informations, voir [Gérer les certificats pour l’authentification unique fédérée dans Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Configurer l’application cible

Pour configurer l’authentification unique dans une application, recherchez la documentation de celle-ci. Pour cela, accédez à la fin de la page de configuration de la connexion SAML, puis cliquez sur **Configurer<application name>**. 

Les valeurs requises dépendent de l’application. Pour plus d’informations, consultez la documentation SAML de l’application. Les URL des services de connexion et de déconnexion correspondent toutes les deux au même point de terminaison, à savoir le point de terminaison de gestion des demandes SAML de votre instance d’Azure AD. L’ID d’entité SAML est la valeur qui s’affiche comme Émetteur dans le jeton SAML émis pour l’application.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Affecter des utilisateurs et des groupes à une application SAML

Une fois que votre application a été configurée pour utiliser Azure AD comme fournisseur d'identité SAML, elle est presque prête pour le test. Dans une optique de contrôle de sécurité, Azure AD n’émet pas de jeton permettant à l’utilisateur de se connecter à l’application tant qu’Azure AD n’a pas accordé l’accès à ce dernier. Les utilisateurs peuvent bénéficier d’un accès direct ou par appartenance à un groupe. 

Pour affecter un utilisateur ou un groupe à votre application, cliquez sur le bouton **Affecter des utilisateurs** . Sélectionnez l'utilisateur ou le groupe que vous souhaitez affecter, puis sélectionnez le bouton **Affecter** .

  ![Affecter des utilisateurs](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

L’affectation d’un utilisateur permettra à Azure AD d’émettre un jeton pour celui-ci. Elle a également pour effet de faire apparaître une vignette pour cette application dans le volet d’accès de l’utilisateur. Une mosaïque de l'application s'affichera également dans le lanceur d'application Office 365 si l'utilisateur utilise Office 365. 

> [!NOTE] 
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application. 


### <a name="test-the-saml-application"></a>Tester l’application SAML

Pour pouvoir tester l’application SAML, vous devez configurer l’application avec Azure AD et y affecter des utilisateurs ou des groupes. Pour tester l’application SAML, consultez [Guide pratique pour déboguer l’authentification unique SAML pour les applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Authentification unique avec mot de passe

Sélectionnez cette option pour configurer l' [authentification unique basée sur un mot de passe](what-is-single-sign-on.md) pour une application Web qui a une page de connexion HTML. L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d'applications de médiaux sociaux de votre organisation. 

Après avoir sélectionné **Suivant**, on vous demandera d'entrer l'URL de la page de connexion de l’application basée sur le Web. Notez qu'il doit s’agir de la page qui contient le nom d’utilisateur et les champs d'entrée du mot de passe. Une fois l'entrée effectuée, Azure AD démarre un processus pour analyser la page de connexion pour un nom d'utilisateur et un mot de passe entrés. Si le processus échoue, il vous guidera à travers un autre processus d'installation d'une extension de navigateur (nécessite Internet Explorer, Chrome ou Firefox) qui vous permettra de capturer manuellement les champs.

Une fois la page de connexion capturée, les utilisateurs et les groupes peuvent être affectés et les stratégies d'informations d'identification peuvent être définies comme [applications SSO avec mot de passe](what-is-single-sign-on.md)standard.

> [!NOTE] 
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application. 
>

## <a name="existing-single-sign-on"></a>Authentification unique existante
Sélectionnez cette option pour ajouter un lien au panneau d'accès Azure AD ou au portail Office 365 de votre organisation. Vous pouvez utiliser ceci pour ajouter des liens à des applications Web personnalisées qui utilisent actuellement des services de fédération Azure Active Directory (ou un autre service de fédération) au lieu d'Azure AD pour l'authentification. Ou bien, vous pouvez ajouter des liens ciblés vers des pages SharePoint spécifiques ou d'autres pages Web dont vous souhaitez qu'elles s'affichent uniquement sur vos panneaux d'accès d'utilisateurs. 

Après avoir sélectionné **Suivant**, on vous demandera d'entrer l'URL de l'application à lier. Une fois l'opération terminée, les utilisateurs et les groupes peuvent être affectés à l'application, qui s'affiche alors dans le [Lanceur d'applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](end-user-experiences.md) pour ces utilisateurs.

> [!NOTE] 
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application. 
>

## <a name="related-articles"></a>Articles connexes
- [Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées](../develop/active-directory-saml-claims-customization.md)
- [Dépannage de l’authentification unique basée sur SAML](../develop/howto-v1-debug-saml-sso-issues.md)

