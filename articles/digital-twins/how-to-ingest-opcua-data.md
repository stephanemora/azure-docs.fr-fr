---
title: Ingestion de données OPC UA avec Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Étapes à suivre pour transmettre vos données Azure OPC UA dans Azure Digital Twins
author: danhellem
ms.author: dahellem
ms.date: 5/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: db195f845a9e2f19108b0e40d569d76939dd6b6b
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111373046"
---
# <a name="ingesting-opc-ua-data-with-azure-digital-twins"></a>Ingestion de données OPC UA avec Azure Digital Twins

[OPC UA (OPC Unified Architecture)](https://opcfoundation.org/about/opc-technologies/opc-ua/) est une architecture orientée services et indépendante de la plateforme pour l’espace de fabrication. Elle sert à récupérer les données de télémétrie d’appareils. 

La transmission des données d’un serveur OPC UA à Azure Digital Twins nécessite l’installation de plusieurs composants sur différents appareils, ainsi que la configuration de code et de paramètres personnalisés. 

Cet article explique comment connecter tous ces éléments pour intégrer vos nœuds OPC UA à Azure Digital Twins. Ces conseils vous permettront par la suite de développer vos propres solutions.

> [!NOTE]
> Cet article ne traite pas de la conversion des nœuds OPC UA en DTDL. Il couvre uniquement la transmission de la télémétrie de votre serveur OPC UA à un modèle Azure Digital Twins existant. Si vous souhaitez générer des modèles DTDL à partir de données OPC UA, visitez les dépôts [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) et [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL).

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, notez les prérequis suivants :
* **Télécharger l’exemple de dépôt** : cet article utilise un fichier de [modèle DTDL](concepts-models.md) et un corps de fonction Azure à partir du [dépôt GitHub OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins). Commencez par télécharger l’exemple de dépôt sur votre ordinateur. Vous pouvez sélectionner le bouton **Code** du dépôt pour le cloner ou le télécharger au format .zip sur votre ordinateur.

    :::image type="content" source="media/how-to-ingest-opcua-data/download-repo.png" alt-text="Capture d’écran du dépôt digital-twins-samples sur GitHub, mettant en évidence les étapes permettant de le cloner ou de télécharger le code." lightbox="media/how-to-ingest-opcua-data/download-repo.png":::
    
    Si vous téléchargez le dépôt au format .zip, veillez à le décompresser et à extraire les fichiers.
* **Télécharger Visual Studio** : cet article utilise Visual Studio pour publier une fonction Azure. Vous pouvez télécharger la dernière version de Visual Studio à partir de la page [Téléchargements de Visual Studio](https://visualstudio.microsoft.com/downloads/).

## <a name="architecture"></a>Architecture

Voici les composants qui seront inclus dans cette solution.

 :::image type="content" source="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png" alt-text="Illustration de l’architecture OPC UA vers Azure Digital Twins" lightbox="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png":::    

| Composant | Description |
| --- | --- |
| Serveur OPC UA | Serveur OPC UA [ProSys](https://www.prosysopc.com/products/opc-ua-simulation-server/) ou [Kepware](https://www.kepware.com/en-us/products/#KEPServerEX) pour simuler les données OPC UA. |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) | IoT Edge est un service IoT Hub qui est installé sur un appareil de passerelle Linux local. Son installation est nécessaire pour que le module OPC Publisher s’exécute et envoie des données à IoT Hub. |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | Il s’agit d’un module IoT Edge créé par l’équipe Azure Industrial IoT. Ce module se connecte à votre serveur OPC UA et envoie les données de nœud à Azure IoT Hub. |
| [Azure IoT Hub](../iot-hub/about-iot-hub.md) | OPC Publisher envoie la télémétrie OPC UA à Azure IoT Hub. À partir de là, vous pouvez traiter les données par le biais d’une fonction Azure et les transmettre à Azure Digital Twins. |
| Azure Digital Twins | Plateforme vous permettant de créer une représentation numérique de choses, de lieux, de processus métier et de personnes qui existent dans la vraie vie. |
| [Fonction Azure](../azure-functions/functions-overview.md) | Une fonction Azure personnalisée est utilisée pour associer la télémétrie transmise à Azure IoT Hub aux propriétés et jumeaux appropriés dans Azure Digital Twins. |

## <a name="set-up-edge-components"></a>Configurer les composants de périphérie

La première étape consiste à configurer les appareils et les logiciels à la périphérie. Voici les composants de périphérie que vous allez configurer, dans cet ordre :
1. [Serveur de simulation OPC UA](#set-up-opc-ua-server)
1. [Appareil IoT Hub et IoT Edge](#set-up-iot-edge-device)
1. [Appareil de passerelle](#set-up-gateway-device)

Cette section vous explique comment configurer chacun de ces composants. 

Pour plus d’informations sur l’installation de chaque composant, consultez les ressources suivantes :
* [Guide pas à pas pour installer OPC Publisher sur Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Installer IoT Edge sur Linux](../iot-edge/how-to-install-iot-edge.md) 
* [OPC Publisher sur GitHub](https://github.com/Azure/iot-edge-opc-publisher)
* [Configurer OPC Publisher](../iot-accelerators/howto-opc-publisher-configure.md)

### <a name="set-up-opc-ua-server"></a>Configurer le serveur OPC UA

Pour cet article, vous n’avez pas besoin d’accéder à des appareils physiques exécutant un serveur OPC UA réel. Au lieu de cela, vous pouvez installer le [serveur de simulation OPC UA Prosys](https://www.prosysopc.com/products/opc-ua-simulation-server/) gratuit sur une machine virtuelle Windows pour générer les données OPC UA. Cette section vous guide tout au long de la procédure de configuration.

Si vous disposez déjà d’un appareil OPC UA physique ou d’un autre serveur de simulation OPC UA que vous souhaitez utiliser, vous pouvez passer à la section [Configurer un appareil IOT Edge](#set-up-iot-edge-device).

#### <a name="create-windows-10-virtual-machine"></a>Créer une machine virtuelle Windows 10

Le logiciel Prosys nécessite une ressource virtuelle simple. Dans le [portail Azure](https://portal.azure.com), [créez une machine virtuelle Windows 10](../virtual-machines/windows/quick-create-portal.md) avec les spécifications suivantes :
* **Options de disponibilité** : Aucune redondance d’infrastructure requise
* **Image** : Windows 10 Professionnel, version 2004 - Gen2
* **Taille** : Standard_B1s - 1 processeur virtuel, 1 Gio de mémoire

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png" alt-text="Capture d’écran du portail Azure montrant l’onglet Informations de base utilisé lors de la configuration d’une machine virtuelle Windows." lightbox="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png":::

Votre machine virtuelle doit être accessible sur Internet. Pour simplifier cette procédure pas à pas, vous pouvez ouvrir tous les ports et attribuer une adresse IP publique à la machine virtuelle. Cette opération s’effectue sous l’onglet **Réseau** de la configuration de la machine virtuelle.

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-2.png" alt-text="Capture d’écran du portail Azure montrant l’onglet Réseau utilisé lors de la configuration d’une machine virtuelle Windows.":::

> [!WARNING]
> Il n’est pas recommandé d’ouvrir tous les ports et de les exposer à Internet pour les solutions de production, car cela peut présenter un risque pour la sécurité. Vous pouvez envisager d’autres stratégies de sécurité pour votre environnement.

Collectez la valeur **Adresse IP publique**, car vous en aurez besoin à l’étape suivante.
 
Terminez la configuration de la machine virtuelle.

#### <a name="install-opc-ua-simulation-software"></a>Installer le logiciel de simulation OPC UA

À partir de votre nouvelle machine virtuelle Windows, installez le [serveur de simulation OPC UA Prosys](https://www.prosysopc.com/products/opc-ua-simulation-server/).

Au terme du téléchargement et de l’installation, lancez le serveur. Le démarrage du serveur OPC UA peut prendre quelques instants. Le serveur doit indiquer l’état **Exécution en cours** quand il est prêt.

Copiez ensuite la valeur de l’**Adresse de connexion (UA TCP)** . Collez-la quelque part pour pouvoir l’utiliser par la suite. Dans la valeur collée, remplacez la partie nom de la machine de l’adresse par l’**Adresse IP publique** de la machine virtuelle collectée précédemment, comme ceci : 

`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`

Vous utiliserez cette valeur modifiée plus loin dans cet article.

Enfin, examinez les nœuds de simulation fournis par défaut avec le serveur. Pour cela, sélectionnez l’onglet **Objets** et développez les dossiers Objects::FolderType et Simulation::FolderType. Les nœuds de simulation apparaissent, chacun avec leur propre valeur `NodeId` unique. 

Capturez les valeurs `NodeId` des nœuds simulés que vous souhaitez publier. Vous aurez besoin de ces ID plus loin dans cet article pour simuler des données à partir de ces nœuds.

> [!TIP]
> Vérifiez que le serveur OPC UA est accessible en suivant la procédure « Vérifier que le service OPC UA est en cours d’exécution et accessible » dans le [guide pas à pas pour installer OPC Publisher sur Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher).

#### <a name="verify-completion"></a>Vérifier l’achèvement des tâches

Dans cette section, vous avez configuré le serveur OPC UA pour simuler des données. Vérifiez que les tâches suivantes ont bien été effectuées :

> [!div class="checklist"]
> * Le serveur de simulation Prosys est configuré et en cours d’exécution.
> * Vous avez copié l’adresse de connexion TCP UA (`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`).
> * Vous avez capturé la liste des `NodeId` pour les nœuds simulés que vous voulez publier. 

### <a name="set-up-iot-edge-device"></a>Configurer un appareil IoT Edge

Dans cette section, vous allez configurer une instance IoT Hub et un appareil IoT Edge. 

Pour commencer, [créez une instance Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md). Pour cet article, vous pouvez créer une instance dans le niveau **F1 - Gratuit**.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-hub.png" alt-text="Capture d’écran du portail Azure montrant les propriétés d’une instance IoT Hub.":::

Après avoir créé l’instance Azure IoT Hub, sélectionnez **IOT Edge** dans le menu de navigation gauche de l’instance, puis **Ajouter un appareil IoT Edge**.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-1.png" alt-text="Capture d’écran de l’ajout d’un appareil IoT Edge dans le portail Azure.":::

Suivez les invites pour créer un appareil. 

Une fois votre appareil créé, copiez la valeur de **Chaîne de connexion principale** ou de **Chaîne de connexion secondaire**. Vous en aurez besoin plus tard quand vous configurerez l’appareil de périphérie.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-2.png" alt-text="Capture d’écran du portail Azure montrant les chaînes de connexion de l’appareil IoT Edge.":::

#### <a name="verify-completion"></a>Vérifier l’achèvement des tâches

Dans cette section, vous avez configuré IoT Edge et IoT Hub en vue de créer un appareil de passerelle. Vérifiez que les tâches suivantes ont bien été effectuées :
> [!div class="checklist"]
> * L’instance IoT Hub a été créée.
> * L’appareil IoT Edge a été provisionné.

### <a name="set-up-gateway-device"></a>Configurer un appareil de passerelle

Pour obtenir les données de votre serveur OPC UA dans IoT Hub, vous avez besoin d’un appareil qui exécute IoT Edge avec le module OPC Publisher. OPC Publisher écoute ensuite les mises à jour des nœuds OPC UA et publie la télémétrie dans IoT Hub au format JSON.

#### <a name="create-ubuntu-server-virtual-machine"></a>Créer une machine virtuelle Ubuntu Server

Dans le [portail Azure](https://portal.azure.com), créez une machine virtuelle Ubuntu Server avec les spécifications suivantes :
* **Options de disponibilité** : Aucune redondance d’infrastructure requise
* **Image** : Ubuntu Server 18.04 LTS - Gen1
* **Taille** : Standard_B1ms - 1 processeur virtuel, 2 Gio de mémoire
    - La taille par défaut (Standard_b1s - 1 processeur virtuel, 1 Gio de mémoire) est trop lente pour RDP. Le passage à 2 Gio de mémoire offre une meilleure expérience RDP.

:::image type="content" source="media/how-to-ingest-opcua-data/ubuntu-virtual-machine.png" alt-text="Capture d’écran du portail Azure montrant les paramètres de la machine virtuelle Ubuntu.":::

> [!NOTE]
> Si vous choisissez d’utiliser le protocole RDP sur votre machine virtuelle Ubuntu, vous pouvez suivre les instructions de la page [Installer et configurer xrdp pour utiliser le Bureau à distance avec Ubuntu](../virtual-machines/linux/use-remote-desktop.md).

#### <a name="install-iot-edge-container"></a>Installer le conteneur IoT Edge

Suivez les instructions pour [installer IOT Edge sur Linux](../virtual-machines/linux/use-remote-desktop.md).

Une fois l’installation terminée, exécutez la commande suivante pour vérifier l’état de votre installation :

```bash
admin@gateway:~$ sudo iotedge check
```

Cette commande exécute plusieurs tests pour vérifier que votre installation est prête.

#### <a name="install-opc-publisher-module"></a>Installer le module OPC Publisher

Ensuite, installez le module OPC Publisher sur votre appareil de passerelle. 

Commencez par récupérer le module dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.iotedge-opc-publisher).

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-1.png" alt-text="Capture d’écran d’OPC Publisher dans la Place de marché Azure.":::

Ensuite, suivez les étapes d’installation décrites dans le [dépôt GitHub OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) pour installer le module sur votre machine virtuelle Ubuntu.

Dans l’étape de [spécification des options de création de conteneur](https://github.com/Azure/iot-edge-opc-publisher#specifying-container-create-options-in-the-azure-portal), veillez à ajouter le code JSON suivant :

```JSON
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=/appdata/publishednodes.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-2.png" alt-text="Capture d’écran des options de création de conteneur dans OPC Publisher.":::

>[!NOTE]
>Les options de création ci-dessus doivent fonctionner dans la plupart des cas sans aucun changement. Toutefois, si vous utilisez un appareil de passerelle auquel les instructions fournies jusqu’à présent dans cet article ne s’appliquent pas, vous devrez peut-être ajuster les paramètres à votre situation.

Suivez le reste des invites pour créer le module. 

Après environ 15 secondes, vous pouvez exécuter la commande `iotedge list` sur votre appareil de passerelle pour lister tous les modules en cours d’exécution sur votre appareil IOT Edge. Le module OPCPublisher doit apparaître comme étant opérationnel.

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-list.png" alt-text="Capture d’écran des résultats de la liste iotedge.":::

Enfin, accédez au répertoire `/iiotedge` et créez un fichier *publishednodes.json*. Les ID dans le fichier doivent correspondre aux valeurs `NodeId` que vous avez [collectées précédemment à partir du serveur OPC](#install-opc-ua-simulation-software). Votre fichier doit ressembler à ceci :

```JSON
[
    {
        "EndpointUrl": "opc.tcp://20.185.195.172:53530/OPCUA/SimulationServer",
        "UseSecurity": false,
        "OpcNodes": [
            {
                "Id": "ns=3;i=1001"
            },
            {
                "Id": "ns=3;i=1002"
            },
            {
                "Id": "ns=3;i=1003"
            },
            {
                "Id": "ns=3;i=1004"
            },
            {
                "Id": "ns=3;i=1005"
            },
            {
                "Id": "ns=3;i=1006"
            }
        ]
    }
]
```

Enregistrez vos modifications dans le fichier *publishednodes.json*.

Ensuite, exécutez la commande suivante :

```bash
sudo iotedge logs OPCPublisher -f
```

La commande génère la sortie des journaux OPC Publisher. Si tout est configuré et fonctionne correctement, un résultat semblable au suivant doit s’afficher :

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-logs.png" alt-text="Capture d’écran des journaux iotedge dans le terminal, avec une colonne de champs d’informations de diagnostic à gauche et une colonne de valeurs à droite.":::

Les données doivent à présent être transmises d’un serveur OPC UA à votre hub IoT.

Pour superviser les messages transmis au hub Azure IoT, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az iot hub monitor-events -n <iot-hub-instance> -t 0
```

> [!TIP]
> Essayez d’utiliser [Azure IOT Explorer](../iot-pnp/howto-use-iot-explorer.md) pour superviser les messages IOT Hub.

#### <a name="verify-completion"></a>Vérifier l’achèvement des tâches

Dans cette section, vous avez configuré un appareil de passerelle exécutant IoT Edge qui reçoit les données du serveur OPC UA. Vérifiez que les tâches suivantes ont bien été effectuées :
> [!div class="checklist"]
> * La machine virtuelle Ubuntu Server a été créée.
> * IoT Edge a été installé et se trouve sur la machine virtuelle Ubuntu.
> * Le module OPC Publisher a été installé.
> * Le fichier *publishednodes.json* a été créé et configuré.
> * Le module OPC Publisher est en cours d’exécution et les données de télémétrie sont transmises à IoT Hub.

À l’étape suivante, vous obtiendrez ces données de télémétrie dans Azure Digital Twins.

## <a name="set-up-azure-digital-twins"></a>Configurer Azure Digital Twins

Maintenant que vos données sont transmises du serveur OPC UA à Azure IoT Hub, l’étape suivante consiste à installer et à configurer Azure Digital Twins. 

Pour cet exemple, vous allez utiliser un seul modèle et une seule instance de jumeau pour faire correspondre les propriétés sur le serveur de simulation. 

>[!TIP]
>Si vous souhaitez voir un scénario plus complexe avec plus de modèles et de jumeaux, consultez l’exemple de chocolaterie dans le [dépôt GitHub OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins).

### <a name="create-azure-digital-twins-instance"></a>Créer une instance Azure Digital Twins

Commencez par déployer une nouvelle instance Azure Digital Twins en suivant les instructions du [guide pratique pour configurer une instance et l’authentification](how-to-set-up-instance-portal.md).

### <a name="upload-model-and-create-twin"></a>Charger le modèle et créer le jumeau

Ensuite, ajoutez un modèle et un jumeau à votre instance. Le fichier de modèle que vous allez charger sur l’instance fait partie de l’exemple de projet que vous avez téléchargé dans la section [Prérequis](#prerequisites), à l’emplacement *Simulation Example/simulation-dtdl-model.json*.

Vous pouvez utiliser [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) pour charger le modèle Simulation et créer un jumeau appelé **simulation-1**.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-digital-twins-explorer.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer avec le modèle Simulation et le jumeau simulation-1.":::

### <a name="verify-completion"></a>Vérifier l’achèvement des tâches

Dans cette section, vous avez configuré une instance Azure Digital Twins contenant un modèle et un jumeau. Vérifiez que les tâches suivantes ont été effectuées :
> [!div class="checklist"]
> * L’instance Azure Digital Twins a été déployée.
> * Le modèle de simulation a été chargé dans l’instance Azure Digital Twins.
> * Le jumeau simulation-1 a été créé à partir du modèle Simulation.

## <a name="set-up-azure-function"></a>Configurer une fonction Azure

Maintenant que les nœuds OPC UA envoient des données dans IoT Hub et qu’une instance Azure Digital Twins est prête à recevoir les données, vous devez mapper et enregistrer les données dans le jumeau et les propriétés appropriés dans Azure Digital Twins. Dans cette section, vous allez configurer ceci à l’aide d’une fonction Azure et d’un fichier *opcua-mapping.json*.

Le flux de données de cette section comprend les étapes suivantes :

1. Une fonction Azure utilise un abonnement à un événement pour recevoir les messages en provenance d’IoT Hub.
1. La fonction Azure traite chaque événement de télémétrie qui arrive. Elle extrait `NodeId` de l’événement et le recherche parmi les éléments du fichier *opcua-mapping.json*. Le fichier mappe chaque `NodeId` à un `twinId` et une propriété dans Azure Digital Twins où la valeur du nœud doit être enregistrée.
1. La fonction Azure génère le document de correctif approprié pour mettre à jour le jumeau numérique correspondant, puis exécute la commande de mise à jour de la propriété de jumeau.

### <a name="create-opcua-mappingjson-file"></a>Créer un fichier opcua-mapping.json

Pour commencer, créez votre fichier *opcua-mapping.json*. Commencez avec un fichier JSON vide et renseignez les entrées qui mappent les valeurs `NodeId` aux valeurs et propriétés `twinId` dans Azure Digital Twins, en fonction de l’exemple et du schéma ci-dessous. Vous devrez créer une entrée de mappage pour chaque `NodeId`.

```JSON
[
    {
        "NodeId": "1001",
        "TwinId": "simulation",
        "Property": "Counter",
        "ModelId": "dtmi:com:microsoft:iot:opcua:simulation;1"
    },
    ...
]
```

Voici le schéma pour les entrées :

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| NodeId | Valeur du nœud OPC UA. Par exemple : ns=3;i={value} | ✔ |
| TwinId | TwinId ($dtId) du jumeau pour lequel vous souhaitez enregistrer la valeur de télémétrie | ✔ |
| Propriété | Nom de la propriété sur le jumeau pour enregistrer la valeur de télémétrie | ✔ |
| ModelId  | ModelId utilisé pour créer le double si le TwinId n’existe pas |  |

> [!TIP]
> Pour obtenir un exemple complet d’un fichier *opcua-mapping.json*, consultez le [dépôt GitHub OPC UA vers Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins).

Quand vous avez terminé d’ajouter des mappages, enregistrez le fichier.

Maintenant que vous avez votre fichier de mappage, vous devez le stocker dans un emplacement auquel la fonction Azure a accès. Vous pouvez par exemple utiliser [Stockage Blob Azure](../storage/blobs/storage-blobs-overview.md).

Suivez les instructions pour [créer un conteneur de stockage](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container), puis importez le fichier *opcua-mapping.json* dans le conteneur. Vous pouvez également réaliser des événements de gestion du stockage avec l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). 

Ensuite, créez une [signature d’accès partagé pour le conteneur](../storage/common/storage-sas-overview.md) et enregistrez cette URL. Plus tard, vous fournirez l’URL à la fonction Azure pour qu’elle puisse accéder au fichier stocké.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-storage-explorer.png" alt-text="Capture d’écran de l’Explorateur Stockage Azure avec la boîte de dialogue permettant de créer un jeton SAS.":::

### <a name="publish-azure-function"></a>Publier une fonction Azure

Dans cette section, vous allez publier une fonction Azure que vous avez téléchargée dans [Prérequis](#prerequisites) pour traiter les données OPC UA et mettre à jour Azure Digital Twins.

#### <a name="step-1-open-the-function-in-visual-studio"></a>Étape 1 : Ouvrir la fonction dans Visual Studio

Accédez au projet [OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) téléchargé sur votre ordinateur local, puis au dossier *Azure Functions/OPCUAFunctions*. Ouvrez la solution **OPCUAFunctions.sln** dans Visual Studio.

#### <a name="step-2-publish-the-function"></a>Étape 2 : Publier la fonction

Publiez le projet de fonction dans une application de fonction dans Azure.

Pour obtenir des instructions sur la façon de procéder, consultez la section [Publier l’application de fonction dans Azure](how-to-create-azure-function.md#publish-the-function-app-to-azure) de l’article *Guide pratique : configurer une fonction pour le traitement des données*.

#### <a name="step-3-configure-the-function-app"></a>Étape 3 : Configurer l’application de fonction

**Attribuez un rôle d’accès** à la fonction et **configurez les paramètres d’application** pour autoriser l’accès à votre instance Azure Digital Twins. Pour obtenir des instructions sur la façon de procéder, consultez la section [Configurer l’accès de sécurité pour l’application de fonction](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) de l’article *Guide pratique : Configurer une fonction pour le traitement des données*.

#### <a name="step-4-add-application-settings"></a>Étape 4 : Ajouter des paramètres d’application

Vous devez également ajouter des paramètres d’application pour configurer entièrement votre environnement. Accédez au [portail Azure](https://portal.azure.com) et accédez à la fonction Azure que vous venez de créer en recherchant son nom dans la barre de recherche du portail.

Sélectionnez Configuration dans le menu de navigation de gauche de la fonction. Utilisez le bouton **+ Nouveau paramètre d’application** pour commencer à créer des paramètres.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-1.png" alt-text="Capture d’écran de l’ajout de paramètres d’application à une fonction Azure dans le portail Azure.":::

Vous devez créer trois paramètres d’application :

| Paramètre | Description | Obligatoire |
| --- | --- | --- |
| ADT_SERVICE_URL | URL de votre instance Azure Digital Twins. Exemple : `https://example.api.eus.digitaltwins.azure.net` | ✔ |
| JSON_MAPPINGFILE_URL | URL de la signature d’accès partagé pour opcua-mapping.json | ✔ |
| LOG_LEVEL | Verbosité au niveau journal. La valeur par défaut est 100. Le mode détaillé (verbose) est 300. | |

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-2.png" alt-text="Capture d’écran des paramètres d’application pour une fonction Azure dans le portail Azure. Les paramètres ci-dessus ont été ajoutés.":::

> [!TIP]
> Définissez le paramètre d’application `LOG_LEVEL` sur la fonction avec la valeur 300 pour une expérience de journalisation plus détaillée. 

### <a name="create-event-subscription"></a>Créer un abonnement aux événements

Enfin, créez un abonnement à un événement pour connecter votre application de fonction et la fonction ProcessOPCPublisherEventsToADT à votre hub IoT. L’abonnement à un événement est nécessaire pour transmettre les données de l’appareil de passerelle au hub IoT par le biais de la fonction, qui met ensuite à jour Azure Digital Twins.

Pour obtenir des instructions, suivez les mêmes étapes que celles utilisées pour [connecter le hub IOT à la fonction Azure](tutorial-end-to-end.md#connect-the-iot-hub-to-the-azure-function) dans le *tutoriel Azure Digital Twins sur la connexion d’une solution de bout en bout*.

L’abonnement à un événement a **Fonction Azure** comme type de point de terminaison et **ProcessOPCPublisherEventsToADT** comme point de terminaison.

:::image type="content" source="media/how-to-ingest-opcua-data/event-subscription.png" alt-text="Capture d’écran du portail Azure montrant la création d’un abonnement à un événement.":::

Après cette étape, tous les composants requis doivent être installés et en cours d’exécution. Les données doivent être transmises de votre serveur de simulation OPC UA, par le biais d’Azure IoT Hub, à votre instance Azure Digital Twins. 

La section suivante fournit des commandes Azure CLI que vous pouvez exécuter pour superviser les événements et vérifier que tout fonctionne correctement.

### <a name="verify-and-monitor"></a>Vérifier et superviser

Les commandes de cette section peuvent être exécutées dans [Azure Cloud Shell](https://shell.azure.com) ou dans une [fenêtre Azure CLI locale](/cli/azure/install-azure-cli).

Exécutez cette commande pour superviser les événements IoT Hub :
```azurecli-interactive
az iot hub monitor-events -n <IoT-hub-name> -t 0
```

Exécutez cette commande pour superviser le traitement des événements de la fonction Azure :
```azurecli-interactive
az webapp log tail –name <function-name> --resource-group <resource-group-name>
```

Enfin, vous pouvez utiliser Azure Digital Twins Explorer pour superviser manuellement les mises à jour des propriétés des jumeaux. 

:::image type="content" source="media/how-to-ingest-opcua-data/adt-explorer-2.png" alt-text="Capture d’écran de l’utilisation d’Azure Digital Twins Explorer pour superviser les mises à jour des propriétés des jumeaux":::

### <a name="verify-completion"></a>Vérifier l’achèvement des tâches

Dans cette section, vous avez configuré une fonction Azure pour connecter les données OPC UA à Azure Digital Twins. Vérifiez que les tâches suivantes ont été effectuées :
> [!div class="checklist"]
> * Le fichier *opcua-mapping.json* a été créé et importé dans un conteneur de stockage d’objets blob. 
> * L’exemple de fonction ProcessOPCPublisherEventsToADT a été publié dans une application de fonction dans Azure.
> * Trois nouveaux paramètres d’application ont été ajoutés à l’application Azure Functions.
> * Un abonnement à un événement a été créé pour envoyer les événements IoT Hub à l’application de fonction.
> * Des commandes Azure CLI ont été utilisées pour vérifier le dernier flux de données

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez configuré un flux de données complet pour transmettre les données d’un serveur OPC UA simulé à Azure Digital Twins, où une propriété est mise à jour sur un jumeau numérique.

Ensuite, utilisez les ressources suivantes pour en savoir plus sur les outils et processus pris en charge qui ont été utilisés dans cet article :

* [Guide pas à pas pour installer OPC Publisher sur Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Installer IoT Edge sur Linux](../iot-edge/how-to-install-iot-edge.md) 
* [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher)
* [Configurer OPC Publisher](../iot-accelerators/howto-opc-publisher-configure.md)
* [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 
* [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL)
