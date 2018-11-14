---
title: Rechercher des salles disponibles avec Azure Digital Twins (C#) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’applications .NET Core afin d’envoyer des données de télémétrie simulées sur les mouvements et le dioxyde de carbone d’un espace dans Azure Digital Twins. L’objectif est de trouver des salles disponibles avec de l’air frais à l’aide des API de gestion après des calculs de traitement dans le cloud.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/7/2018
ms.author: alinast
ms.openlocfilehash: 6e83ca543937948ad8028969cceca0f8787972c9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281216"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Démarrage rapide : Rechercher des salles disponibles avec Azure Digital Twins

Le service Azure Digital Twins vous permet de reproduire une image numérique de votre environnement physique. Vous pouvez ensuite recevoir des notifications sur les événements dans votre environnement et personnaliser les réponses que vous y apportez. 

Ce guide de démarrage rapide utilise [une paire d’exemples .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) permettant de numériser un immeuble de bureaux fictif. De plus, il vous montre comment trouver les pièces disponibles dans cet immeuble. Digital Twins vous permet d’associer plusieurs capteurs à votre environnement. Outre la disponibilité de la pièce, vous pouvez également vérifier si la qualité de l’air y est optimale à l’aide d’une simulation de détection du dioxyde de carbone. L’un des exemples d’applications génère des données de capteurs aléatoires pour vous aider à visualiser ce scénario.

La vidéo suivante résume la configuration à l’aide du guide de démarrage rapide :

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Prérequis

1. Si vous ne disposez pas d’un compte Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

1. Les deux applications de console que vous exécutez dans ce guide de démarrage rapide sont écrits à l’aide de C#. Vous devez installer [Le kit SDK .NET Core version 2.1.403 ou supérieure](https://www.microsoft.com/net/download) sur votre machine de développement. Si vous avez installé le Kit de développement logiciel (SDK) .NET Core, vous pouvez vérifier la version actuelle de C# sur la machine de développement qui exécute le `dotnet --version` dans une invite de commande.

1. Téléchargez l’[exemple de projet C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) et extrayez l’archive digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Créer une instance Digital Twins

Créez une instance Digital Twins dans le [portail](https://portal.azure.com) en suivant les étapes décrites dans cette section.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Définir les autorisations pour votre application

Cette section enregistre votre exemple d’application dans Azure Active Directory (AAD) afin de pouvoir accéder à votre instance Digital Twins. Si vous disposez déjà d’une inscription d’application AAD, vous pouvez vous en servir pour votre exemple en veillant à ce que sa configuration corresponde à celle indiquée dans cette section. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Créer une application

Créez l’application d’occupation en suivant les étapes suivantes :

1. Ouvrez une invite de commande et accédez au dossier dans lequel vos fichiers digital-twins-samples-csharp-master.zip ont été extraits.
1. Exécutez `cd occupancy-quickstart/src`.
1. Exécutez `dotnet restore`.
1. Modifiez *appSettings.json* afin d’actualiser les variables suivantes :
    - *IdClient* : Entrez *ID d’application*de l’inscription de votre application AAD, tel qu’indiqué dans la section précédente.
    - *Locataire* : Entrez l’*Id de répertoire* de votre locataire AAD, également indiqué dans la section précédente.
    - *UrlBase* : L’URL de l’*API de gestion*  de votre instance Digital Twins dans le format suivant `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Remplacez les espaces réservés dans cette URL par les valeurs de votre instance de la section précédente.

## <a name="provision-graph"></a>Approvisionner le graphique

Cette étape permet d’approvisionner votre graphique spatial Digital Twins avec plusieurs espaces, un appareil, deux capteurs, une fonction personnalisée et une attribution de rôle. Le graphique spatial est approvisionné à l’aide du fichier [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Exécutez `dotnet run ProvisionSample`.
    >[!NOTE]
    >Pour authentifier l’utilisateur à Azure AD, nous utilisons l’outil Device Login Azure CLI. L’utilisateur doit entrer un code spécifique pour s’authentifier via la page de [connexion Microsoft](https://microsoft.com/devicelogin). Après avoir entré le code, suivez les étapes pour vous authentifier. L’utilisateur est invité à s’authentifier chaque fois que l’outil est en cours d’exécution.
    
    >[!TIP]
    > Si l’erreur suivante se produit lors de l’exécution de cette étape, veuillez vous assurer que vos variables ont été copiées correctement. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. L’étape d’approvisionnement peut prendre quelques minutes environ. Elle configure également un IoT Hub dans votre instance Digital Twins et le met en boucle jusqu’à ce que IoTHub se trouve avec Status=`Running`.

    ![Exemple d’approvisionnement][4]

1. À la fin de l’exécution, copiez le `ConnectionString` de l’appareil pour l’utiliser dans l’exemple de simulateur d’appareil. Copiez uniquement la chaîne indiquée dans l’image ci-dessous :

    ![Exemple d’approvisionnement][1]

## <a name="send-sensor-data"></a>Envoyer les données du capteur

Vous pouvez créer et exécuter l’application du simulateur de capteur en suivant les étapes ci-dessous :

1. Dans le dossier digital-twins-samples-csharp-master, ouvrez une nouvelle invite de commande et accédez au projet que vous avez téléchargé.
1. Exécutez `cd device-connectivity`.
1. Exécutez `dotnet restore`.
1. Modifiez *appsettings.json* pour actualiser *DeviceConnectionString* avec le `ConnectionString` ci-dessus.
1. Exécutez `dotnet run` pour commencer à envoyer les données du capteur. Vous devriez voir qu’elles sont envoyées au service Digital Twins comme dans l’image ci-dessous :

     ![Connectivité des appareils][2]

1. Laissez ce simulateur fonctionner pour voir les résultats côte à côte avec la prochaine action d’étape. Cette fenêtre affiche les données de simulation des capteurs envoyées à Digital Twins et l’étape suivante effectue des requêtes en temps réel pour trouver les pièces disponibles avec de l’air frais.

    >[!TIP]
    > Si l’erreur suivante se produit lors de l’exécution de cette étape, veuillez vous assurer que votre `DeviceConnectionString` a été copié correctement.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Rechercher des espaces disponibles avec de l’air frais

L’exemple des capteurs simule les valeurs de données aléatoires de deux capteurs : mouvements et dioxyde de carbone. Les espaces disponibles avec de l’air frais sont définies dans notre échantillon par l’absence de présence dans la pièce et un niveau de dioxyde de carbone inférieur à 1 000 ppm. Si la condition n’est pas remplie, l’espace n’est pas disponible ou la qualité de l’air est mauvaise.

1. Ouvrez l’invite de commande que vous avez utilisée pour exécuter l’étape d’approvisionnement ci-dessus.
1. Exécutez `dotnet run GetAvailableAndFreshSpaces`.
1. Examinez côte à côte cette invite de commande et l’invite de commande des données de capteur, comme indiqué ci-dessous. 
1. Une invite de commande envoie toutes les 5 secondes à Digital Twins les données de la simulation des mouvements et du dioxyde de carbone. L’autre commande lit en temps réel le graphique pour trouver les salles disponibles avec de l’air frais à partir de données simulées de façon aléatoire. Une de ces conditions s’affiche presque en temps réel en fonction des dernières données envoyées par le capteur :
    - Salles disponibles avec de l’air frais.
    - Salle occupée ou de mauvaise qualité de l’air.

     ![Obtenir des espaces disponibles avec de l’air frais][3]

Pour comprendre ce qui s’est passé dans ce guide de démarrage rapide et comment les API ont été appelées, ouvrez [Visual Studio Code](https://code.visualstudio.com/Download) avec le projet du code de l’espace de travail qui se trouve dans le menu digital-twins-samples-charp (voir commande ci-dessous). Les didacticiels approfondissent le code, vous expliquent comment modifier les données de configuration et vous montrent les API appelées. Pour en savoir plus sur les API de gestion, consultez votre page Digital Twins Swagger `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger` ou parcourez [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) pour plus de commodité. 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Les didacticiels expliquent en détail comment créer une application pour les gestionnaires d’installations afin d’accroître la productivité des occupants et d’exploiter le bâtiment avec plus d’efficacité.

Si vous prévoyez de suivre les tutoriels, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce guide de démarrage rapide :

1. Supprimez le dossier créé lors du téléchargement de l’exemple de référentiel.
1. Dans le menu de gauche du [Portail Azure](http://portal.azure.com), cliquez sur **Toutes les ressources**, puis sélectionnez votre ressource Digital Twins. Dans la partie supérieure du volet **Toutes les ressources**, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Ce guide de démarrage rapide vous a présenté une vue d’ensemble d’un scénario simple de recherche de locaux avec de bonnes conditions de travail. Pour une analyse plus approfondie de ce scénario, consultez le didacticiel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Déployer Azure Digital Twins et configurer un graphique spatial](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
