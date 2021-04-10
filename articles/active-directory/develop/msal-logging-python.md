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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578751"
---
# <a name="logging-in-msal-for-python"></a>Journalisation dans MSAL pour Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Journalisation MSAL pour Python

La journalisation dans MSAL pour Python s’appuie sur le [module de journalisation de la bibliothèque Python standard](https://docs.python.org/3/library/logging.html). Vous pouvez configurer la journalisation MSAL comme suit (et la voir en action dans [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)) :

### <a name="enable-debug-logging-for-all-modules"></a>Activer la journalisation du débogage pour tous les modules

Par défaut, la journalisation dans n’importe quel script Python est désactivée. Si vous souhaitez activer la journalisation documentée pour **tous** les modules Python de votre script, utilisez `logging.basicConfig` avec un niveau de `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Cela permet d’imprimer tous les messages de journal donnés au module de journalisation dans la sortie standard.

### <a name="configure-msal-logging-level"></a>Configurer le niveau de journalisation MSAL

Vous pouvez configurer le niveau de journalisation de MSAL pour le module fournisseur d’informations Python en utilisant la méthode `logging.getLogger()` avec le nom de l’enregistreur d’événements `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Configurer la journalisation MSAL avec Azure App Insights

Les journaux Python sont fournis à un gestionnaire de journaux qui, par défaut, est le `StreamHandler`. Pour envoyer des journaux MSAL à Application Insights avec une clé d’instrumentation, utilisez le `AzureLogHandler` fourni par la bibliothèque `opencensus-ext-azure`.

Pour installer, `opencensus-ext-azure` ajoutez le package `opencensus-ext-azure` à partir de PyPI à vos dépendances ou à votre installation pip :

```console
pip install opencensus-ext-azure
```

Modifiez ensuite le gestionnaire par défaut du module fournisseur d’informations `"msal"` en une instance de `AzureLogHandler` avec une clé d’instrumentation définie dans la `APP_INSIGHTS_KEY` variable d’environnement :

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Données personnelles et de l’organisation dans Python

MSAL pour Python ne journalise pas les données personnelles ni les données de l’organisation. Il n’existe aucune propriété pour activer ou désactiver la journalisation des données personnelles ou de l’organisation.

Vous pouvez utiliser la journalisation Python standard pour consigner ce que vous voulez, mais vous êtes responsable de la gestion des données sensibles et du respect des conditions réglementaires.

Pour plus d’informations sur la journalisation dans Python, consultez la documentation [Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial) de Python.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de code, consultez [Exemples de code de la plateforme d’identités Microsoft](sample-v2-code.md).
