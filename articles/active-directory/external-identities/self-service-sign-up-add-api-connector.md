---
title: Ajouter des connecteurs d’API aux flux d’inscription en libre-service - Azure AD
description: Configurer une API Web à utiliser dans un workflow utilisateur.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532568"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Ajouter un connecteur d’API à un workflow d’utilisateur

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

> [!IMPORTANT]
>
> - **À partir du 12 juillet 2021**, si les clients B2B Azure AD configurent de nouvelles intégrations Google pour une utilisation avec l’inscription libre-service pour leurs applications métier ou personnalisées, l’authentification avec Google Identities ne fonctionne pas tant que les authentifications ne sont pas déplacées vers les vues web système. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **À partir du 30 septembre 2021**, Google [cesse la prise en charge de la connexion aux vues web intégrée](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vos applications authentifient les utilisateurs avec une vue web incorporée et que vous utilisez Google Federation avec [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) ou Azure AD B2B pour des [invitations utilisateur externes](google-federation.md) ou une [inscription en libre-service](identity-providers.md), les utilisateurs de Google Gmail ne peuvent pas s’authentifier. [Plus d’informations](google-federation.md#deprecation-of-web-view-sign-in-support)

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **All API connectors** (Tous les connecteurs d’API), puis **New API connector** (Nouveau connecteur d’API).

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="Fournir la configuration de base comme une URL cible et un nom d’affichage pour un connecteur d’API pendant l’expérience de création.":::

5. Indiquez un nom d’affichage pour l’appel. Par exemple, **Vérifier l’état d’approbation**.
6. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
7. Choisissez le **Type d'authentification** et configurez les informations d'authentification pour appeler votre API. Découvrez comment [Sécuriser votre connecteur d’API](self-service-sign-up-secure-api-connector.md).

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="Fournir la configuration d’authentification pour un connecteur d’API pendant l’expérience de création.":::

8. Sélectionnez **Enregistrer**.

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

En outre, les revendications sont généralement envoyées dans toutes les requêtes :
- **Paramètres régionaux de l’interface utilisateur ('ui_locales')** : paramètres régionaux de l’utilisateur final comme configurés sur leur appareil. Cela peut être utilisé par votre API pour retourner des réponses internationalisées.
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- **Adresse e-mail ('e-mail')** ou [**identités ('identités')** ](/graph/api/resources/objectidentity) : ces revendications peuvent être utilisées par votre API pour identifier l’utilisateur final qui s’authentifie auprès de l’application.

> [!IMPORTANT]
> Si une revendication n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API. Votre API doit être conçue pour vérifier et gérer explicitement le cas où une revendication ne figure pas dans la requête.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription en libre-service.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Flux d’utilisateurs**, puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
5. Sélectionnez **Connecteurs d’API**, puis sélectionnez les points de terminaison d’API que vous souhaitez appeler aux étapes suivantes dans le workflow d’utilisateur :

   - **Après la fédération avec un fournisseur d’identité lors de l’inscription**
   - **Avant de créer l’utilisateur**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="Sélection du connecteur d’API à utiliser pour une étape dans le flux de l’utilisateur, comme « Avant la création de l’utilisateur ».":::

6. Sélectionnez **Enregistrer**.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Après la fédération avec un fournisseur d’identité lors de l’inscription

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur.

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
| version                                            | String            | Oui      | Version de votre API.                                                                                                                                                                                                                                                                |
| action                                             | String            | Oui      | La valeur doit être `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Non       | Les valeurs peuvent être stockées dans le répertoire si elles sont sélectionnées en tant que **Revendication à recevoir** dans la configuration du connecteur d’API et **Attributs utilisateur** pour un workflow utilisateur. Les valeurs peuvent être renvoyées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication n’a pas besoin de contenir `_<extensions-app-id>_`, cela est *facultatif*. Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur.  |

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
| version     | String | Oui      | Version de votre API.                                                    |
| action      | String | Oui      | La valeur doit être `ShowBlockPage`                                              |
| userMessage | String | Oui      | Message à afficher à l’utilisateur.                                            |

**Expérience de l’utilisateur final avec une réponse de blocage**

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="Voici un exemple d’image de l’expérience de l’utilisateur final après qu’une API a retourné une réponse de blocage.":::

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
| status      | Entier / Chaîne | Oui      | La valeur doit être `400`, ou `"400"` pour une réponse ValidationError.  |
| userMessage | String  | Oui      | Message à afficher à l’utilisateur.                                            |

> [!NOTE]
> Le code d’état HTTP doit être « 400 » en plus de la valeur « Status » dans le corps de la réponse.

**Expérience de l’utilisateur final avec une réponse d’erreur de validation**

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="Voici un exemple d’image de l’expérience de l’utilisateur final après qu’une API a retourné une réponse de blocage.":::

## <a name="best-practices-and-how-to-troubleshoot"></a>Meilleures pratiques et résolution des problèmes

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless

Les fonctions serverless, comme les [déclencheurs HTTP dans Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md), fournissent une façon de créer des points de terminaison d’API à utiliser avec le connecteur d’API. Vous pouvez utiliser la fonction cloud serverless pour, [par exemple](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), effectuer une logique de validation et limiter les inscriptions à des domaines de courrier spécifiques. La fonction cloud serverless peut également appeler et invoquer d’autres API web, magasins de données et d’autres services cloud dans le cade de scénarios plus complexes.

### <a name="best-practices"></a>Meilleures pratiques
Assurez-vous que :
* Votre API suit les contrats de demande et de réponse d’API comme indiqué ci-dessus. 
* L’**URL du point de terminaison** du connecteur d’API pointe vers le point de terminaison d’API approprié.
* Votre API recherche explicitement les valeurs null des revendications reçues dont elle dépend.
* Votre API implémente une méthode d’authentification décrite dans [sécuriser votre connecteur d’API](self-service-sign-up-secure-api-connector.md).
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide.
    * Azure AD attendra un maximum de *20 secondes* pour recevoir une réponse. Si aucune réponse n’est reçue, le service effectuera *une tentative de plus* pour appeler votre API.
    * Si vous utilisez une fonction serverless ou un service web évolutif, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique » en production. Pour Azure Functions, il est recommandé d’utiliser au minimum le [plan Premium](../../azure-functions/functions-scale.md)
* Assurez la haute disponibilité de votre API.
* Analysez et optimisez les performances des API en aval, des bases de données ou d’autres dépendances de votre API.
* Vos points de terminaison doivent respecter les exigences du protocole TLS Azure AD et de sécurité de chiffrement. Pour plus d’informations, consultez [Configuration requise pour TLS et les suites de chiffrement](../../active-directory-b2c/https-cipher-tls-requirements.md). 
 
### <a name="use-logging"></a>Utiliser la journalisation

En général, il est judicieux d’utiliser les outils de journalisation activés par votre service API Web, comme [Application Insights](../../azure-functions/functions-monitoring.md), pour surveiller votre API en cas de codes d’erreur inattendus, d’exceptions et de performances médiocres.
* Analysez les codes d’état HTTP autres que HTTP 200 ou 400.
* Un code d’état HTTP 401 ou 403 indique généralement un problème avec votre authentification. Vérifiez la couche d’authentification de votre API et la configuration correspondante dans le connecteur d’API.
* Si nécessaire, utilisez des niveaux de journalisation plus agressifs (par exemple, « trace » ou « debug ») lors du développement.
* Surveillez votre API en cas de temps de réponse longs. 

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [ajouter un système d’approbation personnalisé à l’inscription en libre-service](self-service-sign-up-add-approvals.md)
- Lancez-vous avec nos [exemples de démarrage rapide](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
