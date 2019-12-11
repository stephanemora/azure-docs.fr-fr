---
title: Migrer du service Custom Speech vers le service Speech
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
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805924"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Effectuer une migration entre le service Custom Speech et le service Speech

Utilisez cet article pour effectuer la migration de vos applications entre le service Custom Speech et le service Speech.

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

## <a name="migration-for-existing-customers"></a>Migration pour les clients actuels

Effectuez la migration de vos clés de ressources existantes vers le service Speech sur le portail du service Speech. Procédez comme suit :

> [!NOTE]
> Les ressources clés peuvent être migrées uniquement dans la même région.

1. Connectez-vous au portail [cris.ai](https://cris.ai/Home/CustomSpeech), puis sélectionnez l’abonnement dans le menu en haut à droite.

2. Sélectionnez **Migrer l’abonnement sélectionné**.

3. Entrez la clé d’abonnement dans la zone de texte, puis sélectionnez **Migrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement le service Speech](get-started.md).
* En savoir plus sur les concepts de [reconnaissance vocale](./speech-to-text.md).

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le Service Speech](overview.md)
* [Documentation sur le SDK Speech et sur le service Speech](speech-sdk.md#get-the-sdk)
