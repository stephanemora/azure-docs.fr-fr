---
title: Migrer de Custom Speech Service vers le service Speech
titleSuffix: Azure Cognitive Services
description: Le service Custom Speech fait désormais partie du service Speech. Basculez vers le service Speech pour profiter des dernières mises à jour qualité et des fonctionnalités.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562770"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrer de Custom Speech Service vers le service Speech

Utilisez cet article pour migrer vos applications du service Custom Speech vers le service Speech.

Le service Custom Speech fait désormais partie du service Speech. Passez aux services Speech pour bénéficier des dernières mises à jour qualité et mises à jour des fonctionnalités.

## <a name="migration-for-new-customers"></a>Migration pour les nouveaux clients

Le modèle tarifaire est plus simple, car il utilise un modèle basé sur un tarif horaire pour le service Speech.  

1. Créez une ressource Azure dans chaque région où votre application est disponible. Le nom de la ressource Azure est **Speech**. Vous pouvez utiliser une seule ressource Azure pour les services suivants dans la même région, au lieu de créer des ressources distinctes :

    * Reconnaissance vocale
    * Reconnaissance vocale personnalisée
    * Synthèse vocale
    * Traduction vocale

2. Téléchargez le [SDK Speech](speech-sdk.md).

3. Suivez les guides de démarrage rapide et les exemples du SDK pour utiliser les API appropriées. Si vous utilisez les API REST, vous devez également utiliser les points de terminaison et les clés de ressources corrects.

4. Mettez à jour l’application cliente de façon à utiliser les API et les services Speech.

## <a name="migration-for-existing-customers"></a>Migration pour les clients actuels

Migrez vos clés de ressources existantes vers les services Speech sur le portail des services Speech. Procédez comme suit :

> [!NOTE]
> Les ressources clés peuvent être migrées uniquement dans la même région.

1. Connectez-vous au portail [cris.ai](https://cris.ai/Home/CustomSpeech), puis sélectionnez l’abonnement dans le menu en haut à droite.

2. Sélectionnez **Migrer l’abonnement sélectionné**.

3. Entrez la clé d’abonnement dans la zone de texte, puis sélectionnez **Migrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement les services Speech](get-started.md).
* En savoir plus sur les concepts de [reconnaissance vocale](./speech-to-text.md).

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation du kit de développement logiciel (SDK) et des services Speech](speech-sdk.md#get-the-sdk)
