---
title: Utiliser un module IoT Edge pour déployer la charge de travail de calcul sur Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Découvrez comment exécuter une charge de travail de calcul à l’aide d’un module IoT Edge préalablement créé sur votre appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6c067fb5f39e82bb1601ce7b4d9dc5e2ce4ac624
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440137"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Tutoriel : Exécuter une charge de travail de calcul avec un module IoT Edge sur Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ce tutoriel explique comment exécuter une charge de travail de calcul à l’aide d’un module IoT Edge sur votre appareil Azure Stack Edge Pro avec GPU. Une fois le calcul configuré, l’appareil transforme les données avant de les envoyer à Azure.

Cette procédure peut prendre environ 10 à 15 minutes.


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer le calcul
> * Ajouter des partages
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

 
## <a name="prerequisites"></a>Prérequis

Avant de configurer un rôle de calcul sur votre appareil Azure Stack Edge Pro avec GPU, vérifiez que :

- Vous avez activé votre appareil Azure Stack Edge Pro, comme décrit dans [Activer votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Vous disposez d’un module IoT Edge que vous pouvez exécuter sur vos données. Dans ce tutoriel, nous avons utilisé un module `filemove2` qui déplace les données d’un partage local Edge se trouvant sur votre appareil vers un partage Edge à partir duquel les données sont placées dans un compte de stockage Azure.


## <a name="configure-compute"></a>Configurer le calcul

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Ajouter des partages

Pour le déploiement simple dans ce tutoriel, vous aurez besoin de deux partages : un partage Edge et un autre partage local Edge.

1. Pour ajouter un partage Edge sur l’appareil, effectuez les étapes suivantes :

    1. Dans votre ressource Azure Stack Edge, accédez à **Passerelle de stockage cloud > Partages**.
    2. Dans la barre de commandes, sélectionnez **+ Ajouter un partage**.
    3. Sur le panneau **Ajouter un partage**, fournissez le nom du partage et sélectionnez le type de partage.
    4. Pour monter le partage Edge, cochez la case **Utiliser le partage avec le computing en périphérie**.
    5. Sélectionnez le **Compte de stockage**, le **Service de stockage**, un utilisateur existant, puis sélectionnez **Créer**.

        ![Ajouter un partage Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Pour monter le partage NFS pour le calcul, le réseau de calcul doit être configuré sur le même sous-réseau que l’adresse IP virtuelle NFS. Pour plus d'informations sur la configuration du réseau de calcul, consultez [Activer le réseau de calcul sur Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Le partage Edge est créé, et vous recevrez une notification de création réussie. La liste de partages peut être mise à jour, mais vous devez attendre que la création du partage soit terminée.

2. Pour ajouter un partage local Edge sur l’appareil, répétez toutes les étapes décrites à l’étape précédente, puis cochez la case **Configurer en tant que partage local Edge**. Les données dans le partage local restent sur l’appareil.

    ![Ajouter un partage local Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Si vous avez créé un partage NFS local, utilisez l’option de commande rsync (synchronisation à distance) suivante pour copier les fichiers sur le partage :

    `rsync <source file path> < destination file path>`

    Pour plus d’informations sur la commande `rsync`, consultez la [documentation de `Rsync`](https://www.computerhope.com/unix/rsync.htm).
 
3. Accédez à **Passerelle de stockage cloud > Partages** pour voir la liste des partages mise à jour.

    ![Liste des partages mise à jour](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Ajouter un module

Vous pouvez ajouter un module prédéfini ou personnalisé. L’appareil n’est pas fourni avec des modules prédéfinis ou personnalisés. Pour savoir comment créer un module personnalisé, accédez à [Développer un module C# pour votre appareil Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md).

Dans cette section, vous ajoutez un module personnalisé à l’appareil IoT Edge que vous avez créé dans [Développer un module C# pour votre appareil Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Ce module personnalisé place des fichiers d’un partage local Edge sur l’appareil de périphérie, puis les déplace vers un partage Edge (cloud) sur l’appareil. Le partage cloud envoie ensuite les fichiers vers le compte de stockage Azure associé au partage cloud.

Pour ajouter un module, effectuez les étapes suivantes :

1. Accédez à **IoT Edge > Modules**. Dans la barre de commandes, sélectionnez **+ Ajouter un module**. 

2. Dans le panneau **Ajouter un module**, entrez les valeurs suivantes :

    
    |Champ  |Valeur  |
    |---------|---------|
    |Nom     | Nom unique pour le module. Ce module est un conteneur Docker que vous pouvez déployer sur un appareil IoT Edge associé à votre Azure Stack Edge Pro.        |
    |URI d’image     | URI d’image de l’image conteneur associée pour le module.        |
    |Informations d’identification obligatoires     | Si cette case est cochée, le nom d’utilisateur et le mot de passe sont utilisés pour récupérer les modules avec une URL correspondante.        |
    |Partage d’entrée     | Sélectionnez un partage d’entrée. Dans ce cas, le partage local Edge est le partage d’entrée. Le module utilisé ici déplace les fichiers depuis le partage local Edge vers un partage Edge où ils sont chargés sur le cloud.        |
    |Partage de sortie     | Sélectionnez un partage de sortie. Dans ce cas, le partage Edge est le partage de sortie.        |
    |Type de déclencheur     | Sélectionnez **Fichier** ou **Planifier**. Un déclencheur de fichier est activé chaque fois qu’un événement de fichier se produit, tel que l’écriture d’un fichier sur le partage d’entrée. Un déclencheur planifié se déclenche en fonction d’une planification définie par vos soins.         |
    |Nom du déclencheur     | Nom unique de votre déclencheur.         |
    |Variables d'environnement| Informations facultatives qui aident à définir l’environnement dans lequel votre module sera exécuté.   |

    ![Ajouter et configurer un module](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Sélectionnez **Ajouter**. Le module est ajouté. La page **IoT Edge > Vue Modules** est mise à jour pour indiquer que le module est déployé. L’état du runtime du module que vous avez ajouté doit être *en cours d’exécution*.

    ![Module déployé](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Vérifier la transformation des données et transférer

L’étape finale consiste à vérifier que le module est en cours d’exécution et qu’il traite les données comme prévu. L’état du runtime du module doit être en cours d’exécution pour votre appareil IoT Edge dans la ressource IoT Hub.

Pour vérifier que le module est en cours d’exécution et qu’il traite les données comme prévu, effectuez les étapes suivantes :


1. Dans l’Explorateur de fichiers, connectez-vous aux partages local Edge et Edge que vous avez créés. Consulter la procédure 

    ![Se connecter à des partages locaux Edge et à des partages cloud Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Ajoutez des données au partage local.

    ![Fichier copié dans le partage local Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Les données sont déplacées vers le partage cloud.

    ![Fichier déplacé vers un partage cloud Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Les données sont ensuite envoyées du partage cloud vers le compte de stockage. Pour consulter les données, vous pouvez utiliser l’Explorateur Stockage ou le Stockage Azure dans le portail.

    ![Vérifier les données présentes dans le compte de stockage](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Vous avez terminé le processus de validation.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le calcul
> * Ajouter des partages
> * Ajouter un module de calcul
> * Vérifier la transformation des données et transférer

Pour savoir comment gérer votre appareil Azure Stack Edge Pro, consultez :

> [!div class="nextstepaction"]
> [Administrer un appareil Azure Stack Edge Pro via l'interface utilisateur web locale](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
