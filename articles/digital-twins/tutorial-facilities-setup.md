---
title: 'Tutoriel : Déployer un environnement en préversion et un graphe spatial - Azure Digital Twins | Microsoft Docs'
description: Découvrez comment déployer votre instance d’Azure Digital Twins et configurer vos ressources spatiales en suivant les étapes de ce tutoriel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 16e4a7e2f06d2630c970f8daa4428e7a184a79df
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163039"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutoriel : Déployer la préversion d’Azure Digital Twins et configurer un graphique spatial

Vous pouvez utiliser le service Azure Digital Twins en préversion pour rassembler des personnes, des lieux et des appareils dans un système spatial homogène. Cette série de tutoriels montre comment utiliser Azure Digital Twins pour détecter l’occupation d’une salle avec des conditions optimales de qualité d’air et de température. 

Ces tutoriels vous guident dans une application de console .NET pour créer un scénario de bâtiment hébergeant des bureaux. L’immeuble comprend plusieurs étages et plusieurs salles à chaque étage. Dans les salles se trouvent des appareils auxquels sont reliés des capteurs qui détectent les mouvements, la température ambiante et la qualité de l’air. 

Vous allez apprendre à répliquer les entités et les zones physiques du bâtiment, tels que des objets numériques en utilisant le service Azure Digital Twins. Vous allez simuler des événements d’appareil à l’aide d’une autre application console. Ensuite, vous allez apprendre à superviser presque en temps réel les événements qui proviennent de ces entités et zones physiques. 

Un administrateur de bureau peut utiliser ces informations pour aider un employé travaillant dans ce bâtiment à réserver des salles de réunion avec des conditions optimales. Un responsable des locaux de bureau peut utiliser votre configuration pour obtenir les tendances d’utilisation des salles, et également surveiller les conditions de travail à des fins de maintenance.

Dans le premier didacticiel de cette série, vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer Digital Twins.
> * Accorder des autorisations à votre application.
> * Modifier un exemple d’application Digital Twins.
> * Provisionner votre bâtiment.

Ces didacticiels utilisent et modifient les mêmes exemples que ceux du [démarrage rapide de recherche de salles disponibles](quickstart-view-occupancy-dotnet.md), pour une couverture plus détaillée des concepts.

## <a name="prerequisites"></a>Conditions préalables requises

- Un abonnement Azure. Si vous ne possédez pas de compte Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Le kit SDK .NET Core. Les exemples Azure Digital Twins utilisés dans ces tutoriels sont écrits en C#. Veillez à installer le [kit SDK .NET Core version 2.1.403 ou ultérieure](https://www.microsoft.com/net/download) sur votre machine de développement pour créer et exécuter l’exemple. Vérifiez que la version appropriée est installée sur votre machine en exécutant `dotnet --version` dans une fenêtre de commande.

- [Visual Studio Code](https://code.visualstudio.com/) pour explorer l’exemple de code. 

## <a name="deploy-digital-twins"></a>Déployer Digital Twins

Utilisez les étapes de cette section pour créer une instance du service Azure Digital Twins. Une seule instance peut être créée par abonnement. Passez à la section suivante si vous en avez déjà une en cours d’exécution. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Accorder des autorisations à votre application

Digital Twins utilise [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) pour contrôler [l’accès en lecture/écriture](../active-directory/develop/v2-permissions-and-consent.md) au service. Les applications devant se connecter à votre instance Digital Twins doivent être inscrites auprès d’Azure AD. La procédure décrite dans cette section montre comment inscrire votre exemple d’application.

Si vous disposez déjà d’une inscription d’application, vous pouvez la réutiliser pour votre exemple. Toutefois, parcourez cette section pour vous assurer que l’inscription de votre application est correctement configurée.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configurer l’exemple Digital Twins

Cette section vous guide dans une application Azure Digital Twins qui communique avec les [API REST Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Télécharger l’exemple

Si vous avez déjà les exemples téléchargés pour le [démarrage rapide de recherche de salles disponibles](quickstart-view-occupancy-dotnet.md), vous pouvez ignorer ces étapes.

1. Téléchargez les [exemples .NET Digital Twins](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extrayez le contenu du dossier zip sur votre machine.

### <a name="explore-the-sample"></a>Explorer l’exemple

Dans l’exemple de dossier extrait, ouvrez le fichier **digital-twins-samples-csharp\digital-twins-samples.code-workspace** dans Visual Studio Code. Il inclut deux projets :

* Vous pouvez utiliser l’exemple de provisionnement **occupancy-quickstart** pour configurer et provisionner un [graphe d’intelligence spatiale](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Ce graphe constitue l’image numérisée de vos espaces physiques et des ressources qu’ils contiennent. Il utilise un [modèle objet](concepts-objectmodel-spatialgraph.md#digital-twins-object-models) qui définit les objets d’un bâtiment intelligent. Pour obtenir une liste complète des objets et API REST Digital Twins, consultez [cette documentation sur les API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou l’URL de l’API de gestion qui a été créée pour [votre instance](#deploy-digital-twins).

   Pour explorer l’exemple et comprendre comment il communique avec votre instance Digital Twins, vous pouvez commencer avec le dossier **src\actions**. Les fichiers dans ce dossier implémentent les commandes que vous allez utiliser dans ces tutoriels :
    - Le fichier **provisionSample.cs** montre comment provisionner votre graphe spatial.
    - Le fichier **getSpaces.cs** récupère des informations sur les espaces provisionnés.
    - Le fichier **getAvailableAndFreshSpaces.cs** récupère les résultats d’une fonction personnalisée appelée fonction définie par l’utilisateur.
    - Le fichier **createEndpoints.cs** crée des points de terminaison pour interagir avec d’autres services.

* L’exemple de simulation **device-connectivity** simule les données de capteur et les envoie au hub IoT qui est provisionné pour votre instance Digital Twins. Vous allez utiliser cet exemple dans [le tutoriel suivant, après avoir provisionné votre graphe spatial](tutorial-facilities-udf.md#simulate-sensor-data). Les identificateurs d’appareils et de capteurs que vous utilisez pour configurer cet exemple doivent être les mêmes que ceux que vous allez utiliser pour provisionner votre graphe.

### <a name="configure-the-provisioning-sample"></a>Configurer l’exemple d’approvisionnement

1. Ouvrez une fenêtre de commande et accédez à l’exemple téléchargé. Exécutez la commande suivante :

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Restaurez les dépendances sur l’exemple de projet en exécutant cette commande :

    ```cmd/sh
    dotnet restore
    ```

1. Dans Visual Studio Code, ouvrez le fichier [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) dans le projet **occupancy-quickstart**. Utilisez les valeurs suivantes :
   * **ClientId** : entrez l’ID d’application de l’inscription de votre application Azure AD. Vous avez noté cet ID à la section où vous avez [défini des autorisations d’application](#grant-permissions-to-your-app).
   * **Tenant** : entrez l’ID de répertoire de votre [locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Vous avez également noté cet ID à la section où vous avez [défini des autorisations d’application](#grant-permissions-to-your-app).
   * **BaseUrl** : entrez l’URL de votre instance Digital Twins. Pour obtenir cette URL, remplacez les espaces réservés dans cette URL par les valeurs de votre instance : `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Vous pouvez également obtenir cette URL en modifiant l’URL API de gestion depuis [la section de déploiement](#deploy-digital-twins). Remplacez **swagger/** par **api/v1.0/** .

1. Examinez la liste des fonctionnalités Digital Twins que vous pouvez explorer à l’aide de l’exemple. Exécutez la commande suivante :

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Comprendre le processus de provisionnement

Cette section montre comment l’exemple approvisionne un graphique spatial d’un bâtiment.

Dans Visual Studio Code, accédez au dossier **occupancy-quickstart\src\actions** et ouvrez le fichier **provisionSample.cs**. Notez la fonction suivante :

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

Cette fonction utilise le fichier [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) dans le même dossier. Ouvrez ce fichier et notez la hiérarchie d’un bâtiment hébergeant des bureaux : *Venue (Lieu)* , *Floor (Étage)* , *Area (Zone)* et *Rooms (Salles)* . Tous ces espaces physiques peuvent contenir des *appareils* et des *capteurs*. Chaque entrée est dotée d’un élément `type` prédéfini, par exemple Floor (Étage), Room (Salle).

L’exemple de fichier **yaml** montre un graphe spatial qui utilise le modèle objet Digital Twins `Default`. Ce modèle fournit des noms génériques pour la plupart des types. Les noms génériques sont suffisants pour un bâtiment. Les exemples sont Temperature (Température) pour SensorDataType (TypeDonnéesCapteur), et Map (Carte) pour SpaceBlobType (TypeObjetBlobEspace). Un exemple de type d’espace est Room (Salle) avec des sous-types, tels que FocusRoom (SalleTravail), ConferenceRoom (SalleRéunion) et ainsi de suite. 

Si vous deviez créer un graphique spatial pour un lieu de type différent, par exemple une usine, vous auriez probablement besoin d’un autre modèle objet. Vous pouvez rechercher les modèles disponibles en exécutant la commande `dotnet run GetOntologies` dans la ligne de commande pour l’exemple de provisionnement. 

Pour plus d’informations sur les graphes spatiaux et les modèles objet, consultez [Présentation du graphe d’intelligence spatiale et des modèles objet Digital Twins](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modifier l’exemple de graphe spatial

Le fichier **provisionSample.yaml** contient les nœuds suivants :

- **Ressources** : le nœud `resources` crée une ressource Azure IoT Hub pour communiquer avec les appareils de votre configuration. Un hub IoT au niveau du nœud racine de votre graphe peut communiquer avec tous les appareils et tous les capteurs dans votre graphe.  

- **spaces** : dans le modèle d’objet Digital Twins, l’élément `spaces` représente les emplacements physiques. Chaque espace est doté d’un élément `Type`, par exemple Region (Région), Venue (Lieu) ou Customer (Client), et d’un élément `Name` convivial. Les espaces peuvent appartenir à d’autres espaces, ce qui crée une structure hiérarchique. Le fichier provisionSample.yaml contient le graphe spatial d’un bâtiment imaginaire. Remarquez l’imbrication logique des espaces de type `Floor` au sein de `Venue`, de `Area` à un étage, et des nœuds `Room` dans une zone. 

- **devices** : les espaces peuvent contenir des éléments `devices`, qui sont des entités physiques ou virtuelles gérant un certain nombre de capteurs. Par exemple, un appareil peut être le téléphone d’un utilisateur, un pod de capteur Raspberry Pi ou une passerelle. Dans le bâtiment imaginaire de votre exemple, notez que la salle nommée **Focus Room** (Salle de réflexion) contient un appareil **Raspberry Pi 3 A1**. Chaque nœud d’appareil est identifié par un élément `hardwareId` unique, qui est codé en dur dans l’exemple. Pour configurer cet exemple pour une production réelle, remplacez ces éléments par des valeurs de votre configuration.  

- **sensors** : un appareil peut contenir plusieurs `sensors`. Ils peuvent détecter et enregistrer des modifications physiques, telles que la température, les mouvements et le niveau de batterie. Chaque nœud de capteur est identifié de façon unique par un élément `hardwareId`, codé en dur ici. Pour une application réelle, remplacez ceux-ci en utilisant les identificateurs uniques des capteurs de votre configuration. Le fichier provisionSample.yaml contient deux capteurs pour enregistrer *Motion* (les mouvements) et *CarbonDioxide* (le dioxyde de carbone). Ajoutez un autre capteur pour enregistrer *Température*, en ajoutant les lignes suivantes sous les lignes du capteur CarbonDioxide. Ces éléments sont fournis dans provisionSample.yaml en tant que lignes commentées. Vous pouvez supprimer les marques de commentaire en retirant le caractère `#` au début de chaque ligne. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Assurez-vous que les clés `dataType` et `hardwareId` correspondent aux instructions données au-dessus de cet extrait de code. Assurez-vous également que votre éditeur ne remplace pas les espaces par des tabulations. 

Enregistrez et fermez le fichier provisionSample.yaml. Dans le tutoriel suivant, vous allez ajouter d’autres informations à ce fichier et provisionner votre exemple de bâtiment Azure Digital Twins.

> [!TIP]
> Vous pouvez afficher et modifier votre graphe spatial à l’aide de la [Visionneuse de graphe Azure Digital Twins](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez arrêter votre exploration d’Azure Digital Twins ici, vous pouvez supprimer les ressources créées dans ce tutoriel :

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**, puis votre groupe de ressources Digital Twins et **Supprimer**.

    > [!TIP]
    > Si vous avez rencontré des difficultés pour supprimer votre instance de Digital Twins, une mise à jour du service a été déployée avec le correctif. Réessayez de supprimer votre instance.

1. Si nécessaire, supprimez l’exemple d’application sur votre machine de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment implémenter une logique personnalisée et superviser les conditions au sein de votre exemple de bâtiment, passez au tutoriel suivant de cette série : 
> [!div class="nextstepaction"]
> [Tutoriel : Provisionner votre bâtiment et surveiller les conditions de travail avec Azure Digital Twins](tutorial-facilities-udf.md)