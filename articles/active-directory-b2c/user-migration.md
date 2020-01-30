---
title: Approches de la migration des utilisateurs
titleSuffix: Azure AD B2C
description: Abordez les principaux concepts et les concepts avancés de la migration utilisateur avec l’API Graph Azure AD et, éventuellement, avec les stratégies personnalisées Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c70ab718235a67d9f039df638f8ba90b21ab15b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850736"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrer des utilisateurs vers Azure Active Directory B2C

Lorsque vous migrez votre fournisseur d’identité vers Azure Active Directory B2C (Azure AD B2C), il se peut que vous deviez également migrer les comptes d’utilisateur. Cet article explique comment migrer des comptes d’utilisateur existants de n’importe quel fournisseur d’identité vers Azure AD B2C. L’article n’a pas vocation à être normatif, mais plutôt à décrire quelques scénarios. Le développeur est responsable du choix de l’approche adaptée.

## <a name="user-migration-flows"></a>Flux de migration utilisateur

Avec Azure AD B2C, vous pouvez migrer des utilisateurs par le biais de [l’API Graph Azure AD][B2C-GraphQuickStart]. Le processus de migration utilisateur se divise en deux flux :

- **Prémigration** : Ce flux s’applique quand avez librement accès aux informations d’identification d’un utilisateur (nom d’utilisateur et mot de passe) ou quand les informations d’identification sont chiffrées, mais que vous pouvez les déchiffrer. Le processus de prémigration implique la lecture des utilisateurs de l’ancien fournisseur d’identité et la création de comptes dans le répertoire Azure AD B2C.

- **Prémigration et réinitialisation du mot de passe** : Ce flux s’applique quand le mot de passe d’un utilisateur n’est pas accessible. Par exemple :
  - Le mot de passe est stocké au format HASH.
  - Le mot de passe est stocké dans un fournisseur d’identité auquel vous n’avez pas accès. Votre ancien fournisseur d’identité valide les informations d’identification utilisateur en appelant un service web.

Dans les deux flux, vous exécutez tout d’abord le processus de prémigration, lisez les utilisateurs de votre ancien fournisseur d’identité et créez des comptes dans le répertoire Azure AD B2C. Si vous n’avez pas le mot de passe, vous créez le compte à l’aide d’un mot de passe généré aléatoirement. Vous demandez ensuite à l’utilisateur de modifier le mot de passe, ou l’utilisateur est invité par Azure AD B2C à le réinitialiser quand il se connecte pour la première fois.

## <a name="password-policy"></a>Stratégie de mot de passe

La stratégie de mot de passe Azure AD B2C (pour les comptes locaux) est basée sur la stratégie Azure AD. Les stratégies de réinitialisation du mot de passe, d’inscription ou de connexion Azure AD B2C utilisent des mots de passe « forts » et sans date d’expiration. Pour plus d’informations, consultez [Stratégie de mot de passe dans Azure AD][AD-PasswordPolicies].

Si les comptes que vous souhaitez migrer présentent un mot de passe moins fort que les [règles de mot de passe fort d’Azure AD B2C][AD-PasswordPolicies], vous pouvez désactiver la condition de mot de passe fort. Pour modifier la stratégie de mot de passe par défaut, définissez la propriété `passwordPolicies` sur `DisableStrongPassword`. Par exemple, vous pouvez modifier la demande de création d’utilisateur comme suit :

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Étape 1 : Utiliser l’API Graph Azure AD pour effectuer une migration des utilisateurs

Vous créez le compte d’utilisateur Azure AD B2C via l’API Graph (avec le mot de passe ou avec un mot de passe aléatoire). Cette section décrit le processus de création de comptes d’utilisateur dans le répertoire Azure AD B2C à l’aide de l’API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Étape 1.1 : Inscrire votre application dans votre locataire

Pour communiquer avec l’API Graph, vous devez d’abord disposer d’un compte de service avec des privilèges Administrateur. Dans Azure AD, vous inscrivez une application et activez l’accès en écriture à l’annuaire. Les informations d’identification de l’application sont **ID de l’application** et **Secret de l’application**. L’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph.

Tout d’abord, inscrivez une application que vous pouvez utiliser pour des tâches de gestion telles que la migration utilisateur.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Étape 1.2 : Accorder des autorisations d’administration à votre application

Ensuite, accordez à l’application les autorisations d’API Graph Azure AD requises pour écrire dans l’annuaire.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Étape 1.3 : Créer le secret d’application

Créez un secret client (une clé) à utiliser par l’application de migration utilisateur que vous configurez dans une étape ultérieure.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Vous disposez maintenant d’une application autorisée à créer, lire et mettre à jour des utilisateurs dans votre locataire Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Étape 1.4 : (Facultatif) Nettoyage de l’environnement

L’autorisation *Accéder en lecture et en écriture aux données de l’annuaire* n’inclut *PAS* le droit de supprimer des utilisateurs. Pour permettre à votre application de supprimer des utilisateurs (pour nettoyer votre environnement), vous devez effectuer une étape supplémentaire, qui implique d’exécuter PowerShell afin de définir des autorisations Administrateur des comptes d’utilisateur. Si vous n’en avez pas besoin, vous pouvez passer à la section suivante.

> [!IMPORTANT]
> Vous devez utiliser un compte d’administrateur de locataire B2C qui est *local* pour le locataire B2C. La syntaxe du nom de compte est la suivante : *admin\@contosob2c.onmicrosoft.com*.

Dans ce script PowerShell, qui requiert le [module Azure AD PowerShell V2][AD-Powershell], procédez comme suit :

1. Connectez-vous à votre service en ligne. Pour ce faire, exécutez la cmdlet `Connect-AzureAD` à l’invite de commandes Windows PowerShell, puis indiquez vos informations d’identification.

1. Utilisez **l’ID de l’application** pour assigner à l’application le rôle d’administrateur de compte utilisateur. Comme ces rôles ont des identificateurs bien connus, il vous suffit d’entrer **l’ID de votre application** dans le script.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Remplacez la valeur `$AppId` par **l’ID de votre application** Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Étape 2 : Exemple d’application de prémigration

Vous pouvez trouver l’exemple de code de prémigration dans le dépôt GitHub `azure-ad-b2c/user-migration` géré par la communauté :

[azure-ad-b2c/user-migration/pre-migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Étape 2.1 : Modifier le fichier de données de migration

L’exemple d’application utilise un fichier JSON qui contient des données utilisateur factices. Après avoir correctement exécuté l’exemple, vous pouvez modifier le code pour utiliser des données de votre propre base de données. Vous pouvez aussi exporter le profil utilisateur vers un fichier JSON, puis configurer l’application pour qu’elle utilise ce fichier.

Pour modifier le fichier JSON, ouvrez la solution Visual Studio `AADB2C.UserMigration.sln`. Dans le projet `AADB2C.UserMigration`, ouvrez le fichier `UsersData.json`.

![Partie du fichier UsersData. JSON montrant les blocs JSON de deux utilisateurs](./media/user-migration/pre-migration-data-file.png)

Comme vous pouvez le voir, le fichier contient une liste d’entités d’utilisateur. Chaque entité d’utilisateur présente les propriétés suivantes :

- email
- displayName
- firstName
- lastName
- un mot de passe (peut être vide)

> [!NOTE]
> Au moment de la compilation, Visual Studio copie le fichier dans le répertoire `bin`.

### <a name="step-22-configure-the-application-settings"></a>Étape 2.2 : Configurer les paramètres de l’application

Dans le projet `AADB2C.UserMigration`, ouvrez le fichier *App.config*. Remplacez les paramètres suivants de l’application par vos propres valeurs :

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - L’utilisation d’une chaîne de connexion de table Azure est décrite dans les sections suivantes.
> - Le nom de votre locataire B2C est le domaine que vous avez entré lors de la création du locataire, et il est affiché dans le portail Azure. Le nom du client se termine généralement par le suffixe *.onmicrosoft.com* (par exemple, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Étape 2.3 : Exécuter le processus de prémigration

Cliquez avec le bouton droit sur la solution `AADB2C.UserMigration`, puis régénérez l’exemple. Si l’opération aboutit, vous devez maintenant disposer d’un fichier exécutable `UserMigration.exe` dans le répertoire `AADB2C.UserMigration\bin\Debug\net461`. Pour exécuter le processus de migration, utilisez l’un des paramètres de ligne de commande suivants :

- Pour **migrer les utilisateurs avec leur mot de passe**, utilisez la commande `UserMigration.exe 1`.

- Pour **migrer les utilisateurs avec un mot de passe aléatoire**, utilisez la commande `UserMigration.exe 2`. Cette opération crée également une entité de table Azure. Vous configurerez ultérieurement la stratégie permettant d’appeler le service d’API REST. Ce service utilise une table Azure pour suivre et gérer le processus de migration.

![Fenêtre d’invite de commandes montrant la sortie de la commande UserMigration.exe](./media/user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Étape 2.4 : Vérifier le processus de prémigration

Pour valider la migration, utilisez l’une des deux méthodes suivantes :

- Pour rechercher un utilisateur par nom d’affichage, utilisez le portail Azure :

   1. Ouvrez **Azure AD B2C**, puis sélectionnez **Utilisateurs**.
   1. Dans la zone de recherche, tapez le nom d’affichage de l’utilisateur, puis affichez le profil de l’utilisateur.

- Pour récupérer un utilisateur par adresse e-mail de connexion, utilisez l’exemple d’application :

   1. Exécutez la commande suivante :

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Vous pouvez également récupérer un utilisateur par nom d’affichage à l’aide de la commande suivante : `UserMigration.exe 4 "<Display name>"`.

   1. Ouvrez le fichier UserProfile.json dans un éditeur JSON pour voir les informations de l’utilisateur.

      ![Fichier UserProfile.json ouvert dans l’éditeur Visual Studio Code](./media/user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Étape 2.5 : (Facultatif) Nettoyage de l’environnement

Si vous souhaitez nettoyer votre locataire Azure AD et supprimer les utilisateurs de votre répertoire Azure AD, exécutez la commande `UserMigration.exe 5`.

> [!NOTE]
> * Pour nettoyer votre locataire, configurez les autorisations Administrateur des comptes d’utilisateur pour votre application.
> * L’exemple d’application de migration nettoie tous les utilisateurs répertoriés dans le fichier JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Étape 2.6. : Se connecter avec les utilisateurs migrés (avec le mot de passe utilisateur)

Une fois que vous avez exécuté le processus de prémigration avec le mot de passe des utilisateurs, les comptes sont prêts à être utilisés et les utilisateurs peuvent se connecter à votre application à l’aide d’Azure AD B2C. Si vous n’avez pas accès au mot de passe des utilisateurs, passez à la section suivante.

## <a name="step-3-help-users-reset-their-password"></a>Étape 3 : Aider les utilisateurs à réinitialiser leur mot de passe

Si vous migrez les utilisateurs avec un mot de passe aléatoire, ils doivent réinitialiser leur mot de passe. Pour les aider à le faire, envoyez leur un e-mail de bienvenue contenant un lien pour réinitialiser le mot de passe.

Pour obtenir le lien vers votre stratégie de réinitialisation du mot de passe, procédez comme suit : Cette procédure suppose que vous avez déjà créé une [stratégie personnalisée](custom-policy-get-started.md) de réinitialisation du mot de passe.

1. Sélectionnez le répertoire contenant votre locataire Azure AD B2C à l’aide du filtre **Répertoire + abonnement**, situé dans la partie supérieure droite du [Portail Microsoft Azure](https://portal.azure.com).
1. Sélectionnez **Azure AD B2C** dans le menu de gauche (ou dans **Tous les services**).
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez une stratégie de réinitialisation de mot de passe. Par exemple, *B2C_1A_PasswordReset*.
1. Sélectionnez votre application dans la liste déroulante **Sélectionner une application**.

    > [!NOTE]
    > Pour pouvoir utiliser la commande **Exécuter maintenant**, une application minimum doit être inscrite auprès du locataire. Pour savoir comment inscrire des applications, voir [Didacticiel : Inscrire une application dans Azure Active Directory B2C][B2C-AppRegister].

1. Copiez l’URL affichée dans la zone de texte **Point de terminaison Exécuter maintenant**, puis envoyez-la à vos utilisateurs.

    ![Page stratégie de réinitialisation du mot de passe avec Exécuter maintenant le point de terminaison en surbrillance](./media/user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Étape 4 : (Facultatif) Modifier votre stratégie pour vérifier et définir l’état de migration utilisateur

> [!NOTE]
> Pour vérifier et modifier l’état de migration utilisateur, vous devez utiliser une stratégie personnalisée. Vous devez suivre les instructions d’installation indiquées dans [Bien démarrer avec les stratégies personnalisées][B2C-GetStartedCustom].

Quand les utilisateurs tentent de se connecter sans commencer par réinitialiser le mot de passe, votre stratégie doit renvoyer un message d’erreur convivial. Par exemple :

> *Votre mot de passe a expiré. Pour le réinitialiser, sélectionnez le lien Réinitialiser le mot de passe.*

Cette étape facultative requiert l’utilisation de stratégies personnalisées Azure AD B2C, comme décrit dans l’article [Bien démarrer avec les stratégies personnalisées][B2C-GetStartedCustom].

Dans cette section, vous modifiez la stratégie pour vérifier l’état de migration utilisateur à la connexion. Si l’utilisateur n’a pas changé le mot de passe, renvoyez un message d’erreur 409 HTTP invitant l’utilisateur à sélectionner le lien **Vous avez oublié votre mot de passe ?** .

Pour suivre la modification de mot de passe, vous utilisez une table Azure. Quand vous exécutez le processus de prémigration avec le paramètre de ligne de commande `2`, vous créez une entité d’utilisateur dans une table Azure. Votre service effectue les opérations suivantes :

- Lors de la connexion, la stratégie Azure AD B2C appelle votre service RESTful de migration, en envoyant un message e-mail en tant que revendication d’entrée. Le service recherche l’adresse e-mail dans la table Azure. Si l’adresse existe, le service génère un message d’erreur : *Vous devez modifier le mot de passe*.

- Une fois que l’utilisateur a correctement modifié le mot de passe, supprimez l’entité de la table Azure.

> [!NOTE]
> Nous utilisons une table Azure pour simplifier l’exemple. Vous pouvez stocker l’état de migration dans n’importe quelle base de données ou en tant que propriété personnalisée dans le compte Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1 : Mettre à jour le paramètre de votre application

1. Pour tester la démonstration de l’API RESTful, ouvrez `AADB2C.UserMigration.sln` dans Visual Studio.
1. Dans le projet `AADB2C.UserMigration.API`, ouvrez le fichier *Web.config*. Remplacez le paramètre par celui configuré à l’[étape 2.2](#step-22-configure-the-application-settings) :

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Étape 4.2 : Déployer votre application web dans Azure App Service

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `AADB2C.UserMigration.API`, sélectionnez « Publier... ». Suivez les instructions pour publier sur Azure App Service. Pour plus d’informations, consultez [Déploiement de votre application dans Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Étape 4.3 : Ajouter un profil technique et une validation de profil technique à votre stratégie

1. Dans l’Explorateur de solutions, développez « Éléments de Solution », puis ouvrez le fichier de stratégie *TrustFrameworkExtensions.xml*.
1. Modifiez les champs `TenantId`, `PublicPolicyUri` et `<TenantId>` en remplaçant `yourtenant.onmicrosoft.com` par le nom de votre locataire.
1. Sous l’élément `<TechnicalProfile Id="login-NonInteractive">`, remplacez toutes les instances de `ProxyIdentityExperienceFrameworkAppId` et `IdentityExperienceFrameworkAppId` par les ID d’application configurés lors des étapes indiquées dans [Bien démarrer avec les stratégies personnalisées][B2C-GetStartedCustom].
1. Sous le nœud `<ClaimsProviders>`, recherchez l’extrait de code XML ci-après. Modifiez la valeur de `ServiceUrl` pour pointer vers l’URL de votre Azure App Service.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Le profil technique précédent définit une revendication d’entrée : `signInName` (envoyée sous forme d’e-mail). Lors de la connexion, la revendication est envoyée à votre point de terminaison RESTful.

Une fois que vous avez défini le profil technique pour votre API RESTful, configurez le profil technique de `SelfAsserted-LocalAccountSignin-Email` existant pour appeler en plus votre profil technique REST API en le remplaçant dans votre fichier *TrustFrameworkExtensions.xml* :

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Modifiez alors le `Id` du profil technique `LocalAccountSignIn` par `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Étape 4.4 : Charger la stratégie sur un client

1. Dans le [portail Azure][Portal], passez au [contexte de votre locataire Azure AD B2C][B2C-NavContext], puis sélectionnez **Azure AD B2C**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Toutes les stratégies**.
1. Sélectionnez **Charger la stratégie**.
1. Activez la case à cocher **Remplacer la stratégie si elle existe**.
1. Chargez le fichier *TrustFrameworkExtensions.xml*, puis assurez-vous que sa validation réussit.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Étape 4.5 : Tester la stratégie personnalisée en utilisant Exécuter maintenant

1. Sélectionnez **Azure AD B2C**, puis sélectionnez **Identity Experience Framework**.
1. Ouvrez *B2C_1A_signup_signin*, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.
1. Saisissez les informations d’identification de l’un des utilisateurs migrés, puis sélectionnez **Connexion**. Votre API REST doit générer le message d’erreur suivant :

    ![Page d’inscription montrant un message d'erreur de changement de mot de passe](./media/user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Étape 4.6 : (Facultatif) Résoudre les problèmes liés à votre API REST

Vous pouvez afficher et surveiller les informations de journalisation en temps quasi-réel.

1. Dans le menu des paramètres de votre application RESTful, sous **Supervision**, sélectionnez **Journaux d’activité de Diagnostic**.
1. Définissez **Journal des applications (Système de fichiers)** sur **Activé**.
1. Définissez le paramètre **Niveau** sur **Détaillé**.
1. Sélectionnez **Enregistrer**.

    ![Page de configuration des journaux de diagnostic dans le portail Azure](./media/user-migration/pre-migration-diagnostic-logs.png)

1. Dans le menu **Paramètres**, sélectionnez **Flux de journaux**.
1. Vérifiez la sortie de l’API RESTful.

> [!IMPORTANT]
> Utilisez les journaux de diagnostic uniquement pendant le développement et le test. La sortie de l’API RESTful peut contenir des informations confidentielles qui ne doivent pas être exposées en production.

## <a name="optional-download-the-complete-policy-files"></a>(Facultatif) Télécharger les fichiers de stratégie complets

Une fois que vous avez [pris en main les stratégies personnalisées][B2C-GetStartedCustom], nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie][UserMigrationSample-policy] sont à votre disposition pour référence.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
