---
title: Didacticiel pour configurer Azure Active Directory B2C avec Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Didacticiel pour configurer Azure Active Directory B2C avec Microsoft Dynamics 365 Fraud Protection pour identifier le compte frauduleux et présentant un risque
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 5/12/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 219cb793da7835922ad707d0ad1ee7e122990ba8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962195"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Microsoft Dynamics 365 Fraud Protection avec Azure Active Directory B2C

Dans cet exemple de tutoriel, découvrez comment intégrer [Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) à Azure Active Directory (AD) B2C.

Microsoft DFP fournit aux organisations la capacité d’évaluer le risque de tentatives de création de comptes et de connexions frauduleuses. L’évaluation de Microsoft DFP peut être utilisée par le client pour bloquer ou contester les tentatives suspectes visant à créer de nouveaux comptes factices ou à compromettre des comptes existants.

Cet exemple montre comment incorporer les points de terminaison de l’API de collecte d’empreintes digitales des appareils et d’évaluation des créations de compte et des connexions de Microsoft DFP dans une stratégie personnalisée Azure AD B2C.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md). Le locataire est lié à votre abonnement Azure.

- Souscrivez un [abonnement](https://dynamics.microsoft.com/pricing/#Sales) Microsoft DFP. Vous pouvez également configurer une [version d’évaluation client](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin).

## <a name="scenario-description"></a>Description du scénario

L’intégration de Microsoft DFP inclut les composants suivants :

- **Locataire Azure AD B2C** : Authentifie l’utilisateur et agit comme un client de Microsoft DFP. Héberge un script de prise d’empreintes qui collecte les données d’identification et de diagnostic de chaque utilisateur qui exécute une stratégie cible. Bloque ou conteste ultérieurement les tentatives de connexion ou d’inscription en fonction du résultat de l’évaluation de la règle renvoyé par Microsoft DFP.

- **Modèles d’interface utilisateur personnalisés** : Utilisés pour personnaliser le contenu HTML des pages affichées par Azure AD B2C. Ces pages comprennent l’extrait de code JavaScript nécessaire à la collecte d’empreintes digitales par Microsoft DFP.

- **Service de collecte d’empreintes Microsoft DFP** : Script incorporé dynamiquement qui enregistre les détails relatifs à la télémétrie des appareils et aux utilisateurs autodéclarés afin de créer une empreinte identifiable de façon unique que l’utilisateur peut utiliser ultérieurement dans le processus de prise de décisions.

- **Points de terminaison de l’API Microsoft DFP** : Fournit le résultat de la décision et accepte un statut final reflétant l’opération entreprise par l’application du client. Azure AD B2C communique directement avec les points de terminaison de Microsoft DFP à l’aide de connecteurs d’API REST. L’authentification de l’API s’effectue via un octroi de client_credentials au locataire Azure AD dans lequel Microsoft DFP est concédé sous licence et installé pour obtenir un jeton du porteur.

Le diagramme d’architecture suivant illustre l’implémentation.

![Image montrant le diagramme de l’architecture de protection contre la fraude de Microsoft dynamics 365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Étape | Description |
|:-----| :-----------|
| 1. | L’utilisateur accède à une page de connexion. Les utilisateurs sélectionnent l’option d’inscription pour créer un compte et entrer des informations sur la page. Azure AD B2C collecte les attributs de l’utilisateur.
| 2. | Azure AD B2C appelle l’API Microsoft DFP et lui transmet les attributs de l’utilisateur.
| 3. | L’API Microsoft DFP consomme ces informations et les traite, puis renvoie le résultat à Azure AD B2C.
| 4. | Azure AD B2C reçoit les informations renvoyées par l’API Microsoft DFP. Si elles montrent une réponse d’échec, un message d’erreur est affiché à l’utilisateur. S’il affiche une réponse Réussite, l’utilisateur est authentifié et écrit dans le répertoire.

## <a name="set-up-the-solution"></a>Configurer la solution

1. [Créez une application Facebook](./identity-provider-facebook.md#create-a-facebook-application) configurée pour autoriser l’association à Azure AD B2C.
2. [Ajoutez le secret Facebook](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) que vous avez créé en tant que clé de stratégie Identity Experience Framework.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configurer votre application sous Microsoft DFP

[Configurez votre locataire Azure AD](/dynamics365/fraud-protection/integrate-real-time-api) pour utiliser Microsoft DFP.

## <a name="set-up-your-custom-domain"></a>Configurer votre domaine personnalisé

Dans un environnement de production, vous devez utiliser un [domaine personnalisé pour Azure AD B2C](./custom-domain.md?pivots=b2c-custom-policy) et pour le [service de collecte d’empreintes digitales de Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting#set-up-dns). Le domaine des deux services doit se trouver dans la même zone DNS racine pour empêcher les paramètres de confidentialité des navigateurs de bloquer les cookies interdomaines, ce qui n’est pas nécessaire dans un environnement hors production.

Vous trouverez ci-dessous un exemple :

| Environnement | Service | Domain |
|:------------|:---------------|:---------------|
| Développement | Azure AD B2C | contoso-dev.b2clogin.com |
| Développement | Collecte d’empreintes digitales Microsoft DFP | fpt.dfp.microsoft-int.com |
| UAT | Azure AD B2C | contoso-uat.b2clogin.com |
| UAT | Collecte d’empreintes digitales Microsoft DFP | fpt.dfp.microsoft.com |
| Production | Azure AD B2C | login.contoso.com |
| Production | Collecte d’empreintes digitales Microsoft DFP | fpt.login.contoso.com |

## <a name="deploy-the-ui-templates"></a>Déployer les modèles d’interface utilisateur

1. Déployez les [modèles d’interface utilisateur Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/ui-templates) fournis sur un service d’hébergement Internet public tel que Stockage Blob Azure.

2. Remplacez la valeur `https://<YOUR-UI-BASE-URL>/` par l’URL racine de votre emplacement de déploiement.

  >[!NOTE]
  >Vous aurez besoin ultérieurement de l’URL de base pour configurer les stratégies d’Azure AD B2C.

3. Dans le fichier `ui-templates/js/dfp.js`, remplacez `<YOUR-DFP-INSTANCE-ID>` par l’identificateur de votre instance Microsoft DFP.

4. Assurez-vous que CORS est activé pour votre nom de domaine Azure AD B2C `https://{your_tenant_name}.b2clogin.com` ou `your custom domain`.

Pour en savoir plus, consultez [la documentation relative à la personnalisation de l’interface utilisateur](./customize-ui-with-html.md?pivots=b2c-custom-policy).

## <a name="azure-ad-b2c-configuration"></a>Configuration d’Azure AD B2C

### <a name="add-policy-keys-for-your-microsoft-dfp-client-app-id-and-secret"></a>Ajouter des clés de stratégie pour l’ID et le secret de votre application cliente Microsoft DFP

1. Dans le locataire Azure AD où Microsoft DFP est configuré, créez une [application Azure AD et accordez le consentement administrateur](/dynamics365/fraud-protection/integrate-real-time-api#create-azure-active-directory-applications).
2. Créez une valeur secrète pour cette inscription d’application et notez les valeurs d’ID client et de clé secrète client de l’application.
3. Enregistrez les valeurs d’ID client et de clé secrète client en tant que [clés de stratégie dans votre locataire Azure AD B2C](./policy-keys-overview.md).

 >[!NOTE]
 >Vous aurez besoin ultérieurement des clés de stratégie pour configurer vos stratégies Azure AD B2C.

### <a name="replace-the-configuration-values"></a>Remplacer des valeurs de configuration

Dans les [stratégies personnalisées](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies) fournies, recherchez les espaces réservés suivants et remplacez-les par les valeurs correspondantes de votre instance.

| Espace réservé | Remplacer par | Notes |
| :-------- | :------------| :-----------|
|{Settings:Production} | Si les stratégies doivent être déployées en mode production | `true` ou `false`  |
|{Settings:Tenant} | Nom abrégé de votre locataire |  `your-tenant` ; provient de your-tenant.onmicrosoft.com  |
| {Settings:DeploymentMode}    |  Mode de déploiement d’Application Insights à utiliser   |  `Production` ou `Development`  |
|  {Settings:DeveloperMode}    | Si les stratégies doivent être déployées en mode développeur dans Application Insights      |   `true` ou `false`    |
|  {Settings:AppInsightsInstrumentationKey}  |  Clé d’instrumentation de votre instance Application Insights*   |  `01234567-89ab-cdef-0123-456789abcdef` |
|  {Settings:IdentityExperienceFrameworkAppId}  | ID de l’application IdentityExperienceFramework configuré dans votre locataire Azure AD B2C  | `01234567-89ab-cdef-0123-456789abcdef`|
|  {Settings:ProxyIdentityExperienceFrameworkAppId}  |   ID de l’application ProxyIdentityExperienceFramework configuré dans votre locataire Azure AD B2C |   `01234567-89ab-cdef-0123-456789abcdef`|
| {Settings:FacebookClientId}  | ID d’application de l’application Facebook que vous avez configurée pour la fédération avec B2C    | `000000000000000`   |
|   {Settings:FacebookClientSecretKeyContainer}  | Nom de la clé de stratégie dans laquelle vous avez enregistré le secret de l’application de Facebook    |  `B2C_1A_FacebookAppSecret` |
|   {Settings:ContentDefinitionBaseUri} |  Point de terminaison dans lequel vous avez déployé les fichiers de l’interface utilisateur   | `https://<my-storage-account>.blob.core.windows.net/<my-storage-container>`   |
|  {Settings:DfpApiBaseUrl}   |  Chemin d’accès de base de votre instance d’API DFP ; trouvé dans le portail DFP   | `https://tenantname-01234567-89ab-cdef-0123-456789abcdef.api.dfp.dynamics.com/v1.0/`   |
| {Settings:DfpApiAuthScope} | Étendue client_credentials pour le service d’API DFP | `https://api.dfp.dynamics-int.com/.default or https://api.dfp.dynamics.com/.default` |
| {Settings:DfpTenantId} | ID du locataire Azure AD (non B2C) dans lequel DFP est concédé sous licence et installé | `01234567-89ab-cdef-0123-456789abcdef` ou `consoto.onmicrosoft.com` |
| {Settings:DfpAppClientIdKeyContainer} | Nom de la clé de stratégie dans laquelle vous enregistrez l’ID client de DFP | `B2C_1A_DFPClientId` |
| {Settings:DfpAppClientSecretKeyContainer} | Nom de la clé de stratégie dans laquelle vous enregistrez la clé secrète client de DFP | `B2C_1A_DFPClientSecret` |

*Application Insights peut être configuré dans n’importe quel locataire ou abonnement Azure AD. Cette valeur est facultative, mais [recommandée pour faciliter le débogage](./troubleshoot-with-application-insights.md).

>[!NOTE]
>Ajoutez une notification de consentement à la page de collecte d’attributs. Notez que la télémétrie des utilisateurs et les informations d’identité des utilisateurs seront enregistrées pour la protection des comptes.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurer la stratégie Azure AD B2C

1. Accédez à la [stratégie d’Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies) dans le dossier stratégies.

2. Suivez ce [document](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) pour télécharger le [Pack de démarrage LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurez la stratégie pour le locataire Azure AD B2C.

>[!NOTE]
>Mettez à jour les stratégies fournies en fonction de votre locataire spécifique.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Ouvrez le locataire Azure AD B2C locataire, puis, sous Stratégies, sélectionnez **Identity Experience Framework**.

2. Sélectionnez votre **SignUpSignIn** créé précédemment.

3. Sélectionnez **Exécuter le flux d’utilisateurs**, puis sélectionnez les paramètres :

   a. **Application** : sélectionnez l’application inscrite (JWT dans l’exemple).

   b. **URL de réponse** : sélectionnez l’**URL de redirection**.

   c. Sélectionnez **Exécuter le flux utilisateur**.

4. Suivez le processus d’inscription et créez un compte

5. Le service Microsoft DFP est appelé pendant le flux, après la création de l’attribut utilisateur. Si le flux est incomplet, vérifiez que l’utilisateur n’est pas enregistré dans l’annuaire.

>[!NOTE]
>Mettez à jour les règles directement depuis le portail Microsoft DFP si vous utilisez le [moteur de règles Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Exemples Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)