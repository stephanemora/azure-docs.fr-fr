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
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103547950"
---
# <a name="model-and-endpoint-lifecycle"></a>Cycle de vie des modèles et des points de terminaison

Custom Speech utilise des *modèles de base* et des *modèles personnalisés*. Chaque langue comporte un ou plusieurs modèles de base. En règle générale, quand un nouveau modèle vocal est mis à disposition dans le service Speech standard, il est également importé dans le service Custom Speech en tant que nouveau modèle de base. Les modèles sont mis à jour tous les 6 à 12 mois. Les modèles plus anciens deviennent généralement de moins en moins utiles au fil du temps, car le modèle le plus récent est généralement d’une plus grande exactitude.

En revanche, vous créez des modèles personnalisés en choisissant un modèle de base et en l’adaptant avec les données de votre scénario client particulier. Si vous disposez d’un modèle personnalisé répondant à vos besoins, vous pouvez continuer à utiliser pendant une longue période. Toutefois, nous vous recommandons de mettre régulièrement à jour le modèle de base le plus récent et de réeffectuer son apprentissage au fil du temps avec des données supplémentaires. 

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

Voici un exemple de données d’expiration provenant de l’appel de l’API GetModel. La propriété « DEPRECATIONDATES » affiche les éléments suivants : 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
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

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner et déployer un modèle](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)