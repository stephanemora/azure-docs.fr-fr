---
title: Cycle de vie des modèles et des points de terminaison de Custom Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech fournit des modèles de base pour l’adaptation et vous permet de créer des modèles personnalisés à partir de vos données. Cet article décrit les chronologies pour les modèles et pour les points de terminaison qui utilisent ces modèles.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: 701a16779a7a485f33e5af44ec30d48801b7c1fd
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111440805"
---
# <a name="model-and-endpoint-lifecycle"></a>Cycle de vie des modèles et des points de terminaison

Notre service vocal standard (non personnalisé) est basé sur les modèles d’intelligence artificielle que nous appelons modèles de base. Dans la plupart des cas, nous formons un modèle de base différent pour chaque langue parlée que nous prenons en charge.  Nous mettons à jour le service vocal avec de nouveaux modèles de base tous les quelques mois pour améliorer la précision et la qualité.  
Avec Custom Speech, vous créez des modèles personnalisés en choisissant un modèle de base et en l’adaptant avec les données de votre scénario client particulier. Une fois que vous avez créé un modèle personnalisé, ce modèle n’est pas mis à jour ou modifié, même si le modèle de base correspondant à partir duquel il a été adapté est mis à jour dans le service vocal standard.  
Cette stratégie vous permet de continuer à utiliser un modèle personnalisé particulier pendant une longue période une fois que vous avez un modèle personnalisé qui répond à vos besoins.  Toutefois, nous vous recommandons de recréer régulièrement votre modèle personnalisé afin de pouvoir l’adapter à partir du dernier modèle de base pour tirer parti de l’amélioration de la précision et de la qualité.

Voici d’autres termes clés liés au cycle de vie du modèle :

* **Adaptation** : personnalisation d’un modèle de base en fonction de votre domaine/scénario à l’aide de données texte et/ou audio.
* **Décodage** : utilisation d’un modèle et exécution d’une reconnaissance vocale (décodage d’audio en texte).
* **Point de terminaison** : déploiement spécifique d’un utilisateur d’un modèle de base ou d’un modèle personnalisé *uniquement* pour un utilisateur donné.

### <a name="expiration-timeline"></a>Chronologie d’expiration

Au fur et à mesure que de nouveaux modèles et de nouvelles fonctionnalités deviennent disponibles et que les anciens modèles, moins justes, sont mis hors service, consultez les chronologies suivantes pour connaître l’expiration des modèles et des points de terminaison :

**Modèles de base** 

* Adaptation : disponible pendant un an. Une fois le modèle importé, il est disponible pendant un an pour créer des modèles personnalisés. Après un an, de nouveaux modèles personnalisés doivent être créés à partir d’une version plus récente du modèle de base.  
* Décodage : disponible pendant deux ans après l’importation. Vous pouvez ainsi créer un point de terminaison et utiliser une transcription par lots pendant deux ans avec ce modèle. 
* Points de terminaison : disponibles sur la même chronologie que le décodage.

**Modèles personnalisés**

* Décodage : disponible pendant deux ans après la création du modèle. Vous pouvez ainsi utiliser le modèle personnalisé pendant deux ans (traitement par lots/temps réel/tests) après sa création. Après deux ans, *vous devez réeffectuer l’apprentissage de votre modèle*, car le modèle de base est généralement déconseillé pour l’adaptation.  
* Points de terminaison : disponibles sur la même chronologie que le décodage.

Quand un modèle de base ou un modèle personnalisé expire, il revient toujours à la *version la plus récente du modèle de base*. Ainsi, votre implémentation ne sera jamais interrompue mais pourrait devenir moins précise pour *vos données spécifiques* si les modèles personnalisés arrivent à expiration. Vous pouvez voir les détails relatifs à l’expiration d’un modèle aux emplacements suivants dans la zone Custom Speech de Speech Studio :

* Récapitulatif de l’entraînement du modèle
* Détail de l’entraînement du modèle
* Résumé du déploiement
* Détail du déploiement

Voici un exemple tiré du résumé de la formation du modèle :

![Résumé de la formation du modèle](media/custom-speech/custom-speech-model-training-with-expiry.png) Et aussi de la page de détails de la formation du modèle :

![Détail de l’entraînement du modèle](media/custom-speech/custom-speech-model-details-with-expiry.png)

Vous pouvez également vérifier les dates d’expiration via les API [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) et [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) de Custom Speech sous la propriété `deprecationDates` dans la réponse JSON.

Voici un exemple de données d’expiration provenant de l’appel de l’API GetModel. **DEPRECATIONDATES** s’affiche lorsque le modèle expire : 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Notez que vous pouvez mettre à niveau le modèle sur un point de terminaison Custom Speech sans temps d’arrêt en changeant le modèle utilisé par le point de terminaison dans la section de déploiement de Speech Studio ou via l’API Custom Speech.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Que se passe-t-il lorsque les modèles expirent et comment les mettre à jour ?
Ce qui se passe quand un modèle expire et la façon de le mettre à jour dépend de la manière dont il est utilisé.
### <a name="batch-transcription"></a>Transcription Batch
Si un modèle qui est utilisé avec la [transcription par lots](batch-transcription.md) expire, les demandes de transcription échoueront avec une erreur 4xx. Pour empêcher cela, mettez à jour le paramètre `model` dans le JSON envoyé dans le corps de la requête **Créer une transcription** pour pointer vers un modèle de base plus récent ou un modèle personnalisé plus récent. Vous pouvez également supprimer l’entrée `model` du JSON pour toujours utiliser le modèle de base le plus récent.
### <a name="custom-speech-endpoint"></a>Point de terminaison de voix personnalisé
Si un modèle expire et qu’il est utilisé par un [point de terminaison vocal personnalisé](how-to-custom-speech-train-model.md), le service revient automatiquement à l’utilisation du modèle de base le plus récent pour la langue que vous utilisez. Pour mettre à jour un modèle, vous pouvez sélectionner **Déploiement** dans le menu **Custom Speech** en haut de la page, puis cliquer sur le nom du point de terminaison pour afficher ses détails. En haut de la page de détails, vous verrez un bouton **Mettre à jour le modèle**, qui vous permet de mettre à jour en toute transparence le modèle utilisé par ce point de terminaison sans temps d’arrêt. Vous pouvez également effectuer cette modification par programme à l’aide de l’API REST [**Mise à jour du modèle**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel).

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner et déployer un modèle](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
