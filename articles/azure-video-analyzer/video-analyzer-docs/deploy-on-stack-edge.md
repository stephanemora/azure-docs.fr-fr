---
title: Déployer Azure Video Analyzer sur Azure Stack Edge
description: Cet article indique les étapes à suivre pour déployer Azure Video Analyzer sur Azure Stack Edge.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: da14368846cd87d5d4e231933cec0068a4e558f9
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546612"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Déployer Azure Video Analyzer sur Azure Stack Edge

Cet article indique les étapes à suivre pour déployer Video Analyzer sur Azure Stack Edge. Une fois l’appareil configuré et activé, il est prêt pour le déploiement de Video Analyzer. 

Pour Video Analyzer, nous allons procéder à un déploiement par le biais d’IoT Hub, bien que les ressources Azure Stack Edge exposent une API Kubernetes, qui permet au client de déployer des solutions supplémentaires sans prise en charge d’IoT Hub, qui peuvent interagir avec Video Analyzer. 

> [!TIP]
> L’utilisation de l’API Kubernetes (K8s) pour le déploiement personnalisé est un cas particulier. Nous recommandons au client de créer des modules Edge et procéder au déploiement via IoT Hub vers chaque ressource Azure Stack Edge au lieu d’utiliser l’API Kubernetes. Dans cet article, nous allons vous montrer les étapes de déploiement du module Video Analyzer avec IoT Hub.

## <a name="prerequisites"></a>Configuration requise

* Compte Video Analyzer

    Ce [service cloud](./overview.md) est utilisé pour inscrire le module de périphérie Video Analyzer ainsi que pour la lecture des vidéos enregistrées et l’analytique vidéo.
* Identité managée

    Il s’agit de l’[identité managée](../../active-directory/managed-identities-azure-resources/overview.md) affectée par l’utilisateur qui permet de gérer l’accès au compte de stockage ci-dessus.
* Une ressource [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)
* [IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* Compte de stockage

    Nous vous recommandons d’utiliser des comptes de stockage à usage général v2 (GPv2).  
    En savoir plus sur le [compte de stockage v2 à usage général](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement. Vérifiez que vous disposez de l’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671. Cette configuration permet à Azure IoT Tools de communiquer avec Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>Configuration d’Azure Stack Edge pour utiliser Video Analyzer

Azure Stack Edge est une solution matérielle en tant que service ainsi qu’un appareil de computing en périphérie basé sur l’intelligence artificielle. Il est doté de fonctionnalités de transfert de données via le réseau. Pour en savoir plus, consultez [Azure Stack Edge et instructions d’installation détaillées](../../databox-online/azure-stack-edge-gpu-deploy-prep.md). Pour commencer, suivez les instructions des liens ci-dessous :

* [Création de ressources Service Azure Stack Edge/Data Box Gateway](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [Installation et configuration](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* Connexion et activation

    1. [Connexion](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [Configurer le réseau](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [Configuration de l’appareil](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [Configurer des certificats](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [Activer](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [Attacher IoT Hub à Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Activer les conditions préalables de calcul sur l’interface utilisateur locale Azure Stack Edge

Avant de poursuivre, assurez-vous que :

* vous avez activé votre ressource Azure Stack Edge ;
* vous avez accès à un système client Windows exécutant PowerShell 5.0 ou une version ultérieure pour accéder à la ressource Azure Stack Edge.
* Pour déployer un cluster Kubernetes, vous devez configurer votre ressource Azure Stack Edge via son [interface utilisateur locale](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 

    * Connecter et configurer :
    
        1. [Connexion](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [Configurer le réseau](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [Configuration de l’appareil](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [Configurer des certificats](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [Activer](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * Pour activer le calcul, accédez à la page Calcul de l’interface utilisateur locale de votre appareil.
    
        * Sélectionnez une interface réseau que vous souhaitez activer pour le calcul. Sélectionnez Activer. L’activation du calcul entraîne la création d’un commutateur virtuel sur votre appareil sur cette interface réseau.
        * Laissez les adresses IP du nœud de test Kubernetes et les adresses IP des services externes Kubernetes vides.
        * Sélectionnez Appliquer. Cette opération doit prendre environ 2 minutes.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text=" Conditions préalables de calcul sur l’interface utilisateur locale Azure Stack Edge":::

        * Si DNS n’est pas configuré pour l’API Kubernetes et la ressource Azure Stack Edge, vous pouvez mettre à jour votre fichier hôte Windows.
        
            * Ouvrez un éditeur de texte en tant qu’administrateur
            * Ouvrez le fichier « to C:\Windows\System32\drivers\etc\hosts' »
            * Ajoutez le nom d’hôte et le nom du périphérique IPv4 de l’API Kubernetes au fichier. (Vous pouvez retrouver ces informations dans le portail Azure Stack Edge sous la section Périphériques.)
            * Enregistrer et fermer

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>Déployer des modules de périphérie Video Analyzer à l’aide du portail Azure

Le Portail Azure vous aide à créer un manifeste de déploiement et à étendre le déploiement à un appareil IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Sélectionner votre appareil et définir des modules

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/) et accédez à votre IoT Hub.
1. Sélectionnez **IoT Edge** dans le menu.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Sélectionnez **Définir modules**.

#### <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Le portail Azure comprend un Assistant qui vous guide lors de la création du manifeste de déploiement. Le processus comprend trois étapes organisées en onglets : **Modules**, **Itinéraires** et **Vérifier + créer**.

#### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Modules IoT Edge** de la page, cliquez sur la liste déroulante **Ajouter**, puis sélectionnez **Module IoT Edge** pour afficher la page **Ajouter un module IoT Edge**.
1. Sous l’onglet **Paramètres du module**, fournissez un nom pour le module, puis spécifiez l’URI de l’image conteneur :   
    Exemples :
    
    * **Nom du module IoT Edge** : avaedge
    * **URI de l’image** : mcr.microsoft.com/media/video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="La capture d’écran montre l’onglet Paramètres du module":::
    
    > [!TIP]
    > Ne sélectionnez pas **Ajouter** tant que vous n’avez pas spécifié de valeur sous les onglets **Paramètres du module**, **Options de création de conteneur** et **Paramètres du jumeau de module**, comme décrit dans cette procédure.
    
    > [!WARNING]
    > Azure IoT Edge respecte la casse lorsque vous effectuez des appels de modules. Notez la chaîne exacte que vous utilisez comme nom de module.

1. Ouvrez l’onglet **variables d’environnement**.
   
   Ajoutez les valeurs suivantes dans les champs de saisie qui s’affichent.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Variables d’environnement":::

1. Ouvrez l’onglet **Options de création de conteneur**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Options de création de conteneur":::
 
    Copiez et collez le JSON suivant dans la zone pour limiter la taille des fichiers journaux générés par le module.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   La section « Lie » du code JSON comporte 2 entrées :
   1. "/var/lib/videoanalyzer:/var/lib/videoanalyzer" : cette entrée permet de lier les données de configuration d’application persistantes du conteneur et de les stocker sur l’appareil périphérique.
   1. « /var/media:/var/media » : Celle-ci lie les dossiers multimédias entre le périphérique et le conteneur. Elle est utilisée pour stocker les enregistrements vidéo quand vous exécutez une topologie de pipeline qui prend en charge le stockage des clips vidéo sur l’appareil périphérique.
   
1. Sous l’onglet **Paramètres de jumeau de module**, copiez le code JSON suivant et collez-le dans la zone.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Paramètres de jumeau":::

    Azure Video Analyzer nécessite un ensemble de propriétés de jumeau obligatoires pour pouvoir être exécuté comme indiqué dans [Schéma de configuration du jumeau de module](module-twin-configuration-schema.md).  

    Le JSON que vous devez entrer dans la zone d’édition des paramètres du jumeau de module se présente comme suit :    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    Ci-dessous, vous trouverez quelques propriétés **recommandées** supplémentaires pouvant être ajoutées au JSON et qui vous aideront à surveiller le module. Pour plus d’informations, consultez [surveillance et enregistrement](monitor-log-edge.md) :
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Sélectionnez **Ajouter**  

Ajouter le module Edge Simulateur RTSP

1. Dans la section **Modules IoT Edge** de la page, cliquez sur la liste déroulante **Ajouter**, puis sélectionnez **Module IoT Edge** pour afficher la page **Ajouter un module IoT Edge**.
1. Sous l’onglet **Paramètres du module**, fournissez un nom pour le module, puis spécifiez l’URI de l’image conteneur :   
    Exemples :
    
    * **Nom du module IoT Edge** : rtspsim
    * **URI d’image** : mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2  


1. Ouvrez l’onglet **Options de création de conteneur**.
 
    Copier et coller le code JSON suivant dans la zone
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. Sélectionnez **Ajouter**  

1. Sélectionnez **Suivant : Itinéraires** pour passer à la section Itinéraires. Spécifiez des itinéraires.

    Sous NOM, entrez **AVAToHub**, puis sous VALEUR, entrez **FROM /messages/modules/avaedge/outputs/ INTO $upstream**
1. Sélectionnez Suivant : **Vérifier + créer** pour passer à la section Vérifier.
1. Vérifiez les informations de votre déploiement, puis sélectionnez **Créer** pour déployer le module.

    > [!TIP]
    > Effectuez ces étapes pour générer le jeton de provisionnement :
1. Ouvrez le portail Azure et accédez à Video Analyzer.
1. Dans le volet de navigation de gauche, cliquez sur **Modules de périphérie**.
1. Sélectionnez le module de périphérie, puis cliquez sur le bouton **Générer un jeton** :

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Générer un jeton" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. Copiez le jeton de provisionnement :

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Copier le jeton":::



#### <a name="optional-setup-docker-volume-mounts"></a>(Facultatif) Configurer les montages de volume Docker

Si vous souhaitez afficher les données dans les répertoires de travail, procédez comme suit pour configurer les montages de volume Docker avant le déploiement. 

Ces étapes décrivent la création d’un utilisateur de passerelle et la configuration de partages de fichiers pour voir le contenu du répertoire de travail Video Analyzer et du dossier multimédia Video Analyzer.

> [!NOTE]
> Les montages de liaisons sont pris en charge, mais les montages de volume permettent d’afficher les données et, le cas échéant, de les copier à distance. Il est possible d’utiliser à la fois des montages de liaison et de volume, mais ils ne peuvent pas pointer vers le même chemin d’accès de conteneur.

1. Ouvre le portail Azure et accédez à votre ressource Azure Stack Edge.
1. Créer un **utilisateur de passerelle** qui peut accéder aux partages.
    
    1. Dans le volet de navigation gauche, cliquez sur **Passerelle de stockage cloud**.
    1. Cliquez sur **Utilisateurs** dans le volet de navigation gauche.
    1. Cliquez sur **+ Ajouter un utilisateur** pour définir le nom d’utilisateur et le mot de passe. (Recommandé : `avauser`).
    1. Cliquez sur **Ajouter**.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Ajouter un utilisateur":::
1. Créez un **partage local** pour la persistance de Video Analyzer.

    1. Cliquez sur **Passerelle de stockage cloud-> Partages**.
    1. Cliquez sur **+ Ajouter des partages**.
    1. Entrez un nom de partage. (Recommandé : `ava`).
    1. Conservez le type de partage SMB.
    1. Assurez-vous que l’option **utiliser le partage avec le computing en périphérie** est activée.
    1. Assurez-vous que l’option **Configurer en tant que partage local Edge** est activée.
    1. Dans détails de l’utilisateur, autorisez l’accès au partage à l’utilisateur créé récemment.
    1. Cliquez sur **Créer**.
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Partage local":::  
    
        > [!TIP]
        > Connectez votre client Windows à votre Azure Stack Edge, pour vous connecter aux partages SMB en suivant les étapes [mentionnées dans ce document](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    
1. Créer un partage distant pour le stockage File Sync.

    1. Commencez par créer un compte de stockage blob dans la même région en cliquant sur **Passerelle de stockage cloud->Comptes de stockage**.
    1. Cliquez sur **Passerelle de stockage cloud-> Partages**.
    1. Cliquez sur **+ Ajouter des partages**.
    1. Entrez un nom de partage. (Recommandé : média).
    1. Conservez le type de partage SMB.
    1. Assurez-vous que l’option **utiliser le partage avec le computing en périphérie** est activée.
    1. Assurez-vous que l’option **Configurer en tant que partage local Edge** n’est pas activée.
    1. Sélectionnez le compte de stockage récemment créé.
    1. Définissez le type de stockage sur l’objet blob de blocs.
    1. Définissez un nom de conteneur.
    1. Dans détails de l’utilisateur, autorisez l’accès au partage à l’utilisateur créé récemment.
    1. Cliquez sur **Créer**.    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Partage distant":::
1. Mettez à jour les options de création de conteneur du module de simulateur RTSP pour utiliser des montages de volume :
    1. Cliquez sur le bouton **Définir des modules** :

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Définir des modules" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. Cliquez sur le module **rtspsim** :

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Sélectionner un module":::
    1. Sélectionnez l’onglet **Options de création de conteneur** et ajoutez les montages comme indiqué ci-dessous :
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Mettre à jour un module":::

        ```json
            "createOptions": 
            {
                "HostConfig": 
                {
                    "Mounts": 
                    [
                        {
                            "Target": "/live/mediaServer/media",
                            "Source": "media",
                            "Type": "volume"
                        }
                    ],
                    "PortBindings": {
                        "554/tcp": [
                            {
                            "HostPort": "554"
                            }
                        ]
                    }
                }
            }
        ```
    1. Cliquez sur le bouton **Mettre à jour**
    1. Cliquez sur le bouton **Vérifier et créer**, puis, enfin, sur le bouton **Créer** pour mettre à jour le module.
    
### <a name="verify-that-the-module-is-running"></a>Vérifiez que le module soit en cours d’exécution

L’étape finale consiste à vérifier que le module est connecté et qu’il s’exécute comme prévu. L’état du runtime du module doit être en cours d’exécution pour votre appareil IoT Edge dans la ressource IoT Hub.

Pour vérifier que le module est en cours d’exécution, effectuez les étapes suivantes :

1. Dans le portail Azure, revenez à votre ressource Azure Stack Edge
1. Sélectionnez la vignette « Modules ». Le panneau Modules apparaît. Dans la liste des modules, identifiez le module que vous avez déployé. L’état du runtime du module que vous avez ajouté doit être en cours d’exécution.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Module personnalisé" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configurer l’extension Azure IoT Tools

Suivez ces instructions pour vous connecter à votre hub IoT à l’aide de l’extension Azure IoT Tools.

1. Dans Visual Studio Code, sélectionnez Affichage > Explorer. Vous pouvez aussi sélectionner Ctrl+Maj+E.
1. Dans l’angle en bas à gauche de l’onglet Explorateur, sélectionnez Azure IoT Hub.
1. Sélectionnez l’icône Plus d’options pour voir le menu contextuel. Sélectionnez ensuite Définir la chaîne de connexion IoT Hub.
1. Quand une zone d’entrée s’affiche, entrez votre chaîne de connexion IoT Hub. 

   * Pour obtenir la chaîne de connexion, accédez à votre IoT Hub sur le portail Azure puis cliquez sur Stratégies d’accès partagé dans le volet de navigation de gauche.
   * Cliquez sur iothubowner pour obtenir les clés d’accès partagé.
   * Copiez la clé primaire de la chaîne de connexion, puis collez-la dans la zone d’entrée du VSCode.

   La chaîne de connexion doit ressembler à :<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Si la connexion aboutit, la liste des appareils de périphérie s’affiche. Votre appareil Azure Stack Edge doit s’afficher. Vous pouvez désormais gérer vos appareils IoT Edge et interagir avec Azure IoT Hub par le biais du menu contextuel. Pour afficher les modules déployés sur le périphérique, sous l’appareil Azure Stack, développez le nœud Modules.
    
## <a name="troubleshooting"></a>Dépannage

* **Accès à l’API Kubernetes (kubectl)**

    * Suivez la documentation pour configurer votre ordinateur afin d’avoir [accès au cluster Kubernetes](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md).
    * Tous les modules IoT Edge déployés utilisent l’espace de noms `iotedge`. Assurez-vous de l’inclure lorsque vous utiliser kubectl.  
* **Journaux de module**

    L’outil `iotedge` n’est pas accessible pour obtenir des journaux. Vous devez utiliser [journaux kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) pour afficher les journaux ou le canal dans un fichier. Exemple : <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Métriques nœud et pod**

    Utilisez [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) pour voir les métriques nœud et pod.
    <br/>`kubectl top pods -n iotedge` 
* **Réseau des modules**   

    Pour la découverte de module sur Azure Stack Edge, il est nécessaire que le module dispose de la liaison avec le port hôte dans createOptions. Le module sera ensuite adressable sur `moduleName:hostport`.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **Montages de volumes**

    Un module ne peut pas démarrer si le conteneur tente de monter un volume dans un répertoire existant qui n’est pas vide.
* **Mémoire partagée lors de l’utilisation de gRPC**

    La fonction de mémoire partagée sur les ressources Azure Stack Edge est prise en charge sur les pods dans tous les espaces de noms en utilisant l’hôte IPC.
    Configuration de la mémoire partagée sur un module Edge pour le déploiement via IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(Avancé) Colocalisation de pods**

    Quand vous utilisez K8s pour déployer des solutions d’inférence personnalisées qui communiquent avec Video Analyzer par le biais de gRPC, vous devez veiller à ce que les pods soient déployés sur les mêmes nœuds que les modules Video Analyzer.

    * **Option 1** : utiliser l’affinité de nœud et les étiquettes de nœud intégrées pour la colocalisation.

    Actuellement, la configuration personnalisée NodeSelector ne semble pas être possible, car les utilisateurs ne disposent pas des accès nécessaires pour définir des étiquettes sur les nœuds. Toutefois, en fonction de la topologie du client et des conventions d’affectation de noms, il peut être en mesure d’utiliser des [étiquettes de nœud intégrées](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Une section nodeAffinity référençant des ressources Azure Stack Edge avec Video Analyzer peut être ajoutée au manifeste de pod d’inférence pour réussir la colocalisation.
    * **Option 2** : utiliser l’affinité de pod pour la colocalisation (recommandé).

        Kubernetes prend en charge [l’affinité de pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity) qui peut planifier des pods sur le même nœud. Une section podAffinity référençant le module Video Analyzer peut être ajoutée au manifeste de pod d’inférence pour réussir la colocalisation.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example Inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```
* **Code d’erreur 404 lors de l’utilisation du module `rtspsim`**  
    
    Le conteneur lit les vidéos à partir d’un seul dossier au sein du conteneur. Si vous mappez/liez un dossier externe à celui qui existe déjà dans l’image conteneur, docker masque les fichiers présents dans l’image conteneur.  
 
    Par exemple, sans liaison, le conteneur peut avoir les fichiers suivants :  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Et votre hôte peut avoir les fichiers suivants :
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Toutefois, lorsque la liaison suivante est ajoutée au fichier manifeste de déploiement, docker remplace le contenu de /live/mediaServer/media afin qu’il corresponde à ce qui se trouve sur l’hôte.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Étapes suivantes

[Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md)
