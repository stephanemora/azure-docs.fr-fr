---
title: Intégration d’applications dans Azure Active Directory
description: Découvrez comment mettre à jour une application dans Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 437217bdd3cc2ae8724d6bf24134d8fe725daac7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093306"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Démarrage rapide : Mettre à jour une application dans Azure Active Directory

Les développeurs en entreprise et les fournisseurs de services SaaS (software-as-a-service) qui ont inscrit des applications auprès d’Azure Active Directory (Azure AD) peuvent avoir besoin de configurer vos applications de sorte qu’elles accèdent à d’autres ressources telles que des API web, pour les rendre disponibles à d’autres organisations, etc.

Ce démarrage rapide vous enseigne les différentes méthodes pour configurer ou mettre à jour votre application de façon à répondre à vos exigences et vos besoins ou à ceux d’autres organisations.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vérifiez que vous avez déjà effectué les étapes suivantes :

* Lire la présentation de [l’infrastructure de consentement Azure AD](consent-framework.md) qu’il est important de comprendre lors de la création d’applications devant être utilisées par d’autres utilisateurs ou applications.
* Vous procurer un locataire Azure AD auprès duquel des applications sont inscrites.
  * Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](quickstart-create-new-tenant.md).
  * Si aucune application n’est inscrite auprès de votre locataire, [découvrez comment ajouter une application à Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Configurer une application cliente pour accéder aux API web

Une application cliente web/confidentielle doit établir des informations d’identification sécurisées afin de pouvoir participer à un flux d’octroi d’autorisations qui requiert une authentification. La méthode d’authentification par défaut prise en charge par le portail Azure est l’ID Client + la clé secrète. Cette section décrit les étapes de configuration requises pour fournir les informations d’identification de votre client avec la clé secrète.

Avant qu’un client puisse accéder à une API web exposée par une application de ressources (comme l’API Microsoft Graph), l’infrastructure de consentement permet au client d’obtenir l’autorisation nécessaire en fonction des autorisations demandées. Par défaut, toutes les applications peuvent choisir des autorisations à partir **d’Azure Active Directory** (API Graph) et du modèle de déploiement Azure Classic. L’[autorisation « Connexion et lecture du profil utilisateur » de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) est également sélectionnée par défaut. Si votre client est en cours d’enregistrement dans un locataire possédant des comptes inscrits à Office 365, vous aurez également la possibilité de sélectionner les API web et les autorisations pour Exchange Online et SharePoint. Vous pouvez choisir à partir de deux types d’autorisations pour chaque API web souhaitée :

- Autorisations pour les applications : votre application cliente doit accéder à l’API web directement en tant que telle (aucun contexte utilisateur). Ce type d'autorisation requiert le consentement de l'administrateur et n'est également pas disponible pour les applications clientes natives.
- Autorisations déléguées : votre application cliente doit accéder à l’API web en tant qu’utilisateur connecté, mais avec un accès limité par l’autorisation sélectionnée. Ce type d'autorisation peut être accordé par un utilisateur, à moins que l'autorisation nécessite le consentement de l'administrateur.

  > [!NOTE]
  > L’ajout d’une autorisation déléguée à une application n’accorde pas automatiquement un consentement aux utilisateurs du client. Les utilisateurs doivent donner manuellement leur consentement pour les autorisations déléguées ajoutées lors de l’exécution, à moins que l’administrateur ne donne son consentement au nom de tous les utilisateurs.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Ajouter des informations d’identification d’application ou des autorisations pour accéder aux API web

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
3. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez **Inscriptions des applications**, puis recherchez/sélectionnez l’application que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Vous accédez à la page d’inscription principale de l’application, qui inclut la page **Paramètres** pour l’application. Pour ajouter des informations d’identification à votre application web :
  1. Sélectionnez la section **Clés** de la page **Paramètres**.
  2. Pour ajouter un certificat :
    - Sélectionnez **Télécharger la clé publique**.
    - Sélectionnez le fichier que vous voulez charger. Il doit s’agir d’un fichier de type .cer, .pem ou .crt.
  - Pour ajouter un mot de passe :
    - Ajoutez une description pour votre clé.
    - Sélectionnez une durée.
    - Sélectionnez **Enregistrer**. La colonne située le plus à droite contient la valeur de clé, après avoir enregistré les modifications de configuration. **Veillez à copier la clé** pour une utilisation dans le code de votre application client, car elle n’est plus accessible après avoir quitté cette page.

5. Pour ajouter des autorisations pour accéder aux API de ressources à partir de votre client
  1. Sélectionnez la section **Autorisations requises** dans la page **Paramètres**, puis sélectionnez **Ajouter**.
  1. Sélectionnez **Select an API** (Sélectionner une API) pour sélectionner le type de ressources souhaité.
  1. Parcourez la liste des API disponibles ou utilisez la zone de recherche pour sélectionner une des applications de ressources qui exposent une API web disponibles dans votre répertoire. Sélectionnez la ressource qui vous intéresse, puis cliquez sur **Sélectionner**.
  1. Dans la page **Activer l’accès**, sélectionnez les permissions d’application et/ou les permissions déléguées dont votre application a besoin lors de l’accès à l’API.
   
  ![Mettre à jour l’inscription d’une application - API d’autorisation](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

  ![Mettre à jour l’inscription d’une application - Autorisations](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Lorsque vous avez terminé, cliquez sur le bouton **Sélectionner** sur la page **Activer l’accès**, puis sur le bouton **Fait** sur la page **Ajouter un accès API**. Vous êtes redirigé sur la page **Autorisations requises**, où la nouvelle ressource est ajoutée à la liste des API.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuration d’une application de ressource pour exposer les API web

Vous pouvez développer une API web et la mettre à disposition des applications clientes en exposant l’[étendue](developer-glossary.md#scopes) et les [rôles](developer-glossary.md#roles) d’accès. Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365. Les étendues d’accès sont exposées par le biais du [manifeste de votre application](developer-glossary.md#application-manifest), c’est-à-dire un fichier JSON représentant la configuration d’identité de votre application.

La section suivante indique comment exposer les étendues d’accès en modifiant le manifeste de l’application de la ressource.

### <a name="add-access-scopes-to-your-resource-application"></a>Ajouter des étendues d’accès à votre ressource d’application

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
3. Dans le volet de navigation gauche, sélectionnez **Azure Active Directory > Inscriptions des applications**, puis recherchez/sélectionnez l’application que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application. Basculez vers la page **Modifier le manifeste** en cliquant sur **Manifeste** depuis la page d’inscription de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Si vous le souhaitez, vous pouvez cliquer sur **Télécharger** et le modifier localement, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application.
5. Dans cet exemple, nous allons exposer une nouvelle étendue appelée `Employees.Read.All` sur notre ressource/API, en ajoutant l’élément JSON suivant pour la collection `oauth2Permissions`. L’étendue `user_impersonation` existante est fournie par défaut lors de l’inscription. `user_impersonation`permet à une application cliente de demander l’autorisation d’accéder à la ressource, sous l’identité de l’utilisateur connecté. Veillez à ajouter la virgule après l’élément d’étendue `user_impersonation` existant, et modifiez les valeurs de propriété en fonction des besoins de votre ressource. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```

  > [!NOTE]
  > La valeur `id` doit être générée par programmation ou à l’aide d’un outil de génération de GUID comme [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). La valeur `id` représente un identificateur unique pour l’étendue comme exposée par l’API web. Une fois qu’un client est correctement configuré avec les autorisations pour accéder à votre API web, Azure AD émet un jeton d’accès OAuth2.0. Lorsque le client appelle l’API web, il présente le jeton d’accès dont la revendication de l’étendue (scp) a été configurée sur les autorisations demandées dans son inscription d’application.
  >
  > Vous pouvez exposer des étendues supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs étendues associées à un éventail de fonctions différentes. Votre ressource peut contrôler l’accès à l’API web lors de l’exécution, en évaluant la/les revendication(s) de l’étendue (`scp`) dans le jeton d’accès OAuth 2.0 reçu.

6. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Votre API web est maintenant configurée pour être utilisée par d’autres applications de votre répertoire.

  ![Mettre à jour l’inscription d’une application](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Vérifier que l’API web est exposée à d’autres applications dans votre locataire

1. Revenez à votre locataire Azure AD, sélectionnez de nouveau **Inscriptions des applications**, puis recherchez/sélectionnez l’application cliente que vous souhaitez configurer.

   ![Mettre à jour l’inscription d’une application](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Répétez l’étape 5 comme vous l’avez fait dans [Configurer une application cliente pour accéder aux API web](#configure-a-client-application-to-access-web-apis). Lorsque vous arrivez à l’étape **Sélectionner une API**, recherchez votre ressource en saisissant son nom d’application dans le champ de recherche, puis cliquez sur **Sélectionner**.

3. Sur la page **Activer l’accès**, vous devriez voir la nouvelle étendue, disponible pour les demandes d’autorisations clientes.

  ![Les nouvelles autorisations sont affichées.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Informations complémentaires concernant le manifeste d’application

Le manifeste d’application sert de mécanisme de mise à jour de l’entité Application, qui définit tous les attributs de configuration d’identité d’une application Azure AD, y compris les étendues d’accès d’API dont il a été question ci-dessus. Pour plus d’informations sur l’entité Application et son schéma, consultez la [documentation relative à l’entité Application de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Cet article contient des informations de référence complètes sur les membres de l’entité Application permettant de spécifier des autorisations pour votre API, y compris :  

- Le membre appRoles, qui est une collection d’entités [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), utilisée pour définir les [autorisations d’application](developer-glossary.md#permissions) pour une API web. 
- Le membre oauth2Permissions, qui est une collection d’entités [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), utilisée pour définir les [autorisations déléguées](developer-glossary.md#permissions) pour une API web.

Pour plus d’informations sur les concepts de manifeste d’application en général, consultez [Manifeste d’application Azure Active Directory](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accès à Azure AD Graph et à Office 365 par le biais des API Microsoft Graph  

Comme mentionné précédemment, vous pouvez non seulement exposer des API et y accéder pour vos propres applications, mais également inscrire votre application cliente pour accéder aux API exposées par les ressources Microsoft. L’API Graph de Microsoft, nommée « Microsoft Graph » dans la liste des API/ressources du portail, est disponible pour toutes les applications inscrites avec Azure AD. Si vous inscrivez votre application cliente dans un locataire contenant des comptes ayant souscrits un abonnement Office 365, vous pouvez également accéder aux étendues exposées par les différentes ressources de Office 365.

Pour découvrir une description complète des étendues exposées par l’API Microsoft Graph, consultez l’article [Référence pour les autorisations pour Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference).

> [!NOTE]
> En raison d'une limitation actuelle, les applications clientes natives ne peuvent appeler l'API Graph Azure AD que si elles utilisent l'autorisation « Accéder au répertoire de votre organisation ». Cette restriction ne s'applique pas aux applications web.

## <a name="configuring-multi-tenant-applications"></a>Configuration d'applications multilocataires

Lorsque vous inscrivez une application dans Azure AD, vous pouvez choisir de rendre votre application accessible uniquement aux utilisateurs de votre organisation. Vous pouvez également rendre votre application accessible aux utilisateurs d'organisations externes. Ces deux types d’applications sont appelés applications à locataire unique et applications multilocataires. Cette section décrit la modification de la configuration d'une application à locataire unique pour en faire une application multilocataire.

Il est important de noter les différences entre une application à locataire unique et une application multilocataire :  

- Une application à locataire unique est prévue pour une utilisation dans une seule organisation. Il s’agit généralement d’une application métier écrite par un développeur de l’entreprise. Une application à locataire unique est uniquement accessible par les utilisateurs disposant de comptes dans le même locataire que l’inscription de l’application. Par conséquent, elle doit être approvisionnée dans un seul répertoire.
- Une application multilocataire est prévue pour une utilisation dans plusieurs organisations. Il s’agit d’une application SaaS (software-as-a-service) web généralement écrite par un éditeur de logiciels indépendant. Les applications multilocataires doivent être approvisionnées dans chaque locataire où les utilisateurs doivent avoir accès. Pour les locataires autres que celui où l’application est inscrite, un consentement de l’utilisateur ou de l’administrateur est nécessaire pour les inscrire. Notez que les applications clientes natives sont multilocataires par défaut lorsqu’elles sont installées sur l’appareil du propriétaire de la ressource. Consultez la section [Vue d’ensemble de l’infrastructure de consentement](#overview-of-the-consent-framework) pour plus d’informations sur l’infrastructure de consentement.

Rendre une application multilocataire nécessite des modifications de l’inscription de l’application ainsi que des modifications au niveau de l’application web elle-même. Les sections suivantes couvrent ces deux points.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Modification de l’inscription de l’application pour devenir multilocataire

Si vous écrivez une application que vous souhaitez proposer à vos clients ou à des partenaires externes à votre organisation, vous devez mettre à jour la définition de l’application dans le portail Azure.

> [!IMPORTANT]
> Azure AD nécessite que l’URI ID d’application des applications multilocataires soit globalement unique. L’URI ID d’application est l’une des méthodes d’identification d'une application dans les messages de protocole. Pour une application à client unique, il suffit que l’URI ID d’application soit unique au sein de ce client. Pour une application mutualisée, l’URI doit être globalement unique afin qu’Azure AD puisse trouver l’application sur tous les clients.
> L’unicité globale est appliquée en obligeant l’URI ID d’application à avoir un nom d’hôte correspondant à un domaine vérifié du client Azure AD. Par exemple, si le nom de votre locataire est contoso.onmicrosoft.com, un URI ID d’application valide serait https://contoso.onmicrosoft.com/myapp. Si votre locataire a un domaine vérifié de contoso.com, un URI ID d’application valide serait également https://contoso.com/myapp. Si l’URI ID d’application ne suit pas ce modèle, une application ne peut pas être définie comme multi-locataire.

Pour permettre aux utilisateurs externes d’accéder à votre application :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. Sur la page **Paramètres**, cliquez sur **Propriétés** et basculez le **Commutateur mutualisé** sur **Oui**.

Après avoir fait les modifications, les utilisateurs et les administrateurs dans d’autres organisations sont en mesure d’accorder à leurs utilisateurs la possibilité de se connecter à votre application, permettant à votre application d’accéder à des ressources sécurisées par leurs locataires.

### <a name="changing-the-application-to-support-multi-tenant"></a>Modification de l’application pour devenir multilocataire

La prise en charge des applications multilocataires s’appuie en grande partie sur l’infrastructure de consentement de Azure AD. Le consentement est le mécanisme permettant à un utilisateur d’un autre locataire, d’accorder l’accès de l’application aux ressources sécurisées par le locataire de l’utilisateur. Cette expérience est appelée « consentement de l’utilisateur ».

Votre application web peut également offrir :

- La possibilité pour les administrateurs « d’inscrire ma société ». Cette expérience, appelée « consentement d’administrateur », donne à un administrateur la possibilité de donner son consentement au nom de *tous les utilisateurs* dans leur organisation. Seul l’utilisateur qui s’authentifie avec un compte appartenant au rôle Administrateur général peut fournir son consentement d’administrateur ; les autres utilisateurs reçoivent un message d’erreur.
- Une expérience d’inscription pour les utilisateurs. Il est prévu que l’utilisateur dispose d’un bouton « inscription » qui redirige le navigateur vers le point de terminaison `/authorize` OAuth2.0 de Azure AD ou un point de terminaison `/userinfo` OpenID Connect. Ces points de terminaison permettent à l'application d’obtenir des informations sur le nouvel utilisateur en inspectant l'id_token. Après la phase d’inscription, l’utilisateur reçoit une invite de consentement similaire à celle illustrée dans la section [Vue d’ensemble de l’infrastructure de consentement](#overview-of-the-consent-framework).

Pour plus d’informations sur les modifications d’application requises pour prendre en charge l’accès multilocataire et les expériences de connexion/inscription, consultez :

- [Comment connecter un utilisateur Azure Active Directory (AD) à l’aide du modèle d’application mutualisée](howto-convert-app-to-be-multi-tenant.md)
- La liste des [exemples de code de l’architecture multilocataire](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Démarrage rapide : Ajouter la marque de votre société à votre page de connexion dans Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Activation de l’octroi implicite OAuth 2.0 pour les applications monopages

Les applications monopages (SPA) se composent généralement d’une partie frontale reposant largement sur JavaScript qui s’exécute dans le navigateur. Celle-ci appelle l’API web principale de l’application pour effectuer sa logique métier. Pour les applications à page unique hébergées dans Azure AD, l’accord implicite OAuth 2.0 permet d’authentifier l’utilisateur auprès de Azure AD et d’obtenir un jeton qui peut servir à sécuriser les appels du client JavaScript de l’application à son API web principale.

Une fois que l’utilisateur a donné son consentement, ce même protocole d’authentification peut servir à obtenir des jetons pour sécuriser les appels entre le client et d’autres ressources API web configurées pour l’application. Pour plus d’informations sur l’octroi d’autorisation implicite et pour vous aider à décider si cette méthode est adaptée à votre scénario d’application, consultez [Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)](v1-oauth2-implicit-grant-flow.md).

Par défaut, l’accord implicite OAuth 2.0 est désactivé pour les applications. Vous pouvez activer l’accord implicite OAuth 2.0 pour votre application en définissant la valeur `oauth2AllowImplicitFlow` dans son [manifeste d’application](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Pour activer l’accord implicite OAuth 2.0

> [!NOTE]
> Pour plus d’informations sur la façon de modifier le manifeste d’application, assurez-vous de revoir d’abord la section précédente, [Configuration d’une application de ressource pour exposer des API web](#configuring-a-resource-application-to-expose-web-apis).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3. Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis trouvez/cliquez sur l’application que vous souhaitez configurer. Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
4. Basculez vers la page **Modifier le manifeste**, en cliquant sur **Manifeste** depuis la page d’inscription de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Recherchez la valeur « oauth2AllowImplicitFlow » et définissez-la sur « true ». Par défaut, elle est définie sur « false ».
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Enregistrez le manifeste mis à jour. Une fois l’enregistrement effectué, votre API web est configurée pour utiliser l’accord implicite OAuth 2.0 pour authentifier les utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications pour les applications utilisant le point de terminaison Azure AD v1.0 :
- [Intégration d’applications dans Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Remove an application from Azure AD](quickstart-v1-remove-azure-ad-app.md) (Supprimer une application d’Azure Active Directory)

Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).

Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
