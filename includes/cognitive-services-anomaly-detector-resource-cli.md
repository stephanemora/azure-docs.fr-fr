---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554695"
---
Commencez à utiliser le service Détecteur d’anomalies en créant une des ressources Azure ci-dessous.

* Une [ressource d’essai](https://azure.microsoft.com/try/cognitive-services/#decision) (aucun abonnement Azure n’est nécessaire) : 
    * Valable pendant sept jours, gratuitement. Une fois l’inscription terminée, une clé d’essai et un point de terminaison seront disponibles sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Il s’agit d’une option intéressante si vous souhaitez essayer le service Détecteur d’anomalies, mais que vous n’avez pas d’abonnement Azure.

* Une [ressource Détecteur d’anomalies](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) :
    * Disponible via le [portail Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) jusqu’à ce que vous supprimiez la ressource.
    * Utilisez le niveau tarifaire Gratuit pour tester le service, puis effectuez par la suite une mise à niveau vers un niveau payant pour la production.

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

>[!NOTE]
> Les points de terminaison pour les ressources autres que d’essai créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

En utilisant votre clé et le point de terminaison de la ressource que vous avez créée, créez deux variables d’environnement pour l’authentification :

* `ANOMALY_DETECTOR_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `ANOMALY_DETECTOR_ENDPOINT` : point de terminaison de ressource pour l’envoi de requêtes d’API. Il se présente comme suit : 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.

***