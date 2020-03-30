---
title: Fichier Include
description: Fichier Include
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772410"
---
### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

À l’aide de votre clé de runtime et du point de terminaison de runtime, créez des variables d’environnement pour l’authentification et l’accès :

* `LUIS_RUNTIME_KEY` - Clé de ressource de runtime pour l’authentification de vos requêtes.
* `LUIS_RUNTIME_ENDPOINT` - Point de terminaison de runtime associé à votre clé.
* `LUIS_APP_ID` - L’ID d’application LUIS IoT public est `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` ou `staging`

Si vous avez l’intention d’utiliser ce guide de démarrage rapide pour accéder à votre propre application, vous devez effectuer des étapes supplémentaires :
* Créer l’application et récupérer l’ID de l’application
* Affecter la clé de runtime à l’application dans le portail LUIS
* Tester l’URL avec le navigateur permettant d’accéder à l’application

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Après avoir ajouté les variables d’environnement, redémarrez la fenêtre de console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Après avoir ajouté les variables d’environnement, exécutez `source ~/.bashrc` à partir de la fenêtre de console pour appliquer les changements.

#### <a name="macos"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Après avoir ajouté les variables d’environnement, exécutez `source .bash_profile` à partir de la fenêtre de console pour appliquer les changements.

***
