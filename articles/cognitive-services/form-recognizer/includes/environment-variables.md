---
title: Variables d'environnement
description: définir des variables d’environnement
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 28362a81461b63440ad752071f11b3603a979995
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86100349"
---
En utilisant votre clé et le point de terminaison de la ressource que vous avez créée, créez deux variables d’environnement pour l’authentification :

* `FORM_RECOGNIZER_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `FORM_RECOGNIZER_ENDPOINT` : point de terminaison de ressource pour l’envoi de requêtes d’API. Il se présente comme suit : 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> Les points de terminaison pour les ressources autres que d’essai créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Suivez les instructions ci-dessous pour définir des variables d’environnement sur votre système d’exploitation.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Après avoir ajouté les variables d’environnement, fermez et rouvrez la fenêtre de la console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macos"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Après avoir ajouté les variables d’environnement, exécutez `source .bash_profile` à partir de la fenêtre de console pour appliquer les changements.
***
