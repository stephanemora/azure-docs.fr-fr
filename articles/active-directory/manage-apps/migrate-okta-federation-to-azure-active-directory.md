---
title: Didacticiel pour migrer une fédération Okta vers l’authentification gérée par Azure AD
titleSuffix: Active Directory
description: Découvrez comment migrer vos applications fédérées Okta vers l’authentification gérée par Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: b2486f4be20d2347f0cadff04ef8d0aa776ccdc5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791685"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Didacticiel : Migrer une fédération Okta vers l’authentification gérée par Azure AD

Ce didacticiel explique comment fédérer vos locataires Office 365 existants avec Okta pour les fonctionnalités d’authentification unique (SSO).

Vous pouvez migrer une fédération vers Azure Active Directory (AD) de manière échelonnée pour vous assurer que l’expérience d’authentification est bien celle que les utilisateurs souhaitent. Et aussi, pour tester l’accès de fédération inverse aux applications d’authentification unique Okta restantes.

## <a name="prerequisites"></a>Prérequis

- Locataire Office 365 fédéré à Okta pour l’authentification unique
- Configurez un serveur ou des agents d’approvisionnement cloud Azure AD Connect pour l’approvisionnement d’utilisateurs dans Azure AD.

## <a name="step-1---configure-azure-ad-connect-for-authentication"></a>Étape 1 : configurer Azure AD Connect pour l’authentification

Il se peut que les clients qui ont fédéré leurs domaines Office 365 avec Okta ne disposent pas d’une méthode d’authentification valide configurée dans Azure AD. Avant une migration vers une authentification gérée, Azure AD Connect doit être validé et configuré avec l’une des options suivantes pour autoriser l’utilisateur à se connecter.

Pour déterminer la méthode la mieux adaptée à votre environnement, utilisez les méthodes suivantes :

- **Synchronisation de hachage du mot de passe** - La [Synchronisation de hachage du mot de passe](../hybrid/whatis-phs.md) est une extension de la fonctionnalité de synchronisation d’annuaires implémentée par un serveur ou des agents d’approvisionnement cloud Azure AD Connect. Vous pouvez utiliser cette fonctionnalité pour vous connecter à des services Azure AD tels que Microsoft 365. Vous vous connectez au service à l’aide du mot de passe que vous utilisez pour vous connecter à votre instance locale d’Active Directory.

- **Authentification directe** - L’[Authentification directe](../hybrid/how-to-connect-pta.md) Azure AD permet à des utilisateurs de se connecter à des applications locales et cloud à l’aide des mêmes mots de passe. Lorsque des utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide les mots de passe des utilisateurs directement par rapport à l’Active Directory local via l’agent d’Authentification directe.

- **Authentification unique transparente** - L’[Authentification unique transparente Azure AD](../hybrid/how-to-connect-sso.md) connecte automatiquement des utilisateurs d’ordinateurs d’entreprise connectés à votre réseau d’entreprise. L’Authentification unique transparente offre à vos utilisateurs un accès aisé à vos applications cloud ne nécessitant pas d’autres composants locaux.

L’Authentification unique transparente peut également être déployée pour la Synchronisation de hachage du mot de passe ou l’Authentification directe afin de créer une expérience d’authentification transparente pour les utilisateurs dans Azure AD.

Veillez à déployer toutes les éléments prérequis de l’Authentification unique transparente pour vos utilisateurs finaux en suivant le [Guide de déploiement](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites).

Pour notre exemple, nous allons configurer la Synchronisation de hachage du mot de passe et l’Authentification unique transparente.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Configurer Azure AD Connect pour la Synchronisation de hachage du mot de passe et l’Authentification unique transparente

Pour configurer Azure AD Connect pour la Synchronisation de hachage du mot de passe, procédez comme suit :

1. Sur votre serveur Azure AD Connect, lancez l’application **Azure AD Connect** à partir du menu Démarrer ou de l’icône du bureau, puis sélectionnez **Configurer**.

   ![Image montrant l’icône Azure AD et le bouton Configurer](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. Sélectionnez **Modifier la connexion utilisateur** > **Suivant**.

   ![Image montrant l’écran Modifier la connexion utilisateur](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. Entrez les informations d’identification d’Administrateur général dans la page suivante.

   ![Image montrant la saisie des informations d’identification d’administrateur général](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. Actuellement, le serveur est configuré pour la fédération avec Okta. Mettez à jour la sélection en choisissant Synchronisation de hachage du mot de passe. Sélectionnez la case à cocher **Activer l’authentification unique**.

5. Une fois la sélection mise à jour, sélectionnez **Suivant**.

Pour activer l’Authentification unique transparente, procédez comme suit :

1. Entrez les informations d’identification d’un administrateur de domaine pour le site local, puis sélectionnez **Suivant**.

   ![Image montrant la saisie des informations d’identification d’un administrateur de domaine](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. Dans la dernière page, sélectionnez **Configurer** pour mettre à jour le serveur de Azure AD Connect.

   ![image montrant la mise à jour du serveur Azure AD Connect](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. Ignorez l’avertissement relatif à la jonction Azure AD Hybride pour l’instant. En revanche, vous devez reconfigurer les **Options de l’appareil** après la désactivation de la fédération d’Okta.

   ![Image montrant la reconfiguration des Options de l’appareil](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="step-2---configure-staged-rollout-features"></a>Étape 2 : configurer les fonctionnalités de déploiement échelonné

Le [déploiement échelonné de l’authentification cloud](../hybrid/how-to-connect-staged-rollout.md) est une fonctionnalité d’Azure AD qui peut être utilisée pour tester la dé-fédération des utilisateurs avant de dé-fédérer un domaine entier. Avant d’opérer le déploiement, consultez les [conditions préalables](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Après avoir activé la Synchronisation de hachage du mot de passe et l’Authentification unique transparente sur le serveur Azure AD Connect, procédez comme suit pour configurer le déploiement échelonné.

1. Accédez au [portail Azure](https://portal.azure.com/#home), puis sélectionnez **Afficher** ou **Gérer Azure Active Directory**.

   ![Image montrant le portail Azure](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. Dans le menu Azure Active Directory, sélectionnez **Azure AD Connect**, puis confirmez que la Synchronisation de hachage du mot de passe est activée dans le locataire.

3. Après confirmation, sélectionnez **Activer le déploiement intermédiaire pour la connexion utilisateur managée**.

   ![Image montrant l’activation du déploiement intermédiaire](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. Il se peut que votre paramètre de Synchronisation de hachage du mot de passe ait été **Activé** après la configuration du serveur. Si ce n’est pas le cas, activez-le maintenant. Vous observerez que l’Authentification unique transparente est **Désactivée**. Si vous tentez de l’activer dans le menu, un message d’erreur s’affiche, indiquant qu’il est déjà activé pour les utilisateurs dans le locataire.

5. Après avoir activé la Synchronisation de hachage du mot de passe, sélectionnez **Gérer les groupes**.

   ![Image montrant l’activation de la Synchronisation de hachage du mot de passe](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Suivez les instructions pour ajouter un groupe au déploiement de la Synchronisation de hachage du mot de passe. Dans l’exemple suivant, un groupe de sécurité est utilisé avec 10 membres pour commencer.

![Image montrant un exemple de groupe de sécurité](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Après avoir ajouté le groupe, patientez environ 30 minutes pour que la fonctionnalité prenne effet dans votre locataire. Une fois que la fonctionnalité a pris effet, vos utilisateurs ne sont plus redirigés vers Okta quand ils tentent d’accéder aux services Office 365.

Certains scénarios d’utilisation de la fonctionnalité de déploiement échelonné ne sont pas pris en charge, à savoir :  

- L’authentification héritée telle que POP3 et SMTP n’est pas prise en charge.

- Si vous avez configuré la jonction Azure AD Hybride pour une utilisation avec Okta, l’acheminement de tous les flux de jonction Azure AD Hybride vers Okta continue jusqu’à ce que le domaine ait été dé-fédéré. Il doit rester dans Okta une stratégie de connexion permettant une authentification héritée pour les clients Windows de jonction Azure AD Hybride.

## <a name="step-3---create-okta-app-in-azure-ad"></a>Étape 3 : créer une application Okta dans Azure AD

Les utilisateurs convertis à l’authentification gérée peuvent toujours avoir des applications dans Okta auxquelles ils doivent accéder, afin de permettre aux utilisateurs d’accéder facilement à ces applications. Découvrez comment configurer une inscription d’application Azure AD qui lie à la page d’accueil Okta pour les utilisateurs.

1. Pour configurer l’inscription d’application d’entreprise pour Okta, accédez au [portail Azure](https://portal.azure.com/#home). Sélectionnez **Afficher** dans **Gérer Azure Active Directory**.

2. Ensuite, sélectionnez **Applications d’entreprise** dans le menu sous la section Gérer.

   ![Image montrant les applications d’entreprise](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. Dans le menu **Toutes les applications**, sélectionnez **Nouvelle application**.

   ![Image montrant les nouvelles applications](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. Sélectionnez **Créer votre propre application**. Dans le menu latéral qui s’affiche, donnez un nom à l’application Okta, sélectionnez le radial pour **Inscrire une application sur laquelle vous travaillez pour l’intégrer avec Azure AD**, puis sélectionnez **Créer**.

   ![Image montrant l’inscription d’une application](media/migrate-okta-federation-to-azure-active-directory/register-application.png)

5. Après avoir inscrit l’application, modifiez-la en définissant un compte dans n’importe quel annuaire d’organisation, tel un annuaire Azure AD Directory mutualisé, puis sélectionnez **Inscrire**.

   ![image montrant l’inscription d’une application et la modification du compte de celle-ci](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. Après avoir ajouté l’inscription, revenez au menu Azure AD, sélectionnez **Inscriptions d’applications**, puis ouvrez l’inscription nouvellement créée.

   ![Image montrant l’inscription d’une application](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. Une fois l’application ouverte, enregistrez l’ID de votre locataire et l’ID de l’application.

   >[!Note]
   >Vous aurez besoin de l’ID de locataire et de l’ID d’application ultérieurement pour configurer le fournisseur d’identité dans Okta.

   ![Image montrant un ID de locataire et un ID d’application](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. Dans le menu de gauche, sélectionnez **Certificats et secrets**. Sélectionnez **Nouveau secret client**, puis attribuez-lui un nom générique et définissez son heure d’expiration.

9. Enregistrez la valeur et l’ID du secret avant de quitter cette page.

   >[!NOTE]
   >Vous ne pourrez pas enregistrer ces informations ultérieurement, et devrez donc régénérer un secret en cas de perte.

   ![Image montrant des secrets d’enregistrement](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. Sélectionnez **Autorisations de l’API** dans le menu de gauche, puis accordez à l’application l’accès à la pile OpenID Connecter (OIDC).

11. Sélectionnez **Ajouter une autorisation** > **Microsoft Graph** > **Autorisations déléguées**.

    ![Image montrant des autorisations déléguées](media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png)

12. Dans la section Autorisations OpenID, ajoutez **E-mail**, **OpenID** et **Profil**, puis sélectionnez **Ajouter les autorisations**.

    ![Image montre l’ajout d’autorisations](media/migrate-okta-federation-to-azure-active-directory/add-permissions.png)

13. Sélectionnez l’option **Accorder le consentement de l’administrateur pour le nom de domaine du locataire**, puis attendez que l’état Accordé apparaisse.

    ![Image montre l’accord du consentement](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. Une fois les autorisations accordées, ajoutez l’URL de la page d’accueil sous **Personnalisation** pour la page d’accueil de l’application de votre utilisateur.

    ![Image montrant l’ajout d’une personnalisation](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. Une fois l’application configurée, passez au portail d’administration Okta et configurez Microsoft en tant que fournisseur d’identité. Sélectionnez **Sécurité** > **Fournisseurs d’identité**, ajoutez un fournisseur d’identité, puis ajoutez l’option **Microsoft** par défaut.

    ![Image montrant la configuration du fournisseur d’identité](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. Dans la page Fournisseur d’identité, copiez votre ID d’application dans le champ ID client, puis la clé secrète client dans le champ Clé secrète client.

17. Sélectionnez **Afficher les paramètres avancées**. Par défaut, ce paramètre lie le Nom d’utilisateur principal (UPN) dans Okta et Azure AD pour l’accès de fédération inverse.

    >[!IMPORTANT]
    >Si vos UPN ne correspondent pas dans Okta et Azure AD, sélectionnez un attribut commun entre les utilisateurs avec lequel établir une correspondance.

18. Finalisez votre sélection pour l’approvisionnement automatique. Par défaut, si un utilisateur ne correspond pas à Okta, il tente de l’approvisionner dans Azure AD. Si vous avez migré l’approvisionnement à partir d’Okta, sélectionnez l’option **Rediriger vers la page de connexion à Okta**.

    ![Image montrant la redirection de la connexion à Okta](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Une fois le fournisseur d’identité créé, une configuration supplémentaire est nécessaire pour envoyer des utilisateurs au fournisseur d’identité approprié.

19. Sélectionnez **Règles de routage** dans le menu Fournisseurs d’identité, puis sélectionnez **Ajouter une règle de routage** en utilisant l’un des attributs disponibles dans le profil Okta.

20. Configurez la stratégie comme indiqué pour diriger les connexions de tous les appareils et adresses IP vers Azure AD.

    Dans l’exemple, notre **Division** d’attribut n’est utilisée sur aucun de nos profils Okta, ce qui facilite son utilisation pour le routage de fournisseur d’identité.

    ![Image montrant la division pour le routage de fournisseur d’identité](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. Après avoir ajouté la règle de routage, enregistrez l’URI de redirection, puis ajoutez-le à l’**Inscription d’application**.

    ![Image montrant l’inscription d’application](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. Revenez à votre inscription d’application, sélectionnez l’onglet Authentification, puis **Ajouter une plateforme** et **Web**.

    ![Image montrant l’ajout d’une plateforme](media/migrate-okta-federation-to-azure-active-directory/add-platform.png)

23. Ajoutez l’URI de redirection à partir du fournisseur d’identité dans Okta, puis sélectionnez **Jetons d’accès et d’ID**.

    ![Image montrant les jetons d’accès et d’ID Okta](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. Dans la console d’administration, sélectionnez **Annuaire** > **Personnes**. Sélectionnez votre premier utilisateur de test et son profil.

25. Pendant la modification du profil, ajoutez **ToAzureAD** pour qu’il corresponde à l’exemple, puis sélectionnez **Enregistrer**.

    ![Image montrant la modification du profil](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. Après avoir enregistré les attributs utilisateur, tentez de vous connecter en tant qu’utilisateur modifié au [portail Microsoft 365](https://portal.office.com). Vous constaterez la formation d’une boucle si votre utilisateur ne fait pas partie du pilote d’authentification géré. Pour que les utilisateurs quittent la boucle, ajoutez-les à l’expérience d’Authentification gérée.

## <a name="step-4---test-okta-app-access-on-pilot-members"></a>Étape 4 : tester l’accès à l’application Okta sur les membres du pilote

Après avoir configuré l’application Okta dans Azure AD et le fournisseur d’identité dans le portail Okta, vous devez affecter l’application aux utilisateurs.

1. Accédez au portail Azure, puis sélectionnez **Azure Active Directory** > **Applications d’entreprise**.

2. Sélectionnez l’inscription d’application créée précédemment, puis accédez à **Utilisateurs et groupes**. Ajoutez le groupe en corrélation avec le pilote d’Authentification gérée.

>[!NOTE]
>Vous ne pouvez ajouter des utilisateurs et des groupes qu’à partir de la sélection Applications d’entreprise. Vous ne pouvez pas ajouter d’utilisateurs dans le menu Inscriptions d’applications.

   ![Image montrant l’ajout d’un groupe](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. Après environ 15 minutes, connectez-vous en tant que l’un des utilisateurs du pilote d’authentification gérée, puis accédez à [Mes applications](https://myapplications.microsoft.com).

   ![Image montrant l’accès à Mes applications](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. Une fois l’authentification effectuée, une vignette **Accès à l’application Okta** s’affiche, qui lie l’utilisateur en retour à la page d’accueil Okta.

## <a name="step-5---test-managed-authentication-on-pilot-members"></a>Étape 5 : authentification gérée par test sur les membres du pilote

Une fois l’application de fédération inverse Okta configurée, demandez à vos utilisateurs d’effectuer un test complet de l’expérience d’authentification gérée. Nous vous recommandons de configurer la marque de la société pour aider vos utilisateurs à distinguer le locataire approprié auquel ils se connectent. Obtenez des [conseils](../fundamentals/customize-branding.md) sur la configuration de la marque de la société.

>[!IMPORTANT]
>Déterminez toutes les stratégies d’accès conditionnel supplémentaires susceptibles d’être nécessaires avant de dé-fédérer l’ensemble des domaines d’Okta. Consultez **Stratégies d’authentification Okta pour la migration d’accès conditionnel Azure AD afin de sécuriser votre environnement avant coupure complète**.

## <a name="step-6---remove-federation-for-office-365-domains"></a>Étape 6 : supprimer la fédération pour les domaines Office 365

Une fois que votre organisation est à l’aise avec l’expérience d’authentification gérée, il est temps de dé-fédérer votre domaine d’Okta. Pour ce faire, connectez-vous à MSOnline PowerShell avec les commandes suivantes. Si vous n’avez pas encore le module PowerShell MSOnline, vous pouvez commencer par le télécharger en exécutant la commande install-module MSOnline.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Une fois le domaine défini sur l’Authentification gérée, vous avez correctement dé-fédéré votre locataire Office 365 d’Okta, tout en conservant l’accès utilisateur à la page d’accueil Okta. 

## <a name="next-steps"></a>Étapes suivantes

- [Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)