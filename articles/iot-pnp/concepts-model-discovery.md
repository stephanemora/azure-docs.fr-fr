---
title: Implémenter la découverte de modèles IoT Plug-and-Play (préversion) | Microsoft Docs
description: Avec cet article destiné aux développeurs de solutions, vous allez découvrir comment implémenter la découverte de modèles IoT Plug-and-Play dans votre solution.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 66da0321930ac38217a336380c9889963a433e67
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531358"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implémenter la découverte de modèles IoT Plug-and-Play (préversion) dans une solution IoT

Cet article destiné aux développeurs de solutions vous explique comment implémenter la découverte de modèles IoT Plug-and-Play (préversion) dans une solution IoT.  La découverte de modèles IoT Plug-and-Play désigne la façon dont les appareils IoT Plug-and-Play identifient leurs modèles et interfaces de capacité pris en charge et dont une solution IoT récupère ces modèles et interfaces.

Il existe deux grandes catégories de solutions IoT : les solutions spécialisées qui fonctionnent avec un ensemble connu d’appareils IoT Plug-and-Play et les solutions basées sur des modèles qui fonctionnent avec n’importe quel appareil IoT Plug-and-Play.

Cet article de présentation explique comment implémenter la détection de modèles dans les deux types de solutions.

## <a name="model-discovery"></a>Découverte de modèles

Quand un appareil IoT Plug-and-Play se connecte pour la première fois à votre hub IoT, il envoie un message de télémétrie Informations sur le modèle. Ce message inclut les ID des interfaces implémentées par l’appareil. Pour que votre solution puisse fonctionner avec l’appareil, elle doit résoudre ces ID et récupérer les définitions pour chaque interface.

Voici les étapes appliquées par un appareil IoT Plug-and-Play quand il utilise le service Device Provisioning (DPS) pour se connecter à un hub :

1. Quand l’appareil est activé, il se connecte au point de terminaison global du service DPS et s’authentifie à l’aide de l’une des méthodes autorisées.
1. Le service DPS authentifie ensuite l’appareil et recherche la règle lui indiquant à quel hub IoT l’appareil doit être attribué. Le service DPS inscrit alors l’appareil auprès de ce hub.
1. Le service DPS retourne une chaîne de connexion du hub IoT à l’appareil.
1. L’appareil envoie alors un message de télémétrie de découverte à votre hub IoT. Le message de télémétrie de découverte contient les ID des interfaces implémentées par l’appareil.
1. L’appareil IoT Plug-and-Play est maintenant prêt à fonctionner avec une solution qui utilise votre hub IoT.

Si l’appareil se connecte directement à votre hub IoT, il utilise pour cela une chaîne de connexion incorporée dans le code de l’appareil. L’appareil envoie alors un message de télémétrie de découverte à votre hub IoT.

Pour en savoir plus sur le message de télémétrie Informations sur le modèle, consultez la documentation sur l’interface [ModelInformation](concepts-common-interfaces.md).

### <a name="purpose-built-iot-solutions"></a>Solutions IoT spécialisées

Une solution IoT spécialisée fonctionne avec un ensemble connu d’interfaces et de modèles de capacité d’appareil IoT Plug-and-Play.

Vous disposerez à l’avance des interfaces et modèles de capacité pour les appareils qui se connecteront à votre solution. Suivez les étapes ci-après pour préparer la solution :

1. Stockez les fichiers JSON d’interface dans Azure, à un emplacement où votre solution peut les lire.
1. Écrivez la logique dans votre solution IoT en fonction de l’interface et des modèles de capacité IoT Plug-and-Play attendus.
1. Abonnez-vous aux notifications du hub IoT utilisé par votre solution.

Quand vous recevez une notification de nouvelle connexion d’appareil, procédez comme suit :

1. Lisez le message de télémétrie de découverte pour récupérer les ID du modèle de capacité et des interfaces implémentées par l’appareil.
1. Comparez l’ID du modèle de capacité aux ID des modèles que vous avez stockés à l’avance.
1. Vous savez à présent quel type d’appareil s’est connecté. Utilisez la logique que vous avez écrite précédemment pour permettre aux utilisateurs d’interagir correctement avec l’appareil.

### <a name="model-driven-solutions"></a>Solutions basées sur des modèles

Une solution IoT basée sur un modèle peut fonctionner avec n’importe quel appareil IoT Plug-and-Play. La génération d’une solution IoT basée sur un modèle est plus complexe. Elle s’avère cependant avantageuse dans la mesure où votre solution pourra fonctionner avec tout appareil ajouté à l’avenir.

Pour générer une solution IoT basée sur un modèle, vous devez créer une logique s’appuyant sur les primitives de l’interface IoT Plug-and-Play : télémétrie, propriétés et commandes. Pour représenter un appareil, la logique de votre solution IoT combine plusieurs capacités de télémétrie, de propriété et de commande.

Vous devez également abonner votre solution aux notifications du hub IoT qu’elle utilise.

Quand la solution reçoit une notification de nouvelle connexion d’appareil, procédez comme suit :

1. Lisez le message de télémétrie de découverte pour récupérer les ID du modèle de capacité et des interfaces implémentées par l’appareil.
1. Pour chaque ID, lisez le fichier JSON complet pour rechercher les capacités de l’appareil.
1. Vérifiez la présence de chaque interface dans tous les caches que vous avez générés pour stocker les fichiers JSON récupérés précédemment par votre solution.
1. Vérifiez ensuite si une interface avec cet ID est présente dans le référentiel de modèles public. Pour plus d’informations, consultez [Référentiel de modèles public](howto-manage-models.md).
1. Si l’interface n’est pas présente dans le référentiel de modèles public, recherchez-la dans tous les référentiels de modèles d’entreprise connus de votre solution. Vous avez besoin d’une chaîne de connexion pour accéder à un référentiel de modèles d’entreprise. Pour plus d’informations, consultez [Référentiel de modèles d’entreprise](howto-manage-models.md).
1. Si vous ne trouvez pas toutes les interfaces dans le référentiel de modèles public ni dans un référentiel de modèles d’entreprise, vous pouvez vérifier si l’appareil peut fournir la définition d’interface. Un appareil peut implémenter l’interface standard [ModelDefinition](concepts-common-interfaces.md) pour publier des informations sur la façon de récupérer des fichiers d’interface à l’aide d’une commande.
1. Si vous avez trouvé des fichiers JSON pour chaque interface implémentée par l’appareil, vous pouvez énumérer les capacités de l’appareil. Utilisez la logique que vous avez écrite précédemment pour permettre aux utilisateurs d’interagir avec l’appareil.
1. Vous pouvez appeler l’API Digital Twins à tout moment pour récupérer l’ID de modèle de capacité et les ID d’interface pour l’appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur la découverte de modèles dans une solution IoT, apprenez-en davantage sur la [plateforme IoT Azure](overview-iot-plug-and-play.md) pour tirer parti d’autres fonctionnalités de votre solution.
