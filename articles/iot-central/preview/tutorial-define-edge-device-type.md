---
title: Tutoriel - Définir un nouveau type d’appareil Azure IoT Edge dans Azure IoT Central
description: Ce tutoriel vous montre comment créer un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central en votre qualité de créateur. Vous définissez la télémétrie, l’état, les propriétés et les commandes pour votre type d’appareil.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 00ab92effbc5167d8bca3242e55d566c71209ac4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979085"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Didacticiel : Définir un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel vous montre comment utiliser un modèle d’appareil pour définir un nouveau type d’appareil Azure IoT Edge dans votre application Azure IoT Central en votre qualité de créateur. 

Pour une vue d’ensemble, consultez [Présentation d’IoT Central (fonctionnalités en préversion)](overview-iot-central.md). 

IoT Edge est constitué de trois composants :
* Les **modules IoT Edge** sont des conteneurs qui exécutent les services Azure, les services partenaire ou votre propre code. Ils sont déployés sur des appareils IoT Edge et s’exécutent localement sur ces appareils.
* Le **runtime IoT Edge** s’exécute sur chaque appareil IoT Edge et gère les modules déployés sur chaque appareil.
* Une **interface basée sur le cloud** permet de superviser et de gérer des appareils IoT Edge à distance. IoT Central est l’interface cloud.

Un appareil **Azure IoT Edge** peut être un appareil de passerelle avec, en aval, des appareils qui se connectent à l’appareil IoT Edge. Ce tutoriel partage plus d’informations sur les modèles de connectivité des appareils en aval.

Un **modèle d’appareil** définit les fonctionnalités de votre appareil et de vos modules IoT Edge. Parmi ces fonctionnalités figurent l’envoi des données de télémétrie par le module, les propriétés de module et les commandes auxquelles un module répond.

Dans ce tutoriel, vous allez créer un modèle d’appareil Capteur environnemental. Un appareil capteur environnemental :

* Envoie des données de télémétrie, comme la température.
* Répond aux propriétés accessibles en écriture durant sa mise à jour dans le cloud, par exemple l’intervalle d’envoi de la télémétrie.
* Répond aux commandes, telles que la réinitialisation de la température.

Dans ce tutoriel, vous allez aussi créer un modèle d’appareil Environment Gateway (Passerelle d’environnement). Un appareil de passerelle environnemental :

* Envoie des données de télémétrie, comme la température.
* Répond aux propriétés accessibles en écriture durant sa mise à jour dans le cloud, par exemple l’intervalle d’envoi de la télémétrie.
* Répond aux commandes telles que la réinitialisation de la température
* Permet l’établissement de relations avec d’autres modèles de capacité d’appareil.


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’appareil Azure IoT Edge.
> * Charger un manifeste de déploiement.
> * Créer des fonctionnalités, notamment la télémétrie, des propriétés et des commandes pour chaque module.
> * Définir une visualisation pour les données de télémétrie des modules.
> * Ajouter des relations aux modèles d’appareil en aval.
> * Publier votre modèle d’appareil.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez [créer une application Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relations des appareils en aval avec une passerelle et des modules

Les appareils en aval peuvent se connecter à un appareil de passerelle IoT Edge via le module `$edgeHub`. Cet appareil IoT Edge devient une passerelle transparente dans ce scénario.

![Diagramme de la passerelle transparente](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Les appareils en aval peuvent également se connecter à un appareil de passerelle IoT Edge via un module personnalisé. Dans le scénario suivant, les appareils en aval se connectent via un module personnalisé Modbus.

![Diagramme de la connexion via un module personnalisé](./media/tutorial-define-edge-device-type/gateway-module.png)

Le diagramme suivant illustre la connexion à un appareil de passerelle IoT Edge via les deux types de modules (personnalisé et `$edgeHub`).  

![Diagramme de connexion via les deux modules de connexion](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Enfin, les appareils en aval peuvent se connecter à un appareil de passerelle IoT Edge via plusieurs modules personnalisés. Le diagramme suivant montre des appareils en aval qui se connectent via un module personnalisé Modbus, un module personnalisé BLE et le module `$edgeHub`. 

![Diagramme de connexion par le biais de plusieurs modules personnalisés](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Créer un modèle

En tant que créateur, vous pouvez créer et modifier des modèles d’appareils IoT Edge dans votre application. Après avoir publié un modèle d’appareil, vous pouvez connecter des appareils réels qui implémentent le modèle d’appareil.

### <a name="select-device-template-type"></a>Sélectionner le type de modèle d’appareil 

Pour ajouter un nouveau modèle d’appareil à votre application, sélectionnez **Modèles d’appareil** dans le volet de gauche.

![Capture d’écran de la fenêtre Afficher un aperçu de l’application, avec mise en évidence de Modèles d’appareil](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Sélectionnez **+ Nouveau** pour créer un modèle d’appareil.

![Capture d’écran de la page Modèles d’appareils, avec mise en évidence de Nouveau](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Dans la page **Sélectionner un type de modèle**, sélectionnez **Azure IoT Edge**, puis sélectionnez **Suivant : Personnaliser**.

![Capture d’écran de la page Sélectionner un type de modèle](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personnaliser un modèle d’appareil

Dans IoT Edge, vous pouvez déployer et gérer la logique métier sous forme de modules. Les modules IoT Edge sont la plus petite unité de calcul gérée par IoT Edge. Ils peuvent contenir des services Azure (par exemple Azure Stream Analytics) ou du code propre à votre solution. Pour comprendre comment les modules sont développés, déployés et gérés, consultez [Modules IoT Edge](../../iot-edge/iot-edge-modules.md).

À un niveau élevé, un manifeste de déploiement est une liste des jumeaux de module configurés avec leurs propriétés souhaitées. Un manifeste de déploiement indique à un appareil IoT Edge (ou à un groupe d’appareils) les modules à installer et comment les configurer. Les manifestes de déploiement incluent les propriétés souhaitées pour chaque jumeau de module. Les appareils IoT Edge rapportent les propriétés indiquées pour chaque module.

Utilisez Visual Studio Code pour créer un manifeste de déploiement. Pour en savoir plus, consultez [Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Voici un exemple de manifeste de déploiement de base à un seul module à utiliser dans ce tutoriel. Copiez le code JSON suivant et enregistrez-le sous la forme d’un fichier .json. 

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

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Charger un manifeste de déploiement IoT Edge

Dans la page **Personnaliser l’appareil**, sous **Charger un manifeste de déploiement Azure IoT Edge**, sélectionnez **Parcourir**. 

![Capture d’écran de la page Personnaliser l’appareil, avec mise en évidence de Parcourir](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Si vous envisagez de créer un modèle d’appareil de passerelle IoT Edge, veillez à cocher la case **Appareil de passerelle avec des appareils en aval**.

![Capture d’écran de la page Personnaliser l’appareil, avec mise en évidence de l’option Appareil de passerelle avec des appareils en aval](./media/tutorial-define-edge-device-type/gateway-upload.png)

Dans la boîte de dialogue de sélection de fichier, sélectionnez le fichier manifeste de déploiement, puis sélectionnez **Ouvrir**.

IoT Edge valide le fichier manifeste de déploiement par rapport à un schéma. Si la validation réussit, sélectionnez **Vérifier**.

![Capture d’écran de la page Personnaliser l’appareil, avec mise en évidence des éléments Manifeste de déploiement et Vérifier](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

L’organigramme suivant montre le cycle de vie d’un manifeste de déploiement dans IoT Central.

![Organigramme du cycle de vie d’un manifeste de déploiement](./media/tutorial-define-edge-device-type/dmflow.png)

Vous verrez ensuite une page de vérification, avec les détails du manifeste de déploiement. Cette page affiche la liste des modules à partir du manifeste de déploiement. Dans ce tutoriel, vous pouvez constater que le module `SimulatedTemperatureSensor` est listé. Sélectionnez **Create** (Créer).

![Capture d’écran de la page de vérification, avec mise en évidence des éléments Module et Créer](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Si vous avez sélectionné un appareil de passerelle, la page de vérification suivante s’affiche.

![Capture d’écran de la page de vérification, avec mise en évidence de Passerelle Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Vous créez un modèle d’appareil avec les modèles de capacité de module. Dans ce tutoriel, vous créez un modèle d’appareil avec le modèle de capacité de module `SimulatedTemperatureSensor`. 

Remplacez le titre du modèle d’appareil par **Environment Sensor Device Template** (Modèle d’appareil Capteur environnemental).

![Capture d’écran du modèle d’appareil, avec mise en évidence du titre mis à jour](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Dans l’appareil IoT Edge, modélisez IoT Plug-and-Play comme suit :
* À chaque modèle d’appareil IoT Edge correspond un modèle de capacité d’appareil.
* Un modèle de capacité de module est généré pour chaque module personnalisé listé dans le manifeste de déploiement.
* Une relation est établie entre chaque modèle de capacité de module et un modèle de capacité d’appareil.
* Un modèle de capacité de module implémente des interfaces de module.
* Chaque interface de module contient des données de télémétrie, des propriétés et des commandes.

![Diagramme de la modélisation d’IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Ajouter des fonctionnalités à un modèle de capacité de module

Voici un exemple de sortie du module `SimulatedTemperatureSensor` :
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

Vous pouvez ajouter des fonctionnalités au module `SimulatedTemperatureSensor`, ce qui a pour effet de refléter la sortie précédente. 

1. Pour gérer une interface du modèle de capacité de module `SimulatedTemperatureSensor`, sélectionnez **Gérer** > **Ajouter une fonctionnalité**. 

    ![Capture d’écran de la page Environment Sensor Template (Modèle Capteur environnemental), avec mise en évidence de la fonctionnalité Ajouter une fonctionnalité](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Ajoutez une machine comme type d’objet.
  
    ![Capture d’écran de la page Environment Sensor Template Capabilities (Fonctionnalités du modèle Capteur environnemental), avec mise en évidence de l’élément Schéma](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Sélectionnez **Définir**. Dans la boîte de dialogue qui s’affiche, remplacez le nom de l’objet par **machine**. Créez les propriétés de température et de pression, puis sélectionnez **Appliquer**.
  
    ![Capture d’écran de la boîte de dialogue des attributs, avec mise en évidence de différentes options](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Ajoutez **ambient** (ambiante) comme type d’objet.

1. Sélectionnez **Définir**. Dans la boîte de dialogue qui s’affiche, remplacez le nom de l’objet par **ambient**. Créez les propriétés de température et d’humidité, puis sélectionnez **Appliquer**.
  
    ![Capture d’écran de la boîte de dialogue des attributs, avec mise en évidence de différentes options](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Ajoutez `timeCreated` comme type `DateTime`, puis sélectionnez **Enregistrer**.
  
    ![Capture d’écran du Modèle Capteur environnemental, avec mise en évidence de l’option Enregistrer](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Ajouter des relations

Si vous avez sélectionné un appareil IoT Edge comme appareil de passerelle, vous pouvez ajouter des relations en aval aux modèles de capacité d’appareil pour les appareils que vous souhaitez connecter à l’appareil de passerelle.
  
  ![Capture d’écran du Modèle Passerelle d’environnement, avec mise en évidence de l’option Ajouter une relation](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Vous pouvez ajouter une relation au niveau d’un appareil ou d’un module.
  
  ![Capture d’écran du Modèle Passerelle d’environnement, avec mise en évidence des relations au niveau de l’appareil et du module](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Vous pouvez sélectionner un modèle de capacité d’appareil en aval ou sélectionner le symbole astérisque. 
  
  ![Capture d’écran du Modèle Passerelle d’environnement, avec mise en évidence de l’élément Cible](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Pour ce tutoriel, sélectionnez l’astérisque. Cette option autorise toute relation en aval. Ensuite, sélectionnez **Enregistrer**.

  ![Capture d’écran du Modèle Passerelle d’environnement, avec mise en évidence de l’élément Cible](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Ajouter des propriétés du cloud

Un modèle d’appareil peut inclure des propriétés du cloud. Les propriétés du cloud existent uniquement dans l’application IoT Central, et ne sont jamais envoyées à un appareil ni reçues à partir d’un appareil.

1. Sélectionnez **Propriétés du cloud** >  **+ Ajouter une propriété cloud**. Utilisez les informations du tableau suivant pour ajouter une propriété du cloud à votre modèle d’appareil.

    | Nom complet      | Type sémantique | Schéma |
    | ----------------- | ------------- | ------ |
    | Dernière date de service | Aucun          | Date   |
    | Nom du client     | Aucun          | Chaîne |

2. Sélectionnez **Enregistrer**.

  
    ![Capture d’écran du Modèle Capteur environnemental, avec mise en évidence de l’option Enregistrer](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Ajouter des personnalisations

Utilisez des personnalisations pour modifier une interface ou ajouter des fonctionnalités propres à IoT Central à une capacité qui ne vous oblige pas à créer une version de votre modèle de capacité d’appareil. Vous pouvez personnaliser les champs quand le modèle de capacité est à l’état Brouillon ou Publié. Vous pouvez uniquement personnaliser les champs qui n’interrompent pas la compatibilité de l’interface. Vous pouvez par exemple :

- Personnaliser le nom complet et les unités d’une capacité.
- Ajouter une couleur par défaut à utiliser quand la valeur apparaît sur un graphique.
- Spécifier les valeurs initiales, minimales et maximales d’une propriété.

Vous ne pouvez pas personnaliser le nom ou le type de la capacité.

Une fois la personnalisation terminée, Sélectionnez **Enregistrer**.
  
![Capture d’écran de la page Personnaliser dans le Modèle Capteur environnemental](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Créer des vues

En tant que générateur, vous pouvez personnaliser l’application pour présenter des informations pertinentes sur l’appareil capteur environnemental à un opérateur. Vos personnalisations permettent à l’opérateur de gérer l’appareil capteur environnemental raccordé à l’application. Vous pouvez créer deux types de vues permettant à un opérateur d’interagir avec des appareils :

* Formulaires pour voir et modifier les propriétés de l’appareil et du cloud
* Tableaux de bord pour visualiser les appareils

### <a name="configure-a-view-to-visualize-devices"></a>Configurer une vue pour visualiser des appareils

Un tableau de bord d’appareil permet à un opérateur de visualiser un appareil à l’aide de graphiques et de métriques. En tant que générateur, vous pouvez définir les informations qui apparaissent sur le tableau de bord d’un appareil. Vous pouvez définir plusieurs tableaux de bord pour les appareils. Pour créer un tableau de bord afin de visualiser la télémétrie du capteur environnemental, sélectionnez **Vues** > **Visualisation de l’appareil** :

  
![Capture d’écran de la page Vues du Modèle Capteur environnemental, avec mise en évidence de la vignette Visualisation de l’appareil](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Ambient Telemetry et Machine Telemetry sont des objets complexes. Pour créer des graphiques :

1. Faites glisser **Ambient Telemetry** et sélectionnez **Line chart** (Graphique en courbes). 
  
   ![Capture d’écran du Modèle Capteur environnemental, avec mise en évidence de l’objet Ambient Telemetry et de l’option Line chart (Graphique en courbes)](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Sélectionnez l’icône de configuration. Sélectionnez **Temperature** et **Humidity** pour visualiser les données, puis sélectionnez **Update configuration** (Mettre à jour la configuration). 
  
   ![Capture d’écran du Modèle Capteur environnemental, avec mise en évidence de différentes options](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Sélectionnez **Enregistrer**.

Vous pouvez ajouter d’autres vignettes qui affichent d’autres propriétés ou valeurs de télémétrie. Vous pouvez également ajouter du texte statique, des liens et des images. Pour déplacer ou redimensionner une vignette sur le tableau de bord, déplacez le pointeur de souris sur la vignette, puis faites-la glisser vers un nouvel emplacement ou redimensionnez-la.
  
![Capture d’écran de la vue du tableau de bord du modèle Capteur environnemental](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Ajouter un formulaire d’appareil

Un formulaire d’appareil permet à un opérateur de modifier les propriétés de l’appareil et les propriétés du cloud accessibles en écriture. En tant que générateur, vous pouvez définir plusieurs formulaires et choisir les propriétés de l’appareil et du cloud à afficher sur chaque formulaire. Vous pouvez également afficher des propriétés d’appareil en lecture seule sur un formulaire.

Pour créer un formulaire afin de voir et de modifier les propriétés d’un capteur environnemental

1. Dans le **modèle Capteur environnemental**, accédez à **Vues**. Sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.
  
   ![Capture d’écran de la page Vues du modèle Capteur environnemental, avec mise en évidence de la vignette Modification des données de l’appareil et du cloud](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Entrez le nom de formulaire **Propriétés du capteur environnemental**.

1. Faites glisser les propriétés du cloud **Nom du client** et **Dernière date de service** sur la section existante du formulaire.
  
   ![Capture d’écran de la page Vues du modèle Capteur environnemental, avec mise en évidence de différentes options](./media/tutorial-define-edge-device-type/views-properties.png)

1. Sélectionnez **Enregistrer**.

## <a name="publish-a-device-template"></a>Publier un modèle d’appareil

Avant de pouvoir créer un capteur environnemental simulé ou connecter un capteur environnemental réel, vous devez publier votre modèle d’appareil.

Pour publier un modèle d’appareil

1. Accédez à votre modèle d’appareil à partir de la page **Modèles d’appareil**.

2. Sélectionnez **Publier**.
  
    ![Capture d’écran du Modèle Capteur environnemental, avec mise en évidence de l’option Publier](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Dans la boîte de dialogue **Publier un modèle d’appareil**, choisissez **Publier**.
  
    ![Capture d’écran de la boîte de dialogue Publier un modèle d’appareil, avec mise en évidence de l’option Publier](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Une fois qu’un modèle d’appareil a été publié, il est visible dans la page **Appareils** et par l’opérateur. Dans un modèle d’appareil publié, vous ne pouvez pas modifier un modèle de capacité d’appareil sans créer de nouvelle version. En revanche, vous pouvez effectuer des mises à jour des propriétés du cloud, des personnalisations et des vues dans un modèle d’appareil publié. Ces mises à jour n’entraînent pas la création d’une version. Après avoir apporté des modifications, sélectionnez **Publier** pour envoyer ces modifications à votre opérateur.
  
![Capture d’écran de la liste Modèles d’appareil des modèles publiés](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Créer un appareil Edge comme modèle d’appareil de nœud terminal.
* Générer des modules à partir d’un manifeste de déploiement chargé.
* Ajouter des propriétés et des données de télémétrie de type complexe.
* Créer des propriétés du cloud.
* Créer des personnalisations.
* Définir une visualisation pour la télémétrie de l’appareil.
* Publier votre modèle d’appareil Edge.

Maintenant que vous avez créé un modèle d’appareil dans votre application Azure IoT Central, vous pouvez effectuer l’opération ci-après :

> [!div class="nextstepaction"]
> [Connecter l’appareil](./tutorial-connect-pnp-device.md)
