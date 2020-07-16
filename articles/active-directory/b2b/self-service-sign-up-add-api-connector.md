---
title: Ajouter des connecteurs d’API aux flux d’inscription en libre-service - Azure AD
description: Configurer une API Web à utiliser dans un workflow utilisateur.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386740"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Ajouter un connecteur d’API à un workflow d’utilisateur

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **tous les connecteurs d’API (version préliminaire)** , puis sélectionnez **Nouveau connecteur d’API**.

   ![Ajoutez un nouveau connecteur d'API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Indiquez un nom d’affichage pour l’appel. Par exemple, **Vérifier l’état d’approbation**.
6. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
7. Saisissez les informations d’authentification pour l’API.

   - Seule l’authentification de base est actuellement prise en charge. Si vous souhaitez utiliser une API sans authentification de base à des fins de développement, saisissez simplement un **nom d’utilisateur** et un **mot de passe** factices que votre API peut ignorer. Pour une utilisation avec une fonction Azure avec une clé API, vous pouvez inclure le code en tant que paramètre de requête dans l’**URL du point de terminaison** (par exemple, https[]()://contoso.azurewebsites.net/api/endpoint<b>? code = 0123456789</b>).

   ![Ajoutez un nouveau connecteur d'API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Sélectionnez les revendications que vous souhaitez envoyer à l’API.
9. Sélectionnez les revendications que vous envisagez de recevoir de l’API.

   ![Définir des revendications de connecteur d’API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Sélectionnez **Enregistrer**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription en libre-service.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Flux d’utilisateurs (préversion)** , puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
5. Sélectionnez **Connecteurs d’API**, puis sélectionnez les points de terminaison d’API que vous souhaitez appeler aux étapes suivantes dans le workflow d’utilisateur :

   - **Après la connexion avec un fournisseur d’identité**
   - **Avant de créer l’utilisateur**

   ![Ajouter des API au flux de l’utilisateur](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Sélectionnez **Enregistrer**.

En savoir plus sur [où vous pouvez activer un connecteur d’API dans un workflow utilisateur](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Requête envoyée à l’API

Un connecteur d’API est matérialisé en tant que requête HTTP POSTÉRIEURE, en envoyant les revendications sélectionnées en tant que paires clé-valeur dans un corps JSON. La réponse doit également comporter l’en-tête HTTP`Content-Type: application/json`. Les attributs sont sérialisés de la même façon que les attributs utilisateur Microsoft Graph. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Exemple de requête

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

La revendication **Paramètres régionaux de l’interface utilisateur (« ui_locales »)** est envoyé par défaut dans toutes les demandes. Il fournit les paramètres régionaux d’un utilisateur et peut être utilisé par l’API pour renvoyer des réponses internationalisées. Elle n’apparaît pas dans le volet de configuration de l’API.

Si une revendication à envoyer n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API.

Des attributs personnalisés peuvent être créés pour l’utilisateur à l’aide du format d’**extension_\<extensions-app-id>_AttributeName (_NomAttribut)** . Votre API doit s’attendre à recevoir des revendications dans ce même format sérialisé. Votre API peut renvoyer des revendications avec ou sans le `<extensions-app-id>`. Pour plus d’informations sur les attributs personnalisés, consultez [définir des attributs personnalisés pour les flux d’inscription en libre-service](user-flow-add-custom-attributes.md).

> [!TIP] 
> Les revendications [**Identités (« identities »)** ](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) et **Adresse de messagerie (« email_address »)** peuvent être utilisées pour identifier un utilisateur avant qu’il n’ait un compte dans votre locataire. La revendication « identities » (Identités) est envoyée lorsqu’un utilisateur s’authentifie auprès de Google ou Facebook ; « email_address » (Adresse de messagerie) est toujours envoyé.

## <a name="expected-response-types-from-the-web-api"></a>Types de réponses attendus de l’API Web

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- [Réponse de continuation](#continuation-response)
- [Réponse de blocage](#blocking-response)
- [Réponse validation-erreur](#validation-error-response)

### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante. Dans une réponse de continuation, l’API peut renvoyer des revendications.

Si une revendication est renvoyée par l’API et sélectionnée en tant que **Revendication à recevoir**, la revendication effectue les opérations suivantes :

- Renseigne les champs d’entrée de la page de collection d’attributs si le connecteur d’API est appelé avant la présentation de la page.
- Remplace toute valeur qui a déjà été attribuée à la revendication.
- Attribue une valeur à la revendication si elle a été précédemment null.

#### <a name="example-of-a-continuation-response"></a>Exemple de réponse de continuation

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
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication renvoyée peut éventuellement ne pas contenir `_<extensions-app-id>_`. Les valeurs sont stockées dans le répertoire si elles sont sélectionnées en tant que **Revendication à recevoir** dans la configuration du connecteur d’API et **Attributs utilisateur** pour un workflow utilisateur. Les attributs personnalisés ne peuvent pas être renvoyés dans le jeton. |

### <a name="blocking-response"></a>Réponse de blocage

Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Voici un exemple de la réponse de blocage renvoyée :

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Paramètre   | Type   | Obligatoire | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Oui      | Version de l’API.                                                    |
| action      | String | Oui      | La valeur doit être `ShowBlockPage`                                              |
| userMessage | String | Oui      | Message à afficher à l’utilisateur.                                            |
| code        | String | Non       | Code d’erreur. Peut être utilisé à des fins de débogage. Non affiché à l’utilisateur. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Expérience de l’utilisateur final avec une réponse de blocage

![Exemple de page de blocage](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Validation-réponse d’erreur

Un appel d’API appelé après une page de collection d’attributs peut renvoyer une réponse d’erreur de validation. Dans ce cas, le workflow de l’utilisateur reste sur la page de collection d’attributs et le `userMessage` s’affiche à l’utilisateur. L’utilisateur peut ensuite modifier et renvoyer le formulaire. Ce type de réponse peut être utilisé pour la validation d’entrée.

#### <a name="example-of-a-validation-error-response"></a>Exemple de réponse d’erreur de validation

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Paramètre   | Type    | Obligatoire | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Oui      | Version de l’API.                                                    |
| action      | String  | Oui      | La valeur doit être `ValidationError`.                                           |
| status      | Integer | Oui      | La valeur doit être `400` pour une réponse ValidationError.                        |
| userMessage | String  | Oui      | Message à afficher à l’utilisateur.                                            |
| code        | String  | Non       | Code d’erreur. Peut être utilisé à des fins de débogage. Non affiché à l’utilisateur. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Expérience de l’utilisateur final avec une réponse d’erreur de validation

![Exemple de page de validation](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Intégration à Azure Functions
Vous pouvez utiliser un déclencheur HTTP dans Azure Functions comme un moyen simple de créer une API à utiliser avec le connecteur d’API. Vous utilisez la fonction Azure pour, [par exemple](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), effectuer une logique de validation et limiter les inscriptions à des domaines spécifiques. Vous pouvez également appeler et invoquer d’autres API Web, magasins d’utilisateurs et autres services Cloud.

## <a name="next-steps"></a>Étapes suivantes

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Découvrez comment [ajouter un système d’approbation personnalisé à l’inscription en libre-service](self-service-sign-up-add-approvals.md)
- Prise en main de nos [exemples de démarrage rapide d’Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
