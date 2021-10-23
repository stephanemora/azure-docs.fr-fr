---
title: Configuration d’AuthN/AuthZ basée sur les fichiers
description: Configurez l’authentification et l’autorisation dans App Service à l’aide d’un fichier config pour activer certaines capacités en préversion.
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 990ce0caf05134d173afb7325682d05d9feb14ea
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047339"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Configuration basée sur les fichiers dans l’authentification Azure App Service

Avec l’[authentification App Service](overview-authentication-authorization.md), les paramètres d’authentification peuvent être configurés avec un fichier. Il se peut que vous deviez utiliser une configuration basée sur des fichiers pour utiliser certaines capacités en préversion de l’authentification/autorisation App Service avant qu’elles ne soient exposées via les [API Azure Resource Manager](../azure-resource-manager/management/overview.md).

> [!IMPORTANT]
> N’oubliez pas que la charge utile de votre application et, par conséquent, ce fichier peuvent passer d’un environnement à un autre, comme avec les [emplacements](./deploy-staging-slots.md). Si vous souhaitez épingler une inscription d’application différente pour chaque emplacement, ce qui est probablement le cas, continuez à appliquer la méthode de configuration standard au lieu d’utiliser le fichier de configuration.

## <a name="enabling-file-based-configuration"></a>Activation de la configuration à l’aide d’un fichier

1. Créez un fichier JSON pour votre configuration à la racine de votre projet (déployé sur D:\home\site\wwwroot dans votre application web/de fonction). Renseignez la configuration de votre choix suivant les [informations de référence de la configuration à l’aide d’un fichier](#configuration-file-reference). Si vous modifiez une configuration Azure Resource Manager existante, veillez à traduire les propriétés capturées dans la collection `authsettings` dans votre fichier de configuration.

2. Modifiez la configuration existante, capturée dans les API [Azure Resource Manager](../azure-resource-manager/management/overview.md) sous `Microsoft.Web/sites/<siteName>/config/authsettingsV2`. Pour cela, vous pouvez utiliser un [modèle Azure Resource Manager](../azure-resource-manager/templates/overview.md) ou un outil comme [Azure Resource Explorer](https://resources.azure.com/). Au sein de la collection authsettingsV2, vous devrez définir deux propriétés (et éventuellement en supprimer d’autres) :

    1. Définissez `platform.enabled` sur « true ».
    2. Définissez `platform.configFilePath` sur le nom du fichier (par exemple, « auth.json »).

> [!NOTE]
> Le format de `platform.configFilePath` varie d’une plateforme à l’autre. Sur Windows, les chemins d’accès relatifs et absolus sont pris en charge. Les chemins d’accès relatifs sont recommandés. Pour Linux, seuls des chemins d’accès absolus sont actuellement pris en charge. Par conséquent, la valeur du paramètre doit être « /Home/site/wwwroot/auth.JSON » ou une valeur similaire.

Une fois cette mise à jour de la configuration effectuée, le contenu du fichier permet de définir le comportement de l’authentification/autorisation App Service pour ce site. Si vous souhaitez un jour revenir à la configuration Azure Resource Manager, vous pouvez le faire en modifiant le paramètre `platform.configFilePath` sur Null.

## <a name="configuration-file-reference"></a>Informations de référence sur le fichier de configuration

Tous les secrets auquel le fichier de configuration fait référence doivent être stockés en tant que [paramètres d’application](./configure-common.md#configure-app-settings). Vous pouvez donner le nom de votre choix aux paramètres. Veillez simplement à ce que les références du fichier de configuration utilisent les mêmes clés.

Voici toutes les configuration possibles dans le fichier :

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|RejectWith401|RejectWith404",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>Plus de ressources

- [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout](tutorial-auth-aad.md)
- [Variables d’environnement et paramètres d’application pour l’authentification](reference-app-settings.md#authentication--authorization)
