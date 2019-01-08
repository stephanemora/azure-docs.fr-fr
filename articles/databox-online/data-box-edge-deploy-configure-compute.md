---
title: Transformer des données avec Azure Data Box Edge | Microsoft Docs
description: Découvrez comment configurer le rôle de calcul sur Data Box Edge et l’utiliser pour transformer des données avant de les envoyer à Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c0901f22e4941fdfaa21138153a06e97c2d6095f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630374"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Tutoriel : Transformer des données avec Azure Data Box Edge (préversion)

Ce tutoriel explique comment configurer un rôle de calcul sur votre appareil Azure Data Box Edge. Une fois que vous avez configuré le rôle de calcul, Data Box Edge peut transformer des données avant de les envoyer à Azure.

Cette procédure peut prendre environ 30 à 45 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une ressource Azure IoT Hub
> * Configurer le rôle de calcul
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

> [!IMPORTANT]
> Data Box Edge est en préversion. Avant de commander et déployer cette solution, lisez les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Prérequis

Avant de configurer un rôle de calcul sur votre appareil Data Box Edge, vérifiez que :

* Vous avez activé votre appareil Data Box Edge comme décrit dans [Connecter, configurer et activer Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Créer une ressource IoT Hub

Avant de configurer un rôle de calcul sur Data Box Edge, vous devez créer une ressource IoT Hub.

Pour obtenir des instructions détaillées, consultez [Créer un IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Utilisez les mêmes abonnement et groupe de ressources que ceux que vous avez utilisés pour votre ressource Data Box Edge.

![Créer une ressource IoT Hub](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Si aucun rôle de calcul Edge n’a été configuré, les remarques suivantes s’appliquent :

- La ressource IoT Hub ne comporte aucun appareil Azure IoT ni Azure IoT Edge.
- Vous ne pouvez pas créer de partages locaux Edge. Lorsque vous ajoutez un partage, l’option permettant de créer un partage local pour le calcul Edge n’est pas activée.


## <a name="set-up-compute-role"></a>Configurer le rôle de calcul

Quand le rôle de calcul Edge est configuré sur l’appareil Edge, il crée deux appareils : un appareil IoT et un appareil IoT Edge. Ces deux appareils peuvent être visualisés dans la ressource IoT Hub.

Pour configurer le rôle de calcul sur l’appareil, procédez comme suit :

1. Accédez à la ressource Data Box Edge, sélectionnez **Vue d’ensemble**, puis **Configurer le rôle de calcul**. 

    ![Lien Vue d’ensemble dans le volet gauche](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Si vous le souhaitez, vous pouvez accéder à **Modules** et sélectionner **Configurer le calcul**.

    ![Liens « Modules » et « Configurer le calcul »](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. Dans la liste déroulante, sélectionnez la **ressource IoT Hub** que vous avez créée à l’étape précédente.  
    À ce stade, seule la plateforme Linux est disponible pour votre appareil IoT Edge. 
    
1. Cliquez sur **Créer**.

    ![Bouton Créer](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    La création du rôle de calcul ne prend que quelques minutes. En raison d’un bogue dans cette version, même lorsque le rôle de calcul est créé, l’écran n’est pas actualisé. Pour vérifier que le rôle de calcul Edge a été configuré, accédez à **Modules**.  

    ![Liste des appareils « Configurer le computing en périphérie »](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Accédez à nouveau à **Vue d’ensemble**.  
    L’écran est maintenant à jour et indique que le rôle de calcul est configuré.

    ![Configurer un rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. Dans le hub IoT que vous avez utilisé quand vous avez créé le rôle de calcul Edge, accédez à **Appareils IoT**.  
    Un appareil IoT est maintenant activé. 

    ![Page « Appareils IoT »](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. Dans le volet gauche, sélectionnez **IoT Edge**.  
    Un appareil IoT Edge est également activé.

    ![Configurer un rôle de calcul](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Sélectionnez et cliquez sur l’appareil IoT Edge.  
    Un agent Edge est en cours d’exécution sur cet appareil IoT Edge. 

    ![Page Détails de l’appareil](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Il n’y aucun module personnalisé sur cet appareil de périphérie, mais vous pouvez maintenant en ajouter un. Pour savoir comment créer un module personnalisé, accédez à [Développer un module C# pour votre appareil Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Ajouter un module personnalisé

Dans cette section, vous allez ajouter un module personnalisé à l’appareil IoT Edge que vous avez créé dans [Développer un module C# pour votre appareil Data Box Edge](data-box-edge-create-iot-edge-module.md). 

La procédure suivante utilise un exemple dans lequel le module personnalisé place des fichiers d’un partage local sur l’appareil de périphérie, puis les déplace vers un partage cloud sur l’appareil. Le partage cloud envoie ensuite les fichiers vers le compte de stockage Azure associé au partage cloud. 

1. Ajoutez un partage local sur l’appareil de périphérie en effectuant les étapes suivantes :

    a. Dans votre ressource Data Box Edge, accédez à **Partages**. 
    
    b. Sélectionnez **Ajouter un partage**, puis fournissez le nom du partage et sélectionnez le type de partage. 
    
    c. Pour créer un partage local, cochez la case **Configurer en tant que partage local Edge**. 
    
    d. Sélectionnez **Créer nouveau** ou **Utiliser l’existant** , puis **Créer**.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Si vous avez créé un partage NFS local, utilisez l’option de commande rsync (synchronisation à distance) suivante pour copier les fichiers sur le partage :

    `rsync --inplace <source file path> < destination file path>`

    Pour plus d’informations sur la commande rsync, consultez la [documentation de Rsync](https://www.computerhope.com/unix/rsync.htm). 

    Le partage local est créé, et vous recevrez une notification de création réussie. La liste de partages peut être mise à jour, mais vous devez attendre que la création du partage soit terminée.
    
1. Accédez à la liste des partages. 

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Pour afficher les propriétés du nouveau partage local, sélectionnez-le. 

1. Dans la zone **Point de montage local pour les modules de computing en périphérie**, copiez la valeur correspondant à ce partage.  
    Vous utiliserez ce point de montage local quand vous déploierez le module.

    ![Zone « Point de montage local pour les modules de computing en périphérie »](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. Sur un partage cloud existant qui a été créé sur votre appareil Data Box Edge, dans la zone **Point de montage local pour les modules de computing en périphérie**, copiez le point de montage local pour les modules de computing en périphérie pour ce partage cloud.  
    Vous utiliserez ce point de montage local quand vous déploierez le module.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Pour ajouter un module personnalisé à l’appareil IoT Edge, accédez à votre ressource IoT Hub puis à **Appareil IoT Edge**. 

1. Sélectionnez l’appareil puis, sous **Détails de l’appareil**, sélectionnez **Définir des modules**. 

    ![Lien Définir des modules](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. Sous **Ajouter des modules**, effectuez les étapes suivantes :

    a. Entrez le nom, l’adresse, le nom d’utilisateur et le mot de passe dans les paramètres du registre de conteneurs du module personnalisé.  
    Le nom, l’adresse et les informations d’identification indiquées sont utilisées pour récupérer des modules avec une URL correspondante. Pour déployer ce module, sous **Modules de déploiement**, sélectionnez **Module IoT Edge**. Ce module IoT Edge est un conteneur Docker que vous pouvez déployer sur un appareil IoT Edge associé à votre appareil Data Box Edge.

    ![Page Définir des modules](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Spécifiez les paramètres du module personnalisé IoT Edge en entrant le nom de votre module et l’URI de l’image conteneur correspondante. 
    
    ![Page Modules personnalisés IoT Edge](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. Dans la zone **Options de création de conteneur**, entrez les points de montage locaux des modules de périphérie que vous avez copiés lors des étapes précédentes pour le partage local et le partage cloud.
    > [!IMPORTANT]
    > Utilisez les chemins copiés ; ne créez pas de nouveaux chemins. Les points de montage locaux sont mappés aux variables **InputFolderPath** et **OutputFolderPath** que vous avez spécifiées dans le module quand vous avez [mis à jour le module avec du code personnalisé](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    Dans la zone **Options de création de conteneur**, vous pouvez coller l’exemple suivant : 
    
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

    Indiquez également ici les variables d’environnement pour votre module.

    ![Zone Options de création de conteneur](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Si nécessaire, configurez les paramètres avancés du runtime Edge, puis cliquez sur **Suivant**.

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  Sous **Spécifier des routes**, définissez des routes entre les modules.  
    Dans cet exemple, entrez le nom du partage local qui enverra des données vers le partage cloud.

    Vous pouvez remplacer *route* par la chaîne de route suivante :       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![Section Spécifier des routes](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Sélectionnez **Suivant**. 

1.  Sous **Passer en revue le déploiement**, passez en revue tous les paramètres et, si vous êtes satisfait, sélectionnez **Soumettre** pour soumettre le module pour déploiement.

    ![Page Définir des modules](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Cette action démarre le déploiement du module, comme illustré ci-dessous :

    ![Ajouter un module personnalisé](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Vérifier la transformation des données et transférer

L’étape finale consiste à vérifier que le module est connecté et qu’il s’exécute comme prévu. L’état du runtime du module doit être en cours d’exécution pour votre appareil IoT Edge dans la ressource IoT Hub.

![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Pour vérifier que le module est en cours d’exécution, effectuez les étapes suivantes :

1. Sélectionnez le module et affichez le Jumeau d’identité de module.  
    L’état du client pour l’appareil et le module de périphérie doit être *Connecté*.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    Une fois que le module est en cours d’exécution, il apparaît également sous la liste des modules de périphérie dans votre ressource Data Box Edge. L’état du runtime du module que vous avez ajouté est *en cours d’exécution*.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  Dans l’Explorateur de fichiers, connectez-vous aux partages local et cloud que vous avez créés.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Ajoutez des données au partage local.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Les données sont déplacées vers le partage cloud.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Les données sont ensuite envoyées du partage cloud vers le compte de stockage. Pour afficher les données, accédez à l’Explorateur Stockage.

    ![Vérifier la transformation des données](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Vous avez terminé le processus de validation.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une ressource IoT Hub
> * Configurer le rôle de calcul
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

Pour découvrir comment gérer votre appareil Data Box Edge, consultez :

> [!div class="nextstepaction"]
> [Utiliser l’interface utilisateur web locale pour administrer un appareil Data Box Edge](https://aka.ms/dbg-docs)


