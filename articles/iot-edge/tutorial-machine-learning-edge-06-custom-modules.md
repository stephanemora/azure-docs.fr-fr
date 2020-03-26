---
title: 'Tutoriel : Créer et déployer des modules personnalisés - Machine Learning sur Azure IoT Edge'
description: Ce tutoriel montre comment créer et déployer des modules IoT Edge qui traitent les données d’appareils de nœuds terminaux via un modèle Machine Learning, puis envoient les insights à IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772361"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutoriel : Créer et déployer des modules IoT Edge personnalisés

> [!NOTE]
> Cet article fait partie d’une série décrivant l’utilisation d’Azure Machine Learning sur IoT Edge. Si vous êtes arrivé directement à cet article, nous vous encourageons à commencer par le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série afin d’obtenir de meilleurs résultats.

Dans cet article, nous créons trois modules IoT Edge qui reçoivent des messages des appareils de nœud terminal, exécutent les données dans votre modèle Machine Learning, puis transfèrent les insights à IoT Hub.

Le hub IoT Edge facilite la communication entre les modules. L’utilisation du hub IoT Edge comme répartiteur de messages permet aux modules de rester indépendants les uns des autres. Il suffit aux modules de spécifier les entrées sur lesquelles ils acceptent des messages et les sorties vers lesquelles ils écrivent des messages.

Nous voulons que l’appareil IoT Edge accomplisse quatre choses pour nous :

* Recevoir des données des appareils de nœud terminal
* Prédire la durée de vie restante de l’appareil qui a envoyé les données
* Envoyer un message à IoT Hub avec seulement la durée de vie restante de l’appareil (cette fonction peut être modifiée pour envoyer des données seulement si la durée de vie restante est inférieure à un certain niveau)
* Enregistrer les données des appareils de nœud terminal dans un fichier local sur l’appareil IoT Edge. Ce fichier de données est régulièrement chargé sur IoT Hub via le chargement de fichier pour affiner l’entraînement du modèle Machine Learning. L’utilisation du chargement de fichier au lieu d’un streaming constant de messages est plus rentable.

Pour accomplir ces tâches, nous utilisons trois modules personnalisés :

* **Classifieur RUL :** Le module turboFanRulClassifier que nous avons créé dans [Entraîner et déployer un modèle Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) est un module Machine Learning standard, qui expose une entrée appelée « amlInput » et une sortie nommée « amlOutput ». « amlInput » nécessite que son entrée ressemble exactement à l’entrée que nous avons envoyée au service web ACI. De même, « amlOutput » retourne les mêmes données que le service web.

* **Enregistreur Avro :** Ce module reçoit les messages sur l’entrée « avroModuleInput » et conserve le message au format Avro sur disque pour le charger par la suite dans IoT Hub.

* **Routeur :** Le module Routeur reçoit des messages des appareils de nœud terminal en aval, puis les met en forme et les envoie au classifieur. Le module reçoit ensuite les messages du classifieur et les transfère au module Enregistreur Avro. Pour finir, le module envoie simplement la prédiction de la durée de vie restante au hub IoT.

  * Entrées :
    * **deviceInput** : reçoit les messages des appareils de nœud terminal
    * **rulInput :** reçoit les messages de « amlOutput »

  * Sorties :
    * **classify :** envoie des messages à « amlInput »
    * **writeAvro :** envoie des messages à « avroModuleInput »
    * **toIotHub :** envoie des messages à $upstream, qui les transmet au hub IoT connecté

Le diagramme ci-dessous illustre les modules, les entrées, les sorties et les routes du hub IoT Edge pour la solution complète :

![Diagramme d’architecture des trois modules IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Les étapes décrites dans cet article sont généralement effectuées par un développeur de cloud.

## <a name="create-a-new-iot-edge-solution"></a>Créer une solution IoT Edge

Pendant l’exécution du deuxième de nos deux notebooks Azure, nous avons créé et publié une image conteneur qui contient notre modèle de durée de vie restante. Pendant le processus de création d’image, Azure Machine Learning empaquetait ce modèle pour offrir la possibilité de déployer l’image en tant que module Azure IoT Edge. Dans cette étape, nous créons une solution Azure IoT Edge à l’aide du module « Azure Machine Learning » et faisons pointer le module sur l’image que nous avons publiée à l’aide de notebooks Azure.

1. Ouvrez une session Bureau à distance sur votre machine de développement.

2. Ouvrez le dossier **C:\\source\\IoTEdgeAndMlSample** dans Visual Studio Code.

3. Cliquez avec le bouton droit sur le panneau Explorateur (dans l’espace vide) et sélectionnez **Nouvelle solution IoT Edge**.

    ![Créer une solution IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Acceptez le nom de solution par défaut **EdgeSolution**.

5. Choisissez **Azure Machine Learning** comme modèle de module.

6. Nommez le module **turbofanRulClassifier**.

7. Choisissez votre espace de travail Machine Learning.

8. Sélectionnez l’image que vous avez créée pendant l’exécution du notebook Azure.

9. Examinez la solution et notez les fichiers qui ont été créés :

   * **deployment.template.json :** Ce fichier contient la définition de chacun des modules dans la solution. Vous devez examiner trois sections dans ce fichier :

     * **Informations d’identification de registre :** Définit l’ensemble des registres de conteneurs personnalisés que vous utilisez dans votre solution. À ce stade, elle doit contenir le registre de votre espace de travail Machine Learning, qui est l’emplacement de stockage de votre image Azure Machine Learning. Vous pouvez avoir n’importe quel nombre de registres de conteneurs, mais par souci de simplicité, nous utilisons ce même registre pour tous les modules

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules :** Cette section contient l’ensemble des modules définis par l’utilisateur associés à cette solution. Notez que cette section contient actuellement deux modules : SimulatedTemperatureSensor et turbofanRulClassifier. Le module SimulatedTemperatureSensor a été installé par le modèle Visual Studio Code, mais nous n’en avons pas besoin pour cette solution. Vous pouvez supprimer la définition du module SimulatedTemperatureSensor de la section des modules. Notez que la définition du module turbofanRulClassifier pointe vers l’image dans votre registre de conteneurs. Les différents modules que nous ajoutons à la solution apparaissent dans cette section.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Routes :** nous utilisons beaucoup les routes dans ce tutoriel. Les routes définissent la façon dont les modules communiquent les uns avec les autres. Les deux routes définies par le modèle ne correspondent pas au routage dont nous avons besoin. La première route envoie toutes les données des sorties du classifieur au hub IoT ($upstream). L’autre route concerne SimulatedTemperatureSensor, que nous venons de supprimer. Supprimez les deux routes par défaut.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json :** ce fichier est la version debug de deployment.template.json. Nous devons refléter tous les changements de deployment.template.json dans ce fichier.

   * **.env :** ce fichier doit contenir le nom d’utilisateur et le mot de passe dont vous avez besoin pour accéder à votre registre.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Cliquez avec le bouton droit sur le fichier deployment.template.json dans l’Explorateur Visual Studio Code et sélectionnez **Générer la solution IoT Edge**.

11. Notez que cette commande crée un dossier config avec un fichier deployment.amd64.json. Ce fichier est le modèle de déploiement concret pour la solution.

## <a name="add-router-module"></a>Ajouter un module Routeur

Ensuite, nous ajoutons le module Routeur à notre solution. Le module Routeur gère plusieurs responsabilités pour notre solution :

* **Recevoir des messages des appareils de nœud terminal :** à mesure que les messages arrivent sur l’appareil IoT Edge à partir des appareils en aval, le module Routeur reçoit les messages et commence à orchestrer leur routage.
* **Envoyer des messages au module Classifieur RUL :** quand un nouveau message est reçu à partir d’un appareil en aval, le module Routeur convertit le message au format attendu par le classifieur RUL. Le routeur envoie le message au classifieur RUL pour une prédiction de durée de vie restante. Une fois que le classifieur a effectué une prédiction, il renvoie le message au module Routeur.
* **Envoyer des messages de durée de vie restante à IoT Hub :** quand le routeur reçoit un message du classifieur, il le transforme pour contenir uniquement les informations essentielles, l’ID d’appareil et la durée de vie restante, puis envoie le message abrégé au hub IoT. Pour plus de précision, vous pouvez envoyer des messages au hub IoT seulement si la prédiction de durée de vie restante est inférieure à un seuil donné (par exemple, quand la durée de vie restante est inférieure à 100 cycles). Nous ne l’avons pas fait ici. Ce type de filtrage réduit le volume de messages et le coût du hub IoT.
* **Envoyer un message au module Enregistreur Avro :** pour préserver toutes les données envoyées par l’appareil en aval, le module Routeur envoie l’intégralité du message reçu du classifieur au module Enregistreur Avro, lequel conserve et charge les données à l’aide du chargement de fichier IoT Hub.

> [!NOTE]
> La description des responsabilités de module peut donner l’impression que le traitement est séquentiel, mais en réalité le flux est basé sur les messages/événements. C’est la raison pour laquelle nous avons besoin d’un module d’orchestration comme notre module Routeur.

### <a name="create-module-and-copy-files"></a>Créer le module et copier les fichiers

1. Cliquez avec le bouton droit sur le dossier des modules dans Visual Studio Code et choisissez **Ajouter un module IoT Edge**.

2. Choisissez **Module C#** .

3. Nommez le module **turbofanRouter**.

4. Quand vous êtes invité à indiquer votre dépôt d’images Docker, utilisez le registre de l’espace de travail Machine Learning (il se trouve sous le nœud registryCredentials de votre fichier *deployment.template.json*). Cette valeur est l’adresse complète du registre, par exemple, **\<votre registre\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Dans cet article, nous utilisons l’instance Azure Container Registry créée par l’espace de travail Azure Machine Learning, que nous avons utilisée pour entraîner et déployer notre classifieur. C’est pour des raisons purement pratiques. Nous aurions pu tout aussi bien créer un registre de conteneurs et y publier nos modules.

5. Ouvrez une nouvelle fenêtre de terminal dans Visual Studio Code (**Afficher** > **Terminal**) et copiez les fichiers du répertoire de modules.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Quand vous êtes invité à remplacer le fichier program.cs, appuyez sur `y`, puis sur `Enter`.

### <a name="build-router-module"></a>Générer un module Routeur

1. Dans Visual Studio Code, sélectionnez **Terminal** > **Configurer la tâche de build par défaut**.

2. Cliquez sur **Créer un fichier tasks.json à partir d’un modèle**.

3. Cliquez sur **.NET Core**.

4. Quand tasks.json s’ouvre, remplacez le contenu par :

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Enregistrez et fermez tasks.json.

6. Exécutez la build avec `Ctrl + Shift + B` ou **Terminal** > **Exécuter la tâche de build**.

### <a name="set-up-module-routes"></a>Configurer des routes de module

Comme mentionné plus haut, le runtime IoT Edge utilise les routes configurées dans le fichier *deployment.template.json* pour gérer la communication entre les modules faiblement couplés. Dans cette section, nous voyons comment configurer des routes pour le module turbofanRouter. Nous étudions d’abord les routes d’entrée, puis les sorties.

#### <a name="inputs"></a>Entrées

1. Dans la méthode Init() de Program.cs, nous inscrivons deux rappels pour le module :

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Le premier rappel écoute les messages envoyés au récepteur **deviceInput**. Dans le diagramme ci-dessus, nous voyons que les messages que nous voulons router vers cette entrée proviennent de n’importe quel appareil de nœud terminal. Dans le fichier *deployment.template.json*, ajoutez une route pour que le hub de périphérie puisse router les messages reçus par l’appareil IoT Edge qui n’ont pas été envoyés par un module IoT Edge vers l’entrée appelée « deviceInput » sur le module turbofanRouter :

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ensuite, ajoutez une route pour les messages du module rulClassifier vers le module turbofanRouter :

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Outputs

Ajoutez quatre routes supplémentaires au paramètre de route $edgeHub, pour gérer les sorties du module Routeur.

1. Program.cs définit la méthode SendMessageToClassifier(), qui utilise le client de module pour envoyer un message au classifieur RUL à l’aide de la route :

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() utilise le client de module pour envoyer seulement les données de durée de vie restante de l’appareil au hub IoT via la route :

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() utilise le client de module pour envoyer le message avec les données de durée de vie restante ajoutées au module avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() envoie les messages ayant échoué en amont du IoT Hub là où ils peuvent être routés pour une utilisation ultérieure.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

En tenant compte de toutes les routes, votre nœud « $edgeHub » doit ressembler au code JSON suivant :

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> L’ajout du module turbofanRouter a créé la route supplémentaire suivante : `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Supprimez cette route et gardez uniquement les routes listées ci-dessus dans votre fichier deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Copier les routes dans deployment.debug.template.json

Pour la dernière étape, afin de synchroniser nos fichiers, répercutez les changements de deployment.template.json dans deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Ajouter le module Enregistreur Avro

Le module Enregistreur Avro a deux responsabilités dans notre solution, stocker les messages et charger les fichiers.

* **Stocker les messages** : quand le module Enregistreur Avro reçoit un message, il écrit le message dans le système de fichiers local au format Avro. Nous utilisons un montage de liaison, qui monte un répertoire (dans ce cas /data/avrofiles) dans un chemin du conteneur du module. Ce montage permet au module d’écrire dans un chemin local (/avrofiles) et de pouvoir accéder à ces fichiers directement à partir de l’appareil IoT Edge.

* **Charger les fichiers** : le module enregistreur Avro utilise la fonctionnalité de chargement de fichier d’Azure IoT Hub pour charger les fichiers dans un compte de stockage Azure. Dès qu’un fichier est chargé, le module le supprime sur le disque

### <a name="create-module-and-copy-files"></a>Créer le module et copier les fichiers

1. Dans la palette de commandes, recherchez et sélectionnez **Python : Sélectionner l’interpréteur**.

1. Choisissez l’interpréteur trouvé dans C:\\Python37.

1. Rouvrez la palette de commandes, et recherchez et sélectionnez **Terminal : Sélectionner l’interpréteur de commandes par défaut**.

1. Quand vous y êtes invité, choisissez **Invite de commandes**.

1. Ouvrez un nouvel interpréteur de commandes du terminal, **Terminal** > **Nouveau terminal**.

1. Cliquez avec le bouton droit sur le dossier des modules dans Visual Studio Code et choisissez **Ajouter un module IoT Edge**.

1. Choisissez **Module Python**.

1. Nommez le module « avroFileWriter ».

1. Quand vous êtes invité à indiquer votre dépôt d’images Docker, utilisez le même registre que celui que vous avez utilisé pour ajouter le module Routeur.

1. Copiez les fichiers de l’exemple de module dans la solution.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Si vous êtes invité à remplacer main.py, tapez `y`, puis appuyez sur `Enter`.

1. Notez que filemanager.py et schema.py ont été ajoutés à la solution et que main.py a été mis à jour.

> [!NOTE]
> Quand vous ouvrez un fichier Python, vous pouvez être invité à installer pylint. Vous n’avez pas besoin d’installer le linter pour suivre ce tutoriel.

### <a name="bind-mount-for-data-files"></a>Montage de liaison pour les fichiers de données

Comme mentionné dans l’introduction, le module enregistreur a besoin d’un montage de liaison pour écrire des fichiers Avro sur le système de fichiers de l’appareil.

#### <a name="add-directory-to-device"></a>Ajouter le répertoire à l’appareil

1. Connectez-vous à la machine virtuelle de votre appareil IoT Edge à l’aide de SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Créez le répertoire pour les messages enregistrés de l’appareil de nœud terminal.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Mettez à jour des autorisations de répertoire pour le rendre accessible en écriture par le conteneur.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Vérifiez que le répertoire a maintenant l’autorisation d’écriture (w) aux niveaux utilisateur, groupe et propriétaire.

   ```bash
   ls -la /data
   ```

   ![Autorisations de répertoire pour avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Ajouter le répertoire au module

Pour ajouter le répertoire au conteneur du module, nous modifions les fichiers Dockerfile associés au module avroFileWriter. Trois fichiers Dockerfile sont associés au module : Dockerfile.amd64, Dockerfile.amd64.debug et Dockerfile.arm32v7. Ces fichiers doivent être synchronisés en prévision d’une opération de débogage ou de déploiement sur un appareil arm32. Pour cet article, seul Dockerfile.amd64 nous intéresse.

1. Sur votre machine de développement, ouvrez le fichier **Dockerfile.amd64**.

2. Modifiez le fichier pour qu’il ressemble à l’exemple suivant :

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Les commandes `mkdir` et `chown` indiquent au processus de génération Docker de créer un répertoire de niveau supérieur appelé /avrofiles dans l’image, puis de définir l’utilisateur du module comme le propriétaire de ce répertoire. Il est important d’insérer ces commandes après l’ajout de l’utilisateur du module à l’image avec la commande `useradd` et avant que le contexte bascule sur l’utilisateur du module (USER moduleuser).

3. Effectuez les changements correspondants dans Dockerfile.amd64.debug et Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Mettre à jour la configuration du module

La dernière étape de création de la liaison est la mise à jour des fichiers deployment.template.json (et deployment.debug.template.json) avec les informations de liaison.

1. Ouvrez deployment.template.json.

2. Modifiez la définition de module pour avroFileWriter en ajoutant le paramètre `Binds` qui fait pointer le répertoire /avrofiles du conteneur vers le répertoire local sur l’appareil de périphérie. Votre définition de module doit correspondre à cet exemple :

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Effectuez les changements correspondants dans deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Montage de liaison pour accéder à config.yaml

Nous devons ajouter une liaison de plus pour le module enregistreur. Cette liaison permet au module de lire la chaîne de connexion du fichier /etc/iotedge/config.yaml sur l’appareil IoT Edge. Nous avons besoin de la chaîne de connexion pour créer un IoTHubClient et pouvoir appeler la méthode upload\_blob\_async pour télécharger des fichiers dans le hub IoT. Les étapes d’ajout de cette liaison sont similaires à celles de la section précédente.

#### <a name="update-directory-permission"></a>Mettre à jour l’autorisation de répertoire

1. Connectez-vous à l’appareil IoT Edge à l’aide de SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Ajoutez l’autorisation de lecture au fichier config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Vérifiez que les autorisations sont définies correctement.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Vérifiez que les autorisations pour config.yaml sont **- r--r--r--** .

#### <a name="add-directory-to-module"></a>Ajouter le répertoire au module

1. Sur votre machine de développement, ouvrez le fichier **Dockerfile.amd64**.

2. Ajouter un jeu supplémentaire de commandes `mkdir` et `chown` au fichier pour qu’il ressemble à :

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Effectuez les changements correspondants dans Dockerfile.amd64.debug et Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Mettre à jour la configuration du module

1. Ouvrez le fichier **deployment.template.json**.

2. Modifiez la définition de module pour avroFileWriter en ajoutant une deuxième ligne au paramètre `Binds` qui fait pointer le répertoire du conteneur (/app/iotconfig) vers le répertoire local sur l’appareil (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Effectuez les changements correspondants dans deployment.debug.template.json.

## <a name="install-dependencies"></a>Installer des dépendances

Le module enregistreur prend une dépendance sur deux bibliothèques Python, fastavro et PyYAML. Nous devons installer les dépendances sur notre machine de développement et indiquer au processus de génération Docker de les installer dans l’image de notre module.

### <a name="pyyaml"></a>PyYAML

1. Sur votre machine de développement, ouvrez le fichier **requirements.txt** et ajoutez pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Ouvrez le fichier **Dockerfile.amd64** et ajoutez une commande `pip install` pour mettre à niveau setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Effectuez les changements correspondants dans Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Installer pyyaml localement en ouvrant un terminal dans Visual Studio Code et en tapant

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Dans requirements.txt, ajoutez fastavro après pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installez fastavro sur votre machine de développement à l’aide du terminal Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurer IoT Hub

En introduisant l’appareil IoT Edge et les modules dans le système, nous avons changé nos attentes concernant les données à envoyer au hub et le but de cet envoi. Nous devons reconfigurer le routage dans le hub pour gérer notre nouvelle réalité.

> [!NOTE]
> Nous reconfigurons le hub avant de déployer des modules, car certains paramètres du hub, en particulier le chargement de fichier, doit être correctement configuré pour que le module avroFileWriter s’exécute correctement

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurer une route pour les messages de durée de vie restante dans IoT Hub

Une fois le routeur et le classifieur en place, nous espérons recevoir des messages normaux, contenant uniquement l’ID d’appareil et la prédiction de durée de vie restante pour l’appareil. Nous voulons router les données de durée de vie restante vers leur propre emplacement de stockage où nous pouvons superviser l’état des appareils, générer des rapports et déclencher des alertes en fonction des besoins. En même temps, nous voulons que toutes les données d’appareil qui sont encore envoyées directement par un appareil de nœud terminal qui n’a pas encore été attaché à notre appareil IoT Edge restent routées vers l’emplacement de stockage actuel.

#### <a name="create-a-rul-message-route"></a>Créez une route des messages de durée de vie restante

1. Dans le portail Azure, accédez à votre hub IoT.

2. Dans la barre de navigation gauche, choisissez **Routage des messages**.

3. Sélectionnez **Ajouter**.

4. Nommez la route **RulMessageRoute**.

5. Sélectionnez **Ajouter** à côté du sélecteur de **point de terminaison** et choisissez **Stockage d’objets blob**.

6. Dans le formulaire **Ajouter un point de terminaison de stockage**, nommez le point de terminaison **ruldata**.

7. Sélectionnez **Choisir un conteneur**.

8. Choisissez le compte de stockage utilisé dans ce tutoriel, dont le nom ressemble à **iotedgeandml\<suffixe unique\>** .

9. Choisissez le conteneur **ruldata** et cliquez sur **Sélectionner**.

10. Cliquez sur **Créer** pour créer le point de terminaison de stockage.

11. Pour la **requête de routage**, entrez la requête suivante :

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Développez la section **Test**, puis la section **Corps du Message**. Remplacez le message par cet exemple des messages que nous espérons :

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Sélectionnez **Tester la route**. Si le test a réussi, vous voyez « Le message correspond à la requête ».

14. Cliquez sur **Enregistrer**.

#### <a name="update-turbofandevicetostorage-route"></a>Mettre à jour la route turbofanDeviceToStorage

Nous ne voulons pas router les nouvelles données de prédiction vers notre ancien emplacement de stockage, par conséquent, mettez à jour la route pour l’éviter.

1. Dans la page **Routage des messages** du hub IoT, sélectionnez l’onglet **Routes**.

2. Sélectionnez **turbofanDeviceDataToStorage** ou le nom que vous avez donné à la route de données initiale de l’appareil.

3. Mettre à jour la requête de routage vers

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Développez la section **Test**, puis la section **Corps du Message**. Remplacez le message par cet exemple des messages que nous espérons :

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Sélectionnez **Tester la route**. Si le test a réussi, vous voyez « Le message correspond à la requête ».

6. Sélectionnez **Enregistrer**.

### <a name="configure-file-upload"></a>Configurer le chargement de fichiers

Configurez la fonctionnalité de chargement de fichier IoT Hub pour activer le module enregistreur de fichier afin de charger des fichiers dans le stockage.

1. Dans le navigateur de gauche de votre hub IoT, choisissez **Chargement de fichier**.

2. Sélectionnez **Conteneur de stockage Azure**.

3. Sélectionnez votre compte de stockage dans la liste.

4. Sélectionnez le conteneur **uploadturbofanfiles** et cliquez sur **Sélectionner**.

5. Sélectionnez **Enregistrer**. Le portail vous avertit une fois que l’enregistrement est effectué.

> [!Note]
> Nous n’activons pas les notifications de chargement pour ce tutoriel, mais consultez [Recevoir une notification de chargement de fichier](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) pour plus d’informations sur la gestion des notifications de chargement de fichier.

## <a name="build-publish-and-deploy-modules"></a>Générer, publier et déployer des modules

Maintenant que nous avons modifié la configuration, nous sommes prêts à générer les images et à les publier sur notre registre de conteneurs Azure. Le processus de génération utilise le fichier deployment.template.json pour déterminer les modules à générer. Les paramètres de chaque module, y compris la version, se trouvent dans le fichier module.json du dossier de module. Le processus de génération exécute d’abord une build Docker sur les fichiers Dockerfile correspondant à la configuration actuelle du fichier module.json afin de créer une image. Il publie ensuite l’image sur le registre à partir du fichier module.json avec une balise de version correspondant à celle du fichier module.json. Enfin, il génère un manifeste de déploiement propre à la configuration (par exemple, deployment.amd64.json), que nous déployons sur l’appareil IoT Edge. L’appareil IoT Edge lit les informations du manifeste de déploiement et, en fonction des instructions, télécharge les modules, configure les routes et définit les propriétés souhaitées. Cette méthode de déploiement a deux effets secondaires que vous devez connaître :

* **Décalage du déploiement :** comme le runtime IoT Edge doit reconnaître le changement de ses propriétés souhaitées avant de commencer la reconfiguration, vous pouvez avoir un délai entre le moment où vous déployez vos modules et le moment où le runtime les choisit et commence la mise à jour de l’appareil IoT Edge.

* **Les versions de module sont prises en compte :** si vous publiez une nouvelle version du conteneur d’un module sur votre registre de conteneurs à l’aide des mêmes balises de version que celles du module précédent, le runtime ne télécharge pas la nouvelle version du module. Il compare la balise de version de l’image locale et celle de l’image souhaitée dans le manifeste de déploiement. Si ces versions correspondent, le runtime n’effectue aucune action. Par conséquent, vous devez incrémenter la version de votre module chaque fois que vous voulez déployer de nouveaux changements. Incrémentez la version en changeant la propriété **version** sous la propriété **tag** dans le fichier module.json du module que vous changez. Ensuite, générez et publiez le module.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Générer et publier

1. Dans Visual Studio Code sur votre machine virtuelle de développement, ouvrez une fenêtre de terminal Visual Studio Code et connectez-vous à votre registre de conteneurs.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Dans Visual Studio Code, cliquez avec le bouton droit sur deployment.template.json et choisissez **Générer et pousser la solution IoT Edge**.

### <a name="view-modules-in-the-registry"></a>Voir les modules dans le registre

Une fois la build effectuée, nous pouvons utiliser le portail Azure pour passer en revue nos modules publiés.

1. Dans le portail Azure, accédez à votre espace de travail Azure Machine Learning et cliquez sur le lien hypertexte de **Registre**.

    ![Accédez au registre à partir de l’espace de travail de service Machine Learning](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Dans le navigateur latéral du registre, sélectionnez **Dépôts**.

3. Notez que les deux modules que vous avez créés, **avrofilewriter** et **turbofanrouter**, apparaissent comme des dépôts.

4. Sélectionnez **turbofanrouter** et notez que vous avez publié une image marquée avec 0.0.1-amd64.

   ![Voir la première version marquée de turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Déployer des modules sur un appareil IoT Edge

Nous avons généré et configuré les modules dans notre solution, maintenant nous déployons les modules sur l’appareil IoT Edge.

1. Dans Visual Studio Code, cliquez avec le bouton droit sur le fichier **deployment.amd64.json** du dossier config.

2. Choisissez **Créer un déploiement pour un seul appareil**.

3. Choisissez votre appareil IoT Edge, **aaTurboFanEdgeDevice**.

4. Actualisez le panneau des appareils Azure IoT Hub dans l’Explorateur Visual Studio Code. Vous devez voir que les trois nouveaux modules sont déployés, mais pas encore en cours d’exécution.

5. Réactualisez au bout de quelques minutes pour voir les modules en cours d’exécution.

   ![Voir les modules en cours d’exécution dans Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Entre le démarrage des modules et l’atteinte d’un état stable d’exécution, plusieurs minutes peuvent s’écouler. Dans cet intervalle, vous pouvez voir les modules démarrer et s’arrêter alors qu’ils essaient d’établir une connexion au module du hub IoT Edge.

## <a name="diagnosing-failures"></a>Diagnostic des échecs

Dans cette section, nous partageons quelques techniques pour comprendre ce qui a échoué avec un module ou plusieurs modules. Souvent, un échec peut d’abord être détecté en consultant l’état dans Visual Studio Code.

### <a name="identify-failed-modules"></a>Identifier les modules ayant échoué

* **Visual Studio Code :** Examinez le panneau des appareils Azure IoT Hub. Si la plupart des modules sont en cours d’exécution sauf un, qui est arrêté, vous devez en examiner les raisons. Si tous les modules sont arrêtés pendant une longue période de temps, il peut s’agit d’un échec également.

* **Portail Azure :** En accédant à votre hub IoT dans le portail, puis à la page de détails de l’appareil (sous IoT Edge, examinez votre appareil) vous pouvez voir qu’un module a signalé une erreur ou n’a jamais rien signalé au hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnostic de l’appareil

En vous connectant à l’appareil IoT Edge, vous pouvez accéder à une grande partie des informations sur l’état de vos modules. Le mécanisme principal que nous utilisons sont les commandes Docker qui nous permettent d’examiner les conteneurs et les images sur l’appareil.

1. Listez les conteneurs en cours d’exécution. Nous espérons voir un conteneur pour chaque module avec un nom qui correspond au module. Par ailleurs, cette commande liste l’image exacte du conteneur, y compris la version. Vous pouvez également lister des images en remplaçant « container » par « image » dans la commande.

   ```bash
   sudo docker container ls
   ```

2. Obtenez les journaux d’un conteneur. Cette commande génère tout ce qui a été écrit dans StdErr et StdOut dans le conteneur. Cette commande fonctionne pour les conteneurs qui ont démarré et se sont arrêtés pour une raison quelconque. Il est également utile de comprendre ce qui s’est passé avec les conteneurs edgeAgent ou edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspectez un conteneur. Cette commande donne une multitude d’informations sur l’image. Les données peuvent être filtrées selon ce que vous recherchez. Par exemple, si vous voulez vérifier que les liaisons sur avroFileWriter sont correctes, vous pouvez utiliser la commande :

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Connectez-vous à un conteneur en cours d’exécution. Cette commande peut être utile si vous voulez examiner le conteneur pendant son exécution :

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons créé une solution IoT Edge dans Visual Studio Code avec trois modules, un classifieur, un routeur et un enregistreur/chargeur de fichier. Nous avons configuré des routes pour permettre aux modules de communiquer entre eux sur l’appareil de périphérie, modifié la configuration de l’appareil de périphérie et mis à jour les fichiers Dockerfile pour installer des dépendances et ajouter des montages de liaisons aux conteneurs des modules. Ensuite, nous avons mis à jour la configuration du hub IoT pour router nos messages selon le type et gérer les chargements de fichier. Une fois tous les éléments en place, nous avons déployé les modules sur l’appareil IoT Edge et vérifié que les modules étaient exécutés correctement.

Des informations supplémentaires sont disponibles dans les pages suivantes :

* [Découvrir comment déployer des modules et établir des routes dans IoT Edge](module-composition.md)
* [Syntaxe des requêtes de routage des messages IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Routage des messages IoT Hub : maintenant avec le routage dans le corps du message](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Chargement de fichiers avec IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Passez à l’article suivant pour commencer à envoyer des données et voir votre solution en action.

> [!div class="nextstepaction"]
> [Envoyer des données via une passerelle transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)
