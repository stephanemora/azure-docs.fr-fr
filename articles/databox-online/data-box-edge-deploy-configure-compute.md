---
title: Transformer des données avec Azure Data Box Edge | Microsoft Docs
description: Découvrez comment configurer le rôle de calcul sur Data Box Edge et l’utiliser pour transformer des données avant de les envoyer à Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: e970f7cc0d4c4620f2da69286be36d0c22e0d747
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260361"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Didacticiel : transformer des données avec Azure Data Box Edge (préversion)

Ce didacticiel explique comment configurer le rôle de calcul sur Data Box Edge. Une fois que le rôle de calcul est configuré, Data Box Edge peut transformer des données avant de les envoyer à Azure.

Cette procédure peut prendre environ 30 à 45 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une ressource IoT Hub
> * Configurer le rôle de calcul
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution.
 
## <a name="prerequisites"></a>Prérequis

Avant d’ajouter le calcul sur votre appareil Data Box Edge, assurez-vous que :

* Votre appareil Data Box Edge est activé comme indiqué dans [Connect and activate your Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) (Connecter et activer votre appareil Azure Data Box Edge).


## <a name="create-an-iot-hub-resource"></a>Créer une ressource IoT Hub

Avant de configurer un rôle de calcul sur Data Box Edge, vous devez créer une ressource IoT Hub.

Pour obtenir des instructions détaillées, consultez [Créer un IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Utilisez les mêmes abonnement et groupe de ressources que ceux que vous avez utilisés pour votre ressource Data Box Edge.

![Créer une ressource IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Lorsque le rôle de calcul Edge n’est pas configuré, notez que :

- La ressource IoT Hub ne comporte aucun appareil IoT ni IoT Edge.
- Vous ne pouvez pas créer de partages locaux Edge. Lorsque vous ajoutez un partage, l’option permettant de créer un partage local pour le calcul Edge n’est pas activée.


## <a name="set-up-compute-role"></a>Configurer le rôle de calcul

Lorsque le rôle de calcul Edge est configuré sur l’appareil Edge, il crée deux appareils : un appareil IoT et un appareil IoT Edge. Ces deux appareils peuvent être visualisés dans la ressource IoT Hub.

Pour configurer le rôle de calcul sur l’appareil, procédez comme suit.

1. Accédez à la ressource Data Box Edge, à **Vue d’ensemble**, puis cliquez sur **Set up compute role** (Configurer le rôle de calcul). 

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Vous pouvez également accéder à **Modules**, puis cliquer sur **Configure compute** (Configurer le calcul).

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. Dans la liste déroulante, sélectionnez la **ressource IoT Hub** que vous avez créée à l’étape précédente. Seule la plateforme Linux est disponible à ce stade pour votre appareil IoT Edge. Cliquez sur **Créer**.

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. La création du rôle de calcul ne prend que quelques minutes. En raison d’un bogue dans cette version, même lorsque le rôle de calcul est créé, l’écran n’est pas actualisé. Accédez à **Modules**, vous pouvez alors voir que le calcul Edge est configuré.  

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Accédez une nouvelle fois à **Vue d’ensemble**. L’écran est maintenant à jour et indique que le rôle de calcul est configuré.

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Accédez à la ressource IoT Hub que vous avez utilisée lors de la création du rôle de calcul Edge. Accédez à **IoT devices** (Appareils IoT). Vous pouvez voir qu’un appareil IoT est maintenant activé. 

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Accédez à **IoT Edge**. Vous voyez qu’un appareil IoT Edge est également activé.

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Sélectionnez et cliquez sur l’appareil IoT Edge. Un agent Edge est en cours d’exécution sur cet appareil IoT Edge. 

    ![Configurer le rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Il n’existe, toutefois, aucun module personnalisé sur cet appareil Edge. Vous pouvez maintenant ajouter un module personnalisé à cet appareil. Pour savoir comment créer un module personnalisé, accédez à [Develop a C# module for your Data Box Edge](data-box-edge-create-iot-edge-module.md) (Développer un module C# pour Data Box Edge).


## <a name="add-a-custom-module"></a>Ajouter un module personnalisé

Dans cette section, vous allez ajouter un module personnalisé à l’appareil IoT Edge que vous avez créé dans [Develop a C# module for your Data Box Edge](data-box-edge-create-iot-edge-module.md) (Développer un module C# pour Data Box Edge). 

Cette procédure utilise un exemple dans lequel le module personnalisé utilisé place des fichiers d’un partage local sur l’appareil Edge, puis les déplace vers un partage cloud sur l’appareil. Le partage cloud envoie alors les fichiers vers le compte de stockage Azure associé au partage cloud. 

1. La première étape consiste à ajouter un partage local sur l’appareil Edge. Dans votre ressource Data Box Edge, accédez à **Partages**. Cliquez sur **+ Ajouter un partage**. Donnez un nom au partage et sélectionnez son type. Pour créer un partage local, cochez l’option **Configure as Edge local share** (Configurer en tant que partage local Edge). Sélectionnez un **utilisateur existant** ou **créez-en un nouveau**. Cliquez sur **Créer**.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Si vous avez créé un partage NFS local, utilisez l’option de commande rsync suivante pour copier les fichiers sur le partage :

    `rsync --inplace <source file path> < destination file path>`

     Pour plus d’informations sur la commande rsync, consultez la [documentation de Rsync](https://www.computerhope.com/unix/rsync.htm). 

 
2. Une fois que le partage local est créé et que vous avez reçu une notification de création réussie (la liste de partages peut être mise à jour avant, mais vous devez attendre la fin de la création du partage), accédez à la liste des partages. 

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Sélectionnez et cliquez sur le nouveau partage local créé et affichez ses propriétés. Copiez et enregistrez le **point de montage local des modules Edge** correspondant à ce partage.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Accédez à un partage cloud existant créé sur votre appareil Data Box Edge. Copiez et enregistrez une nouvelle fois le point de montage local des modules calcul Edge pour ce partage cloud. Ces points de montage locaux seront utilisés lors du déploiement du module.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Pour ajouter un module personnalisé à l’appareil IoT Edge, accédez à votre ressource IoT Hub puis à **Appareil IoT Edge**. Sélectionnez et cliquez sur l’appareil. Dans **Device details** (Détails de l’appareil), dans la barre de commandes située en haut, cliquez sur **Set Modules** (Définir les modules). 

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. Sous **Add Modules** (Ajouter des modules), procédez comme suit :

    1. Indiquez le **nom**, l’**adresse**, le **nom d’utilisateur** et le **mot de passe** dans les **paramètres du registre de conteneurs** du module personnalisé. Le nom, l’adresse et les informations d’identification indiquées sont utilisées pour récupérer des modules avec une URL correspondante. Pour déployer ce module, sous **Deployment modules** (Modules de déploiement), sélectionnez **IoT Edge module** (Module IoT Edge). Ce module IoT Edge est un conteneur Docker que vous pouvez déployer sur un appareil IoT Edge associé à votre appareil Data Box Edge.

        ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Spécifiez les paramètres du module personnalisé IoT Edge. Fournissez le **nom** de votre module et l’**URI d’image** de l’image de conteneur associée. 
    
        ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. Dans les **options de création de conteneur**, indiquez les points de montage locaux des modules Edge copiés dans les étapes précédentes pour le partage cloud et local (il est important d’utiliser ces chemins d’accès plutôt que d’en créer de nouveaux). Les points de montage locaux sont mappés aux variables **InputFolderPath** et **OutputFolderPath** que vous avez spécifiées dans le module lorsque vous avez [mis à jour le module avec du code personnalisé](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
        Vous pouvez copier et coller l’exemple ci-dessous dans vos **options de création de conteneur** : 
        
        ```
        {
         "HostConfig": {
          "Binds": [
           "/home/hcsshares/mysmblocalshare:/home/LocalShare",
           "/home/hcsshares/mysmbshare1:/home/CloudShare"
           ]
         }
        }
        ```

        Indiquez également ici des variables d’environnement pour votre module.

        ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. Si nécessaire, **configurez les paramètres avancés du runtime Edge**, puis cliquez sur **Suivant**.

        ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  Sous **Specify routes** (Spécifier des itinéraires), définissez des itinéraires entre les modules. Dans ce cas, indiquez le nom du partage local qui enverra des données vers le partage cloud. Cliquez sur **Suivant**.

    Vous pouvez remplacer l’itinéraire par la chaîne suivante :       "route": "FROM /* WHERE topic = ’mysmblocalshare’ INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  Sous **Review deployment** (Vérifier le déploiement), passez en revue tous les paramètres et, si vous êtes satisfait, **soumettez** le module pour déploiement.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Le déploiement du module démarre, comme indiqué par **IoT Edge Custom module** (Module personnalisé IoT Edge) sous **Modules**.

![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Vérifier la transformation des données et transférer

L’étape finale consiste à vérifier que le module est connecté et qu’il s’exécute comme prévu. Procédez comme suit pour vérifier que le module est en cours d’exécution.

1. L’état du runtime du module doit être en cours d’exécution pour votre appareil IoT Edge dans la ressource IoT Hub.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Sélectionnez et cliquez sur le module, puis observez le **jumeau d’identité de module**. L’état du client pour l’appareil Edge et le module doit être **Connecté**.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Une fois que le module est en cours d’exécution, il apparaît également sous la liste des modules Edge dans votre ressource Data Box Edge. L’**état du runtime** du module que vous avez ajouté est **en cours d’exécution**.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Connectez-vous aux partages local et cloud que vous avez créés par le biais de l’Explorateur de fichiers.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Ajoutez des données au partage local.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Les données sont déplacées vers le partage cloud.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Les données sont ensuite envoyées du partage cloud vers le compte de stockage. Accédez à l’Explorateur Stockage pour afficher les données.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Le processus de validation est terminé.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Edge et avez notamment appris à :

> [!div class="checklist"]
> * Créer une ressource IoT Hub
> * Configurer le rôle de calcul
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

Passez au didacticiel suivant pour découvrir comment administrer votre appareil Data Box Edge.

> [!div class="nextstepaction"]
> [Utiliser l’interface utilisateur web locale pour administrer un appareil Data Box Edge](https://aka.ms/dbg-docs)


