---
title: Authentification personnalisée dans Azure Static Web Apps
description: En savoir plus sur la configuration de l’authentification personnalisée pour Azure Static Web Apps
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: e4583c6474872cc1de909d86d812aa9ac9630536
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854573"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Authentification personnalisée dans Azure Static Web Apps

Azure Static Web Apps fournit une [authentification managée](authentication-authorization.md) qui utilise les inscriptions de fournisseur gérées par Azure. Pour bénéficier d’une plus grande souplesse au niveau de l’inscription, vous pouvez remplacer les valeurs par défaut par une inscription personnalisée.

- L’authentification personnalisée vous permet également de [configurer des fournisseurs personnalisés](#configure-a-custom-openid-connect-provider) qui prennent en charge [OpenID Connect](https://openid.net/connect/). Cette configuration permet l’inscription de plusieurs fournisseurs externes.

- L’utilisation de toute inscription personnalisée désactive tous les fournisseurs préconfigurés.

- En ce qui concerne les inscriptions Azure Active Directory (AAD), vous avez la possibilité de fournir un locataire, ce qui vous permet de contourner le [flux d’invitation](./authentication-authorization.md#role-management) pour la gestion des groupes.

> [!NOTE]
> L’authentification personnalisée est uniquement disponible dans le plan Standard d’Azure Static Web Apps.

## <a name="override-pre-configured-provider"></a>Remplacer le fournisseur préconfiguré

Les paramètres utilisés pour remplacer un fournisseur sont configurés dans la section `auth` du [fichier config](configuration.md).

Pour éviter de placer des secrets dans le contrôle de code source, la configuration recherche dans les [paramètres d’application](application-settings.md) un nom correspondant dans le fichier config. Vous pouvez également choisir de stocker vos secrets dans [Azure Key Vault](./key-vault-secrets.md).

### <a name="configuration"></a>Configuration

Les tableaux suivants contiennent les différentes options de configuration pour chaque fournisseur.

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

| Chemin de champ                             | Description                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `registration.openIdIssuer`            | Point de terminaison de la configuration OpenID du locataire AAD.                                                  |
| `registration.clientIdSettingName`     | Nom du paramètre d’application contenant l’ID d’application (client) pour l’inscription d’application d’Azure AD. |
| `registration.clientSecretSettingName` | Nom du paramètre d’application contenant la clé secrète client pour l’inscription d’application d’Azure AD.           |

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "<AAD_CLIENT_ID>",
          "clientSecretSettingName": "<AAD_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Azure Active Directory comporte des points de terminaison versionnés qui affectent le mode de configuration de votre inscription. Si vous utilisez AAD v1 (le point de terminaison de l’émetteur ne se termine pas par « /v2.0 »), vous devez ajouter l’entrée `userDetailsClaim` suivante à votre configuration dans l’objet `"azureActiveDirectory"`.

```json
"azureActiveDirectory": {
  "registration": { ... },
  "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" 
}
```

Pour plus d’informations sur la configuration d’Azure Active Directory, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-aad.md).

# <a name="apple"></a>[Apple](#tab/apple)

| Chemin de champ                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nom du paramètre d’application contenant l’ID client.                                       |
| `registration.clientSecretSettingName` | Nom du paramètre d’application contenant la clé secrète client.                                   |

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "<APPLE_CLIENT_ID>",
          "clientSecretSettingName": "<APPLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration d’Apple en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-apple.md).

# <a name="facebook"></a>[Facebook](#tab/facebook)

| Chemin de champ                          | Description                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `registration.appIdSettingName`     | Nom du paramètre d’application contenant l’ID d’application.                             |
| `registration.appSecretSettingName` | Nom du paramètre d’application contenant le secret de l’application.                         |

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "<FACEBOOK_APP_ID>",
          "appSecretSettingName": "<FACEBOOK_APP_SECRET>"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Facebook en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-facebook.md).

# <a name="github"></a>[GitHub](#tab/github)

| Chemin de champ                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nom du paramètre d’application contenant l’ID client.                                |
| `registration.clientSecretSettingName` | Nom du paramètre d’application contenant la clé secrète client.                            |

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "<GITHUB_CLIENT_ID>",
          "clientSecretSettingName": "<GITHUB_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)

| Chemin de champ                             | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nom du paramètre d’application contenant l’ID client.                                |
| `registration.clientSecretSettingName` | Nom du paramètre d’application contenant la clé secrète client.                            |

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "<GOOGLE_CLIENT_ID>",
          "clientSecretSettingName": "<GOOGLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Google en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-google.md).

# <a name="twitter"></a>[Twitter](#tab/twitter)

| Chemin de champ                               | Description                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `registration.consumerKeySettingName`    | Nom du paramètre d’application contenant la clé du consommateur.                                   |
| `registration.consumerSecretSettingName` | Nom du paramètre d’application contenant le secret du consommateur.                                |

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "<TWITTER_CONSUMER_KEY>",
          "consumerSecretSettingName": "<TWITTER_CONSUMER_SECRET>"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Twitter en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-twitter.md).

---

## <a name="configure-a-custom-openid-connect-provider"></a>Configurer un fournisseur OpenID Connect personnalisé

Cette section vous montre comment configurer Azure Static Web Apps pour utiliser un fournisseur d’authentification personnalisé conforme à la [spécification OpenID Connect (OIDC)](https://openid.net/connect/). Les étapes suivantes sont requises pour utiliser un fournisseur OIDC personnalisé.

- Un ou plusieurs fournisseurs OIDC sont autorisés.
- Chaque fournisseur doit avoir un nom unique dans la configuration.
- Un seul fournisseur peut servir de cible de redirection par défaut.

### <a name="register-your-application-with-the-identity-provider"></a>Inscrire votre application auprès du fournisseur d’identité

Vous devez enregistrer les détails de votre application auprès d’un fournisseur d’identité. Vérifiez auprès du fournisseur les étapes nécessaires à la génération d’un **ID client** et d’une **clé secrète client** pour votre application.

> [!IMPORTANT]
> Les secrets d’application sont des informations d’identification de sécurité sensibles. Ne partagez ce secret avec personne, ne le distribuez pas dans une application cliente et ne l’archivez pas dans le contrôle de code source.

Une fois que vous disposez des informations d’identification de l’inscription, procédez comme suit pour créer une inscription personnalisée.

1. Ajoutez l’ID client et la clé secrète client comme [paramètres d’application](application-settings.md) pour l’application, en utilisant les noms de paramètres de votre choix. Prenez note de ces noms pour plus tard. L’ID client peut également être inclus dans le fichier config.

1. Vous avez besoin des métadonnées OpenID Connect pour le fournisseur. Ces informations sont souvent exposées via un [document de métadonnées de configuration](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), qui est l’_URL d’émetteur_ du fournisseur avec le suffixe `/.well-known/openid-configuration`. Notez cette URL de configuration.

1. Ajoutez une section `auth` du [fichier config](configuration.md) avec un bloc de configuration pour les fournisseurs OIDC et la définition de votre fournisseur.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "<MY_PROVIDER_CLIENT_ID_SETTING_NAME>",
               "clientCredential": {
                 "clientSecretSettingName": "<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  Modifiez les jetons de remplacement suivants dans le code avec vos valeurs.

  | Remplacez ceci… | par… |
  | --- | --- |
  | `<MY_PROVIDER_CLIENT_ID_SETTING_NAME>` | Nom du paramètre d’application associé à l’ID client généré par votre inscription personnalisée. |
  | `<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>` | Nom du paramètre d’application associé à la clé secrète client générée par votre inscription personnalisée. |
  | `<PROVIDER_ISSUER_URL>` | Chemin d’accès à l’_URL de l’émetteur_ du fournisseur. |

- Le nom du fournisseur, `myProvider` dans cet exemple, est l’identifiant unique utilisé par Azure Static Web Apps.
- L’objet `login` vous permet de fournir des valeurs pour : des étendues personnalisées, des paramètres de connexion ou des revendications personnalisées.

### <a name="login-logout-and-purging-user-details"></a>Connexion, déconnexion et suppression définitive des détails de l’utilisateur

Pour utiliser un fournisseur OIDC personnalisé, utilisez les modèles d’URL suivants.

| Action             | Modèle                                  |
| ------------------ | ---------------------------------------- |
| Connexion              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Suppression définitive des détails de l’utilisateur | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

### <a name="authentication-callbacks"></a>Rappels d’authentification

Les fournisseurs d’authentification ont besoin d’une URL de redirection pour terminer la demande de connexion ou de déconnexion. Les points de terminaison suivants sont disponibles comme destinations de redirection.

| Type   | Modèle d’URL                                                 |
| ------ | ----------------------------------------------------------- |
| Connexion  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

> [!Note]
> Ces URL sont fournies par Azure Static Web Apps pour recevoir la réponse du fournisseur d’authentification ; vous n’avez pas besoin de créer des pages sur ces itinéraires.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sécurisation des secrets d’authentification dans Azure Key Vault](./key-vault-secrets.md)
