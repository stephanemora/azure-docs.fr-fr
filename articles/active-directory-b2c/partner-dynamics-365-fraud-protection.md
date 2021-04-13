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
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9483b0a8829c042a7cf8d516c6007cbbf14a97ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639742"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Microsoft Dynamics 365 Fraud Protection avec Azure Active Directory B2C

Dans cet exemple de didacticiel, nous fournissons des conseils sur l’intégration de [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) avec Azure Active Directory (AD) B2C.

Microsoft DFP fournit aux clients la possibilité d’évaluer si le risque de tentatives de création de nouveaux comptes et les tentatives de connexion à l’écosystème du client sont frauduleux. L’évaluation de Microsoft DFP peut être utilisée par le client pour bloquer les tentatives suspectes de création de nouveaux comptes factices ou les piéger, ou bien pour compromettre les comptes existants. La protection des comptes comprend l’identification de l’empreinte des appareils par intelligence artificielle, des API pour l’évaluation des risques en temps réel, une règle et une liste pour optimiser la stratégie de risque en fonction des besoins de l’entreprise du client et une carte de performance pour surveiller l’efficacité et les tendances de la protection contre les fraudes dans l’écosystème du client.

Dans cet exemple, nous allons intégrer les fonctionnalités de protection de compte de Microsoft DFP avec un flux d’utilisateurs Azure AD B2C. Le service effectue un empreinte digitale externe à chaque tentative de connexion ou d’inscription et surveille tout comportement suspect passé ou présent. Azure AD B2C appelle un point de terminaison de décision de Microsoft DFP, qui retourne un résultat sur la base du comportement passé et présent de l’utilisateur identifié, ainsi que les règles personnalisées spécifiées dans le service DFP de Microsoft. Azure AD B2C prend une décision d’approbation en fonction de ce résultat et renvoie la même décision à Microsoft DFP.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md). Le locataire est lié à votre abonnement Azure.

- Souscrivez un [abonnement](https://dynamics.microsoft.com/pricing/#Sales) Microsoft DFP. Vous pouvez également configurer une [version d’évaluation client](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin).

## <a name="scenario-description"></a>Description du scénario

L’intégration de Microsoft DFP inclut les composants suivants :

- **Locataire Azure AD B2C** : Authentifie l’utilisateur et agit comme un client de Microsoft DFP. Héberge un script de prise d’empreintes qui collecte les données d’identification et de diagnostic de chaque utilisateur qui exécute une stratégie cible. Par la suite, les tentatives de connexion ou d’inscription sont bloquées si Microsoft DFP les détecte comme suspectes.

- **Service d’application personnalisé** : application web assurant deux fonctions.

  - Sert les pages HTML à utiliser comme interface utilisateur de l’infrastructure d’Identity Experience Framework. Responsable de l’incorporation de prises d’empreintes Microsoft Dynamics 365.

  - Un contrôleur d’API avec des points de terminaison RESTful qui connecte Microsoft DFP à Azure AD B2C. Structure et traitement des données du descripteur, et conforme aux exigences de sécurité pour les deux.

- **Service de collecte d’empreintes Microsoft DFP** : Script incorporé dynamiquement qui enregistre les détails relatifs à la télémétrie des appareils et aux utilisateurs autodéclarés afin de créer une empreinte identifiable de façon unique que l’utilisateur peut utiliser ultérieurement dans le processus de prise de décisions.

- **Points de terminaison de l’API Microsoft DFP** : Fournit le résultat de la décision et accepte un statut final reflétant l’opération entreprise par l’application du client. Azure AD B2C ne communique pas avec les points de terminaison directement en raison de la diversité des exigences de sécurité et de charge utile d’API. Elle utilise plutôt le service d’application comme intermédiaire.

Le diagramme d’architecture suivant illustre l’implémentation.

![Image montrant le diagramme de l’architecture de protection contre la fraude de Microsoft dynamics 365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Étape | Description |
|:-----| :-----------|
| 1. | L’utilisateur accède à une page de connexion. Les utilisateurs sélectionnent l’option d’inscription pour créer un compte et entrer des informations sur la page. Azure AD B2C collecte les attributs de l’utilisateur.
| 2. | Azure AD B2C appelle l’API de couche intermédiaire et transmet les attributs de l’utilisateur.
| 3. | Une API de couche intermédiaire collecte des attributs d’utilisateur et les convertit dans un format consommable par l’API Microsoft DFP. Elle l’envoie ensuite à l’API Microsoft DFP.
| 4. | L’API Microsoft DFP consomme ces informations et les traite, puis renvoie le résultat à l’API de couche intermédiaire.
| 5. | L’API de couche intermédiaire traite les informations et renvoie les informations pertinentes à Azure AD B2C.
| 6. | Azure AD B2C reçoit les informations renvoyées par l’API de couche intermédiaire. Si elles montrent une réponse d’échec, un message d’erreur est affiché à l’utilisateur. S’il affiche une réponse Réussite, l’utilisateur est authentifié et écrit dans le répertoire.

## <a name="set-up-the-solution"></a>Configurer la solution

1. [Créez une application Facebook](./identity-provider-facebook.md#create-a-facebook-application) configurée pour autoriser l’association à Azure AD B2C.
2. [Ajoutez le secret Facebook](./custom-policy-get-started.md#create-the-facebook-key) que vous avez créé en tant que clé de stratégie Identity Experience Framework.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configurer votre application sous Microsoft DFP

[Configurez votre locataire Azure AD](/dynamics365/fraud-protection/integrate-real-time-api) pour utiliser Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Déployer dans l'application web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implémenter la collecte d’empreintes du service Microsoft DFP

L' [identification par collecte d’empreintes des appareils Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) est requise pour la protection des comptes Microsoft DFP.

>[!NOTE]
>En plus des pages de l’interface utilisateur Azure AD B2C, le client peut également implémenter le service d’empreintes digitales à l’intérieur du code d’application pour un profilage plus complet des appareils. Le service de collecte des empreintes digitales dans le code de l’application n’est pas inclus dans cet exemple.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Déployer le code de l’API Azure AD B2C

Déployez le [code d’API fourni](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) dans un service Azure. Le code peut être publié [à partir de Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Configurer CORS, ajouter l’**origine autorisée** `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Vous aurez par après besoin de l’URL du service déployé pour configurer Azure AD avec les paramètres requis.

Pour en savoir plus, consultez [la documentation de service de l’application](../app-service/app-service-web-tutorial-rest-api.md).

### <a name="add-context-dependent-configuration-settings"></a>Ajouter des paramètres de configuration en fonction du contexte

Configurez les paramètres de l’application dans le [service d’application dans Azure](../app-service/configure-common.md#configure-app-settings). Cela permet de configurer les paramètres en toute sécurité sans les archiver dans un référentiel. L’API REST a besoin des paramètres suivants :

| Paramètres de l’application | Source | Notes |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Configuration de Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Votre ID client de collecte d’empreintes d’appareils Microsoft |     |
| FraudProtectionSettings:ApiBaseUrl |  Votre URL de base à partir du portail Microsoft DFP   | Supprimer « -int » pour appeler l’API de production à la place|
|  TokenProviderConfig : Ressource  | Votre URL de base - https://api.dfp.dynamics-int.com     | Supprimer « -int » pour appeler l’API de production à la place|
|   TokenProviderConfig:ClientId       |ID de votre application cliente Azure AD de prestataire de protection contre la fraude      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Autorité de locataire Azure AD de prestataire de protection contre la fraude |
| TokenProviderConfig:CertificateThumbprint* | Empreinte numérique du certificat à utiliser pour l’authentification auprès de votre application cliente Azure AD de prestataire |
| TokenProviderConfig:ClientSecret* | Le secret de votre application cliente Azure AD Merchant | Recommandé pour utiliser un gestionnaire de secrets |

*Définissez uniquement l’un des deux paramètres marqués en fonction de votre mode d’authentification : avec un certificat ou un secret, tel qu’un mot de passe.

## <a name="azure-ad-b2c-configuration"></a>Configuration d’Azure AD B2C

### <a name="replace-the-configuration-values"></a>Remplacer des valeurs de configuration

Dans les [stratégies personnalisées](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) fournies, recherchez les espaces réservés suivants et remplacez-les par les valeurs correspondantes de votre instance.

| Espace réservé | Remplacer par | Notes |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Nom abrégé de votre locataire |  « yourtenant » de yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID de locataire de votre locataire Azure AD B2C |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID de l’application IdentityExperienceFramework configuré dans votre locataire Azure AD B2C    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID de l’application ProxyIdentityExperienceFramework configuré dans votre locataire Azure AD B2C      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  ID d’application de l’application de stockage de votre locataire   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | ID d’objet de l’application de stockage de votre locataire    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Clé d’instrumentation de votre instance App Insights*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Point de terminaison dans votre service d’application à partir duquel vos fichiers d’interface utilisateur sont servis    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_URL}  | URL de votre service d’application    |  https://yourapp.azurewebsites.net  |
|   {your-facebook-app-id}  |  ID d’application de l’application Facebook que vous avez configurée pour la Fédération avec Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Nom de la clé de stratégie que vous avez donné au secret de l’application Facebook   | B2C_1A_FacebookAppSecret   |

*Application Insights peut se trouver dans un autre locataire. Cette étape est facultative. Supprimez les éléments TechnicalProfiles et OrchestrationSteps correspondants s’ils ne sont pas nécessaires.

### <a name="call-microsoft-dfp-label-api"></a>Appeler l’API de l’étiquette Microsoft DFP

Les clients doivent [implémenter l’API d’étiquette](/dynamics365/fraud-protection/integrate-ap-api). Pour en savoir plus, consultez l'[API Microsoft DFP](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0).

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

La valeur de l’userID doit être identique à celle de la valeur de configuration d’Azure AD B2C correspondante (ObjectID).

>[!NOTE]
>Ajoutez une notification de consentement à la page de collecte d’attributs. Notez que la télémétrie des utilisateurs et les informations d’identité des utilisateurs seront enregistrées pour la protection des comptes.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurer la stratégie Azure AD B2C

1. Accédez à la [stratégie d’Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) dans le dossier stratégies.

2. Suivez ce [document](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) pour télécharger le [Pack de démarrage LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

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

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
