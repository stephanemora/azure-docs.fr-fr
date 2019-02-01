---
title: Migrer de Custom Speech Service vers les services Speech
titlesuffix: Azure Cognitive Services
description: Le service Custom Speech fait désormais partie du service Speech. Basculez vers le service Speech pour profiter des dernières mises à jour qualité et des fonctionnalités.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 594233b9e345f9578c218b042a64ea167d50addb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211030"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrer de Custom Speech Service vers le service Speech

Utilisez cet article pour migrer vos applications du service Custom Speech vers le service Speech.

Le service Custom Speech fait désormais partie du service Speech. Basculez vers le service Speech pour profiter des dernières mises à jour qualité et des fonctionnalités.

## <a name="migration-for-new-customers"></a>Migration pour les nouveaux clients

Le modèle tarifaire est plus simple, car il utilise un modèle basé sur un tarif horaire pour le service Speech.  

1. Créez une ressource Azure dans chaque région où votre application est disponible. Le nom de la ressource Azure est **Speech**. Vous pouvez utiliser une seule ressource Azure pour les services suivants dans la même région, au lieu de créer des ressources distinctes :

    * Reconnaissance vocale
    * Reconnaissance vocale personnalisée
    * Synthèse vocale
    * Traduction vocale

2. Téléchargez le [SDK Speech](speech-sdk.md).

3. Suivez les guides de démarrage rapide et les exemples du SDK pour utiliser les API appropriées. Si vous utilisez les API REST, vous devez également utiliser les points de terminaison et les clés de ressources corrects.

4. Mettez à jour l’application cliente pour utiliser les API et le service Speech.

> [!NOTE]
> * Si vous avez activé la reconnaissance vocale dans LUIS (Language Understanding Intelligent Service), une seule ressource LUIS dans la même région fonctionne pour LUIS, ainsi que pour tous les services de reconnaissance vocale. Pour plus d’informations, consultez la documentation [Reconnaître les intentions vocales](how-to-recognize-intents-from-speech-csharp.md).
> * La traduction de texte en texte ne fait pas partie du service Speech. Cette fonctionnalité nécessite son propre abonnement de ressources Azure.
 


## <a name="migration-for-existing-customers"></a>Migration pour les clients actuels

Migrez vos clés de ressources existantes vers le service Speech sur le portail du service Speech. Procédez comme suit :

> [!NOTE]
> Les ressources clés peuvent être migrées uniquement dans la même région.

1. Connectez-vous au portail [cris.ai](http://www.cris.ai), puis sélectionnez l’abonnement dans le menu en haut à droite.

2. Sélectionnez **Migrer l’abonnement sélectionné**.

3. Entrez la clé d’abonnement dans la zone de texte, puis sélectionnez **Migrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement le Service Speech](get-started.md).
* En savoir plus sur les concepts de [reconnaissance vocale](./speech-to-text.md).

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation sur le Service Speech et le Kit de développement logiciel (SDK)](speech-sdk.md#get-the-sdk)
