---
title: Définir un nouveau type d’appareil Azure IoT Edge dans Azure IoT Central | Microsoft Docs
description: Ce tutoriel vous montre comment créer un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central en votre qualité de créateur. Vous définissez la télémétrie, l’état, les propriétés et les commandes pour votre type d’appareil.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892025"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Didacticiel : Définir un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel vous montre comment utiliser un modèle d’appareil pour définir un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central en votre qualité de créateur. 

Pour obtenir une vue d’ensemble d’Azure IoT Edge, [consultez cet article](overview-iot-central.md). 

Azure IoT Edge est constitué de trois composants :
* Les **modules IoT Edge** sont des conteneurs qui exécutent les services Azure, les services tiers ou votre propre code. Ils sont déployés sur des appareils IoT Edge et s’exécutent localement sur ces appareils.
* Le **runtime IoT Edge** s’exécute sur chaque appareil IoT Edge et gère les modules déployés sur chaque appareil.
* Une **interface basée sur le cloud** permet de superviser et de gérer des appareils IoT Edge à distance. IoT Central est l’interface cloud.

Un appareil **Azure IoT Edge** peut être un appareil de passerelle avec, en aval, des appareils qui se connectent à l’appareil Azure IoT Edge. Les modèles de connectivité des appareils en aval seront abordés dans ce tutoriel.

Un **modèle d’appareil** définit les fonctionnalités de votre appareil et de vos modules IoT Edge. Parmi ces fonctionnalités figurent l’envoi des données de télémétrie par le module, les propriétés de module et les commandes auxquelles un module répond.

Dans ce tutoriel, vous allez créer un modèle d’appareil **Capteur environnemental**. Un appareil capteur environnemental :

* Envoie des données de télémétrie comme la température.
* Répond à des propriétés accessibles en écriture quand elles sont mises à jour dans le cloud, comme l’intervalle d’envoi des données de télémétrie.
* Répond à des commandes, par exemple, de réinitialisation de la température.

Dans ce tutoriel, vous allez aussi créer un modèle d’appareil **Environment Gateway** (Passerelle d’environnement). Un appareil de passerelle environnemental :

* Envoie des données de télémétrie comme la température.
* Répond à des propriétés accessibles en écriture quand elles sont mises à jour dans le cloud, comme l’intervalle d’envoi des données de télémétrie.
* Répond à des commandes, par exemple, de réinitialisation de la température.
* Autorise les relations à d’autres modèles de fonctionnalité d’appareil.


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’appareil Azure IoT Edge.
> * Charger un manifeste de déploiement.
> * Créer des fonctionnalités, notamment la télémétrie, des propriétés et des commandes pour chaque module.
> * Définir une visualisation pour les données de télémétrie des modules.
> * Ajouter des relations aux modèles d’appareil en aval.
> * Publier votre modèle d’appareil.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer d’une application Azure IoT Central. Suivez ce guide de démarrage rapide pour [créer une application Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Relations des appareils en aval avec la passerelle et les modules

Les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via le module $edgeHub. Cet appareil Azure IoT Edge devient une passerelle transparente dans ce scénario.

![Page d’application Central](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via un module personnalisé. Dans le scénario ci-dessous, les appareils en aval se connectent via un module personnalisé Modbus, et les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via le module $edgeHub.

![Page d’application Central](./media/tutorial-define-edge-device-type/gateway-module.png)

Les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via un module personnalisé. Dans le scénario ci-dessous, les appareils en aval se connectent via un module personnalisé Modbus. 

![Page d’application Central](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via plusieurs modules personnalisés. Dans le scénario ci-dessous, les appareils en aval se connectent via un module personnalisé Modbus. Le module personnalisé BLE et les appareils en aval peuvent se connecter à l’appareil de passerelle Azure IoT Edge via le module $edgeHub. 

![Page d’application Central](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Créer un modèle

En tant que créateur, vous pouvez créer et modifier des modèles d’appareils Azure IoT Edge dans votre application. Une fois que vous avez publié un modèle d’appareil, vous pouvez connecter des appareils réels qui implémenteront ce modèle.

### <a name="select-device-template-type"></a>Sélectionner un type de modèle d’appareil 

Pour ajouter un nouveau modèle d’appareil à votre application, accédez à la page **Modèles d’appareil**. Pour ce faire, sélectionnez l’onglet **Device Templates** (Modèles d’appareils) dans le volet de gauche.

![Page d’application Central](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Cliquez sur **+ New** (Nouveau) pour commencer à créer un modèle d’appareil.

![Device Templates (Modèles d’appareil) – New (Nouveau)](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Vous accéder alors à la page de sélection du type de modèle d’appareil. Sélectionnez la vignette **Azure IoT Edge**, puis cliquez sur le bouton **Next: Customize** (Suivant : Personnaliser) au bas de l’écran.

![Sélection de modèles d’appareil – Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personnaliser un modèle d’appareil

Azure IoT Edge vous permet de déployer et de gérer la logique métier sous forme de modules. Les **modules Azure IoT Edge** sont la plus petite unité de calcul gérée par IoT Edge. Ils peuvent contenir des services Azure (par exemple Azure Stream Analytics) ou du code propre à votre solution. Pour comprendre comment les modules sont développés, déployés et gérés, consultez [Modules IoT Edge](../../iot-edge/iot-edge-modules.md).

À un niveau élevé, un manifeste de déploiement est une liste des jumeaux de module configurés avec leurs propriétés souhaitées. Un manifeste de déploiement indique à un appareil IoT Edge (ou à un groupe d’appareils) les modules à installer et comment les configurer. Les manifestes de déploiement incluent les propriétés souhaitées pour chaque jumeau de module. Les appareils IoT Edge rapportent les propriétés indiquées pour chaque module.

Utilisez Visual Studio Code pour créer un manifeste de déploiement. Consultez la documentation pour savoir comment créer un [manifeste de déploiement](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Voici un exemple de manifeste de déploiement de base à un seul module à utiliser dans ce tutoriel. Copiez le code JSON ci-dessous et enregistrez-le sous forme de fichier .json. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Charger un manifeste de déploiement Azure IoT Edge**

Cliquez sur le bouton **Browse** (Parcourir). 

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Si vous envisagez de créer un modèle d’appareil de passerelle Azure IoT Edge, veillez à cocher la case **Gateway device with downstream devices** (Appareil de passerelle avec des appareils en aval).

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Une boîte de dialogue de sélection de fichier s’affiche. Sélectionnez le fichier manifeste de déploiement, puis cliquez sur le bouton **Open** (Ouvrir).

Le fichier manifeste de déploiement est validé par rapport à un schéma. Une fois la validation terminée, cliquez sur le bouton **Review** (Vérifier).

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Voici le déroulement du cycle de vie d’un manifeste de déploiement dans IoT Central.

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

La page de vérification s’affiche avec les détails du manifeste de déploiement. La liste des modules du manifeste de déploiement s’affiche dans la page de vérification. Dans ce tutoriel, le module SimulatedTemperatureSensor est listé. Cliquez sur le bouton **Créer**.

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Si vous sélectionnez un appareil de passerelle, voici la page de vérification qui s’affiche.

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Quand vous créez un modèle d’appareil, une boucle de création s’affiche pour le modèle d’appareil qui est créé dans IoT Central.

Le modèle d’appareil est créé avec les modèles de fonctionnalité de module. Dans ce tutoriel, le modèle de fonctionnalité de module créé est SimulatedTemperatureSensor. 

Remplacez le titre du modèle d’appareil par Environment Sensor Device Template (Modèle d’appareil Capteur environnemental).

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

La modélisation de la fonctionnalité Plug-and-Play de l’appareil Azure IoT Edge s’effectue comme suit :
* À chaque modèle d’appareil Azure IoT Edge correspond un **modèle de fonctionnalité d’appareil**.
* Un **modèle de fonctionnalité de module** est généré pour chaque module personnalisé listé dans le manifeste de déploiement.
* Une **relation** est établie entre chaque modèle de fonctionnalité de module et un modèle de fonctionnalité d’appareil.
* Un modèle de fonctionnalité de module implémente des **interfaces de module**.
* Chaque interface de module contient les éléments suivants :
   - Télémétrie
   - properties
   - Commandes

![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Ajouter des fonctionnalités au modèle de fonctionnalité de module**

Voici un exemple de sortie du module SimulatedTemperatureSensor :
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Ajoutez des fonctionnalités au module SimulatedTemperatureSensor qui refléteront le code JSON ci-dessus. 

* Cliquez sur **Manage** (Gérer) pour l’interface du modèle de fonctionnalité de module SimulatedTemperatureSensor. Cliquez sur **Add Capability** (Ajouter une fonctionnalité). 

    ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Ajoutez l’ordinateur comme type d’objet, car il s’agit d’un type complexe.
  
    ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Cliquez sur **Define** (Définir). Dans la fenêtre modale contextuelle, remplacez le nom d’objet par machine, créez les propriétés temperature (température) et pressure (pression), puis cliquez sur **Apply** (Appliquer).
  
    ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Ajoutez ambient (ambiant) comme type d’objet, car il s’agit d’un type complexe.

    Cliquez sur **Define** (Définir). Dans la fenêtre modale contextuelle, remplacez le nom d’objet par ambiant (ambiant), créez les propriétés temperature (température) et humidity (humidité), puis cliquez sur **Apply** (Appliquer).
  
    ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Ajoutez timeCreated comme type DateTime, puis cliquez sur **Save** (Enregistrer).
  
    ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Ajouter des relations

Si vous avez sélectionné l’appareil Azure IoT Edge comme appareil de passerelle, vous pouvez ajouter des relations en aval aux modèles de fonctionnalité d’appareil pour les appareils que vous prévoyez de connecter à l’appareil de passerelle.
  
  ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Une relation peut être ajoutée au niveau d’un appareil ou d’un module.
  
  ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Vous pouvez sélectionner un modèle de fonctionnalité d’appareil en aval ou sélectionner un astérisque. 
  
  ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Pour ce tutoriel, nous allons sélectionner un astérisque, ce qui signifie que toute relation en aval sera autorisée. Cliquez sur **Enregistrer**.

  ![Device Model (Modèle d’appareil) – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Ajouter des propriétés du cloud

Un modèle d’appareil peut inclure des propriétés du cloud. Les propriétés du cloud existent uniquement dans l’application IoT Central, et ne sont jamais envoyées à un appareil ni reçues à partir d’un appareil.

1. Sélectionnez **Propriétés du cloud**, puis **+ Ajouter une propriété cloud**. Utilisez les informations du tableau suivant pour ajouter une propriété du cloud à votre modèle d’appareil.

    | Nom d’affichage      | Type de sémantique | Schéma |
    | ----------------- | ------------- | ------ |
    | Dernière date de service | Aucun          | Date   |
    | Nom du client     | Aucun          | Chaîne |

2. Cliquez sur **Enregistrer** pour enregistrer vos modifications :

  
    ![Propriétés cloud – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Ajouter des personnalisations

Utilisez des personnalisations quand vous devez modifier une interface ou ajouter des fonctionnalités propres à IoT Central à une capacité qui ne vous oblige pas à créer une version de votre modèle de capacité d’appareil. Vous pouvez personnaliser les champs quand le modèle de capacité est à l’état Brouillon ou Publié. Vous pouvez uniquement personnaliser les champs qui n’interrompent pas la compatibilité de l’interface. Vous pouvez par exemple :

- Personnaliser le nom complet et les unités d’une capacité.
- Ajouter une couleur par défaut à utiliser quand la valeur apparaît sur un graphique.
- Spécifier les valeurs initiales, minimales et maximales d’une propriété.

Vous ne pouvez pas personnaliser le nom ou le type de la capacité. Cliquez sur **Enregistrer**.
  
![Personnalisations – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Créer des vues

En tant que générateur, vous pouvez personnaliser l’application pour présenter des informations pertinentes sur l’appareil capteur environnemental à un opérateur. Vos personnalisations permettent à l’opérateur de gérer l’appareil capteur environnemental raccordé à l’application. Vous pouvez créer deux types de vues permettant à un opérateur d’interagir avec des appareils :

* Formulaires pour voir et modifier les propriétés de l’appareil et du cloud
* Tableaux de bord pour visualiser les appareils

### <a name="configure-a-view-to-visualize-devices"></a>Configurer une vue pour visualiser des appareils

Un tableau de bord d’appareil permet à un opérateur de visualiser un appareil à l’aide de graphiques et de métriques. En tant que générateur, vous pouvez définir les informations qui s’affichent sur le tableau de bord d’un appareil. Vous pouvez définir plusieurs tableaux de bord pour les appareils. Pour créer un tableau de bord afin de visualiser la télémétrie du capteur environnemental, sélectionnez **Vues**, puis **Visualisation de l’appareil** :

  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Ambient Telemetry et Machine Telemetry sont des objets complexes. Pour créer des graphiques, procédez comme suit :

Faites glisser Ambient Telemetry et sélectionnez Line chart (Graphique en courbes). 
  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Cliquez sur l’icône Configure (Configurer), sélectionnez temperature (température) et humidity (humidité) pour visualiser les données, puis cliquez sur le bouton **Update configuration** (Mettre à jour la configuration). 
  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Cliquez sur **Enregistrer** pour enregistrer votre vue :

Vous pouvez ajouter d’autres vignettes qui affichent d’autres propriétés ou valeurs de télémétrie. Vous pouvez également ajouter du texte statique, des liens et des images. Pour déplacer ou redimensionner une vignette sur le tableau de bord, déplacez le pointeur de souris sur la vignette, puis faites-la glisser vers un nouvel emplacement ou redimensionnez-la.
  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Ajouter un formulaire d’appareil

Un formulaire d’appareil permet à un opérateur de modifier les propriétés de l’appareil et les propriétés du cloud accessibles en écriture. En tant que générateur, vous pouvez définir plusieurs formulaires et choisir les propriétés de l’appareil et du cloud à afficher sur chaque formulaire. Vous pouvez également afficher des propriétés d’appareil en lecture seule sur un formulaire.

Pour créer un formulaire afin de voir et de modifier les propriétés d’un capteur environnemental

Accédez à **Vues** dans le modèle **Capteur environnemental**. Sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.
  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Entrez le nom de formulaire **Propriétés du capteur environnemental**.

Faites glisser les propriétés du cloud **Nom du client** et **Dernière date de service** sur la section existante du formulaire.
  
![Vues – Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Cliquez sur **Enregistrer** pour enregistrer votre vue.

### <a name="generate-default-views"></a>Générer des vues par défaut

La fonctionnalité de génération de vues par défaut n’est pas prise en charge pour les modèles Azure IoT Edge. 

## <a name="publish-device-template"></a>Publier le modèle d’appareil

Avant de pouvoir créer un capteur environnemental simulé ou connecter un capteur environnemental réel, vous devez publier votre modèle d’appareil.

Pour publier un modèle d’appareil

1. Accédez à votre modèle d’appareil à partir de la page **Modèles d’appareil**.

2. Sélectionnez **Publier**.
  
    ![Vues – Publier](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Dans la boîte de dialogue **Publier un modèle d’appareil**, choisissez **Publier** :
  
    ![Vues – Publier](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Une fois qu’un modèle d’appareil a été publié, il est visible dans la page **Appareils** et par l’opérateur. Dans un modèle d’appareil publié, vous ne pouvez pas modifier un modèle de capacité d’appareil sans créer de nouvelle version. En revanche, vous pouvez effectuer des mises à jour des propriétés du cloud, des personnalisations et des vues dans un modèle d’appareil publié sans gestion des versions. Après avoir apporté des modifications, sélectionnez **Publier** pour envoyer ces modifications à votre opérateur.
  
![Vues – Publier](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Créer un nouvel appareil Edge comme modèle d’appareil de nœud terminal.
* Générer des modules à partir d’un manifeste de déploiement chargé.
* Ajouter des propriétés et des données de télémétrie de type complexe.
* Créer des propriétés du cloud.
* Créer des personnalisations.
* Définir une visualisation pour la télémétrie de l’appareil.
* Publier votre modèle d’appareil Edge.

Maintenant que vous avez créé un modèle d’appareil dans votre application Azure IoT Central, nous vous suggérons d’effectuer l’étape suivante :

> [!div class="nextstepaction"]
> [Connecter l’appareil](./tutorial-connect-pnp-device.md)
