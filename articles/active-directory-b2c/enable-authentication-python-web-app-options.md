---
title: Activer les options d’application web Python à l’aide d’Azure Active Directory B2C
description: Cet article explique comment activer l’utilisation des options de l’application web Python.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 155e09cc0aeda6e9a93358f25ca44d852fd34ea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041060"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>Activer les options d’authentification dans une application web Python à l’aide d’Azure AD B2C 

Cet article décrit les méthodes permettant d’activer, de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application web Python. 

Avant de commencer, nous vous recommandons de consulter [Configurer l’authentification dans un exemple d’application web Python à l’aide d’Azure AD B2C](configure-authentication-sample-python-web-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et votre ID de locataire dans l’URL d’authentification : 

1. Suivez les instructions dans [Activer les domaines personnalisés](custom-domain.md).
1. Dans le fichier *app_config.py*, mettez à jour le membre de classe `authority_template` avec votre domaine personnalisé.

Le code Python suivant montre les paramètres de l’application avant la modification :

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

Le code Python suivant montre les paramètres de l’application après la modification :

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Recherchez la méthode `initiate_auth_code_flow`, puis ajoutez le paramètre `login_hint` avec le nom de domaine du fournisseur d’identité (par exemple, *facebook.com*).

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Recherchez la méthode `initiate_auth_code_flow`, puis ajoutez le paramètre `domain_hint` avec l’indicateur de connexion.

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, consultez [Options de configuration de MSAL pour Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki).
