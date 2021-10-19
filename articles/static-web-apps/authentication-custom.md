---
title: Authentification personnalisée dans Azure Static Web Apps
description: En savoir plus sur la configuration de l’authentification personnalisée pour Azure Static Web Apps
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 49921eba1a7f4c6c898eaadf1d8743d8d210057a
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729802"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Authentification personnalisée dans Azure Static Web Apps

Azure Static Web Apps fournit une [authentification managée](authentication-authorization.md) qui utilise les inscriptions de fournisseur gérées par Azure. Pour bénéficier d’une plus grande souplesse au niveau de l’inscription, vous pouvez remplacer les valeurs par défaut par une inscription personnalisée.

- L’authentification personnalisée vous permet également de [configurer des fournisseurs personnalisés](./authentication-custom.md?tabs=openid-connect#configure-a-custom-identity-provider) qui prennent en charge [OpenID Connect](https://openid.net/connect/). Cette configuration permet l’inscription de plusieurs fournisseurs externes.

- L’utilisation de toute inscription personnalisée désactive tous les fournisseurs préconfigurés.

> [!NOTE]
> L’authentification personnalisée est uniquement disponible dans le plan Standard d’Azure Static Web Apps.

## <a name="configure-a-custom-identity-provider"></a>Configurer un fournisseur d’identité personnalisé

Les fournisseurs d’identité personnalisés sont configurés dans la section `auth` du [fichier config](configuration.md).

Pour éviter de placer des secrets dans le contrôle de code source, la configuration recherche dans les [paramètres d’application](application-settings.md) un nom correspondant dans le fichier config. Vous pouvez également choisir de stocker vos secrets dans [Azure Key Vault](./key-vault-secrets.md).

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `AAD_CLIENT_ID` | ID d’application (client) pour l’inscription de votre application Azure AD. |
| `AAD_CLIENT_SECRET` | Clé secrète client d’inscription d’application Azure AD. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

Les fournisseurs Azure Active Directory sont disponibles dans deux versions différentes. La version 1 définit explicitement le `userDetailsClaim`, qui permet à la charge utile de retourner des informations utilisateur. Inversement, la version 2 renvoie des informations utilisateur par défaut, et est désignée par `v2.0` dans l’URL `openIdIssuer`.

### <a name="azure-active-directory-version-1"></a>Version 1 Azure Active Directory

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Assurez-vous de remplacer la valeur `<TENANT_ID>` par l’ID de locataire Azure Active Directory.

### <a name="azure-active-directory-version-2"></a>Version 2 Azure Active Directory

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>/v2.0",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Assurez-vous de remplacer la valeur `<TENANT_ID>` par l’ID de locataire Azure Active Directory.

Pour plus d’informations sur la configuration d’Azure Active Directory, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-aad.md#-option-2-use-an-existing-registration-created-separately) au sujet de l’utilisation d’une inscription existante.

> [!NOTE]
> Tandis que la section de configuration pour Azure Active Directory est `azureActiveDirectory`, la plateforme prend l’alias de `aad` dans l’URL pour la connexion, la déconnexion et la purge des informations utilisateur. Consultez la section [authentification et autorisation](authentication-authorization.md) pour plus d’informations.

# <a name="apple"></a>[Apple](#tab/apple)

Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `APPLE_CLIENT_ID` | ID client Apple. |
| `APPLE_CLIENT_SECRET` | Clé secrète client Apple. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "APPLE_CLIENT_ID",
          "clientSecretSettingName": "APPLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration d’Apple en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-apple.md).

# <a name="facebook"></a>[Facebook](#tab/facebook)

Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `FACEBOOK_APP_ID` | ID d'application Facebook. |
| `FACEBOOK_APP_SECRET` | Secret d'application Facebook. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "FACEBOOK_APP_ID",
          "appSecretSettingName": "FACEBOOK_APP_SECRET"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Facebook en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-facebook.md).

# <a name="github"></a>[GitHub](#tab/github)


Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `GITHUB_CLIENT_ID` | ID client GitHub. |
| `GITHUB_CLIENT_SECRET` | Clé secrète client GitHub. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "GITHUB_CLIENT_ID",
          "clientSecretSettingName": "GITHUB_CLIENT_SECRET"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)


Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `GOOGLE_CLIENT_ID` | ID de client Google. |
| `GOOGLE_CLIENT_SECRET` | Clé secrète client Google. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "GOOGLE_CLIENT_ID",
          "clientSecretSettingName": "GOOGLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Google en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-google.md).

# <a name="twitter"></a>[Twitter](#tab/twitter)

Pour créer l’inscription, commencez par créer les [paramètres d’application](application-settings.md) suivants :

| Nom du paramètre | Valeur |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Clé du client Twitter. |
| `TWITTER_CONSUMER_SECRET` | Secret du client Twitter. |

Ensuite, utilisez l’exemple suivant pour configurer le fournisseur dans le [fichier config](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "TWITTER_CONSUMER_KEY",
          "consumerSecretSettingName": "TWITTER_CONSUMER_SECRET"
        }
      }
    }
  }
}
```

Pour plus d’informations sur la configuration de Twitter en tant que fournisseur d’authentification, consultez la [documentation relative à l’authentification et à l’autorisation d’App Service](../app-service/configure-authentication-provider-twitter.md).

# <a name="openid-connect"></a>[OpenID Connect](#tab/openid-connect)

Vous pouvez configurer Azure Static Web Apps pour utiliser un fournisseur d’authentification personnalisé conforme à la [spécification OpenID Connect (OIDC)](https://openid.net/connect/). Les étapes suivantes sont requises pour utiliser un fournisseur OIDC personnalisé.

- Un ou plusieurs fournisseurs OIDC sont autorisés.
- Chaque fournisseur doit avoir un nom unique dans la configuration.
- Un seul fournisseur peut servir de cible de redirection par défaut.

### <a name="register-your-application-with-the-identity-provider"></a>Inscrire votre application auprès du fournisseur d’identité

Vous devez enregistrer les détails de votre application auprès d’un fournisseur d’identité. Vérifiez auprès du fournisseur les étapes nécessaires à la génération d’un **ID client** et d’une **clé secrète client** pour votre application.

Une fois l’application inscrite auprès du fournisseur d’identité, créez les secrets d’application suivants dans les [paramètres d’application](application-settings.md) de l’application web statique :

| Nom du paramètre | Valeur |
| --- | --- |
| `MY_PROVIDER_CLIENT_ID` | L’ID client généré par le fournisseur d’authentification pour votre application web statique. |
| `MY_PROVIDER_CLIENT_SECRET` | La clé secrète client générée par l’inscription personnalisée du fournisseur d’authentification pour votre application web statique. |

Si vous inscrivez des fournisseurs supplémentaires, chacun d’entre eux a besoin d’un ID client et d’un magasin de clé secrète client associés dans les paramètres de l’application.

> [!IMPORTANT]
> Les secrets d’application sont des informations d’identification de sécurité sensibles. Ne partagez ce secret avec personne, ne le distribuez pas dans une application cliente et ne l’archivez pas dans le contrôle de code source.

Une fois que vous disposez des informations d’identification de l’inscription, procédez comme suit pour créer une inscription personnalisée.

1. Vous avez besoin des métadonnées OpenID Connect pour le fournisseur. Ces informations sont souvent exposées via un [document de métadonnées de configuration](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), qui est l’_URL d’émetteur_ du fournisseur avec le suffixe `/.well-known/openid-configuration`. Notez cette URL de configuration.

1. Ajoutez une section `auth` du [fichier config](configuration.md) avec un bloc de configuration pour les fournisseurs OIDC et la définition de votre fournisseur.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "MY_PROVIDER_CLIENT_ID",
               "clientCredential": {
                 "clientSecretSettingName": "MY_PROVIDER_CLIENT_SECRET"
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

  - Le nom du fournisseur, `myProvider` dans cet exemple, est l’identifiant unique utilisé par Azure Static Web Apps.
  - Assurez-vous de remplacer `<PROVIDER_ISSUER_URL>` par le chemin de l’_URL de l’émetteur_ du fournisseur.
  - L’objet `login` vous permet de fournir des valeurs pour : des étendues personnalisées, des paramètres de connexion ou des revendications personnalisées.

---

## <a name="authentication-callbacks"></a>Rappels d’authentification

Les fournisseurs d’identité ont besoin d’une URL de redirection pour terminer la requête de connexion ou de déconnexion. La plupart des fournisseurs exigent que vous ajoutiez les URL de rappel à une liste d’autorisation. Les points de terminaison suivants sont disponibles comme destinations de redirection.

| Type   | Modèle d’URL                                                 |
| ------ | ----------------------------------------------------------- |
| Connexion  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Si vous utilisez Azure Active Directory, utilisez `aad` comme valeur de l’espace réservé `<PROVIDER_NAME_IN_CONFIG>`.

> [!Note]
> Ces URL sont fournies par Azure Static Web Apps pour recevoir la réponse du fournisseur d’authentification ; vous n’avez pas besoin de créer des pages sur ces itinéraires.

## <a name="login-logout-and-purging-user-details"></a>Connexion, déconnexion et suppression définitive des détails de l’utilisateur

Pour utiliser un fournisseur d’identité personnalisé, utilisez les modèles d’URL suivants.

| Action             | Modèle                                  |
| ------------------ | ---------------------------------------- |
| Connexion              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Suppression définitive des détails de l’utilisateur | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Si vous utilisez Azure Active Directory, utilisez `aad` comme valeur de l’espace réservé `<PROVIDER_NAME_IN_CONFIG>`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sécurisation des secrets d’authentification dans Azure Key Vault](./key-vault-secrets.md)
