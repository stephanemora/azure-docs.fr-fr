---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99524922"
---
Pour configurer le calcul sur votre appareil Azure Stack Edge Pro, vous allez créer une ressource IoT Hub via le portail Azure.

1. Dans le portail Azure de votre ressource Azure Stack Edge, accédez à **Vue d’ensemble**, puis sélectionnez **IoT Edge**.

   ![Bien démarrer avec le calcul](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. Dans **Activer le service IoT Edge**, sélectionnez **Ajouter**.

   ![Configurer le calcul](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Dans le panneau **Configurer le computing en périphérie**, entrez les informations suivantes :
   
    |Champ  |Valeur  |
    |---------|---------|
    |Abonnement     |Sélectionnez un abonnement pour votre ressource IoT Hub. Vous pouvez choisir le même abonnement que celui utilisé par la ressource Azure Stack Edge.         |
    |Resource group     |Sélectionnez un groupe de ressources pour votre ressource IoT Hub. Vous pouvez choisir le même groupe de ressources que celui utilisé par la ressource Azure Stack Edge.         |
    |IoT Hub     | Choisissez **Nouveau** ou **Existant**. <br> Par défaut, un niveau Standard (S1) est utilisé pour créer une ressource IoT. Pour utiliser une ressource IoT de niveau gratuit, créez-en une, puis sélectionnez-la. <br> Dans chaque cas, la ressource IoT Hub utilise les mêmes abonnement et groupe de ressources que la ressource Azure Stack Edge.     |
    |Nom     |Acceptez le nom par défaut ou entrez un autre nom pour votre ressource IoT Hub.         |

   ![Bien démarrer avec le calcul 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Lorsque vous avez terminé de configurer les paramètres, sélectionnez **Vérifier + créer**. Vérifiez les paramètres de votre ressource IoT Hub, puis sélectionnez **Créer**.

   La création d’une ressource IoT Hub prend plusieurs minutes. Une fois la ressource créée, la **Vue d’ensemble**  indique que le service IoT Edge est en cours d’exécution.

   ![Bien démarrer avec le calcul 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Pour vérifier que le rôle de calcul Edge a été configuré, accédez à **IoT Edge > Propriétés**.

   ![Bien démarrer avec le calcul 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Quand le rôle de calcul Edge est configuré sur l’appareil Edge, il crée deux appareils : un appareil IoT et un appareil IoT Edge. Ces deux appareils peuvent être visualisés dans la ressource IoT Hub. Un runtime IoT Edge est également exécuté sur cet appareil IoT Edge. À ce stade, seule la plateforme Linux est disponible pour votre appareil IoT Edge.

La configuration du calcul en arrière-plan peut prendre entre 20 et 30 minutes, le temps que des machines virtuelles et un cluster Kubernetes soient créés.

Une fois le calcul correctement configuré sur le portail Azure, un cluster Kubernetes et un utilisateur par défaut associé à l’espace de noms IoT (espace de noms système contrôlé par Azure Stack Edge Pro) sont disponibles.
