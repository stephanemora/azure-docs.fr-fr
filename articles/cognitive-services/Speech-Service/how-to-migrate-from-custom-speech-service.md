---
title: Migrer de Custom Speech Service vers le service Speech
titlesuffix: Azure Cognitive Services
description: Découvrez comment migrer du service Custom Speech vers le service Speech.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: 656a5df21d1f7dd2e7662dc3a0415581eae02b00
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471235"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrer de Custom Speech Service vers le service Speech

Utilisez cet article pour migrer vos applications du service Custom Speech vers le service Speech.

Le service Custom Speech fait désormais partie du service Speech. Basculez vers le service Speech pour profiter des dernières mises à jour qualité et des fonctionnalités.
 
## <a name="migration-for-new-customers"></a>Migration pour les nouveaux clients

Le modèle tarifaire est plus simple, car il passe à un modèle basé sur un tarif horaire pour le service Speech.   

1. Créez une ressource Azure dans chaque région où votre application est disponible. Le nom de la ressource Azure est **Speech**. Vous pouvez utiliser une seule ressource Azure pour les services suivants dans la même région, au lieu de créer des ressources distinctes :

    * Reconnaissance vocale
    * Reconnaissance vocale personnalisée
    * Synthèse vocale
    * Traduction vocale

2. Téléchargez le [SDK Speech](speech-sdk.md). 

3. Suivez les guides de démarrage rapide et les exemples du SDK pour utiliser les API appropriées. Si vous utilisez les API REST, vous devez également utiliser les points de terminaison et les clés de ressources corrects. 

4. Mettez à jour l’application cliente pour utiliser les API et le service Speech. 

> [!NOTE]
> * LUIS : si vous avez activé la reconnaissance vocale dans LUIS (Language Understanding Intelligent Service), une seule ressource LUIS dans la même région fonctionne pour LUIS, ainsi que pour tous les services de reconnaissance vocale. Consultez la documentation [Reconnaître les intentions vocales](how-to-recognize-intents-from-speech-csharp.md).
> * La traduction de texte en texte ne fait pas partie du service Speech. Elle nécessite son propre abonnement de ressources Azure.
  


## <a name="migration-for-existing-customers"></a>Migration pour les clients actuels

Les clients actuels sont obligés de migrer leurs clés de ressources existantes vers le service Speech sur le portail du service Speech. Procédez comme suit : 

> [!NOTE] 
> Les ressources clés peuvent être migrées uniquement dans la même région. 

1. Connectez-vous au portail [cris.ai](http://www.cris.ai), puis sélectionnez l’abonnement dans le menu en haut à droite. 

2. Sélectionnez **Migrer l’abonnement sélectionné**.

3. Entrez la clé d’abonnement dans la zone de texte, puis sélectionnez **Migrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement le service Speech](get-started.md)
* En savoir plus sur les concepts de [reconnaissance vocale](./speech-to-text.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech ?](overview.md)
* [Documentation sur le SDK et le service Speech](speech-sdk.md#get-the-sdk)