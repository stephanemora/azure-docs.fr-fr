---
title: Intégrer à Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Découvrez comment diffuser en continu la télémétrie Azure Digital Twins vers des clients à l’aide d’Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775328"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Intégrer Azure Digital Twins avec le service Azure SignalR

Dans cet article, vous allez apprendre à intégrer Azure Digital Twins avec [Azure SignalR Service](../azure-signalr/signalr-overview.md).

La solution décrite dans cet article vous permet de transmettre des données de télémétrie de jumeau numérique à des clients connectés, par exemple une page web unique ou une application mobile. Par conséquent, les clients sont mis à jour avec les métriques et l’état en temps réel des appareils IoT, sans qu’il soit nécessaire d’interroger le serveur ou d’envoyer de nouvelles requêtes HTTP pour les mises à jour.

## <a name="prerequisites"></a>Prérequis

Voici les conditions préalables que vous devez remplir avant de continuer :

* Avant d’intégrer votre solution à Azure SignalR Service dans cet article, vous devez suivre le [_**Tutoriel : Connecter une solution de bout en bout**_](tutorial-end-to-end.md) d’Azure Digital Twins, car cet article de guide pratique s’appuie dessus. Ce tutoriel vous guide dans la configuration d’une instance Azure Digital Twins qui fonctionne avec un appareil IoT virtuel pour déclencher des mises à jour de jumeaux numériques. Cet article de guide pratique permet de connecter ces mises à jour à un exemple d’application web à l’aide d’Azure SignalR Service.

* Vous aurez besoin des valeurs suivantes du tutoriel :
  - Rubrique Event Grid
  - Resource group
  - Nom App Service/de l’application de fonction
    
* [**Node.js**](https://nodejs.org/) doit être installé sur votre machine.

Vous devez également vous connecter au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

## <a name="solution-architecture"></a>Architecture de solution

Vous allez attacher Azure SignalR Service à Azure Digital Twins par le biais du chemin ci-dessous. Dans le diagramme, les sections A, B et C sont tirées du diagramme d’architecture des [prérequis du tutoriel de bout en bout](tutorial-end-to-end.md). Dans cet article de guide pratique, vous allez générer la section D sur l’architecture existante.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Une vue des services Azure dans un scénario de bout en bout. Décrit le flux de données depuis un appareil vers successivement IoT Hub, une fonction Azure (flèche B), une instance Azure Digital Twins (section A), Event Grid et une autre fonction Azure en vue du traitement (flèche C). La section D illustre le flux de données à partir du même Event Grid en flèche C vers une fonction Azure appelée « broadcast ». « broadcast » communique avec une autre fonction Azure nommée « negotiate », et les fonctions « broadcast » et « negotiate » communiquent toutes deux avec les appareils informatiques." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Téléchargement des exemples d'applications

Tout d’abord, téléchargez les exemples d’applications requis. Vous aurez besoin des deux éléments suivants :
* [**Exemples Azure Digital Twins de bout en bout**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) : cet exemple comprend une application *AdtSampleApp* contenant deux fonctions Azure pour déplacer des données dans une instance Azure Digital Twins (vous pouvez découvrir ce scénario plus en détail dans le [*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)). Il contient également un exemple d’application *DeviceSimulator* qui simule un appareil IoT générant une nouvelle valeur de température chaque seconde.
    - Si vous n’avez pas encore téléchargé l’exemple dans le cadre des [*prérequis*](#prerequisites) du tutoriel, suivez le [lien](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) de l’exemple, puis sélectionnez le bouton *Browse code* (Parcourir le code) situé sous le titre. Vous accédez alors au dépôt GitHub d’exemples, que vous pouvez télécharger au format *.ZIP* en sélectionnant le bouton *Code*, puis *Download ZIP* (Télécharger le ZIP).

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Vue du dépôt digital-twins-samples sur GitHub. La sélection du bouton Code entraîne l’ouverture d’une petite boîte de dialogue dans laquelle le bouton Download ZIP est mis en évidence." lightbox="media/includes/download-repo-zip.png":::

    Cette opération télécharge une copie du dépôt d’exemples sur votre machine : **digital-twins-samples-master.zip**. Décompressez le dossier.
* [**Exemple d’application web d’intégration SignalR**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/) : il s’agit d’un exemple d’application web React qui consomme des données de télémétrie Azure Digital Twins à partir d’un service Azure SignalR Service.
    -  Accédez à l’exemple de lien, puis utilisez le même processus de téléchargement pour télécharger une copie de l’exemple sur votre machine, comme _**digitaltwins-signalr-webapp-sample-main.zip**_. Décompressez le dossier.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Laissez la fenêtre du navigateur ouverte sur le Portail Azure, car vous l’utiliserez à nouveau dans la section suivante.

## <a name="publish-and-configure-the-azure-functions-app"></a>Publier et configurer l’application Azure Functions

Dans cette section, vous allez configurer deux fonctions Azure :
* **negotiate** : une fonction de déclencheur HTTP. Elle utilise la liaison d’entrée *SignalRConnectionInfo* pour générer et retourner des informations de connexion valides.
* **broadcast** - Une fonction de déclencheur [Event Grid](../event-grid/overview.md). Elle reçoit les données de télémétrie Azure Digital Twins via Event Grid et utilise la liaison de sortie de l’instance *SignalR* que vous avez créée à l’étape précédente pour diffuser le message à toutes les applications clientes connectées.

Démarrez Visual Studio (ou un autre éditeur de code de votre choix), puis ouvrez la solution de code dans le dossier *digital-twins-samples-master > ADTSampleApp*. Puis procédez comme suit pour créer les fonctions :

1. Dans le projet *SampleFunctionsApp*, créez une nouvelle classe C# appelée **SignalRFunctions.cs**.

1. Remplacez le contenu du fichier de classe par le code suivant :
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Dans la fenêtre *Console du gestionnaire de package* de Visual Studio ou toute fenêtre de commande sur votre machine, accédez au dossier *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp*, puis exécutez la commande suivante pour installer le package NuGet `SignalRService` dans le projet :
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Cela devrait résoudre tous les problèmes de dépendance dans la classe.

1. Publiez votre fonction dans Azure, en suivant les étapes décrites dans la [section *Publier l’application*](tutorial-end-to-end.md#publish-the-app) du tutoriel *Connecter une solution de bout en bout*. Vous pouvez la publier dans la même application de fonction ou dans le même service d’application que ceux utilisés dans le tutoriel de bout en bout [prérequis](#prerequisites) ou en créer une nouvelle, mais vous préférerez peut-être utiliser la même pour réduire la duplication. 

Configurez ensuite les fonctions pour communiquer avec votre instance Azure SignalR. Vous allez commencer par collecter la **chaîne de connexion** de l’instance SignalR, puis l’ajouter aux paramètres de l’application de fonctions.

1. Accédez au [portail Azure](https://portal.azure.com/), puis recherchez le nom de votre instance SignalR dans la barre de recherche située en haut du portail. Sélectionnez l’instance pour l’ouvrir.
1. Sélectionnez **Clés** dans le menu d’instance pour afficher les chaînes de connexion de l’instance du service SignalR.
1. Sélectionnez l’icône *Copier* pour copier la chaîne de connexion principale.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Capture d’écran du Portail Azure montrant la page Clés pour l’instance SignalR. L’icône « Copier dans le Presse-papiers » en regard de la CHAÎNE DE CONNEXION PRINCIPALE est mise en surbrillance." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Enfin, ajoutez votre **chaîne de connexion** Azure SignalR aux paramètres d’application de la fonction, en utilisant la commande Azure CLI suivante. Remplacez également les espaces réservés par le nom de votre groupe de ressources et le nom App Service/de l’application de fonction dans les [prérequis du tutoriel](how-to-integrate-azure-signalr.md#prerequisites). La commande peut être exécutée dans [Azure Cloud Shell](https://shell.azure.com) ou localement si Azure CLI est [installée sur votre ordinateur](/cli/azure/install-azure-cli) :
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    La sortie de cette commande imprime tous les paramètres d’application configurés pour votre fonction Azure. Recherchez l’élément `AzureSignalRConnectionString` en bas de la liste pour vérifier qu’il a été ajouté.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Extrait de sortie dans une fenêtre de commande, avec affichage d’un élément de liste appelé « AzureSignalRConnectionString »":::

#### <a name="connect-the-function-to-event-grid"></a>Connecter la fonction à Event Grid

Inscrivez ensuite la fonction Azure *broadcast* dans la **rubrique Event Grid** que vous avez créée lors des [prérequis du tutoriel](how-to-integrate-azure-signalr.md#prerequisites). Cela permet aux données de télémétrie de circuler du jumeau thermostat67 à la rubrique Event Grid et à la fonction. À partir de là, la fonction peut diffuser les données à tous les clients.

Pour ce faire, vous allez créer un **abonnement à un événement** à partir de votre rubrique Event Grid vers votre fonction Azure *broadcast* comme point de terminaison.

Dans le [portail Azure](https://portal.azure.com/), accédez à votre rubrique Event Grid en recherchant son nom dans la barre de recherche supérieure. Sélectionnez *+ Abonnement aux événements*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portail Azure : abonnement aux événements Event Grid":::

Dans la page *Créer un abonnement aux événements*, renseignez les champs comme suit (les champs remplis par défaut ne sont pas mentionnés) :
* *DÉTAILS DE L’ABONNEMENT AUX ÉVÉNEMENTS* > **Nom** : Donnez un nom à votre abonnement aux événements.
* *DÉTAILS DU POINT DE TERMINAISON* > **Type de point de terminaison** : Sélectionnez *Fonction Azure* dans les options de menu.
* *DÉTAILS DU POINT DE TERMINAISON* > **Point de terminaison** : Cliquez sur le lien *Sélectionner un point de terminaison*. Une fenêtre *Sélectionner une fonction Azure* s’ouvre :
    - Renseignez vos **Abonnement**, **Groupe de ressources**, **Application de fonction** et **Fonction** (*broadcast*). Il est possible que certaines de ces informations soient automatiquement renseignées une fois que vous avez sélectionné l’abonnement.
    - Cliquez sur **Confirmer la sélection**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Vue du Portail Azure de la création d’un abonnement aux événements. Les champs ci-dessus sont renseignés et les boutons « Confirmer la sélection » et « Créer » sont mis en surbrillance.":::

Dans la page *Créer un abonnement aux événements*, cliquez sur **Créer**.

À ce stade, vous devez normalement voir deux abonnements à des événements dans la page *Rubrique Event Grid*.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Portail Azure : vue de deux abonnements à des événements dans la page Rubrique Event Grid." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Configurer et exécuter l’application web

Dans cette section, vous verrez le résultat en action. Tout d’abord, configurez l’**exemple d’application web cliente** pour vous connecter au flux Azure SignalR que vous avez configuré. Ensuite, vous allez démarrer l’**exemple d’application d’appareil simulé** qui envoie des données de télémétrie via votre instance d’Azure Digital Twins. Après cela, vous pourrez consulter l’exemple d’application web pour voir les données de l’appareil simulé mettant à jour l’exemple d’application web en temps réel.

### <a name="configure-the-sample-client-web-app"></a>Configurer l’exemple d’application web cliente

Vous allez ensuite configurer l’exemple d’application web cliente. Commencez par recueillir l’**URL de point de terminaison HTTP** de la fonction *negotiate*, puis utilisez-la pour configurer le code d’application sur votre machine.

1. Accédez à la page [Applications de fonctions](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) du portail Azure, puis sélectionnez votre application de fonction dans la liste. Dans le menu de l’application, sélectionnez *Fonctions* et choisissez la fonction *negotiate*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Vue du Portail Azure de l’application de fonction, avec « Fonctions » mis en surbrillance dans le menu. La liste des fonctions est affichée dans la page, et la fonction « negotiate » est également mise en évidence.":::

1. Appuyez sur *Obtenir l’URL de la fonction* et copiez la valeur **jusqu’à _/api_ (n’incluez pas la partie _/negotiate?_ à la fin)** . Vous l’utiliserez à l’étape suivante.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Vue de la fonction « negotiate » dans le Portail Azure. Le bouton « Obtenir l’URL de la fonction » est mis en surbrillance, avec la partie de l’URL du début jusqu’à « /api »":::

1. À l’aide de Visual Studio ou de l’éditeur de code de votre choix, ouvrez le dossier décompressé _**digitaltwins-signalr-webapp-sample-main**_ que vous avez téléchargé dans la section [*Télécharger les exemples d’application*](#download-the-sample-applications).

1. Ouvrez le fichier *src/App.js* et remplacez l’URL de fonction dans `HubConnectionBuilder` par l’URL de point de terminaison HTTP de la fonction **negotiate** que vous avez enregistrée à l’étape précédente :

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Dans l’*invite de commandes développeur* de Visual Studio ou dans toute fenêtre de commande sur votre machine, accédez au dossier *digitaltwins-signalr-webapp-sample-main\src*. Exécutez la commande suivante pour installer les packages des nœuds dépendants :

    ```cmd
    npm install
    ```

Ensuite, définissez les autorisations dans votre application de fonction dans le Portail Azure :
1. Dans la page [Applications de fonction](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) du Portail Azure, sélectionnez l’instance de votre application de fonction.

1. Faites défiler le menu de l’instance et sélectionnez *CORS*. Sur la page CORS, ajoutez `http://localhost:3000` comme origine autorisée en l’entrant dans la zone vide. Cochez la case *Activer Access-Control-Allow-Credentials* et appuyez sur *Enregistrer*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Paramètre CORS dans la fonction Azure":::

### <a name="run-the-device-simulator"></a>Exécuter le simulateur d’appareil

Au cours du tutoriel de bout en bout prérequis, vous avez [configuré le simulateur d’appareil](tutorial-end-to-end.md#configure-and-run-the-simulation) pour envoyer des données via IoT Hub et à votre instance Azure Digital Twins.

Maintenant, il vous suffit de démarrer le projet de simulateur, situé dans *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln*. Si vous utilisez Visual Studio, vous pouvez ouvrir le projet, puis l’exécuter avec ce bouton dans la barre d’outils :

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Bouton de démarrage dans Visual Studio (projet DeviceSimulator)":::

Une fenêtre de console s’ouvre, affichant les messages de télémétrie de température simulée. Ils sont envoyés par le biais de votre instance Azure Digital Twins, où ils sont ensuite récupérés par les fonctions Azure et SignalR.

Vous n’avez rien d’autre à faire dans cette console, mais laissez-la s’exécuter pendant que vous effectuez l’étape suivante.

### <a name="see-the-results"></a>Afficher les résultats

Pour voir les résultats en action, démarrez l’**exemple d’application web d’intégration SignalR**. Vous pouvez effectuer cette opération à partir de n’importe quelle fenêtre de console située à l’emplacement *digitaltwins-signalr-webapp-sample-main\src* en exécutant la commande suivante :

```cmd
npm start
```

Cela ouvre une fenêtre de navigateur exécutant l’exemple d’application, qui affiche une jauge de température visuelle. Une fois que l’application est en cours d’exécution, vous devriez commencer à voir les valeurs de télémétrie de température du simulateur d’appareil qui se propagent via Azure Digital Twins reflétées par l’application web en temps réel.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Extrait de l’exemple d’application web cliente, présentant une jauge de température visuelle. La température reflétée est 67,52":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources créées dans cet article, effectuez les étapes suivantes pour les supprimer. 

Dans Azure Cloud Shell ou Azure CLI en local, vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](/cli/azure/group#az_group_delete). La suppression du groupe de ressources entraînera également la suppression...
* de l’instance Azure Digital Twins (du tutoriel de bout en bout)
* du IoT Hub et de l’inscription de l’appareil Hub (du tutoriel de bout en bout)
* de la rubrique Event Grid et des abonnements associés
* de l’application Azure Functions, y compris les trois fonctions et les ressources associées, comme le stockage
* de l’instance Azure SignalR

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Enfin, supprimez les dossiers d’exemples de projets que vous avez téléchargés sur votre machine locale (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* et leurs équivalents décompressés).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous configurez des fonctions Azure avec SignalR pour diffuser des événements de télémétrie Azure Digital Twins vers un exemple d’application cliente.

Ensuite, vous en apprendrez plus sur Azure SignalR Service :
* [*Qu’est-ce que le service Azure SignalR ?*](../azure-signalr/signalr-overview.md)

Ou apprenez-en davantage sur l’authentification de service SignalR Azure avec Azure Functions :
* [*Authentification par Azure SignalR Service*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)