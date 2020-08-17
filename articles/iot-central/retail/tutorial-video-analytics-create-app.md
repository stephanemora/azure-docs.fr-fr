---
title: Tutoriel - Créer une application d’analytique vidéo pour la détection d’objets et de mouvements dans Azure IoT Central
description: Ce tutoriel montre comment créer une application d’analytique vidéo dans IoT Central. Vous pouvez la créer, la personnaliser et la connecter à d’autres services Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: 897262dcdb8cbacd512f19823da375e2c603b97e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037873"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Tutoriel : Créer une application d’analytique vidéo pour la détection d’objets et de mouvements dans Azure IoT Central

En tant que créateur de solutions, apprenez à créer une application d’analytique avec le modèle d’application *Analytique vidéo - Détection d’objets et de mouvements* IoT Central, les appareils Azure IoT Edge et Azure Media Services. La solution utilise un magasin de vente au détail pour montrer comment répondre aux besoins courants des entreprises afin de surveiller les caméras de sécurité. La solution utilise la détection automatique d’objets dans un flux vidéo pour identifier et localiser rapidement les événements intéressants.

L’exemple d’application comprend deux appareils simulés et une passerelle IoT Edge. Les tutoriels suivants présentent deux approches permettant d’expérimenter et de comprendre les fonctionnalités de la passerelle :

* Créer la passerelle IoT Edge sur une machine virtuelle Azure et connecter une caméra simulée.
* Créer la passerelle IoT Edge sur un appareil réel, tel qu’un processeur Intel NUC, et connecter une caméra réelle.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Utiliser le modèle d’application d’analytique vidéo Azure IoT Central pour créer une application de magasin de vente au détail
> * Vérifier les paramètres d’application
> * Créer un modèle d’appareil pour un appareil de passerelle IoT Edge
> * Ajouter un appareil de passerelle à votre application IoT Central

## <a name="prerequisites"></a>Prérequis

Pour réaliser cette série de tutoriels, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un sur la [page d’inscription à Azure](https://aka.ms/createazuresubscription).
* Si vous utilisez une caméra réelle, il vous faut une connectivité entre l’appareil IoT Edge et la caméra, de même que le canal **Real Time Streaming Protocol**.

## <a name="initial-setup"></a>Configuration initiale

Dans ces tutoriels, vous mettez à jour et utilisez plusieurs fichiers de configuration. Les versions initiales de ces fichiers sont disponibles dans le référentiel GitHub [LVA-gateway](https://github.com/Azure/live-video-analytics). Ce référentiel comprend également un fichier texte de bloc-notes que vous pouvez télécharger et utiliser pour enregistrer les valeurs de configuration des services que vous déployez.

Créez un dossier appelé *lva-configuration* sur votre ordinateur local pour enregistrer les copies de ces fichiers. Cliquez ensuite avec le bouton droit sur chacun des liens suivants, puis sélectionnez **Enregistrer sous** pour enregistrer le fichier dans le dossier *lva-configuration* :

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Le référentiel GitHub comprend également le code source des modules IoT Edge **LvaEdgeGatewayModule** et **lvaYolov3**. Pour plus d’informations sur l’utilisation du code source, consultez [Générer les modules LVA Gateway](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Déployer et configurer Azure Media Services

La solution utilise un compte Azure Media Services pour stocker les clips vidéo de détection d’objets réalisés par l’appareil de passerelle IoT Edge.

Lorsque vous créez le compte Media Services :

- Vous devez fournir un nom de compte, un abonnement Azure, un emplacement, un groupe de ressources et un compte de stockage. Créez un compte de stockage à l’aide des paramètres par défaut lors de la création du compte Media Services.

- Sélectionnez la région **USA Est** comme emplacement.

- Créez un groupe de ressources appelé *lva-rg* dans la région **USA Est** pour les comptes de stockage et Media Services. Une fois les tutoriels terminés, supprimez facilement toutes les ressources en supprimant le groupe de ressources *lva-rg* .

Dans le portail Azure, créez le [compte Media Services](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Tous les exemples de ces tutoriels utilisent la région **USA Est**. Vous pouvez utiliser votre région la plus proche si vous préférez.

Notez le nom de votre compte **Media Services** dans le fichier *scratchpad.txt* .

Une fois le déploiement terminé, accédez à la page **Propriétés** de votre compte **Media Services**. Notez l’**ID de ressource** dans le fichier *scratchpad.txt*. Vous utiliserez plus tard cette valeur lorsque vous configurerez le module IoT Edge.

Ensuite, configurez un principal de service Azure Active Directory pour votre ressource Media Services. Sélectionnez **Accès API**, puis **Authentification du principal de service**. Créez une nouvelle application Azure Active Directory portant le même nom que votre ressource Media Services, puis créez un secret avec pour description *Accès IoT Edge*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Configurer l’application AAD pour AMS":::

Une fois le secret généré, faites défiler jusqu’à la section **Copier les informations d’identification pour connecter votre application de principal du service**. Sélectionnez ensuite **JSON**. Vous pouvez copier simultanément toutes les informations d’identification depuis cet emplacement. Notez ces informations dans le fichier *scratchpad.txt*. Vous les utiliserez plus tard lorsque vous configurerez l’appareil IoT Edge.

> [!WARNING]
> Il s’agit de la seule possibilité d’afficher et d’enregistrer le secret. Si vous le perdez, vous devez en générer un autre.

## <a name="create-the-azure-iot-central-application"></a>Créer l’application Azure IoT Central

Dans cette section, vous créerez une application Azure IoT Central à partir d’un modèle. Vous allez utiliser cette application tout au long de la série de tutoriels pour générer une solution complète.

Pour créer une nouvelle application Azure IoT Central :

1. Accédez au site web du [gestionnaire d’applications d’Azure IoT Central](https://aka.ms/iotcentral).

1. Connectez-vous avec les informations d’identification que vous utilisez pour accéder à votre abonnement Azure.

1. Pour commencer à créer une application Azure IoT Central, sélectionnez **Nouvelle application** sur la page **Générer**.

1. Sélectionnez **Distribution**. La page de vente au détail affiche plusieurs modèles d’application de vente au détail.

Pour créer une application d’analytique vidéo :

1. Sélectionnez le modèle d’application **Analytique vidéo - Détection d’objets et de mouvements**. Ce modèle comprend des modèles d’appareil pour les appareils utilisés dans le tutoriel. Le modèle fournit également un tableau de bord d’opérateur à des fins de surveillance de la vidéo.

1. Si vous le souhaitez, choisissez un **nom d’application** convivial. Cette application est basée sur un magasin fictif de vente au détail nommé Northwind Traders. Le didacticiel utilise le **nom d’application** *d’analytique vidéo Northwind Traders*.

    > [!NOTE]
    > Si vous utilisez un **nom d’application** convivial, vous devez toujours utiliser une valeur unique pour l’**URL** de l’application.

1. Si vous disposez d’un abonnement Azure, sélectionnez votre **répertoire**, votre **abonnement Azure** et **États-Unis** comme **emplacement**. Si vous n’avez pas d’abonnement, vous pouvez activer une **version d’évaluation gratuite de 7 jours** et remplir les coordonnées requises. Ce tutoriel utilise trois appareils : deux caméras et un appareil IoT Edge et dès lors, à défaut d’utiliser la version d’évaluation gratuite, vous serez facturé pour l’utilisation.

    Pour plus d’informations sur les répertoires et abonnements et emplacements, consultez le guide de démarrage rapide [Créer une application](../core/quick-deploy-iot-central.md).

1. Sélectionnez **Create** (Créer).

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Page de création d’une application Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Récupérer les données de configuration

Plus loin dans ce tutoriel, lorsque vous configurerez la passerelle IoT Edge, vous aurez besoin de certaines données de configuration de l’application IoT Central. L’appareil IoT Edge utilise ces informations pour s’inscrire et se connecter à l’application.

Dans la section **Administration**, sélectionnez **votre application** et notez l’**URL d’application** et l’**ID d’application** dans le fichier *scratchpad.txt* :

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Administration":::

Sélectionnez **Jetons d’API** et générez un nouveau jeton appelé **LVAEdgeToken** pour le rôle **Opérateur** :

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Générer un jeton":::

Notez le jeton dans le fichier *scratchpad.txt* pour plus tard. Une fois la boîte de dialogue fermée, vous ne pouvez plus afficher le jeton.

Dans la section **Administration**, sélectionnez **Connexion de l’appareil**, puis **SAS-IoT-Devices**.

Notez la **clé primaire** des appareils dans le fichier *scratchpad.txt*. Vous utiliserez ce *jeton de signature d’accès partagé au groupe principal* lorsque vous configurerez l’appareil IoT Edge.

## <a name="edit-the-deployment-manifest"></a>Modifier le manifeste de déploiement

Vous déployez un module IoT Edge à l’aide d’un manifeste de déploiement. Dans IoT Central, vous pouvez importer le manifeste en tant que modèle d’appareil, puis laisser IoT Central gérer le déploiement du module.

Pour préparer le manifeste de déploiement :

1. À l’aide d’un éditeur de texte, ouvrez le fichier *deployment.amd64.json* que vous avez enregistré dans le dossier *lva-configuration*.

1. Recherchez les paramètres `LvaEdgeGatewayModule` et modifiez le nom de l’image comme indiqué dans l’extrait de code suivant :

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Ajoutez le nom de votre compte Media Services au nœud `env` de la section `LvaEdgeGatewayModule`. Vous avez noté le nom de ce compte dans le fichier *scratchpad.txt*  :

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Le modèle n’exposant pas ces propriétés dans IoT Central, vous devez ajouter les valeurs de configuration Media Services au manifeste de déploiement. Recherchez le `lvaEdge`module et remplacez les espaces réservés par les valeurs que vous avez notées dans le fichier *scratchpad.txt* lors de la création de votre compte Media Services.

    `azureMediaServicesArmId` correspond à l’**ID de ressource** que vous avez noté dans le fichier *scratchpad.txt* lors de la création du compte Media Services.

    Vous avez noté les valeurs `aadTenantId`, `aadServicePrincipalAppId` et `aadServicePrincipalSecret` dans le fichier *scratchpad.txt* lors de la création du principal du service pour votre compte Media Services :

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Enregistrez les modifications.

Vous pouvez également remplacer le module Yolov3 par le [module d’extension OpenVINO&trade; Model Server-Edge AI optimisé par le matériel](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) d’Intel. Vous pouvez télécharger un exemple de manifeste de déploiement [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Ce manifeste remplace les paramètres de configuration du module `lvaYolov3` par la configuration suivante :

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Créer l’appareil de passerelle Azure IoT Edge

L’application Analytique vidéo - Détection d’objets et de mouvements comprend un modèle d’appareil **LVA Edge Object Detector** et un modèle d’appareil **LVA Edge Motion Detection**. Dans cette section, vous allez créer un modèle d’appareil de passerelle à l’aide du manifeste de déploiement et ajouter cet appareil à votre application IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Créer un modèle d’appareil pour l’appareil LVA Edge Gateway

Pour importer le manifeste de déploiement et créer le modèle d’appareil **LVA Edge Gateway** :

1. Dans votre application Azure IoT Central, accédez à **Modèles d’appareil**, puis sélectionnez **+ Nouveau**.

1. Dans la page **Sélectionner un type de modèle**, sélectionnez la vignette **Azure IoT Edge**. Ensuite, sélectionnez **Next: Personnaliser**.

1. Sur la page **Charger un manifeste de déploiement Azure IoT Edge**, entrez *LVA Edge Gateway* comme nom de modèle, puis cochez **Appareil de passerelle avec des appareils en aval**.

    Ne recherchez pas le manifeste de déploiement pour le moment. Ce faisant, l’Assistant Déploiement attendrait une interface pour chaque module alors que vous devez uniquement exposer l’interface pour **LvaEdgeGatewayModule**. Vous chargerez le manifeste au cours d’une étape ultérieure.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Ne pas télécharger le manifeste de déploiement":::

    Sélectionnez **Suivant : Vérification**).

1. Dans la page **Vérifier**, sélectionnez **Créer**.

### <a name="import-the-device-capability-model"></a>Importer le modèle de capacité d’appareil

Le modèle d’appareil doit inclure un modèle de capacité d’appareil. Sur la page **LVA Edge Gateway**, sélectionnez la vignette **Importer le modèle de capacité**. Accédez au dossier *lva-configuration* créé précédemment, puis sélectionnez le fichier *LvaEdgeGatewayDcm.json*.

Le modèle d’appareil **LVA Edge Gateway** comprend désormais le **module LVA Edge Gateway**, ainsi que trois interfaces : **Informations sur l’appareil**, **Paramètres LVA Edge Gateway** et **Interface LVA Edge Gateway**.

### <a name="replace-the-manifest"></a>Remplacer le manifeste

Sur la page de **LVA Edge Gateway**, sélectionnez **+ Remplacer le manifeste**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Remplacer le manifeste":::

Accédez au dossier *lva-configuration* et sélectionnez le fichier manifeste *deployment.amd64.json* modifié précédemment. Sélectionnez **Télécharger**. Une fois la validation terminée, sélectionnez **Remplacer**.

### <a name="add-relationships"></a>Ajouter des relations

Dans le modèle d’appareil **LVA Edge Gateway**, sous **Modules/LVA Edge Gateway Module**, sélectionnez **Relations**. Sélectionnez **+ Ajouter une relation** et ajoutez les deux relations suivantes :

|Nom d’affichage               |Nom          |Cible |
|-------------------------- |------------- |------ |
|LVA Edge Motion Detector   |Utiliser la valeur par défaut   |LVA Edge Motion Detector Device |
|LVA Edge Object Detector   |Utiliser la valeur par défaut   |LVA Edge Object Detector Device |

Ensuite, sélectionnez **Enregistrer**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Ajouter des relations":::

### <a name="add-views"></a>Ajout de vues

Le modèle d’appareil **LVA Edge Gateway** n’inclut aucune définition d’affichage.

Pour ajouter une vue au modèle d’appareil :

1. Dans le modèle d’appareil **LVA Edge Gateway**, accédez à **Affichages**, puis sélectionnez la vignette **Visualisation de l'appareil**.

1. Entrez *Appareil LVA Edge Gateway* comme nom d’affichage.

1. Ajoutez les vignettes suivantes l’affichage :

    * Vignette avec les propriétés **Informations sur l’appareil** : **Modèle d’appareil**, **Fabricant**, **Système d’exploitation**, **Architecture du processeur**, **Version du logiciel**, **Mémoire totale** et **Stockage total**.
    * Vignette de graphique en courbes avec valeurs de télémétrie **Mémoire libre** et **Pulsation système**.
    * Vignette d’historique des événements avec les événements suivants : **Créer une caméra**, **Supprimer une caméra**, **Redémarrer le module**, **Module démarré**, **Module arrêté**.
    * Vignette de dernière valeur connue 2x1 indiquant la télémétrie **État du client IoT Central**.
    * Vignette de dernière valeur connue 2x1 indiquant la télémétrie **État du module**.
    * Vignette de dernière valeur connue 1x1 indiquant la télémétrie **Pulsation système**.
    * Vignette de dernière valeur connue1x1 indiquant la télémétrie **Caméras connectées**.

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Tableau de bord":::

1. Sélectionnez **Enregistrer**.

### <a name="publish-the-device-template"></a>Publier le modèle d’appareil

Avant d’ajouter un appareil à l’application, vous devez publier le modèle d’appareil :

1. Dans le modèle d’appareil **LVA Edge Gateway**, sélectionnez **Publier**.

1. Sur la page **Publier ce modèle d’appareil dans l’application**, sélectionnez **Publier**.

**LVA Edge Gateway** est maintenant disponible en tant que type d’appareil à utiliser sur la page **Appareils** de l’application.

## <a name="add-a-gateway-device"></a>Ajouter un appareil de passerelle

Pour ajouter un appareil **LVA Edge Gateway** à l’application :

1. Accédez à la page **Appareils** et sélectionnez le modèle d’appareil **LVA Edge Gateway**.

1. Sélectionnez **+Nouveau**.

1. Dans la boîte de dialogue **Créer un nouvel appareil**, remplacez le nom de l’appareil par *LVA Gateway 001*, puis l’ID de l’appareil par *lva-gateway-001*.

    > [!NOTE]
    > L’ID de l’appareil doit être unique dans l’application.

1. Sélectionnez **Create** (Créer).

L’état de l’appareil correspond à **Inscrit**.

### <a name="get-the-device-credentials"></a>Obtenir les informations d’identification de l’appareil

Vous avez besoin des informations d’identification qui permettent à l’appareil de se connecter à votre application IoT Central. Pour obtenir les informations d’identification de l’appareil :

1. Sur la page **Appareils**, sélectionnez l’appareil **lva-gateway-001** que vous avez créé.

1. Sélectionnez **Connecter**.

1. Sur la page **Connexion de l’appareil**, notez les valeurs *Étendue de l’ID*, **ID de l’appareil** et **Clé primaire** dans le fichier **scratchpad.txt**. Vous utiliserez ces valeurs plus tard.

1. Assurez-vous que la méthode de connexion est définie sur **Signature d'accès partagé**.

1. Sélectionnez **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de créer une application IoT Central à l’aide du modèle d’application **Analytique vidéo - Détection d’objets et de mouvements**, de créer un modèle d’appareil pour l’appareil de passerelle et d’ajouter un appareil de passerelle à l’application.

Si vous souhaitez tester l’application Analytique vidéo - Détection d’objets et de mouvements à l’aide des modules IoT Edge exécutant une machine virtuelle cloud avec des flux vidéo simulés :

> [!div class="nextstepaction"]
> [Créer une instance IoT Edge pour l’analytique vidéo (machine virtuelle Linux)](./tutorial-video-analytics-iot-edge-vm.md)

Si vous souhaitez tester l’application Analytique vidéo - Détection d’objets et de mouvements à l’aide des modules IoT Edge exécutant un appareil réel avec la caméra réelle **ONVIF** :

> [!div class="nextstepaction"]
> [Créer une instance IoT Edge pour l’analytique vidéo (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
