---
title: Ajouter des connecteurs d’API aux flux d’inscription en libre-service - Azure AD
description: Configurer une API Web à utiliser dans un workflow utilisateur.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 703e3b4c951bc4c3a22f82b9faa31789d1abf868
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008720"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Ajouter un connecteur d’API à un workflow d’utilisateur

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

> [!IMPORTANT]
>**À compter du 4 janvier 2021**, Google [déconseille la prise en charge de la connexion WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Si vous utilisez la fédération Google ou l’inscription en libre-service avec Gmail, [testez la compatibilité de vos applications métier natives](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **All API connectors** (Tous les connecteurs d’API), puis **New API connector** (Nouveau connecteur d’API).

   ![Ajoutez un nouveau connecteur d'API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Indiquez un nom d’affichage pour l’appel. Par exemple, **Vérifier l’état d’approbation**.
6. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
7. Choisissez le **type d'authentification** et configurez les informations d'authentification pour appeler votre API. Consultez la section ci-dessous pour connaître les options de sécurisation de votre API.

    ![Configurer un connecteur d'API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Sélectionnez **Enregistrer**.

## <a name="securing-the-api-endpoint"></a>Sécuriser le point de terminaison d'API
Vous pouvez protéger votre point de terminaison d'API à l'aide de l'authentification de base HTTP ou de l'authentification par certificat client HTTPS (préversion). Dans les deux cas, vous fournissez les informations d'identification qu'Azure Active Directory utilisera lors de l'appel de votre point de terminaison d'API. Votre point de terminaison d'API vérifie ensuite les informations d'identification et prend les décisions relatives aux autorisations.

### <a name="http-basic-authentication"></a>Authentification HTTP de base
L’authentification de base HTTP est définie dans le document [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure Active Directory envoie une requête HTTP avec les informations d'identification du client (`username` et `password`) dans l'en-tête `Authorization`. Les informations d'identification sont mises en forme en tant que chaîne `username:password` codée en base64. Votre API vérifie ensuite ces valeurs pour déterminer si elle doit rejeter ou non un appel d'API.

### <a name="https-client-certificate-authentication-preview"></a>Authentification par certificat client HTTPS (préversion)

> [!IMPORTANT]
> Cette fonctionnalité actuellement disponible en préversion est fournie sans contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'authentification par certificat client est une authentification mutuelle basée sur un certificat dans laquelle le client fournit un certificat client au serveur pour prouver son identité. Dans ce cas, Azure Active Directory utilisera le certificat que vous chargez dans le cadre de la configuration du connecteur d'API. Cela se produit dans le cadre de la négociation SSL. Seuls les services disposant des certificats appropriés peuvent accéder à votre service API. Le certificat client est un certificat numérique X.509. Dans les environnements de production, il doit être signé par une autorité de certification. 

Pour créer un certificat, vous pouvez utiliser [Azure Key Vault](../../key-vault/certificates/create-certificate.md), qui propose des options pour les certificats auto-signés et des intégrations auprès de fournisseurs d'émetteurs de certificats pour les certificats signés. Vous pouvez ensuite [exporter le certificat](../../key-vault/certificates/how-to-export-certificate.md) et le charger pour l'utiliser dans la configuration des connecteurs d'API. Notez que le mot de passe n'est requis que pour les fichiers de certificat protégés par un mot de passe. Vous pouvez également utiliser la cmdlet [New-SelfSignedCertificate](../../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) de PowerShell pour générer un certificat auto-signé.

Pour Azure App Service et Azure Functions, consultez [Configurer l'authentification mutuelle TLS](../../app-service/app-service-web-configure-tls-mutual-auth.md) pour savoir comment activer et valider le certificat à partir de votre point de terminaison d'API.

Nous vous recommandons de définir des alertes de rappel avant la date d'expiration de votre certificat. Pour charger un nouveau certificat sur un connecteur d'API existant, sélectionnez le connecteur d'API sous **Tous les connecteurs d'API**, puis cliquez sur **Charger un nouveau certificat**. Le dernier certificat chargé qui n'a pas expiré et dont la date de début est dépassée sera automatiquement utilisé par Azure Active Directory.

### <a name="api-key"></a>Clé d’API
Certains services utilisent un mécanisme de « clé API » pour obfusquer l'accès à vos points de terminaison HTTP pendant le développement. Pour [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), vous pouvez effectuer cette opération en incluant le `code` comme paramètre de requête dans l'**URL du point de terminaison**. Par exemple, `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>). 

Ce mécanisme ne doit pas être utilisé seul en production. Par conséquent, la configuration de l'authentification de base ou par certificat est toujours requise. Si vous ne souhaitez implémenter aucune méthode d'authentification (non recommandé) à des fins de développement, vous pouvez choisir l'authentification de base et utiliser des valeurs temporaires pour `username` et `password`, que votre API peut ignorer pendant l'implémentation de l'autorisation dans votre API.

## <a name="the-request-sent-to-your-api"></a>Demande envoyée à votre API
Un connecteur d’API est matérialisé en tant que requête **HTTP POST**, en envoyant les attributs utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. Les attributs sont sérialisés de la même façon que les propriétés utilisateur [Microsoft Graph](/graph/api/resources/user#properties). 

**Exemple de requête**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Seules les propriétés des utilisateurs et les attributs personnalisés répertoriés dans l’expérience **Azure Active Directory** > **Identités externes** > **Attributs utilisateur personnalisés** peuvent être envoyés dans la demande.

Les attributs personnalisés existent au format **extension_\<extensions-app-id>_NomAttribut** dans le répertoire. Votre API doit s’attendre à recevoir des revendications dans ce même format sérialisé. Pour plus d’informations sur les attributs personnalisés, consultez [définir des attributs personnalisés pour les flux d’inscription en libre-service](user-flow-add-custom-attributes.md).

De plus, la revendication **Paramètres régionaux de l’interface utilisateur (« ui_locales »)** est envoyée par défaut dans toutes les demandes. Elle fournit les paramètres régionaux d’un utilisateur tels qu’ils sont configurés sur son appareil et ils peuvent être utilisés par l’API pour renvoyer des réponses internationalisées.

> [!IMPORTANT]
> Si une revendication n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API. Votre API doit être conçue pour vérifier et gérer explicitement le cas où une revendication ne figure pas dans la requête.

> [!TIP] 
> Les revendications [**identités (« identities »)**](/graph/api/resources/objectidentity) et **Adresse e-mail (« email »)** peuvent être utilisées par votre API pour identifier un utilisateur avant qu’il n’ait un compte dans votre locataire. La revendication « identities » (identités) est envoyée quand un utilisateur s’authentifie avec un fournisseur d’identité tel que Google ou Facebook. « email » est toujours envoyé.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription en libre-service.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Flux d’utilisateurs**, puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
5. Sélectionnez **Connecteurs d’API**, puis sélectionnez les points de terminaison d’API que vous souhaitez appeler aux étapes suivantes dans le workflow d’utilisateur :

   - **Après la connexion avec un fournisseur d’identité**
   - **Avant de créer l’utilisateur**

   ![Ajouter des API au flux de l’utilisateur](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Sélectionnez **Enregistrer**.

## <a name="after-signing-in-with-an-identity-provider"></a>après la connexion avec un fournisseur d’identité

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Les revendications exactes envoyées à l’API dépendent des informations fournies par le fournisseur d’identité. « email » est toujours envoyé.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : la page de collecte d’attribut.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Renseigne le champ d’entrée dans la page de collections d’attributs.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage

Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé dans Azure AD. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Les revendications exactes envoyées à l’API dépendent des informations collectées par l’utilisateur ou fournies par le fournisseur d’identité.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage
- Réponse de validation

#### <a name="continuation-response"></a>Réponse de continuation
Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : créer l’utilisateur dans le répertoire.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Remplace toute valeur qui a déjà été attribuée à la revendication à partir de la page de collection d’attributs.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage
Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Validation-réponse d’erreur
 Lorsque l’API répond avec une réponse d’erreur de validation, le workflow de l’utilisateur reste sur la page de collection d’attributs et un `userMessage` est présenté à l’utilisateur. L’utilisateur peut ensuite modifier et renvoyer le formulaire. Ce type de réponse peut être utilisé pour la validation d’entrée.

Consultez un exemple de [réponse d’erreur de validation](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Exemples de réponses

### <a name="example-of-a-continuation-response"></a>Exemple de réponse de continuation

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Paramètre                                          | Type              | Obligatoire | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | Oui      | Version de l’API.                                                                                                                                                                                                                                                                |
| action                                             | String            | Oui      | La valeur doit être `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Non       | Les valeurs peuvent être stockées dans le répertoire si elles sont sélectionnées en tant que **Revendication à recevoir** dans la configuration du connecteur d’API et **Attributs utilisateur** pour un workflow utilisateur. Les valeurs peuvent être renvoyées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication retournée n’a pas besoin de contenir `_<extensions-app-id>_`. Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur. Elles peuvent également être renvoyées dans le jeton si elles sont configurées dans le cadre de l'application.  |

### <a name="example-of-a-blocking-response"></a>Exemple de réponse de blocage

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Paramètre   | Type   | Obligatoire | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Oui      | Version de l’API.                                                    |
| action      | String | Oui      | La valeur doit être `ShowBlockPage`                                              |
| userMessage | String | Oui      | Message à afficher à l’utilisateur.                                            |

**Expérience de l’utilisateur final avec une réponse de blocage**

![Exemple de page de blocage](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exemple de réponse d’erreur de validation

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| Paramètre   | Type    | Obligatoire | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Oui      | Version de votre API.                                                    |
| action      | String  | Oui      | La valeur doit être `ValidationError`.                                           |
| status      | Integer | Oui      | La valeur doit être `400` pour une réponse ValidationError.                        |
| userMessage | String  | Oui      | Message à afficher à l’utilisateur.                                            |

> [!NOTE]
> Le code d’état HTTP doit être « 400 » en plus de la valeur « Status » dans le corps de la réponse.

**Expérience de l’utilisateur final avec une réponse d’erreur de validation**

![Exemple de page de validation](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Meilleures pratiques et résolution des problèmes

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless
Les fonctions serverless, comme les déclencheurs HTTP dans Azure Functions, fournissent une méthode simple pour créer des points de terminaison d’API à utiliser avec le connecteur d’API. Vous pouvez utiliser la fonction cloud serverless pour, [par exemple](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), effectuer une logique de validation et limiter les inscriptions à des domaines de courrier spécifiques. La fonction cloud serverless peut également appeler et invoquer d’autres API web, magasins d’utilisateurs et autres services cloud dans le cade de scénarios plus complexes.

### <a name="best-practices"></a>Meilleures pratiques
Assurez-vous que :
* Votre API suit les contrats de demande et de réponse d’API comme indiqué ci-dessus. 
* L’**URL du point de terminaison** du connecteur d’API pointe vers le point de terminaison d’API approprié.
* Votre API recherche explicitement les valeurs null des revendications reçues.
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide.
    * Si vous utilisez une fonction serverless ou un service web scalable, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique ». en production. Pour Azure Functions, il est recommandé d’utiliser le [plan Premium](../../azure-functions/functions-scale.md)

### <a name="use-logging"></a>Utiliser la journalisation
En général, il est judicieux d’utiliser les outils de journalisation activés par votre service API Web, comme [Application Insights](../../azure-functions/functions-monitoring.md), pour surveiller votre API en cas de codes d’erreur inattendus, d’exceptions et de performances médiocres.
* Analysez les codes d’état HTTP autres que HTTP 200 ou 400.
* Un code d’état HTTP 401 ou 403 indique généralement un problème avec votre authentification. Vérifiez la couche d’authentification de votre API et la configuration correspondante dans le connecteur d’API.
* Si nécessaire, utilisez des niveaux de journalisation plus agressifs (par exemple, « trace » ou « debug ») lors du développement.
* Surveillez votre API en cas de temps de réponse longs.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [ajouter un système d’approbation personnalisé à l’inscription en libre-service](self-service-sign-up-add-approvals.md)
- Lancez-vous avec nos [exemples de démarrage rapide](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).