---
title: Comment utiliser le portail Azure pour appeler des méthodes directes pour Azure Video Analyzer
description: Cet article explique comment utiliser le portail Azure en vue d’appeler des méthodes directes Azure Video Analyzer.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 944819e70d94b365a8f7c3e885ffc0175d84eca7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603865"
---
# <a name="use-azure-portal-to-invoke-direct-methods-for-azure-video-analyzer"></a>Utiliser le portail Azure en vue d’appeler des méthodes directes pour Azure Video Analyzer

IoT Hub vous donne la possibilité d’appeler des [méthodes directes](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) sur des périphériques à partir du cloud. Le module Azure Video Analyzer (Video Analyzer) expose plusieurs [méthodes directes](./direct-methods.md) permettant de définir, déployer et activer différents pipelines pour l’analyse de vidéos en direct.

Dans cet article, vous allez apprendre à effectuer des appels de méthode directe vers le module Video Analyzer via le portail Azure.

## <a name="prerequisites"></a>Prérequis

* Le module Video Analyzer est exécuté sur un appareil de périphérie, soit à l’aide des méthodes décrites dans [Démarrage rapide : Video Analyzer](get-started-detect-motion-emit-events.md), soit à l’aide du [portail](./deploy-iot-edge-device.md).
* Vous connaissez [Video Analyzer](overview.md) et vous comprenez le [concept de pipeline](pipeline.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Appel de méthodes directes via le portail Azure

Chacune des [méthodes directes](./direct-methods.md) exposées par le module Video Analyzer peut être appelée via le portail Azure. Les étapes ci-dessous fournissent les détails d’une méthode directe. Vous pouvez appeler d’autres méthodes directes en procédant de manière similaire. Toutefois, chaque méthode directe nécessite une charge utile spécifique.

Utilisez l’appel de la méthode `livePipelineList` pour récupérer la liste de toutes les topologies de pipeline actuellement déployées sur le module Video Analyzer. Pour appeler cette méthode directe, procédez comme suit :

1. Connectez-vous au portail Azure.
1. Recherchez le groupe de ressources approprié à partir de la page d’accueil de votre portail pour localiser votre IoT Hub ou, si vous connaissez votre IoT Hub, sélectionnez-le.
    ![groupe de ressources dans la page d’accueil du portail](./media/use-azure-portal-to-invoke-direct-methods/portal-rg-home.png)
1. Une fois sur la page IoT Hub, sélectionnez IoT Edge sous Gestion automatique des appareils pour répertorier les différents ID d’appareil. Sélectionnez l’ID d’appareil approprié pour répertorier les modules s’exécutant sur l’appareil.
    ![page iot hub](./media/use-azure-portal-to-invoke-direct-methods/iot-hub-page.png)
1. Sélectionnez le module Video Analyzer pour afficher sa page de configuration.<br><br>
    ![Sélectionner le module Video Analyzer pour afficher sa page de configuration](./media/use-azure-portal-to-invoke-direct-methods/modules.png)
1. Cliquez sur l’option de menu Méthode directe. <br><br>
    ![Cliquer sur l’option de menu Méthode directe](./media/use-azure-portal-to-invoke-direct-methods/module-details.png)
    > [!NOTE]
    > Il se peut que vous deviez ajouter une valeur dans les sections Chaîne de connexion, comme vous pouvez le voir sur la page actuelle. Vous n’avez pas besoin de masquer ou de modifier quoi que ce soit dans la section Nom du paramètre. Vous pouvez laisser ces informations publiques.

1. Tapez *livePipelineList* dans le champ **Nom de la méthode**.
1. Copiez et collez le code JSON ci-dessous dans le champ **Charge utile**.
    ```json
    {
    "@apiVersion": "1.0"
    }
    ```
1. Sélectionnez le bouton **Appeler la méthode** en haut de la page.<br><br>
    ![bouton appeler la méthode](./media/use-azure-portal-to-invoke-direct-methods/direct-method.png)
1. Vous devriez voir un message d’état 200 dans la zone **Résultat**.<br><br>
    ![expiration de la connexion](./media/use-azure-portal-to-invoke-direct-methods/connection-timeout.png)

## <a name="responses"></a>Réponses

| Condition             | Code d’état | Code d’erreur détaillé |
|-----------------------|-------------|---------------------|
| Succès               | 200         | N/A                 |
| Erreurs utilisateur générales   | Plage 400   |                     |
| Erreurs de serveur générales | Plage 500   |                     |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez d’autres méthodes directes dans la page [Méthodes directes](./direct-methods.md).

> [!NOTE]
> Un pipeline active une topologie spécifique. Vérifiez donc que vous disposez de la topologie appropriée avant d’activer un pipeline.

Le [Démarrage rapide : Détecter des événements d’émission de mouvement](detect-motion-emit-events-quickstart.md) est une bonne référence pour comprendre la séquence exacte des appels de méthode directe à effectuer.
