---
title: Comment utiliser le portail Azure pour appeler des méthodes directes
description: Cet article présente une vue d’ensemble de l’utilisation du portail Azure pour appeler des méthodes directes.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279285"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Comment utiliser le portail Azure pour appeler des méthodes directes

IoT Hub vous donne la possibilité d’appeler des [méthodes directes](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) sur des périphériques à partir du cloud. Le module Live Video Analytics (LVA) sur IoT Edge expose plusieurs [méthodes directes](/azure/media-services/live-video-analytics-edge/direct-methods) permettant de définir, déployer et instancier différents flux de travail pour l’analyse de vidéos en direct.

Dans cet article, vous allez apprendre à effectuer des appels de méthode directe sur Live Video Analytics pour un module IoT Edge via le portail Azure.

## <a name="prerequisites"></a>Prérequis

* Vous disposez du module Live Video Analytics sur IoT Edge en cours d’exécution sur votre périphérique, utilisant l’une des méthodes décrites dans [Démarrage rapide : Live Video Analytics sur IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) ou le [portail](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device).

* Vous comprenez [Live Video Analytics](/azure/media-services/live-video-analytics-edge/overview) et le [concept de graphe multimédia](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Appel de méthodes directes via le portail Azure

Chacune des [méthodes directes](/azure/media-services/live-video-analytics-edge/direct-methods) exposées par le module LVA peut être appelée via le portail Azure. Les étapes ci-dessous fournissent les détails d’une méthode directe. Vous pouvez appeler d’autres méthodes directes en procédant de manière similaire. Toutefois, chaque méthode directe requiert un corps JSON spécifique.

Utilisez l’appel de la méthode `GraphTopologyList` pour récupérer la liste de toutes les topologies de graphe actuellement déployées sur le module Live Video Analytics sur IoT Edge. Pour appeler cette méthode directe, procédez comme suit :

1. Connectez-vous au portail Azure.
1. Recherchez le groupe de ressources approprié à partir de la page d’accueil de votre portail pour localiser votre IoT Hub ou, si vous connaissez votre IoT Hub, sélectionnez-le.
    ![groupe de ressources dans la page d’accueil du portail](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Une fois sur la page IoT Hub, sélectionnez IoT Edge sous Gestion automatique des appareils pour répertorier les différents ID d’appareil. Sélectionnez l’ID d’appareil approprié pour répertorier les modules s’exécutant sur l’appareil.
    ![page iot hub](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Sélectionnez le module Live Video Analytics sur IoT Edge pour afficher sa page de configuration.<br><br>
    ![Sélectionner le module Live Video Analytics sur IoT Edge pour afficher sa page de configuration](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Cliquez sur l’option de menu Méthode directe. <br><br>
    ![Cliquer sur l’option de menu Méthode directe](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Il se peut que vous deviez ajouter une valeur dans les sections Chaîne de connexion, comme vous pouvez le voir sur la page actuelle. Vous n’avez pas besoin de masquer ou de modifier quoi que ce soit dans la section Nom du paramètre. Vous pouvez laisser ces informations publiques.

1. Tapez *GraphTopologyList* dans le champ **Nom de la méthode**.
1. Copiez et collez le code JSON ci-dessous dans le champ **Charge utile**.
    ```json
    {
    "@apiVersion":
    }
    ```
1. Sélectionnez le bouton **Appeler la méthode** en haut de la page.<br><br>
    ![bouton appeler la méthode](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Vous devriez voir un message d’état 200 dans la zone **Résultat**.<br><br>
    ![expiration de la connexion](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Réponses

| Condition             | Code d’état | Code d’erreur détaillé |
|-----------------------|-------------|---------------------|
| Succès               | 200         | N/A                 |
| Erreurs utilisateur générales   | Plage 400   |                     |
| Erreurs de serveur générales | Plage 500   |                     |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez d’autres méthodes directes dans la page [Méthodes directes](/azure/media-services/live-video-analytics-edge/direct-methods).

> [!NOTE]
> Une instance de graphe instancie une topologie spécifique. Vérifiez donc que vous disposez de la topologie appropriée définie avant de créer une instance de graphe.

Le [Démarrage rapide : Détecter des événements d’émission de mouvement](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) est une bonne référence pour comprendre la séquence exacte des appels de méthode directe à effectuer.
