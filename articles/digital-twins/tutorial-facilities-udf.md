---
title: Surveiller un espace avec Azure Digital Twins | Microsoft Docs
description: Découvrez comment approvisionner vos ressources spatiales et surveiller les conditions de travail avec Azure Digital Twins en suivant les étapes de ce didacticiel.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1e5cb18b4e526cd0a0607f5bc93788fcf07430e1
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364232"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Didacticiel : Approvisionner votre bâtiment et surveiller les conditions de travail avec Azure Digital Twins

Ce didacticiel montre comment utiliser Azure Digital Twins pour surveiller les conditions de température et le niveau de confort souhaités pour vos espaces. Une fois que vous avez [configuré votre bâtiment exemple](tutorial-facilities-setup.md), vous pouvez l’approvisionner et exécuter des fonctions personnalisées dans vos données de capteur en suivant les étapes de ce didacticiel.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Définition des conditions à surveiller
> * Création d’une fonction définie par l’utilisateur
> * Simulation de données de capteur
> * Obtention des résultats d’une fonction définie par l’utilisateur

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous avez [configuré votre configuration Azure Digital Twins](tutorial-facilities-setup.md). Avant de poursuivre, assurez-vous que vous avez :
- un [compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F),
- une instance de Digital Twins en cours d’exécution, 
- les [exemples Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) téléchargés et extraits sur votre machine de travail, 
- [Le kit SDK .NET Core version 2.1.403 ou supérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour générer et exécuter l’exemple. Exécutez `dotnet --version` pour vérifier que la version appropriée est installée. 
- [Visual Studio Code](https://code.visualstudio.com/) pour explorer l’exemple de code. 

## <a name="define-conditions-to-monitor"></a>Définition des conditions à surveiller
Vous pouvez définir un ensemble de conditions spécifiques à surveiller dans les données de capteur ou de l’appareil, appelé **détecteur de problèmes**. Vous pouvez ensuite définir des fonctions appelées *fonctions définies par l’utilisateur*, qui exécutent une logique personnalisée sur les données provenant de vos espaces et appareils, quand les conditions spécifiées par les détecteurs de problèmes se produisent. Pour plus d’informations, consultez [Traitement des données et fonctions définies par l’utilisateur](concepts-user-defined-functions.md). 

À partir de l’exemple de projet **_occupation-quickstart_**, ouvrez le fichier **_src\actions\provisionSample.yaml_** dans Visual Studio Code. Notez la section qui commence par le type **matchers**. Chaque entrée sous ce type crée un détecteur de problèmes avec le **Nom** spécifié, qui surveille un capteur de type **dataTypeValue**. Notez sa relation avec l’espace nommé *Focus salle A1*, qui a un nœud **d’appareils**, qui contient quelques **capteurs**. Pour approvisionner un détecteur de problèmes effectuant le suivi d’un de ces capteurs, ses **dataTypeValue** doivent correspondre au **dataType** du capteur. 

Ajoutez le détecteur de problèmes suivant en-dessous des détecteurs de problèmes existants, en vous assurant que les clés sont alignées et que les espaces ne sont pas remplacés par des tabulations :

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Cela permet de suivre le capteur *SAMPLE_SENSOR_TEMPERATURE* que vous avez ajouté dans [le premier didacticiel](tutorial-facilities-setup.md). Notez que ces lignes sont également présentes dans le fichier *provisionSample.yaml* comme commentés dans les lignes ; vous pouvez simplement supprimer les marques de commentaire en effaçant le caractère « # » au début de chaque ligne. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Création d’une fonction définie par l’utilisateur
Les fonctions définies par l’utilisateur (UDF) vous permettent de personnaliser le traitement de vos données de capteur. Elles sont constituées d’un code JavaScript personnalisé pouvant être exécuté au sein de votre instance Digital Twins, lorsque des conditions spécifiques se produisent comme décrit par les détecteurs de problèmes. Vous pouvez créer des *détecteurs des problèmes* et des *fonctions définies par l’utilisateur* pour chaque capteur que vous souhaitez surveiller. Pour plus d’informations détaillées, consultez [Traitement des données et fonctions définies par l’utilisateur](concepts-user-defined-functions.md). 

Dans l’exemple de fichier *provisionSample.yaml*, recherchez une section commençant par le type **userdefinedfunctions**. Cette section configure une fonction définie par l’utilisateur avec un **nom** donné, qui agit sur la liste des détecteurs de problèmes sous **matcherNames**. Notez la façon dont vous pouvez fournir votre propre fichier JavaScript à l’UDF en tant que **script**. Notez également la section nommée **roleassignments**. Elle assigne le rôle *Administrateur d’espace* à la fonction définie par l’utilisateur, pour lui permettre d’accéder aux événements provenant d’un des espaces approvisionnés. 

1. Configurez l’UDF pour inclure le détecteur de problèmes de température en ajoutant ou en supprimant les marques de commentaire de la ligne suivante dans le nœud `matcherNames` du fichier *provisionSample.yaml* :

    ```yaml
            - Matcher Temperature
    ```

1. Ouvrez le fichier **_src\actions\userDefinedFunctions\availability.js_** dans votre éditeur. Il s’agit du fichier indiqué dans l’élément **script** du fichier *provisionSample.yaml*. La fonction définie par l’utilisateur dans ce fichier recherche des conditions d’absence de mouvement dans la salle, ainsi que des niveaux de dioxyde de carbone inférieurs à 1 000 ppm. Modifiez le fichier JavaScript pour surveiller la température en plus des autres conditions. Ajoutez les lignes suivantes du code pour rechercher des conditions où aucun mouvement n’est détecté dans la salle et les niveaux de dioxyde de carbone sont inférieurs à 1000 ppm température est inférieure à 78 degrés Fahrenheit.

   > [!NOTE]
   > Cette section modifie le fichier *src\actions\userDefinedFunctions\availability.js* afin que vous puissiez apprendre en détail une façon d’écrire une fonction définie par l’utilisateur. Toutefois, vous pouvez choisir d’utiliser directement le fichier [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) dans votre configuration. Ce fichier contient toutes les modifications requises pour ce didacticiel. Si vous utilisez ce fichier à la place, veillez à utiliser le nom de fichier approprié pour la clé **_script_** dans le fichier [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. En haut du fichier, ajoutez les lignes suivantes pour la température en-dessous du commentaire `// Add your sensor type here`:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Ajoutez les lignes suivantes après l’instruction qui définit `var motionSensor`, en-dessous du commentaire `// Add your sensor variable here`:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Ajoutez la ligne suivante après l’instruction qui définit `var carbonDioxideValue`, en-dessous du commentaire `// Add your sensor latest value here`:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Supprimez les lignes de code suivantes en dessous du commentaire `// Modify this line to monitor your sensor value` : 

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
    
    1. Supprimez les lignes de code suivantes en dessous du commentaire `// Modify these lines as per your sensor` :

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Remplacez-les par les lignes suivantes :

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Supprimez le bloc de code *if-else* suivant après le commentaire `// Modify this code block for your sensor` :

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
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
        
        L’UDF modifiée recherchera une condition où la salle est disponible et où son niveau de dioxyde de carbone et sa température se situent dans la plage acceptable. Il génère une notification avec l’instruction `parentSpace.Notify(JSON.stringigy(alert));` lorsque cette condition est remplie. Il définit la valeur de l’espace surveillé, sans tenir compte du fait que la condition est remplie ou non, avec le message correspondant.
    
    1. Enregistrez le fichier . 
    
1. Ouvrez la fenêtre de commande et accédez au dossier **_occupation-quickstart\src_**. Exécutez la commande suivante pour configurer votre graphique d’intelligence spatiale et la fonction définie par l’utilisateur. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Pour empêcher tout accès non autorisé à votre API de gestion Digital Twins, l’application **_occupation-quickstart_** vous oblige à vous connecter avec vos informations d’identification de compte Azure. Elle enregistre vos informations d’identification pour une courte période, afin que vous n’ayez pas besoin de vous connecter à chaque exécution. Lorsque ce programme s’exécute pour la première fois et lorsque vos informations d’identification enregistrées expirent, il vous dirige vers une page de connexion et vous donne un code spécifique à la session devant être saisi sur cette page. Suivez les instructions de l’invite pour vous connecter avec votre compte Azure.


1. Une fois que votre compte est authentifié, l’application commence à créer un exemple de graphique spatial tel que configuré dans le fichier *provisionSample.yaml*. Attendez la fin de l’approvisionnement,il prend quelques minutes. Une fois terminé, observez les messages dans la fenêtre de commande et notez comment votre graphique spatial est créé. Notez la façon dont il crée un hub IoT au niveau du nœud racine ou de `Venue`. 

1. À partir de la sortie dans la fenêtre de commande, copiez la valeur de `ConnectionString`, sous la section `Devices`, dans le presse-papiers. Vous en aurez besoin pour simuler la connexion de l’appareil dans la section suivante.

    ![Exemple d’approvisionnement](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Si vous obtenez un message d’erreur semblable à « L’opération d’E/S a été abandonnée en raison d’une sortie du thread ou d’une requête d’une application » au milieu de l’approvisionnement, essayez de réexécuter la commande. Cela peut se produire si le client HTTP a expiré en raison d’un problème de réseau.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Simulation de données de capteur
Dans cette section, vous allez utiliser le projet nommé *device-connectivity* dans l’exemple, pour simuler des données de capteur pour la détection du mouvement, de la température et du dioxyde de carbone. Ce projet génère des valeurs aléatoires pour les capteurs, puis les envoie au hub IoT en utilisant la chaîne de connexion d’appareil.

1. Dans une fenêtre de commande distincte, accédez à l’exemple Digital Twins, puis au dossier **_device-connectivity_**.

1. Exécutez cette commande pour vous assurer que les dépendances du projet sont correctes :

    ```cmd/sh
    dotnet restore
    ```

1. Ouvrez le fichier *appSettings.json* dans votre éditeur, modifiez les valeurs suivantes :
    1. *DeviceConnectionString* : affectez la valeur de `ConnectionString` dans la fenêtre de sortie à partir de la section précédente. Veillez à copier cette chaîne complètement, entre guillemets, pour que le simulateur se connecte correctement auprès du hub IoT.

    1. *HardwareId* au sein du tableau *Capteurs* : étant donné que vous simulez des événements à partir des capteurs approvisionnés dans votre instance Digital Twins, l’ID du matériel et les noms des capteurs dans ce fichier doivent correspondre au nœud `sensors` du fichier *provisionSample.yaml*. Ajoutez une nouvelle entrée pour le capteur de température. Le nœud **Capteurs** dans le fichier *appSettings.json* doit se présenter comme suit :

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
   > Étant donné que l’exemple de simulation ne communique pas directement avec votre instance Digital Twin, il ne nécessite pas d’authentification.

## <a name="get-results-of-user-defined-function"></a>Obtention des résultats d’une fonction définie par l’utilisateur
La fonction définie par l’utilisateur s’exécute chaque fois que votre instance reçoit des données du capteur et de l’appareil. Cette section interroge votre instance Digital Twins afin d’obtenir les résultats de la fonction définie par l’utilisateur. Vous pouvez alors visualiser la disponibilité d’une pièce, la qualité de l’air à l’intérieur et sa température quasiment en temps réel. 

1. Ouvrez la fenêtre de commande utilisée pour approvisionner l’exemple, ou une nouvelle fenêtre de commande et accédez de nouveau au dossier **_occupation-quickstart\src_** de l’exemple. 

1. Exécutez la commande suivante et connectez-vous lorsque vous y êtes invité :

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

La fenêtre de sortie affiche comment la fonction définie par l’utilisateur s’exécute et intercepte les événements à partir de la simulation d’appareil. 

   ![Exécuter des fonctions définies par l’utilisateur](./media/tutorial-facilities-udf/udf-running.png)

Selon si la condition surveillée est remplie ou non, la fonction définie par l’utilisateur définit la valeur d’espace avec le message approprié comme nous l’avons vu dans [la section ci-dessus](#udf), que la fonction `GetAvailableAndFreshSpaces` imprime sur la console. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre découverte d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce didacticiel :

1. Dans le menu de gauche du [Portail Azure](http://portal.azure.com), cliquez sur **Toutes les ressources**, puis sélectionnez votre groupe de ressources Digital Twins et **supprimez-le**.
2. Si vous le souhaitez, vous pouvez également supprimer les exemples d’applications sur votre machine de travail. 


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez approvisionné vos espaces et créé une infrastructure pour déclencher des notifications personnalisées, vous pouvez passer à l’un des didacticiels suivants. 

> [!div class="nextstepaction"]
> [Didacticiel : Recevoir des notifications à partir de vos espaces Azure Digital Twins à l’aide de Logic Apps](tutorial-facilities-events.md)

Ou,

> [!div class="nextstepaction"]
> [Didacticiel : Visualiser et analyser des événements à partir de vos espaces Azure Digital Twins à l’aide de Time Series Insights](tutorial-facilities-analyze.md)
