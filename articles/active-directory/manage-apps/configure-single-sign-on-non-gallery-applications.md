---
title: Authentification unique - applications hors galerie - plateforme d’identité Microsoft | Microsoft Docs
description: Configurer l’authentification unique (SSO) aux applications hors galerie dans la plateforme d’identité Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34e6257b4800387470cdc1b7d624bf3ebd1d3e6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989232"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurer l’authentification unique pour les applications hors galerie dans la plateforme d’identité Microsoft

Cet article concerne une fonctionnalité qui permet aux administrateurs de configurer l’authentification unique pour les applications manquant dans la galerie d’applications plateforme Microsoft identity *sans écrire de code*.

Si vous recherchez plutôt des instructions destinées aux développeurs sur l’intégration d’applications personnalisées avec Azure AD grâce au code, consultez [Scénarios d’authentification pour Azure AD](../develop/authentication-scenarios.md).

La galerie d’applications plateforme Microsoft identity fournit une liste d’applications qui prennent en charge une forme de l’authentification unique avec la plateforme d’identité Microsoft, comme décrit dans [cet article](what-is-single-sign-on.md). Une fois que vous (spécialiste informatique ou intégrateur système de votre organisation) avez trouvé l’application que vous voulez connecter, vous pouvez commencer par suivre les instructions détaillées présentées dans le portail Azure pour activer l’authentification unique.

Les fonctionnalités suivantes sont également disponibles, en fonction de votre contrat de licence. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/active-directory/).

- Intégration libre-service d’une application qui utilisent un protocole modern comme [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) pour authentifier ses utilisateurs et obtenir des jetons pour [Microsoft Graph](https://graph.microsoft.com).
- Intégration libre-service de toute application qui prend en charge [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) fournisseurs d’identité (initié par SP ou initiées par un fournisseur d’identité)
- Intégration libre-service de toute application Web dont la page de connexion est basée sur le HTML et utilise une [authentification unique par mot de passe](what-is-single-sign-on.md#password-based-sso)
- Connexion libre-service des applications qui utilisent le [système pour le protocole de Cross-Domain Identity Management (SCIM) pour l’approvisionnement](use-scim-to-provision-users-and-groups.md)
- Possibilité d'ajouter des liens à n'importe quelle application dans le [Lanceur d'application Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](what-is-single-sign-on.md#linked-sign-on)

Ces fonctionnalités peuvent inclure libre-service l’intégration d’un logiciel en tant qu’une application de service (SaaS) que vous utilisez, même si personne n’a intégré l’application dans la galerie d’applications Azure AD encore. Une autre capacité est libre service d’intégration d’une application web tiers que votre organisation a déployé sur les serveurs que vous contrôlez, dans le nuage ou sur site.

Également appelé *modèles d’intégration d’application*, ces fonctionnalités fournissent des points de connexion basée sur des normes pour les applications qui prennent en charge SAML, SCIM ou l’authentification basée sur les formulaires. Les fonctionnalités incluent des options flexibles et les paramètres de compatibilité avec un grand nombre d’applications.

## <a name="adding-an-unlisted-application"></a>Ajout d’une application non répertoriée

La plateforme d’identité Microsoft fournit deux mécanismes pour inscrire des applications.

Une application qui utilise un protocole modern comme [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) pour authentifier ses utilisateurs est inscrit à l’aide de la [portail d’inscription des applications](../develop/quickstart-register-app.md).

Pour inscrire des applications à l’aide de tous les autres types de [pris en charge les mécanismes d’authentification](what-is-single-sign-on.md), comme le [SAML](../develop/single-sign-on-saml-protocol.md) de protocole, utilisez le **Applications d’entreprise** panneau pour vous connecter les avec la plateforme d’identité Microsoft.

Pour vous connecter une application non répertoriée, à l’aide d’un modèle d’intégration d’application, procédez comme suit :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/) à l’aide de votre compte d’administrateur plateforme Microsoft identity.
2. Sélectionnez **Applications d’entreprise** > **nouvelle application**.
3. (Facultatif mais recommandé) Dans le **ajouter à partir de la galerie** zone de recherche, entrez le nom complet de l’application. Si l’application apparaît dans les résultats de recherche, sélectionnez-la et ignorez le reste de cette procédure.
4. Sélectionnez **application de la galerie de Non**. Le **ajouter votre propre application** page s’affiche.

   ![Ajouter une application](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Entrez le nom d’affichage pour votre nouvelle application.
6. Sélectionnez **Ajouter**.

En ajoutant une application de cette façon, vous fournir une expérience similaire à celle disponible pour les applications pré-intégrées. Sélectionnez d’abord **Single sign-on** à partir de la barre latérale de l’application. La page suivante (**sélectionner une méthode d’authentification unique**) présente les options de configuration de l’authentification unique :

- **SAML**
- **Par mot de passe**
- **Liée**

![Sélectionner une méthode d'authentification unique](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Pour plus d’informations sur ces options, consultez les sections suivantes de cet article.

## <a name="saml-based-single-sign-on"></a>Authentification unique SAML

Sélectionnez le **SAML** option pour configurer l’authentification SAML pour l’application. (Cette option nécessite que l’application prend en charge SAML 2.0.) Le **définir des Single Sign-On with SAML** page s’affiche.

![Configurer l’authentification unique avec SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Cette page contient cinq positions différentes :

| Numéro de titre | Nom du titre | Pour obtenir un résumé de cette rubrique, consultez : |
| --- | --- | --- |
| 1 | **Configuration de base SAML** | [Entrez la configuration de base SAML](#enter-basic-saml-configuration) |
| 2 | **Attributs d’utilisateur et revendications** | [Passez en revue ou personnaliser les revendications émises dans le jeton SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certificat de signature SAML** | [Données d’expiration du certificat révision, l’état et notification par courrier électronique](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Configurer \<nom_application >** | [Configurer application cible](#set-up-target-application) |
| 5. | **Tester l’authentification unique avec \<nom_application >** | [Tester l’application SAML](#test-the-saml-application) |

Maintenant collecter des informations sur la façon d’utiliser les fonctionnalités SAML de l’application avant de continuer. Consultez les sections suivantes pour configurer l’authentification unique entre l’application et Azure AD.

### <a name="enter-basic-saml-configuration"></a>Entrer la configuration SAML de base

Pour configurer Azure AD, accédez à la **base SAML Configuration** titre et sélectionnez ses **modifier** icône (un crayon). Vous pouvez entrer manuellement les valeurs ou charger un fichier de métadonnées pour extraire la valeur des champs.

![Configuration de base SAML](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Les deux champs suivants sont requis :

- **Identificateur**. Cette valeur doit identifier de manière unique l’application pour lequel l’authentification unique est configurée. Vous pouvez trouver cette valeur en tant que le **émetteur** élément dans le **AuthnRequest** (demande SAML) envoyée par l’application. Cette valeur apparaît également comme **l’ID d’entité** dans les métadonnées SAML fournies par l’application. Consultez la documentation SAML de l’application pour plus d’informations sur ce que son **l’ID d’entité** ou **public** est de valeur.

  Le code suivant montre comment la **identificateur** ou **émetteur** apparaît dans la demande SAML que l’application envoie à Azure AD :

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **L’URL de réponse**. L’URL de réponse est où l’application s’attend à recevoir le jeton SAML. Cette URL est également appelée l’URL assertion consumer service (ACS). Consultez la documentation SAML de l’application pour plus d’informations sur ce que sont les jetons de réponse SAML de l’URL ou de l’URL ACS.

  Pour configurer plusieurs URL de réponse, vous pouvez utiliser le script PowerShell suivant.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Les trois champs suivants sont facultatifs :

- **URL de connexion (initiée par SP uniquement)**. Cette valeur indique où l’utilisateur se connecte à cette application. Si l’application effectue l’authentification unique initiée, puis lorsqu’un utilisateur accède à cette URL, la procédure stockée effectue la redirection nécessaire vers Azure AD pour authentifier et connecter l’utilisateur. Si vous spécifiez ce champ, Azure AD utilise cette URL pour lancer l’application à partir d’Office 365 et le volet d’accès Azure AD. Si vous omettez ce champ, Azure AD effectue l’authentification initiée par le fournisseur d’identité lors de lancements d’applications à partir d’Office 365, le panneau d’accès Azure AD ou l’URL d’authentification unique Azure AD (que vous pouvez copier à partir de la **tableau de bord** page).

- **État de relais**. Vous pouvez spécifier un état de relais dans SAML pour indiquer à l’application où rediriger les utilisateurs après l’authentification. La valeur est généralement une URL ou URL du chemin d’accès qui mène à un emplacement spécifique au sein de l’application.

- **URL de déconnexion**. Cette valeur est utilisée pour envoyer la réponse de déconnexion SAML à l’application.

Pour plus d’informations, consultez [unique protocole SAML d’authentification](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Examiner ou personnaliser les revendications émises dans le jeton SAML

Lorsqu’un utilisateur s’authentifie à l’application, Azure AD émet l’application d’un jeton SAML avec des informations (ou revendications) sur l’utilisateur qui identifie de façon. Par défaut, ces informations incluent le nom d’utilisateur de l’utilisateur, adresse e-mail, prénom et nom de famille.

Pour afficher ou modifier les revendications envoyées dans le jeton SAML à l’application :

- Accédez à la **attributs utilisateur & revendications** titre et sélectionnez le **modifier** icône. Le **attributs utilisateur & revendications** page s’affiche.

![Attributs utilisateur et revendications](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Vous devrez peut-être modifier les revendications émises dans le jeton SAML pour deux raisons :

- L’application a besoin d’un ensemble différent d’URI ou de valeurs de revendication.
- Votre application nécessite la **nom de valeur d’identificateur** prétendent être autre chose qu’un nom d’utilisateur (également connu sous le nom d’utilisateur principal) stocké dans la plateforme d’identité Microsoft.

Pour plus d’informations, consultez [Procédure : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Examiner les données d’expiration du certificat, l’état et la notification par e-mail

Lorsque vous créez une galerie ou une application non issue de la galerie, Azure AD crée un certificat spécifique à l’application qui expire trois ans à partir de la date de sa création. Vous aurez besoin de ce certificat pour configurer l’approbation entre Azure AD et l’application. Pour plus d’informations sur le format du certificat, consultez la documentation SAML de l’application.

À partir d’Azure AD, vous pouvez télécharger le certificat actif au format Base64 ou Raw directement à partir de la main **définir des Single Sign-On with SAML** page. Vous pouvez également obtenir le certificat actif en téléchargeant le fichier XML de métadonnées application ou à l’aide de l’URL de métadonnées de fédération application.

Pour afficher, créer ou télécharger vos certificats (actifs ou inactifs), accédez à la **certificat de signature SAML** titre et sélectionnez le **modifier** icône. Le **certificat de signature SAML** s’affiche.

![Certificat de signature SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Vérifiez que le certificat comporte :

- *La date d’expiration souhaitées.* Vous pouvez configurer la date d’expiration pendant trois ans dans le futur.
- *Un état actif pour le certificat voulu.* Si l’état est **inactif**, changez l’état en **Active**. Pour modifier l’état, cliquez sur les lignes du certificat souhaité, puis sélectionnez **activer le certificat**.
- *L’option de signature appropriée et l’algorithme.*
- *Les adresses e-mail de notification correct.* Lorsque le certificat actif est proche de la date d’expiration, Azure AD envoie une notification à l’adresse de messagerie configuré dans ce champ.  

Pour plus d’informations, consultez [gérer des certificats pour fédérés l’authentification unique](manage-certificates-for-federated-single-sign-on.md) et [options dans le jeton SAML de signature de certificat avancée](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Configurer l’application cible

Pour configurer l’application pour l’authentification unique, recherchez la documentation de l’application. Pour trouver la documentation, accédez à la **configurer \<nom_application >** titre et sélectionnez **afficher les instructions pas à pas**. La documentation s’affiche dans le **configurer l’authentification** page. Cette page vous guide dans la remplissez le **URL de connexion**, **Azure AD identificateur**, et **URL de déconnexion** des valeurs dans le **configurer \<nom de l’application >** titre.

Les valeurs requises dépendent de l’application. Pour plus d’informations, consultez la documentation SAML de l’application. Le **URL de connexion** et **URL de déconnexion** valeurs à la fois résoudre au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d’Azure AD. Le **Azure AD identificateur** est la valeur de la **émetteur** dans le jeton SAML émis pour l’application.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Affecter des utilisateurs et des groupes à une application SAML

Une fois que vous avez configuré votre application pour utiliser Azure AD comme fournisseur d’identité basée sur SAML, il est presque prêt à tester. Contrôler la sécurité, Azure AD émet uniquement un jeton qui permet à un utilisateur pour se connecter à l’application si Azure AD a accordé l’accès à l’utilisateur. Les utilisateurs peuvent accéder directement ou via une appartenance de groupe.

Pour affecter un nouvel utilisateur ou un groupe à votre application :

1. Dans le volet de l’application, sélectionnez **utilisateurs et groupes**. Le  **\<nom_application >-les utilisateurs et groupes** page s’affiche, qui affiche la liste actuelle des utilisateurs et groupes attribués.
2. Sélectionnez **ajouter des utilisateurs**. Le **ajouter des affectations** page s’affiche.
3. Sélectionnez **utilisateurs et groupes (\<nombre > sélectionnés)**. Le **utilisateurs et groupes** page apparaît, affichant une liste des utilisateurs et les groupes.
4. Type ou faites défiler pour rechercher l’utilisateur ou groupe que vous souhaitez attribuer à partir de la liste.
5. Sélectionnez chaque utilisateur ou groupe que vous souhaitez ajouter, puis sélectionnez le **sélectionnez** bouton. Le **utilisateurs et groupes** disparaît de la page.
6. Dans le **ajouter des affectations** page, sélectionnez **affecter**. Le  **<application name> -utilisateurs et groupes** page s’affiche avec les utilisateurs supplémentaires affichés dans la liste.

   ![Groupes et utilisateurs de l’application](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Dans cette liste, vous pouvez :

- Supprimer un utilisateur.
- Modifiez leur rôle.
- Mettre à jour leurs informations d’identification (nom d’utilisateur et mot de passe) pour que l’utilisateur puisse authentifier à l’application à partir de volet de l’utilisateur d’accès.

Vous pouvez modifier ou supprimer plusieurs utilisateurs ou groupes à la fois.

Affectation d’un utilisateur permet à Azure AD émettre l’utilisateur un jeton. Elle a également pour effet de faire apparaître une vignette pour cette application dans le volet d’accès de l’utilisateur. Une vignette de l’application s’affiche également dans le Lanceur d’applications Office 365 si l’utilisateur utilise Office 365.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

### <a name="test-the-saml-application"></a>Tester l’application SAML

Avant de tester l’application SAML, vous devez avoir déjà configurer l’application avec Azure AD et affecter des utilisateurs ou des groupes à l’application. Pour tester l’application SAML, sélectionnez **Single sign-on**, ce qui vous renvoie à la **basée sur SAML authentification** page. (Si une autre méthode d’authentification unique a été en vigueur, sélectionnez **modifier les modes d’authentification uniques** > **SAML** trop.) Ensuite, dans le **tester l’authentification unique avec \<nom_application >** titre, sélectionnez **Test**. Pour plus d’informations, consultez [basée sur SAML déboguer-session unique aux applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Authentification unique avec mot de passe

Sélectionnez cette option pour configurer [par mot de passe SSO](what-is-single-sign-on.md) pour une application web avec une page de connexion HTML. L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Il est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, comme aux comptes d’application sociaux de votre organisation.

Une fois que vous sélectionnez **par mot de passe**, vous êtes invité à entrer l’URL de basée sur le web-page de connexion l’application.

![Authentification unique avec mot de passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Ensuite, procédez comme suit :

1. Entrez l’URL. Cette chaîne doit correspondre à la page qui inclut le champ d’entrée de nom d’utilisateur.
2. Sélectionnez **Enregistrer**. Azure AD tente d’analyser la page de connexion pour un nom d’utilisateur et un mot de passe d’entrée.
3. Si Azure AD de l’analyse tentative échoue, sélectionnez **configurer \<nom_application > mot de passe Single Sign-on Settings** pour afficher le **configurer l’authentification** page. (Si la tentative réussit, vous pouvez ignorer le reste de cette procédure.)
4. Sélectionnez **détecter manuellement les champs de connexion**. Des instructions supplémentaires décrivant la détection manuelle des champs de connexion s’affichent.

   ![Configuration manuelle du mot de passe authentification unique](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Sélectionnez **capturer les champs de connexion**. Une page d’état de capture s’ouvre dans un nouvel onglet, montrant le message **capture des métadonnées est en cours**.
6. Si le **Extension de panneau de configuration de l’accès requis** zone s’affiche dans un nouvel onglet, sélectionnez **installer maintenant** pour installer le **sécurisée à mes applications-Extension de connexion** extension de navigateur. (Nécessite de l’extension de navigateur Microsoft Edge, Chrome ou Firefox.) Puis installez, lancer, activer l’extension et actualisez la page d’état de capture.

   L’extension de navigateur ouvre ensuite un autre onglet qui affiche l’URL saisie.
7. Dans l’onglet avec l’URL saisie, parcourez le processus de connexion. Renseignez les champs nom d’utilisateur et mot de passe et essayez de vous connecter. (Il est inutile de fournir le mot de passe correct.)

   Un message vous invite à enregistrer les champs de connexion capturées.
8. Sélectionnez **OK**. Ferme l’onglet, la page d’état de capture des mises à jour de l’extension de navigateur avec le message **métadonnées a été mis à jour pour l’application**et ce navigateur onglet également se ferme.
9. Dans Azure AD **configurer l’authentification** page, sélectionnez **Ok, j’ai pu se connecter à l’application correctement**.
10. Sélectionnez **OK**.

Après la capture de la page de connexion, vous pouvez attribuer des utilisateurs et groupes, et vous pouvez définir des stratégies d’informations d’identification comme normal [applications SSO de mot de passe](what-is-single-sign-on.md).

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

## <a name="existing-single-sign-on"></a>Authentification unique existante

Sélectionnez cette option pour ajouter un lien vers l’application dans le portail panneau d’accès Azure AD ou Office 365 de votre organisation. Vous pouvez utiliser cette méthode pour ajouter des liens vers les applications web personnalisées qui utilisent actuellement Active Directory Federation Services (ou autre service de fédération) au lieu d’Azure AD pour l’authentification. Ou bien, vous pouvez ajouter des liens ciblés vers des pages SharePoint spécifiques ou d'autres pages Web dont vous souhaitez qu'elles s'affichent uniquement sur vos panneaux d'accès d'utilisateurs.

Une fois que vous sélectionnez **lié**, vous êtes invité à entrer l’URL de l’application à lier. Tapez l’URL, puis sélectionnez **enregistrer**. Vous pouvez attribuer des utilisateurs et groupes à l’application, ce qui entraîne l’application s’affichent dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou [volet d’accès Azure AD](end-user-experiences.md) pour ces utilisateurs.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

## <a name="related-articles"></a>Articles connexes

- [Guide pratique : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise](../develop/active-directory-saml-claims-customization.md)
- [Débogage basée sur SAML SSO aux applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Plateforme d’identité Microsoft (anciennement Azure Active Directory pour les développeurs)](../develop/index.yml)
