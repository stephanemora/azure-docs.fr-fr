---
title: Authentification unique - applications hors galerie - Plateforme d’identité Microsoft | Microsoft Docs
description: Configurer l’authentification unique (SSO) sur des applications hors galerie dans la plateforme d’identité Microsoft (Azure AD)
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
ms.openlocfilehash: 95b7cbcf4e485dc93d49b9559dcb7d0d4f597ebe
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550347"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurer l’authentification unique sur des applications hors galerie dans la plateforme d’identité Microsoft

Cet article concerne une fonctionnalité permettant aux administrateurs de configurer l’authentification unique pour les applications qui ne figurent pas dans la galerie d’applications de la plateforme d’identité Microsoft, *sans écrire de code*.

Si vous recherchez plutôt des instructions destinées aux développeurs sur l’intégration d’applications personnalisées avec Azure AD grâce au code, consultez [Scénarios d’authentification pour Azure AD](../develop/authentication-scenarios.md).

La galerie d'applications de la plateforme d’identité Microsoft contient une liste d’applications qui prennent en charge une forme d’authentification unique avec la plateforme d’identité Microsoft, conformément à la description dans [cet article](what-is-single-sign-on.md). Une fois que vous (spécialiste informatique ou intégrateur système de votre organisation) avez trouvé l’application que vous voulez connecter, vous pouvez commencer par suivre les instructions détaillées présentées dans le portail Azure pour activer l’authentification unique.

Les fonctionnalités suivantes sont également disponibles, en fonction de votre contrat de licence. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/active-directory/).

- Intégration libre-service d’une application qui utilise un protocole moderne comme [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) pour authentifier ses utilisateurs et obtenir des jetons pour [Microsoft Graph](https://graph.microsoft.com)
- Intégration libre-service de toute application prenant en charge les fournisseurs d’identité [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (initiée par le fournisseur de services ou par le fournisseur d’identité fédérée)
- Intégration libre-service de toute application Web dont la page de connexion est basée sur le HTML et utilise une [authentification unique par mot de passe](what-is-single-sign-on.md#password-based-sso)
- Connexion libre-service des applications qui utilisent le protocole [System for Cross-Domain Identity Management (SCIM) pour l’approvisionnement d’utilisateurs](use-scim-to-provision-users-and-groups.md)
- Possibilité d'ajouter des liens à n'importe quelle application dans le [Lanceur d'application Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](what-is-single-sign-on.md#linked-sign-on)

Ces fonctionnalités peuvent inclure l’intégration libre-service d’une application SaaS (software as a service) que vous utilisez, même si personne n’a encore intégré l’application dans la galerie d’applications Azure AD. Une autre fonctionnalité est l’intégration libre-service d’une application web tierce que votre organisation a déployée sur les serveurs que vous contrôlez, dans le cloud ou sur site.

Également appelées *modèles d’intégration d’application*, ces fonctionnalités fournissent des points de connexion basés sur des normes pour les applications prenant en charge l’authentification SCIM, SAML ou par formulaire. Elles incluent des paramètres et options flexibles pour la compatibilité avec un grand nombre d’applications.

## <a name="adding-an-unlisted-application"></a>Ajout d’une application non répertoriée

La plateforme d’identité Microsoft propose deux mécanismes pour inscrire des applications.

Une application qui utilise un protocole moderne comme [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) pour authentifier ses utilisateurs doit être inscrite dans le [portail d’inscription des applications](../develop/quickstart-register-app.md).

Pour inscrire des applications ayant recours à tous les autres [mécanismes d’authentification pris en charge](what-is-single-sign-on.md), comme le protocole [SAML](../develop/single-sign-on-saml-protocol.md), utilisez le panneau **Applications d’entreprise** pour les connecter avec la plateforme d’identité Microsoft.

Pour connecter une application non répertoriée à l’aide d’un modèle d’intégration d’application, procédez comme suit :

1. Connectez-vous au [Portail Azure Active Directory](https://aad.portal.azure.com/) à l’aide de votre compte d’administrateur de plateforme d’identité Microsoft.
2. Sélectionnez **Applications d’entreprise** > **Nouvelle application**.
3. (Facultatif mais recommandé) Dans la zone de recherche **Ajouter à partir de la galerie**, entrez le nom complet de l’application. Si l’application apparaît dans les résultats de recherche, sélectionnez-la et ignorez le reste de cette procédure.
4. Sélectionnez **Application ne figurant pas dans la galerie**. La page **Ajouter votre propre application** s’affiche.

   ![Ajouter l’application](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Entrez le nom complet de votre nouvelle application.
6. Sélectionnez **Ajouter**.

En ajoutant une application de cette manière, vous offrez une expérience similaire à celle des applications préintégrées. Dans la barre latérale de l’application, commencez par sélectionner **Authentification unique**. La page suivante (**Sélectionner une méthode d’authentification unique**) présente les options de configuration de l’authentification unique :

- **SAML**
- **Par mot de passe**
- **Liée**

![Sélectionner une méthode d’authentification unique](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Pour plus d’informations sur ces options, consultez les sections suivantes de cet article.

## <a name="saml-based-single-sign-on"></a>Authentification unique SAML

Sélectionnez l’option **SAML** pour configurer l’authentification basée sur ce protocole pour l’application. (Si vous choisissez cette option, l’application doit prendre en charge le protocole SAML 2.0.) La page **Configurer l’authentification unique avec SAML** s’affiche.

![Configurer l’authentification unique avec SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Cette page contient cinq rubriques différentes :

| Numéro de rubrique | Nom de rubrique | Pour obtenir un résumé de cette rubrique, voir : |
| --- | --- | --- |
| 1 | **Configuration SAML de base** | [Entrer la configuration SAML de base](#enter-basic-saml-configuration) |
| 2 | **Attributs utilisateur et revendications** | [Examiner ou personnaliser les revendications émises dans le jeton SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certificat de signature SAML** | [Examiner les données d’expiration du certificat, l’état et la notification par e-mail](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Configurer \<nom de l’application>** | [Configurer l’application cible](#set-up-target-application) |
| 5\. | **Tester l’authentification unique avec \<nom de l’application>** | [Tester l’application SAML](#test-the-saml-application) |

Recueillez à présent des informations sur la façon d’utiliser les fonctionnalités SAML de l’application avant de continuer. Lisez les sections suivantes pour configurer l’authentification unique entre l’application et Azure AD.

### <a name="enter-basic-saml-configuration"></a>Entrer la configuration SAML de base

Pour configurer Azure AD, accédez à la rubrique **Configuration SAML de base** et sélectionnez l’icône **Modifier** (crayon). Vous pouvez entrer manuellement les valeurs ou charger un fichier de métadonnées pour extraire la valeur des champs.

![Configuration SAML de base](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Les deux champs suivants sont obligatoires :

- **Identificateur**. Cette valeur doit identifier de façon unique l’application pour laquelle l’authentification unique est en cours de configuration. Cette valeur correspond à l’élément **Émetteur** dans la demande SAML **AuthnRequest** envoyée par l’application. Cette valeur apparaît également comme **l’ID d’entité** dans les métadonnées SAML fournies par l’application. Consultez la documentation SAML de l’application pour plus d’informations sur ce que sont les valeurs **ID d’entité** ou **Audience**.

  Le code suivant montre comment les valeurs **Identificateur** ou **Émetteur** apparaissent dans la demande SAML envoyée par l’application à Azure AD :

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **URL de réponse**. L’URL de réponse est l’adresse où l’application attend le jeton SAML. Cette URL est aussi appelée URL ACS (Assertion Consumer Service). Consultez la documentation SAML de l’application pour plus d’informations sur ce que sont les jetons de réponse SAML de l’URL ou de l’URL ACS.

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

Les trois champs suivants sont facultatifs :

- **URL de connexion (initiée par le fournisseur de services)** . Cette valeur indique où l’utilisateur se connecte à cette application. Si l’application utilise une authentification unique initiée par le fournisseur de services, lorsque l’utilisateur accède à cette URL, le fournisseur de services effectue la redirection nécessaire vers Azure AD pour authentifier et connecter l’utilisateur. Si vous renseignez ce champ, Azure AD utilise cette URL pour lancer l’application à partir d’Office 365 et du panneau d’accès Azure AD. Si vous ignorez ce champ, Azure AD utilise l’authentification initiée par le fournisseur d’identité en cas de démarrage de l’application depuis Office 365, le panneau d’accès Azure AD ou l’URL d’authentification unique Azure AD (que vous pouvez copier dans la page **Tableau de bord**).

- **État de relais**. Vous pouvez spécifier un état de relais dans SAML pour indiquer à l’application où rediriger les utilisateurs après l’authentification. Cette valeur correspond généralement à une URL ou à un chemin d’accès d’URL qui redirige les utilisateurs vers un emplacement spécifique au sein de l’application.

- **URL de déconnexion**. Cette valeur est utilisée pour renvoyer les réponses de déconnexion SAML à l’application.

Pour plus d’informations, consultez [Protocole SAML d’authentification unique](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Examiner ou personnaliser les revendications émises dans le jeton SAML

Lorsqu’un utilisateur s’authentifie auprès de l’application, Azure AD émet pour l’application un jeton SAML contenant des informations (ou des revendications) sur l’utilisateur qui l’identifient de façon unique. Par défaut, ces informations incluent le nom d’utilisateur, son adresse e-mail, son prénom et son nom.

Pour afficher ou modifier les revendications envoyées dans le jeton SAML à l’application :

- Accédez à la rubrique **Attributs utilisateur et revendications** et sélectionnez l’icône **Modifier**. La page **Attributs utilisateur et revendications** s’affiche.

![Attributs utilisateur et revendications](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Vous pourrez avoir à modifier les revendications émises dans le jeton SAML pour deux raisons :

- L’application a besoin d’un ensemble différent d’URI ou de valeurs de revendication.
- Votre application a besoin que la revendication **Valeur de nom d’identificateur** soit différente du nom d’utilisateur (également appelé nom d’utilisateur principal) stocké dans la plateforme d’identité Microsoft.

Pour plus d’informations, consultez [Procédure : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Examiner les données d’expiration du certificat, l’état et la notification par e-mail

Lorsque vous créez une application, qu’elle figure ou non dans la galerie, Azure AD crée un certificat propre à l’application qui expire trois ans à compter de sa date de création. Vous aurez besoin de ce certificat pour configurer l’approbation entre Azure AD et l’application. Pour plus d’informations sur le format du certificat, consultez la documentation SAML de l’application.

Depuis Azure AD, vous pouvez télécharger le certificat actif au format Base64 ou Raw directement à partir de la page principale **Configurer l’authentification unique avec SAML**. Sinon, vous pouvez le récupérer en téléchargeant le fichier XML de métadonnées de l’application ou en utilisant l’URL des métadonnées de fédération de l’application.

Pour afficher, créer ou télécharger vos certificats (actifs ou inactifs), accédez à la rubrique **Certificat de signature SAML** et sélectionnez l’icône **Modifier**. Le **Certificat de signature SAML** s’affiche.

![Certificat de signature SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Vérifiez que le certificat comporte :

- *La date d’expiration souhaitée.* Vous pouvez configurer une date d’expiration allant jusqu’à trois ans dans le futur.
- *Un état actif pour le certificat souhaité.* Si l’état est **inactif**, définissez-le sur **Actif**. Pour modifier l’état, cliquez sur la ligne du certificat souhaité avec le bouton droit de la souris, puis sélectionnez **Définir comme certificat actif**.
- *L’option et l’algorithme de signature appropriés.*
- *Les adresses e-mail de notification correctes.* Lorsque le certificat actif est proche de sa date d’expiration, Azure AD envoie une notification à l’adresse e-mail configurée dans ce champ.  

Pour plus d’informations, consultez [Gérer des certificats pour l’authentification unique fédérée](manage-certificates-for-federated-single-sign-on.md) et [Options avancées de signature de certificats dans le jeton SAML](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Configurer l’application cible

Pour configurer l’authentification unique dans une application, recherchez la documentation de celle-ci. Pour trouver la documentation, accédez à la rubrique **Configurer \<nom de l’application>** et sélectionnez **Afficher les instructions détaillées**. La documentation s’affiche dans la page **Configurer l’authentification**. Cette page vous aide à renseigner l’**URL de connexion**, l’**Identificateur Azure AD** et l’**URL de déconnexion** dans la rubrique **Configurer \<nom de l’application >** .

Les valeurs requises dépendent de l’application. Pour plus d’informations, consultez la documentation SAML de l’application. Les valeurs **URL de connexion** et **URL de déconnexion** correspondent toutes les deux au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d'Azure AD. L’**Identificateur Azure AD** est la valeur de l’**Émetteur** dans le jeton SAML émis pour l’application.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Affecter des utilisateurs et des groupes à une application SAML

Une fois votre application configurée pour utiliser Azure AD comme fournisseur d’identité SAML, elle est presque prête pour le test. Dans une optique de contrôle de sécurité, Azure AD n’émet un jeton permettant à l’utilisateur de se connecter à l’application que si Azure AD a accordé l’accès à ce dernier. Les utilisateurs peuvent bénéficier d’un accès direct ou par appartenance à un groupe.

Pour affecter un nouvel utilisateur ou un nouveau groupe à votre application :

1. Dans la barre latérale de l’application, sélectionnez **Utilisateurs et groupes**. La page **\<nom de l’application> - Utilisateurs et groupes** s’affiche. Elle contient la liste actuelle des utilisateurs et groupes affectés.
2. Sélectionnez **Ajouter des utilisateurs**. La page **Ajouter des affectations** s’affiche.
3. Sélectionnez **Utilisateurs et groupes (\<Nombre> Sélectionné)** . La page **Utilisateurs et groupes** s’affiche. Elle contient une liste des utilisateurs et groupes disponibles.
4. Tapez du texte ou faites défiler la liste pour trouver l’utilisateur ou le groupe à affecter.
5. Sélectionnez chaque utilisateur ou groupe à ajouter, puis cliquez sur le bouton **Sélectionner**. La page **Utilisateurs et groupes** disparaît.
6. Dans la page **Ajouter des affectations**, sélectionnez **Affecter**. La page **\<nom de l’application > - Utilisateurs et groupes** s’affiche. Les utilisateurs supplémentaires sont affichés dans la liste.

   ![Utilisateurs et groupes de l’application](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Depuis cette liste, vous pouvez :

- Supprimer un utilisateur.
- Modifier leur rôle.
- Mettre à jour leurs informations d’identification (nom d’utilisateur et mot de passe) pour qu’ils puissent s’authentifier à l’application à partir de leur panneau d’accès.

Vous pouvez modifier ou supprimer plusieurs utilisateurs ou groupes à la fois.

L’affectation d’un utilisateur permet à Azure AD d’émettre un jeton pour l’utilisateur. Elle a également pour effet de faire apparaître une vignette pour cette application dans le volet d’accès de l’utilisateur. Une mosaïque de l’application s’affiche également dans le lanceur d’application Office 365 si l’utilisateur utilise Office 365.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

### <a name="test-the-saml-application"></a>Tester l’application SAML

Pour pouvoir tester l’application SAML, vous devez déjà avoir configuré l’application avec Azure AD et y avoir affecté des utilisateurs ou des groupes. Pour tester l’application SAML, sélectionnez **Authentification unique**, ce qui vous renvoie à la page **Authentification basée sur SAML**. (Si une autre méthode d’authentification unique était auparavant en vigueur, sélectionnez également **Modifier les modes d’authentification unique** > **SAML**.) Ensuite, dans la rubrique **Tester l’authentification unique avec \<nom de l’application >** , sélectionnez **Test**. Pour plus d’informations, voir [Guide pratique pour déboguer l’authentification unique SAML pour les applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Authentification unique avec mot de passe

Sélectionnez cette option pour configurer l’[authentification unique basée sur un mot de passe](what-is-single-sign-on.md) pour une application web avec page de connexion HTML. L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile dans les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de médias sociaux de votre organisation.

Après avoir sélectionné **Authentification par mot de passe**, vous serez invité à entrer l’URL de la page de connexion web de l’application.

![Authentification unique par mot de passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Effectuez ensuite les étapes suivantes :

1. Entrez l’URL. Il doit s’agir de la page qui contient le champ d’entrée du nom d’utilisateur.
2. Sélectionnez **Enregistrer**. Azure AD tente d’analyser la page de connexion à la recherche d’entrées de nom d’utilisateur et de mot de passe.
3. Si la tentative d’analyse Azure AD échoue, sélectionnez **Configurer les paramètres d’authentification unique par mot de passe \<nom de l’application>** pour afficher la page **Configurer l’authentification**. (Si la tentative réussit, vous pouvez ignorer le reste de cette procédure.)
4. Sélectionnez **Détecter manuellement les champs de connexion**. Des instructions supplémentaires décrivant la détection manuelle des champs de connexion s’affichent.

   ![Configuration manuelle de l’authentification unique par mot de passe](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Sélectionnez **Capturer les champs de connexion**. Une page d’état de la capture s’ouvre dans un nouvel onglet affichant le message **Capture des métadonnées en cours**.
6. Si la mention **Extension Volet d’accès requise** s’affiche dans un nouvel onglet, sélectionnez **Installer maintenant** pour installer l’extension de navigateur **My Apps Secure Sign-in Extension**. (Cette extension de navigateur est compatible avec Microsoft Edge, Chrome ou Firefox.) Ensuite, installez, lancez et activez l’extension, puis actualisez la page d’état de la capture.

   L’extension de navigateur ouvre ensuite un nouvel onglet qui affiche l’URL entrée.
7. Dans l’onglet contenant l’URL saisie, suivez le processus de connexion. Renseignez les champs de nom d’utilisateur et de mot de passe et essayez de vous connecter. (Vous n’êtes pas obligé de fournir le mot de passe correct.)

   Un message vous invite à enregistrer les champs de connexion capturés.
8. Sélectionnez **OK**. L’onglet se ferme, l’extension de navigateur met à jour la page d’état de la capture avec le message **Les métadonnées ont été mises à jour pour l’application**, puis cet onglet se ferme à son tour.
9. Dans la page Azure AD **Configurer l’authentification**, sélectionnez **J’ai pu me connecter à l’application**.
10. Sélectionnez **OK**.

Après la capture de la page de connexion, vous pouvez affecter des utilisateurs et des groupes, et vous pouvez configurer des stratégies d’informations d’identification comme pour les [applications avec authentification unique par mot de passe](what-is-single-sign-on.md) standard.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

## <a name="existing-single-sign-on"></a>Authentification unique existante

Sélectionnez cette option pour ajouter un lien vers l’application au panneau d’accès Azure AD ou au portail Office 365 de votre organisation. Vous pouvez utiliser cette méthode pour ajouter des liens à des applications web personnalisées qui utilisent actuellement des services de fédération Active Directory (ou un autre service de fédération) au lieu d’Azure AD pour l’authentification. Ou bien, vous pouvez ajouter des liens ciblés vers des pages SharePoint spécifiques ou d'autres pages Web dont vous souhaitez qu'elles s'affichent uniquement sur vos panneaux d'accès d'utilisateurs.

Après avoir sélectionné **Lié**, vous serez invité à entrer l’URL de l’application à lier. Tapez l’URL, puis sélectionnez **Enregistrer**. Vous pouvez affecter des utilisateurs et des groupes à l’application, qui s’affiche alors dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d’accès Azure AD](end-user-experiences.md) pour ces utilisateurs.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

## <a name="related-articles"></a>Articles connexes

- [Guide pratique pour Customize claims issued in the SAML token for enterprise applications](../develop/active-directory-saml-claims-customization.md) (Comment : Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise)
- [Déboguer l’authentification unique SAML aux applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Plateforme d’identités Microsoft (anciennement Azure Active Directory pour développeurs)](../develop/index.yml)
