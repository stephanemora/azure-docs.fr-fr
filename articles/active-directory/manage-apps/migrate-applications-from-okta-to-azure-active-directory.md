---
title: Didacticiel pour migrer vos applications d’Okta vers Azure Active Directory
titleSuffix: Active Directory
description: Découvrez comment migrer vos applications d’Okta vers Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c46410a6998998ace9bc1a9e9809262970a131f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791723"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Didacticiel : Migrer vos applications d’Okta vers Azure Active Directory

Ce didacticiel explique comment migrer vos applications d’Okta vers Azure Active Directory (Azure AD).

## <a name="create-an-inventory-of-current-okta-applications"></a>Créer un inventaire des applications Okta actuelles

Lors de la conversion d’applications Okta vers Azure AD, nous vous recommandons de commencer par documenter les paramètres actuels d’environnement et d’application avant d’opérer la migration.

Okta offre une API permettant de collecter ces informations à partir d’un emplacement centralisé. Pour utiliser l’API, un outil explorateur d’API tel que [Postman](https://www.postman.com/) est requis.

Pour dresser un inventaire des applications, procédez comme suit :

1. Installer l’application Postman. Après l’installation, générez un jeton d’API à partir de la console d’administration Okta.

2. Accédez au tableau de bord de l’API dans la section Sécurité, puis sélectionnez **Jetons** > **Créer un jeton**.

   ![Image montrant la création d’un jeton](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. Insérez un nom de jeton, puis sélectionnez **Créer un jeton**.

   ![Image montrant un jeton créé](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. Après avoir sélectionné **Créer un jeton**, enregistrez la valeur et mettez-la de côté, car elle ne sera plus accessible après que vous aurez sélectionné **OK**.

   ![Image montrant un enregistrement créé](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. Une fois que vous avez enregistré le jeton d’API, revenez à l’application Postman, puis, dans l’espace de travail, sélectionnez **Importer**.

   ![Image montrant l’importation d’une API](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. Dans la page Importer, sélectionnez **lien**, puis utilisez le lien suivant pour importer l’API : <https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment>

   ![Image montrant un lien pour importer](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

>[!NOTE]
>Ne modifiez pas le lien avec les valeurs de votre locataire.

7. Passez au menu suivant en sélectionnant **Importer**.

   ![Image montrant le menu Importer suivant](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. Une fois l’importation effectuée, changez la sélection de l’environnement en la définissant sur **{yourOktaDomain}** .

   ![Image montrant le changement d’environnement](media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png)

9. Après avoir modifié la sélection de l’environnement, modifiez votre environnement Okta en sélectionnant l’œil, puis **Modifier**.

   ![Image montrant la modification de l’environnement](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. Mettez à jour les valeurs d’URL et de clé API dans les champs **Valeur initiale** et **Valeur actuelle** en modifiant également le nom de manière à refléter votre environnement, puis enregistrez les valeurs.

    ![Image montrant la mise à jour des valeurs pour l’API](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. Après avoir enregistré la clé API, [chargez l’API Applications dans Postman](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

12. Une fois l’API chargée dans Postman, sélectionnez la liste déroulante **Apps** (Applications), **Get List Apps** (Obtenir la liste des applications), puis **Send** (Envoyer).

Vous pouvez maintenant imprimer toutes les applications de votre locataire Okta dans un format JSON.

![image montrant la liste des applications](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Nous vous recommandons de copier et convertir cette liste JSON au format CSV à l’aide d’un convertisseur public tel que <https://konklone.io/json/> ou PowerShell en utilisant les commandes [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) et [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv).

Une fois le fichier CSV téléchargé, les applications dans votre locataire Okta sont correctement enregistrées pour référence ultérieure.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migrer une application SAML vers Azure AD

Pour migrer une application SAML 2.0 vers Azure AD, commencez par configurer l’application dans votre client Azure AD pour l’accès à l’application. Dans cet exemple, nous allons convertir une instance Salesforce. Pour intégrer les applications, suivez [ce didacticiel](../saas-apps/salesforce-tutorial.md).

Pour accomplir le processus de migration, répétez les étapes de configuration pour toutes les applications découvertes dans le locataire Okta.

1. Accédez au [portail Azure AD](https://aad.portal.azure.com), puis sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Nouvelle application**.

   ![Image montrant la liste de nouvelles applications](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. Salesforce est disponible dans la galerie Azure AD. Recherchez Salesforce, sélectionnez l’application, puis sélectionnez **Créer**.

   ![Image montrant l’application Salesforce](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. Une fois l’application créée, accédez à l’onglet **Authentification unique (SSO)** , puis sélectionnez **SAML**.

   ![Image montrant l’application SAML](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. Après avoir sélectionné SAML, téléchargez le **fichier XML de métadonnées de fédération et le certificat (brut)** à importer dans Salesforce.

   ![Image montrant le téléchargement de métadonnées de fédération](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. Une fois le fichier XML capturé, accédez à la console d’administration de Salesforce, puis sélectionnez **Identité** > **Paramètres d’authentification unique** > **Nouveau à partir d’un fichier de métadonnées**.

   ![Image montrant la console d’administration de Salesforce](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. Chargez le fichier XML téléchargé à partir du portail Azure AD, puis sélectionnez **Créer**.

   ![Image montrant le chargement du fichier XML](media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png)

7. Chargez le certificat téléchargé à partir d’Azure, puis sélectionnez **Enregistrer** dans le menu suivant afin de créer le fournisseur SAML dans Salesforce.

   ![Image montrant la création d’un fournisseur SAML](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. Enregistrez les valeurs suivantes à utiliser dans Azure : **ID d’entité**, **URL de connexion** et **URL de déconnexion**. Sélectionnez ensuite l’option **Télécharger les métadonnées**.

   ![Image montrant des valeurs d’enregistrement dans Azure](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. Revenez au menu Applications d’entreprise Azure AD, puis, dans le portail Azure AD, dans les paramètres d’authentification unique SAML, **chargez le fichier de métadonnées**. Confirmez que les valeurs importées correspondent aux valeurs enregistrées avant l’enregistrement.

   ![Image montrant le chargement du fichier de métadonnées dans Azure AD](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. Une fois la configuration de l’authentification unique enregistrée, revenez à la console d’administration Salesforce, puis sélectionnez **Paramètres de la société** > **Mon domaine**. Accédez à **Configuration de l’authentification**, puis sélectionnez **Modifier**.

    ![Image montrant le chargement des paramètres de modification de la société](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. Sélectionnez le nouveau fournisseur SAML configuré aux étapes précédentes en tant qu’option de connexion disponible, puis sélectionnez **Enregistrer**.

    ![Image montrant l’option d’enregistrement du fournisseur SAML](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. Revenez à l’Application d’entreprise dans Azure AD, sélectionnez **Utilisateurs et groupes**, puis ajoutez des **utilisateurs de test**.

    ![Image montrant l’ajout d’un utilisateur de test](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. Pour tester, connectez-vous en tant qu’utilisateur de test, puis accédez à <https://aka.ms/myapps> et sélectionnez la vignette **Salesforce**.

    ![Image montrant la connexion en tant qu’utilisateur de test](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. Après avoir sélectionné la vignette Salesforce, sélectionnez le fournisseur d’identité nouvellement configuré pour la connexion.

    ![Image montrant la sélection du nouveau fournisseur d’identité](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

15. Si tout a été correctement configuré, l’utilisateur atterrit sur la page d’accueil de Salesforce. En cas de problème, suivez le [guide de débogage](../manage-apps/debug-saml-sso-issues.md).

16. Après avoir testé la connexion avec authentification unique à partir d’Azure, revenez à l’application d’entreprise et affectez les utilisateurs restants à l’application Salesforce avec les rôles appropriés.

>[!NOTE]
>Après avoir ajouté les utilisateurs restants à l’application Azure AD, nous vous recommandons de demander aux utilisateurs de tester la connexion et de s’assurer qu’aucun problème d’accès ne se pose avant de passer à l’étape suivante.

17. Une fois que les utilisateurs ont confirmé qu’il n’y a aucun problème de connexion, revenez à la console d’administration Salesforce et sélectionnez **Paramètres de la société** > **Mon domaine**.

18. Accédez à la **Configuration de l’authentification**, sélectionnez **Modifier**, puis désélectionnez Okta en tant que service d’authentification.

    ![Image montrant la désélection d’Okta en tant que service d’authentification](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce a été correctement configuré sur Azure AD pour l’authentification unique. Les étapes de nettoyage du portail Okta seront incluses plus loin dans ce document.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migrer une application OIDC/OAuth 2.0 vers Azure AD

Commencez par configurer l’application dans votre locataire Azure AD pour l’accès à l’application. Dans cet exemple, nous allons convertir une application OIDC personnalisée.

Pour accomplir le processus de migration, répétez les étapes de configuration pour toutes les applications découvertes dans le locataire Okta.

1. Accédez au [portail Azure AD](https://aad.portal.azure.com), puis sélectionnez **Azure Active Directory** > **Applications d’entreprise**. Dans le menu **Toutes les applications**, sélectionnez **Nouvelle application**.

2. Sélectionnez **Créer votre propre application**. Dans le menu latéral qui s’affiche, donnez un nom à l’application OIDC, sélectionnez le radial pour **Inscrire une application sur laquelle vous travaillez pour l’intégrer avec Azure AD**, puis sélectionnez **Créer**.

   ![Image montrant une nouvelle application OIDC](media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png)

3. Sur la page suivante, vous avez la possibilité de choisir la location de votre inscription d’application. Pour plus d’informations, consultez [cet article](../develop/single-and-multi-tenant-apps.md).

Dans cet exemple, nous sélectionnons **Comptes dans un annuaire organisationnel**, un annuaire Azure AD **mutualisé**, puis **Registre**.

![Image montrant un annuaire Azure AD mutualisé](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. Une fois l’application inscrite, accédez à la page **Inscriptions d’applications** sous **Azure Active Directory**, puis ouvrez l’inscription nouvellement créée.

   Selon le [scénario d’application](../develop/authentication-flows-app-scenarios.md), différentes actions de configuration peuvent être nécessaires. Comme la plupart des scénarios requièrent une clé secrète client d’application, nous allons aborder ces exemples.

5. Dans la page **vue d’ensemble**, enregistrez l’ID d’application (client) pour l’utiliser dans votre application ultérieurement.

   ![Image montrant l’ID client de l’application](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. Après avoir enregistré l’ID d’application, sélectionnez **Certificats et secrets** dans le menu de gauche. Sélectionnez **Nouveau secret client**, puis attribuez-lui un nom et définissez son expiration en conséquence.

   ![Image montrant le nouveau secret client](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. Enregistrez la valeur et l’ID du secret avant de quitter cette page.

>[!NOTE]
>Vous ne pourrez pas enregistrer ces informations ultérieurement, et devrez donc régénérer un secret en cas de perte.

8. Après avoir enregistré les informations des étapes ci-dessus, sélectionnez les **Autorisations des API** sur la gauche, puis accordez à l’application l’accès à la pile OIDC.

9. Sélectionnez **Ajouter une autorisation**, puis **Microsoft Graph** et **Autorisations déléguées**.

10. Dans la section **Autorisations OpenID**, ajoutez E-mail, OpenID et Profil, puis sélectionnez **Ajouter des autorisations**.

    ![Image montrant l’ajout d’autorisations OpenID](media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png)

11. Une fois les autorisations ajoutées, pour améliorer l’expérience utilisateur et supprimer les invites de consentement de l’utilisateur, sélectionnez l’option **Accorder le consentement de l’administrateur pour le nom de domaine du locataire**, puis attendez que l’état **Accordé** apparaisse.

    ![Image montrant un accord de consentement d’administrateur](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. Si votre application dispose d’un URI de redirection ou d’une URL de réponse, accédez à l’onglet **Authentification**, puis sélectionnez **Ajouter une plateforme** et **Web**. Entrez l’URL appropriée, puis sélectionnez Jetons d’accès et des jetons d’ID en bas avant de sélectionner **Configurer**.

    ![Image montrant la configuration de jetons](media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png)

    Si nécessaire, sous **Paramètres avancés** dans le menu Authentification, définissez **Autoriser les flux clients publics** sur Oui.

    ![Image montrant l’autorisation de flux de client public](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. Revenez à votre application configurée OIDC, puis importez l’ID d’application et la clé secrète client dans votre application avant de tester. Configurez votre application pour qu’elle utilise la configuration ci-dessus, comme l’ID client, le secret et les étendues.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migrer un serveur d’autorisation personnalisé vers Azure AD

Les serveurs d’autorisation Okta mappent de un à un aux inscriptions d’application qui [exposent une API](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope).

Le serveur d’autorisation Okta par défaut doit être mappé à des étendues/autorisations Microsoft Graph.

![Image montrant une autorisation Okta par défaut](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Étapes suivantes 

- [Migrer la fédération Okta vers Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)