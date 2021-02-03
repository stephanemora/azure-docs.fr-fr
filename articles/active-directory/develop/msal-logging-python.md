---
title: Journalisation des erreurs et des exceptions dans MSAL pour Python
titleSuffix: Microsoft identity platform
description: En savoir plus sur la journalisation des erreurs et des exceptions dans MSAL pour Python
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063112"
---
# <a name="logging-in-msal-for-python"></a>Journalisation dans MSAL pour Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Journalisation MSAL pour Python

La journalisation dans MSAL Python utilise le mécanisme de journalisation Python standard, par exemple `logging.info("msg")`. Vous pouvez configurer la journalisation MSAL comme suit (et la voir en action dans [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)) :

### <a name="enable-debug-logging-for-all-modules"></a>Activer la journalisation du débogage pour tous les modules

Par défaut, la journalisation dans n’importe quel script Python est désactivée. Si vous souhaitez activer la journalisation du débogage pour tous les modules dans l’intégralité de votre script Python, utilisez :

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Mettre au silence uniquement la journalisation MSAL

Pour mettre au silence uniquement la journalisation de la bibliothèque MSAL, tout en activant la journalisation du débogage dans tous les autres modules de votre script Python, désactivez le journaliseur utilisé par MSAL Python :

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Données personnelles et de l’organisation dans Python

MSAL pour Python ne journalise pas les données personnelles ni les données de l’organisation. Il n’existe aucune propriété pour activer ou désactiver la journalisation des données personnelles ou de l’organisation.

Vous pouvez utiliser la journalisation Python standard pour consigner ce que vous voulez, mais vous êtes responsable de la gestion des données sensibles et du respect des conditions réglementaires.

Pour plus d’informations sur la journalisation dans Python, consultez la documentation [Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial) de Python.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).
