---
title: Activer les options d’application web Python à l’aide d’Azure Active Directory B2C
description: Activez l’utilisation des options d’application web Python à l’aide de plusieurs méthodes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 4d27b22cc0ac682ec6f3fdb5a189462026fc7755
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641217"
---
# <a name="configure-authentication-options-in-a-python-web-application-using-azure-active-directory-b2c"></a>Configurer les options d’authentification dans une application web Python avec Azure Active Directory B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application web Python. 

Avant de commencer, nous vous recommandons de lire l’article [Configurer l’authentification dans un exemple d’application web Python](configure-authentication-sample-python-web-app.md).

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
1. Recherchez la méthode `initiate_auth_code_flow` et ajoutez le paramètre `login_hint` avec le nom de domaine du fournisseur d’identité. Par exemple, facebook.com.

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Recherchez la méthode `initiate_auth_code_flow` et ajoutez le paramètre `domain_hint` avec l’indicateur de connexion.

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, consultez l’article présentant les [options de configuration de MSAL pour Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki)
