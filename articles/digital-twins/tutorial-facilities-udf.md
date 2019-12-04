---
title: 'Didacticiel : Superviser l’espace d’un appareil IoT - Azure Digital Twins | Microsoft Docs'
description: Découvrez comment provisionner vos ressources spatiales et superviser les conditions de travail avec Azure Digital Twins en suivant les étapes de ce tutoriel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 80fd1275f3bf9585ff8e40a94d0de2d422baec71
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383224"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Didacticiel : Provisionner votre bâtiment et superviser les conditions de travail avec la préversion d’Azure Digital Twins

Ce didacticiel montre comment utiliser la préversion d’Azure Digital Twins pour surveiller les conditions de température et le niveau de confort souhaités pour vos espaces. Après avoir [configuré votre exemple de bâtiment](tutorial-facilities-setup.md), vous pouvez le provisionner et exécuter des fonctions personnalisées dans vos données de capteur en suivant les étapes de ce tutoriel.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Définir les conditions à superviser.
> * Créer une fonction définie par l’utilisateur.
> * Simuler des données de capteur.
> * Obtenir les résultats d’une fonction définie par l’utilisateur.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel suppose que vous avez [terminé votre configuration Azure Digital Twins](tutorial-facilities-setup.md). Avant de poursuivre, assurez-vous que vous avez les éléments suivants :

- Un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une instance de Digital Twins en cours d’exécution. 
- Les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail. 
- Le [kit SDK .NET Core version 2.1.403 ou ultérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour générer et exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée. 
- [Visual Studio Code](https://code.visualstudio.com/) pour explorer l’exemple de code. 

> [!TIP]
> Utilisez un nom d’instance Digital Twins unique si vous provisionnez une nouvelle instance.

## <a name="define-conditions-to-monitor"></a>Définition des conditions à surveiller

Vous pouvez définir un ensemble de conditions spécifiques à superviser dans les données de capteur ou d’appareil ; il est appelé *détecteur de problèmes de correspondance*. Vous pouvez ensuite définir des fonctions appelées *fonctions définies par l’utilisateur*. Ces fonctions exécutent une logique personnalisée sur les données provenant de vos espaces et de vos appareils, quand les conditions spécifiées par les détecteurs de problèmes de correspondance se produisent. Pour plus d’informations, consultez [Traitement des données et fonctions définies par l’utilisateur](concepts-user-defined-functions.md). 

À partir de l’exemple de projet **occupancy-quickstart**, ouvrez le fichier **src\actions\provisionSample.yaml** dans Visual Studio Code. Notez la section qui commence par le type **matchers**. Chaque entrée sous ce type crée un détecteur de problèmes de correspondance avec la valeur **Name** (Nom) spécifiée. Cet outil de détection supervise un capteur de type **dataTypeValue**. Notez sa relation avec l’espace nommé *Focus Room A1* (Salle de travail A1), qui est doté d’un nœud **d’appareils** contenant quelques capteurs. Pour provisionner un détecteur de problèmes de correspondance effectuant le suivi d’un de ces capteurs, assurez-vous que sa valeur de **dataTypeValue** correspond à celle de **dataType** du capteur. 

Ajoutez le détecteur de problèmes de correspondance suivant sous ceux qui existent déjà. Assurez-vous que les clés sont alignées et que les espaces ne sont pas remplacés par des tabulations. Ces lignes sont également présentes dans le fichier *provisionSample.yaml* en tant que lignes commentées. Vous pouvez supprimer les marques de commentaire en retirant le caractère `#` au début de chaque ligne.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Ce détecteur de problèmes de correspondance va assurer le suivi du capteur `SAMPLE_SENSOR_TEMPERATURE` que vous avez ajouté dans [le premier tutoriel](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Création d’une fonction définie par l’utilisateur

Vous pouvez utiliser les fonctions définies par l’utilisateur pour personnaliser le traitement de vos données de capteur. Celles-ci sont constituées d’un code JavaScript personnalisé pouvant être exécuté au sein de votre instance Azure Digital Twins lorsque des conditions spécifiques se produisent, telles que décrites par les détecteurs de problèmes de correspondance. Vous pouvez créer des détecteurs de problèmes de correspondance et des fonctions définies par l’utilisateur pour chaque capteur que vous souhaitez superviser. Pour plus d’informations, consultez [Traitement des données et fonctions définies par l’utilisateur](concepts-user-defined-functions.md). 

Dans l’exemple de fichier *provisionSample.yaml*, recherchez une section qui commence par le type **userdefinedfunctions**. Cette section configure une fonction définie par l’utilisateur avec un **Nom** donné. Cette fonction définie par l’utilisateur agit sur la liste des détecteurs de problèmes de correspondance, sous **matcherNames** (Noms des détecteurs de problèmes de correspondance). Notez la façon dont vous pouvez fournir votre propre fichier JavaScript à l’UDF en tant que **script**.

Notez également la section nommée **roleassignments**. Il affecte le rôle d’administrateur de l’espace à la fonction définie par l’utilisateur. Ce rôle lui permet d’accéder aux événements qui proviennent d’un des espaces provisionnés. 

1. Configurez l’UDF pour inclure le détecteur de problèmes de température en ajoutant ou en supprimant les marques de commentaire de la ligne suivante dans le nœud `matcherNames` du fichier *provisionSample.yaml* :

    ```yaml
            - Matcher Temperature
    ```

1. Ouvrez le fichier **src\actions\userDefinedFunctions\availability.js** dans votre éditeur. Il s’agit du fichier référencé dans l’élément **script** du fichier *provisionSample.yaml*. La fonction définie par l’utilisateur dans ce fichier recherche des conditions d’absence de mouvement dans la salle, et des niveaux de dioxyde de carbone inférieurs à 1 000 ppm. 

   Modifiez le fichier JavaScript pour surveiller la température ainsi que d’autres conditions. Ajoutez les lignes de code suivantes pour rechercher les conditions où aucun mouvement n’est détecté dans la salle, où le niveau de dioxyde de carbone est inférieur à 1 000 ppm et celui de la température inférieur à 25,5 degrés Celsius (78 degrés Fahrenheit).

   > [!NOTE]
   > Cette section modifie le fichier *src\actions\userDefinedFunctions\availability.js* afin que vous puissiez apprendre en détail une façon d’écrire une fonction définie par l’utilisateur. Toutefois, vous pouvez choisir d’utiliser directement le fichier [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) dans votre configuration. Ce fichier contient toutes les modifications requises pour ce didacticiel. Si vous utilisez ce fichier à la place, veillez à utiliser le nom de fichier approprié pour la clé **script** dans le fichier [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. En haut du fichier, ajoutez les lignes suivantes pour la température en-dessous du commentaire `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Ajoutez les lignes suivantes après l’instruction qui définit `var motionSensor`, en-dessous du commentaire `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Ajoutez la ligne suivante après l’instruction qui définit `var carbonDioxideValue`, en-dessous du commentaire `// Add your sensor latest value here` :

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Supprimez les lignes de code suivantes en dessous du commentaire `// Modify this line to monitor your sensor value` :

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Remplacez-les par les lignes suivantes :

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Supprimez les lignes de code suivantes en dessous du commentaire `// Modify these lines as per your sensor` :

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Remplacez-les par les lignes suivantes :

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Supprimez le bloc de code *if-else* suivant après le commentaire `// Modify this code block for your sensor` :

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Remplacez-le par le bloc *if-else* suivant :

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    L’UDF modifiée recherchera une condition où la salle est disponible et où son niveau de dioxyde de carbone et sa température se situent dans la plage acceptable. Il génère une notification avec l’instruction `parentSpace.Notify(JSON.stringify(alert));` lorsque cette condition est remplie. Il définit la valeur de l’espace surveillé, sans tenir compte du fait que la condition est remplie ou non, avec le message correspondant.

    g. Enregistrez le fichier .

1. Ouvrez une fenêtre de commande et accédez au dossier **occupancy-quickstart\src**. Exécutez la commande suivante pour configurer le graphe d’intelligence spatiale et la fonction définie par l’utilisateur :

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Pour empêcher tout accès non autorisé à votre API de gestion Digital Twins, l’application **occupancy-quickstart** vous oblige à vous connecter avec vos informations d’identification de compte Azure. Elle enregistre vos informations d’identification pour une courte période, afin que vous n’ayez pas besoin de vous connecter à chaque exécution. Lorsque ce programme s’exécute pour la première fois et que vos informations d’identification enregistrées expirent, l’application vous dirige vers une page de connexion et vous donne un code propre à la session qui doit être entré sur cette page. Suivez les instructions de l’invite pour vous connecter avec votre compte Azure.

1. Une fois votre compte authentifié, l’application commence à créer un exemple de graphe spatial, tel que configuré dans *provisionSample.yaml*. Attendez la fin du provisionnement. Cette opération peut prendre quelques minutes. Examinez ensuite les messages dans la fenêtre de commande et voyez comment votre graphe spatial est créé. Remarquez la façon dont l’application crée un hub IoT au niveau du nœud racine ou de `Venue`.

1. À partir de la sortie dans la fenêtre de commande, copiez la valeur de `ConnectionString`, sous la section `Devices`, dans le presse-papiers. Vous en aurez besoin pour simuler la connexion de l’appareil à la section suivante.

    [![Provisionner l’exemple](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> Si au cours de l’opération de provisionnement, vous obtenez un message d’erreur semblable à celui-ci : « L’opération d’E/S a été abandonnée en raison d’une sortie du thread ou d’une requête d’application », essayez de réexécuter la commande. Il est possible que le client HTTP ait expiré suite à un problème de réseau.

## <a name="simulate-sensor-data"></a>Simulation de données de capteur

Dans cette section, vous utilisez le projet nommé *device-connectivity* dans l’exemple. Vous simulez des données de capteur pour la détection de mouvement, de température et de dioxyde de carbone. Ce projet génère des valeurs aléatoires pour les capteurs, puis les envoie au hub IoT en utilisant la chaîne de connexion d’appareil.

1. Dans une fenêtre de commande distincte, accédez à l’exemple Azure Digital Twins, puis au dossier **device-connectivity**.

1. Exécutez cette commande pour vous assurer que les dépendances du projet sont correctes :

    ```cmd/sh
    dotnet restore
    ```

1. Ouvrez le fichier [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) dans votre éditeur et modifiez les valeurs suivantes :

   a. **DeviceConnectionString** : affectez la valeur de `ConnectionString` dans la fenêtre de sortie de la section précédente. Copiez cette chaîne entièrement, entre guillemets, pour que le simulateur puisse se connecter correctement au hub IoT.

   b. **HardwareId** dans le tableau **Sensors** : comme vous simulez des événements à partir des capteurs provisionnés dans votre instance Azure Digital Twins, l’ID du matériel et les noms des capteurs contenus dans ce fichier doivent correspondre au nœud `sensors` du fichier *provisionSample.yaml*.

      Ajoutez une nouvelle entrée pour le capteur de température. Le nœud **Sensors** (Capteurs) dans le fichier *appsettings.json* doit se présenter de la façon suivante :

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Exécutez cette commande pour démarrer la simulation des événements d’appareil concernant la température, le mouvement et le dioxyde de carbone :

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Comme l’exemple de simulation ne communique pas directement avec votre instance Digital Twin, aucune authentification n’est nécessaire.

## <a name="get-results-of-the-user-defined-function"></a>Obtention des résultats de la fonction définie par l’utilisateur

La fonction définie par l’utilisateur s’exécute chaque fois que votre instance reçoit des données du capteur et de l’appareil. Cette section interroge votre instance Azure Digital Twins afin d’obtenir les résultats de la fonction définie par l’utilisateur. Vous pouvez alors visualiser la disponibilité d’une pièce, la qualité de l’air à l’intérieur et sa température pratiquement en temps réel. 

1. Ouvrez la fenêtre de commande que vous avez utilisée pour provisionner l’exemple, ou une nouvelle fenêtre de commande, et accédez de nouveau au dossier **occupancy-quickstart\src** de l’exemple.

1. Exécutez la commande suivante et connectez-vous lorsque vous y êtes invité :

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

La fenêtre de sortie illustre la façon dont la fonction définie par l’utilisateur s’exécute et intercepte les événements à partir de la simulation d’appareil. 

   [![Sortie de la fonction définie par l’utilisateur](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

Si la condition supervisée est remplie, la fonction définie par l’utilisateur détermine la valeur de l’espace avec le message approprié, comme nous l’avons vu [plus tôt](#create-a-user-defined-function). La fonction `GetAvailableAndFreshSpaces` affiche le message sur la console.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre exploration d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce tutoriel :

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis votre groupe de ressources Digital Twins et **Supprimer**.

    > [!TIP]
    > Si vous avez rencontré des difficultés pour supprimer votre instance de Digital Twins, une mise à jour du service a été déployée avec le correctif. Réessayez de supprimer votre instance.

2. Si nécessaire, supprimez les exemples d’applications de votre machine de travail.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez provisionné vos espaces et créé une infrastructure pour déclencher des notifications personnalisées, vous pouvez passer à l’un des tutoriels suivants :

> [!div class="nextstepaction"]
> [Tutoriel : Recevoir des notifications à partir de vos espaces Azure Digital Twins à l’aide de Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Tutoriel : Visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide de Time Series Insights](tutorial-facilities-analyze.md)
