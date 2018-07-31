---
title: Basculement manuel d’un Azure IoT hub | Microsoft Docs
description: Montrer comment effectuer un basculement manuel pour un Azure IoT hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f0e8bf922f142b795dd1a2ded4b3ec265c43481a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249992"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Didacticiel : Effectuer un basculement manuel pour un IoT hub (préversion publique)

Basculement manuel est une fonctionnalité du service IoT Hub qui permet aux clients le[basculement](https://en.wikipedia.org/wiki/Failover) des opérations de leur hub à partir d’une région primaire vers la région Azure associée géographiquement correspondante. Basculement manuel est possible en cas de sinistre régional ou une panne de service étendue. Vous pouvez également effectuer un basculement planifié pour tester vos capacités de récupération d’urgence, mais nous vous recommandons d’utiliser un IoT hub de test plutôt que celui en cours d’exécution en production. La fonctionnalité de basculement manuel est proposée aux clients sans coût supplémentaire.

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créez un hub IoT, à l’aide du portail Azure. 
> * Effectuer un basculement. 
> * Consultez le hub en cours d’exécution dans l’emplacement secondaire.
> * Effectuer une restauration automatique pour ramener les opérations d’IoT hub à l’emplacement principal. 
> * Confirmer que le hub s’exécute correctement dans l’emplacement approprié.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Cliquez sur **+ Créer une ressource** et sélectionnez **Internet des objets**, puis **IoT Hub**.

   ![Capture d’écran montrant la création d’un IoT hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Sélectionnez l’onglet **Fonctions de base**. Complétez les champs suivants.

    **Abonnement** : sélectionnez l’abonnement Azure que vous souhaitez utiliser.

    **Groupe de ressources** : cliquez sur **Créer nouveau**  et spécifiez **ManIFailRG** pour le nom du groupe de ressources.

    **Région**: sélectionnez une région proche de vous fait partie de la préversion. Ce didacticiel utilise`westus2`. Un basculement ne peut être effectué entre des régions Azure géographiquement associées. La région géographiquement associée à westus2 est WestCentralUS.
    
   > [!NOTE]
   > Le basculement manuel est actuellement en préversion publique et *pas* disponible dans les régions Azure suivantes : USA Est, USA Ouest, Europe Nord, Europe Ouest, Brésil Sud et USA Centre Sud.

   **Nom de IoT Hub** : spécifiez un nom pour votre IoT Hub. Le nom du hub doit être globalement unique. 

   ![Capture d’écran montrant le volet d’informations de base pour la création d’un IoT Hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Cliquez sur **Revoir + créer**. (Il utilise les valeurs par défaut pour la taille et échelle.) 

4. Passez en revue les informations, puis cliquez sur **Créer** pour créer l’IoT hub. 

   ![Capture d’écran montrant l’étape finale pour la création d’un IoT Hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Effectuer un basculement manuel

Notez qu’il existe une limite de deux basculements et deux restaurations par jour pour un IoT Hub.

1. Cliquez sur **Groupes de ressources** et sélectionnez le groupe de ressources **ManlFailRG**. Dans la liste des ressources, cliquez sur votre IoT Hub. 

2. Sous **Résilience** dans le volet de IoT Hub, cliquez sur **Basculement manuel (préversion)**. Notez que si votre hub n’est pas configuré dans une région valide, l’option de basculement manuel va être désactivée.

   ![Capture d’écran montrant le volet de propriétés de IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

3. Dans le volet de Basculement manuel, vous voyez **l’Emplacement principal de l’IoT Hub**  et **l’emplacement secondaire de l’IoT Hub** . L’emplacement principal est initialement défini à l’emplacement spécifié lorsque vous avez créé l’IoT hub et indique toujours l’emplacement dans lequel le hub est actuellement actif. L’emplacement secondaire est le standard [région Azure géographiquement associée ](../best-practices-availability-paired-regions.md) qui est associé à l’emplacement principal. Vous ne pouvez pas modifier les valeurs de l’emplacement. Pour ce didacticiel, l’emplacement principal est `westus2` et l’emplacement secondaire est `WestCentralUS`.

   ![Capture d’écran montrant le volet de basculement manuel](./media/tutorial-manual-failover/trigger-failover-02.png)

3. En haut du volet de Basculement manuel, cliquez sur **Lancer le basculement**. Vous voyez le volet **Confirmer le basculement manuel**. Complétez le nom de votre IoT hub pour vérifier que c’est bien celui que vous souhaitez basculer. Ensuite, pour lancer le basculement, cliquez sur **OK**.

   La quantité de temps nécessaire pour effectuer le basculement manuel est proportionnelle au nombre d’appareils qui sont inscrits pour votre hub. Par exemple, si vous avez 100 000 appareils,cela peut prendre 15 minutes, mais si vous avez cinq millions d’appareils, cela peut prendre une heure ou plus.

4. Dans le volet**Confirmer le basculement manuel**, complétez le nom de votre IoT hub pour vérifier que c’est bien celui que vous souhaitez basculer. Ensuite, pour lancer le basculement, cliquez sur OK. 

   ![Capture d’écran montrant le volet de basculement manuel](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Lorsque le processus de basculement manuel est en cours d’exécution, il existe une bannière dans le volet Basculement manuel qui vous indique qu’un basculement manuel est en cours d’exécution. 

   ![Capture d’écran montrant le volet de Basculement manuel en cours](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Si vous fermez le volet de IoT Hub et que vous l’ouvrez à nouveau en cliquant dessus dans le volet Groupe de ressources, vous voyez une bannière qui vous indique que le hub n'est pas actif. 

   ![Capture d’écran montrant que l’IoT Hub est inactif](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Une fois terminé, les régions primaires et secondaires sur la page de Basculement manuel sont retournées et le hub est à nouveau actif. Pour cet exemple, l’emplacement principal est maintenant `WestCentralUS` et l’emplacement secondaire est maintenant `westus2`. 

   ![Capture d’écran montrant que le basculement est terminé](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Effectuer une restauration automatique 

Une fois que vous avez effectué un basculement manuel, vous pouvez basculer des opérations de hub vers la région primaire d’origine, il s’agit d’une restauration automatique. Si vous venez d’exécuter un basculement, vous devez patienter environ une heure avant de pouvoir déposer une requête de restauration automatique. Si vous essayez d’effectuer la restauration automatique dans un délai plus court, un message d’erreur s’affichera.

Une restauration automatique est effectuée à l’instar d’un basculement manuel. Voici les étapes : 

1. Pour effectuer une restauration automatique, retournez dans le volet de Iot Hub pour votre Iot Hub.

2. Sous **Résilience** dans le volet de IoT Hub, cliquez sur **Basculement manuel (préversion)**. 

3. En haut du volet de Basculement manuel, cliquez sur **Lancer le basculement**. Vous voyez le volet **Confirmer le basculement manuel**. 

4. Dans le volet**Confirmer le basculement manuel**, complétez le nom de votre IoT hub pour vérifier que c’est bien celui que vous souhaitez restaurer. Pour ensuite lancer la restauration automatique, cliquez sur OK. 

   ![Capture d’écran de la requête de restauration manuelle](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Les bannières sont affichées comme expliqué dans la section [Effectuer un basculement](#perform-a-failover). Une fois la restauration automatique terminée, il affiche à nouveau `westus2` comme emplacement principal et `WestCentralUS` comme emplacement secondaire,comme défini à l’origine.

## <a name="clean-up-resources"></a>Supprimer les ressources 

Pour supprimer toutes les ressources que vous avez créées, supprimez le groupe de ressources. Cette opération supprime toutes les ressources contenues dans le groupe. Dans le cas présent, l’Iot hub et le groupe de ressources sont supprimés. 

1. Cliquez sur **Groupes de ressources**. 

2. Recherchez et sélectionnez le groupe de ressource **ManlFailRG**. Cliquez sur le compte pour l’ouvrir. 

3. Cliquez sur **Supprimer le groupe de ressources**. Lorsque vous y êtes invité, entrez le nom du groupe de ressources et cliquez sur **Supprimer** pour confirmer. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment configurer et effectuer un basculement manuel, et comment déposer une requête de restauration automatique en effectuant les tâches suivantes :

> [!div class="checklist"]
> * Créez un IoT hub, à l’aide du portail Azure. 
> * Effectuer un basculement. 
> * Consultez le hub en cours d’exécution dans l’emplacement secondaire.
> * Effectuer une restauration automatique pour ramener les opérations d’IoT hub à l’emplacement principal. 
> * Confirmer que le hub s’exécute correctement dans l’emplacement approprié.

Passez au didacticiel suivant pour découvrir comment gérer l’état d’un appareil IoT. 

> [!div class="nextstepaction"]
[Gérer l’état d’un appareil IoT](tutorial-device-twins.md)