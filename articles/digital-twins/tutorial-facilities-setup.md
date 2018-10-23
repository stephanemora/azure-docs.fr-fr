---
title: Déployer Azure Digital Twins | Microsoft Docs
description: Découvrez comment déployer votre instance d’Azure Digital Twins et configurer vos ressources spatiales grâce à la procédure décrite dans ce didacticiel.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363331"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Didacticiel : Déployer Azure Digital Twins et configurer un graphique spatial

Le service Azure Digital Twins vous permet de rassembler des personnes, des lieux et des appareils dans un système spatial cohérent. Cette série de didacticiels montre comment utiliser Azure Digital Twins pour détecter l’occupation d’une salle avec des conditions optimales de qualité d’air et de température. Ces didacticiels vous guident dans une application de console .NET pour créer un scénario de bâtiment hébergeant des bureaux, comprenant plusieurs étages, avec des salles à chaque étage. Dans les salles se trouvent des appareils, avec des capteurs attachés qui détectent les mouvements, la température ambiante et la qualité de l’air. Vous allez apprendre à répliquer les entités et zones physiques du bâtiment, tels que les objets numériques à l’aide du service Digital Twins. Vous allez simuler des événements d’appareil à l’aide d’une autre console d’application. Vous allez ensuite apprendre à surveiller les événements provenant de ces entités et zones physiques presque en temps réel. Un administrateur de bureau peut utiliser ces informations pour aider un employé travaillant dans ce bâtiment à réserver des salles de réunion avec des conditions optimales. Un responsable des installations de bureau peut utiliser votre configuration pour déterminer les tendances d’utilisation des salles, ainsi que surveiller les conditions de travail à des fins de maintenance.

Dans le premier didacticiel de cette série, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer Digital Twins
> * Accorder des autorisations à votre application
> * Modifier l’exemple d’application Digital Twins
> * Approvisionner votre bâtiment


Ces didacticiels utilisent et modifient les mêmes exemples que ceux du [démarrage rapide de recherche de salles disponibles](quickstart-view-occupancy-dotnet.md), pour une couverture plus détaillée des concepts.


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous ne possédez pas de compte Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Les exemples Digital Twins utilisés dans ces didacticiels sont écrits en C#. Veillez à installer le [kit SDK .NET Core version 2.1.403 ou supérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour créer et exécuter l’exemple. Vérifiez si la version appropriée est installée sur votre machine en exécutant `dotnet --version` dans une fenêtre de commande.

- [Visual Studio Code](https://code.visualstudio.com/) pour explorer l’exemple de code. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Déployer Digital Twins

Utilisez les étapes de cette section pour créer une instance du service Digital Twins. Une seule instance peut être créée par abonnement ; passez à la section suivante si vous en avez déjà une en cours d’exécution. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Accorder des autorisations à votre application

Digital Twins utilise [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pour contrôler [l’accès en lecture/écriture](../active-directory/develop/v1-permissions-and-consent.md) au service. Les applications devant se connecter à votre instance Digital Twins doivent être inscrites auprès d’Azure Active Directory. La procédure décrite dans cette section montre comment inscrire votre exemple d’application.

Si vous avez déjà une *inscription d’application*, vous pouvez la réutiliser pour votre exemple. Toutefois, parcourez cette section pour vous assurer que l’inscription de votre application est correctement configurée.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Configurer l’exemple Digital Twins

Cette section vous guide dans une application Digital Twins qui communique avec les [API REST Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Télécharger l’exemple
Si vous avez déjà les exemples téléchargés pour le [démarrage rapide de recherche de salles disponibles](quickstart-view-occupancy-dotnet.md), vous pouvez ignorer ces étapes.

1. Téléchargez les [exemples .Net Digital Twins](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Extrayez le contenu du dossier ZIP sur votre machine. 

### <a name="explore-the-sample"></a>Explorer l’exemple
Dans l’exemple de dossier extrait, ouvrez le fichier **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** dans Visual Studio Code. Il inclut deux projets : 

1. L’exemple d’approvisionnement **_occupancy-quickstart_** vous permet de configurer et d’approvisionner un [graphique d’intelligence spatiale](concepts-objectmodel-spatialgraph.md#graph), qui est l’image numérisée de vos espaces physiques et des ressources qu’ils contiennent. Il utilise un [modèle objet](concepts-objectmodel-spatialgraph.md#model) qui définit les objets d’un bâtiment intelligent. Pour obtenir une liste complète des objets et des API REST Digital Twins, consultez [cette documentation sur l’API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou l’URL **API de gestion** qui a été créée pour [votre instance](#deploy).

   Pour explorer l’exemple et voir comment il communique avec votre instance Digital Twins, vous pouvez commencer avec le dossier **_src\actions_**. Les fichiers dans ce dossier implémentent les commandes que vous allez utiliser dans ces didacticiels :
    - le fichier *provisionSample.cs* montre comment approvisionner votre graphique spatial,
    - le fichier *getSpaces.cs* récupère des informations sur les espaces approvisionnés,
    - le fichier *getAvailableAndFreshSpaces.cs* récupère les résultats d’une fonction personnalisée appelée par une fonction définie par un utilisateur, et
    - le fichier *createEndpoints.cs* crée des points de terminaison pour interagir avec d’autres services.

1. L’exemple de simulation **_device-connectivity_** simule les données de capteur et les envoie au hub IoT approvisionné pour votre instance Digital Twins. Vous allez utiliser cet exemple dans [le didacticiel suivant, après avoir approvisionné votre graphique spatial](tutorial-facilities-udf.md#simulate). Les identificateurs d’appareils et de capteurs utilisés pour configurer cet exemple doivent être identiques à ceux que vous allez utiliser pour approvisionner votre graphique.

### <a name="configure-the-provisioning-sample"></a>Configurer l’exemple d’approvisionnement
1. Ouvrez une fenêtre de commande et accédez à l’exemple téléchargé. Exécutez la commande suivante :

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Restaurez les dépendances sur l’exemple de projet en exécutant cette commande :

    ```cmd/sh
    dotnet restore
    ```

1. Dans Visual Studio Code, ouvrez le fichier **_appSettings.json_** appartenant au projet **occupancy-quickstart** et mettez à jour les valeurs suivantes :
    1. *ClientId*: entrez **l’ID d’application** de votre inscription d’application AAD, indiqué dans la section pour [définir des autorisations d’application](#permissions).
    1. *Locataire*: entrez **l’ID de répertoire** de votre [locataire AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), également indiqué dans la section pour [définir des autorisations d’application](#permissions).
    1. *BaseUrl*: entrez l’URL de votre instance Digital Twins. Pour obtenir cette URL, remplacez les espaces réservés dans cette URL par les valeurs de votre instance : **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. Vous pouvez également obtenir cette URL en modifiant l’URL **API de gestion** de la [section de déploiement](#deploy) en remplaçant**swagger/** par **api/v1.0/**.

1. Affichez la liste des fonctionnalités Digital Twins que vous pouvez explorer à l’aide de l’exemple, en exécutant la commande suivante.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>Comprendre le processus d’approvisionnement
Cette section montre comment l’exemple approvisionne un graphique spatial d’un bâtiment. 

Dans Visual Studio Code, accédez au dossier **_occupancy-quickstart\src\actions_** et ouvrez le fichier *provisionSample.cs*. Notez la fonction suivante :

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Cette fonction utilise le fichier *provisionSample.yaml* dans le même dossier. Ouvrez ce fichier et notez la hiérarchie d’un bâtiment hébergeant des bureaux : le *lieu*, *l’étage*, la *zone*et les *salles*. Tous ces espaces physiques peuvent contenir des *appareils* et des *capteurs*. Chaque entrée a un élément `type` prédéfini, par exemple *Étage*, *Salle*. 

L’exemple de fichier *yaml* montre un graphique spatial utilisant le modèle objet Digital Twins `Default`. Ce modèle fournit des noms génériques pour la plupart des types (par exemple, Température pour SensorDataType, Carte pour SpaceBlobType) et des types d’espace (par exemple, Salle avec des sous-types FocusRoom, ConferenceRoom, etc.), ce qui est suffisant pour un bâtiment. Si vous deviez créer un graphique spatial pour un lieu de type différent, par exemple une usine, vous auriez probablement besoin d’un autre modèle objet. Vous pouvez rechercher les modèles disponibles en exécutant la commande `dotnet run GetOntologies` dans la ligne de commande pour l’exemple d’approvisionnement. Pour plus d’informations sur les graphiques spatiaux et les modèles objets, consultez [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Comprendre le graphique d’intelligence spatiale et les modèles objets Digital Twins). 

### <a name="modify-sample-spatial-graph"></a>Modifier l’exemple de graphique spatial
Le fichier *provisionSample.yaml* contient les nœuds suivants :

- **resources** : le nœud `resources` crée une ressource IoT Hub pour communiquer avec les appareils dans votre configuration. Un hub IoT au niveau du nœud racine de votre graphique peut communiquer avec tous les appareils et capteurs dans votre graphique.  

- **spaces** : dans le modèle d’objet Digital Twins, l’élément `spaces` représente les emplacements physiques. Chaque espace a un élément `Type`, par exemple, *Région*, *Lieu*, ou un *client* et un élément `Name` convivial. Les espaces peuvent appartenir à d’autres espaces, ce qui crée une structure hiérarchique. Le graphique *provisionSample.yaml* a un graphique spatial d’un bâtiment imaginaire. Notez l’imbrication logique des espaces de type `Floor` au sein des éléments `Venue`, `Area` dans un étage, et des nœuds `Room` dans une zone. 

- **devices**: les espaces peuvent contenir des éléments `devices`, qui sont des entités physiques ou virtuelles gérant un certain nombre de capteurs. Par exemple, un appareil peut être le téléphone d’un utilisateur, un pod de capteur Raspberry Pi, une passerelle, etc. Dans le bâtiment imaginaire de votre exemple, notez que la salle nommée *Focus Room* (Salle de réflexion) contient un appareil *Raspberry Pi 3 A1*. Chaque nœud d’appareil est identifié par un élément `hardwareId` unique, qui est codé en dur dans l’exemple. Pour configurer cet exemple pour une production réelle, remplacez ces éléments par des valeurs de votre configuration.  

- **sensors** : un appareil peut contenir plusieurs éléments `sensors`, capables de détecter et d’enregistrer des modifications physiques comme la température, les mouvements, le niveau d’une batterie, etc. Chaque nœud de capteur est identifié de façon unique par un élément `hardwareId`, codé en dur ici. Pour une application réelle, remplacez ces éléments par les identificateurs uniques des capteurs de votre configuration. Le fichier *provisionSample.yaml* contient deux capteurs pour enregistrer *Mouvement* et *CarbonDioxide*. Ajoutez un autre capteur pour enregistrer *Température*, en ajoutant les lignes suivantes sous les lignes du capteur CarbonDioxide. Notez que ces éléments sont fournis dans le fichier *provisionSample.yaml* comme commentés dans les lignes ; vous pouvez simplement supprimer les marques de commentaire en effaçant le caractère « # » au début de chaque ligne. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Assurez-vous que l’alignement des clés `dataType` et `hardwareId` correspond aux instructions au-dessus de cet extrait de code. Assurez-vous également que votre éditeur ne remplace pas les espaces par des tabulations. 

Enregistrez et fermez le fichier *provisionSample.yaml*. Dans le didacticiel suivant, vous allez ajouter plus d’informations à ce fichier, puis approvisionner votre exemple de bâtiment Azure Digital Twins.


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez arrêter votre découverte d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce didacticiel :

1. Dans le menu de gauche du [Portail Azure](http://portal.azure.com), cliquez sur **Toutes les ressources**, puis sélectionnez votre groupe de ressources Digital Twins et **supprimez-le**.
2. Si vous le souhaitez, vous pouvez également supprimer l’exemple d’application sur votre machine de travail. 


## <a name="next-steps"></a>Étapes suivantes

Passez au prochain didacticiel de la série pour apprendre à implémenter une logique personnalisée et surveiller les conditions au sein de votre exemple de bâtiment. 
> [!div class="nextstepaction"]
> [Tutorial: Provision your building and monitor working conditions with Azure Digital Twins](tutorial-facilities-udf.md) (Didacticiel : Approvisionner votre bâtiment et surveiller les conditions de travail avec Azure Digital Twins)

