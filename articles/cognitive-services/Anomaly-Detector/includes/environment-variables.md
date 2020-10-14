---
title: Variables d’environnement du Détecteur d’anomalies
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 74e5373ebafbd39748f8b002069eaa6d732be069
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018447"
---
### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

>[!NOTE]
> Les points de terminaison pour les ressources autres que d’essai créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

En utilisant votre clé et le point de terminaison de la ressource que vous avez créée, créez deux variables d’environnement pour l’authentification :

* `ANOMALY_DETECTOR_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `ANOMALY_DETECTOR_ENDPOINT` : point de terminaison de ressource pour l’envoi de requêtes d’API. Il se présente comme suit : 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macos"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.

***