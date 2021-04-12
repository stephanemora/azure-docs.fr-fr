---
title: Configurer une fonction dans Azure pour le traitement des données
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction dans Azure qui peut accéder à des jumeaux numériques et être déclenchée par ceux-ci.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f1ed4b9beda9848bba8fb12783f49dcf8016d3dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590617"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connecter des applications de fonction dans Azure pour le traitement des données

La mise à jour des jumeaux numériques en fonction des données s’effectue à l’aide de [**routes d’événements**](concepts-route-events.md), par le biais de ressources de calcul, par exemple une fonction qui est écrite à l’aide d’[Azure Functions](../azure-functions/functions-overview.md). Les fonctions Azure peuvent être utilisées pour mettre à jour un jumeau numérique en réponse à :
* Des données de télémétrie d’appareil provenant d’IoT Hub
* La modification d’une propriété ou autres données provenant d’un autre jumeau numérique au sein du graphe jumeau

Cet article explique comment créer une fonction dans Azure en vue de l’utiliser avec Azure Digital Twins. 

Voici un aperçu des étapes qui sont présentées :

1. Créer un projet Azure Functions dans Visual Studio
2. Écrire une fonction avec un déclencheur [Event Grid](../event-grid/overview.md)
3. Ajouter du code d’authentification à la fonction (pour pouvoir accéder à Azure Digital Twins)
4. Publier l’application de fonction dans Azure
5. Configurer l’accès de [sécurité](concepts-security.md) pour l’application de fonction

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Condition préalable : Configurer l’instance Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Créer une application de fonction dans Visual Studio

Dans Visual Studio 2019, sélectionnez _Fichier > Nouveau > Projet_. Recherchez et sélectionnez le modèle _Azure Functions_. Sélectionnez _Suivant_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue Nouveau projet. Le modèle de projet Azure Functions est mis en évidence.":::

Spécifiez un nom pour l’application de fonction, puis sélectionnez _Créer_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue permettant de configurer un nouveau projet, avec le nom du projet, l’emplacement de l’enregistrement, l’option Créer une solution et le nom de la solution.":::

Sélectionnez le type d’application de fonction *Déclencheur Event Grid* et sélectionnez _Créer_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue permettant de créer une application Azure Functions. L’option Déclencheur Event Grid est mise en évidence.":::

Une fois l’application de fonction créée, Visual Studio génère un exemple de code dans un fichier **Function1.cs** dans le dossier de votre projet. Cette courte fonction est utilisée pour journaliser des événements.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Capture d’écran de Visual Studio montrant la fenêtre du projet qui vient d’être créé. Cette fenêtre contient le code d’un exemple de fonction appelé Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Écrire une fonction avec un déclencheur Event Grid

Vous pouvez écrire une fonction en ajoutant le SDK à votre application de fonction. L’application de fonction interagit avec Azure Digital Twins à l’aide du [Kit de développement logiciel (SDK) Azure Digital Twins pour .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Pour utiliser le kit SDK, vous devez inclure les packages suivants dans votre projet. Vous pouvez soit installer les packages à l’aide du gestionnaire de package NuGet de Visual Studio, soit ajouter les packages avec `dotnet` dans un outil en ligne de commande.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Ensuite, dans votre Explorateur de solutions Visual Studio, ouvrez le fichier _Function1.cs_ dans lequel se trouve l’exemple de code, puis ajoutez les instructions `using` suivantes concernant ces packages à votre fonction.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Ajouter du code d’authentification à la fonction

Vous allez à présent déclarer les variables au niveau de la classe, et ajoutez le code d’authentification qui permettra à la fonction d’accéder à Azure Digital Twins. Vous allez ajouter ce qui suit à votre fonction dans le fichier _Function1.cs_.

* Code permettant de lire l’URL du service Azure Digital Twins en tant que **variable d’environnement**. Il est recommandé de lire l’URL du service à partir d’une variable d’environnement, plutôt que de la coder en dur dans la fonction. Vous définirez la valeur de cette variable d’environnement [plus loin dans cet article](#set-up-security-access-for-the-function-app). Pour plus d’informations sur les variables d’environnement, consultez [*Gérer votre application de fonction*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Une variable statique destinée à contenir une instance HttpClient. HttpClient est relativement coûteux à créer. Nous voulons donc éviter d’avoir à le faire pour chaque appel de fonction.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Vous pouvez utiliser les informations d’identification de l’identité managée dans Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Ajoutez une variable locale _DigitalTwinsClient_ à l’intérieur de votre fonction pour stocker votre instance du client Azure Digital Twins. Vous *ne devez pas* rendre cette variable statique dans votre classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Ajoutez un contrôle de valeur Null pour _adtInstanceUrl_ et wrappez la logique de votre fonction dans un bloc try/catch pour intercepter toutes les exceptions.

Une fois ces modifications effectuées, le code de votre fonction doit ressembler à ce qui suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Une fois votre application écrite, vous pouvez la publier sur Azure en effectuant les étapes de la section suivante.

## <a name="publish-the-function-app-to-azure"></a>Publier l’application de fonction dans Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>Vérifier la publication de la fonction

1. Connectez-vous avec vos informations d’identification dans le [portail Azure](https://portal.azure.com/).
2. Dans la barre de recherche en haut de la fenêtre, recherchez le **nom de votre application de fonction**.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Recherchez votre application de fonction avec son nom dans le portail Azure." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Dans la page *Application de fonction* qui s’ouvre, choisissez *Fonctions* dans les options de menu sur la gauche. Si votre fonction est correctement publiée, vous verrez le nom de votre fonction dans la liste.
Notez que vous devrez peut-être attendre quelques minutes ou actualiser la page avant de pouvoir voir votre fonction répertoriée dans la liste des fonctions publiées.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Affichez les fonctions publiées dans le portail Azure." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Pour que votre application de fonction puisse accéder à Azure Digital Twins, elle devra disposer d’une identité managée par le système avec les autorisations nécessaires pour accéder à votre instance d’Azure Digital Twins. Vous allez maintenant configurer cela.

## <a name="set-up-security-access-for-the-function-app"></a>Configurer l’accès de sécurité pour l’application de fonction

Vous pouvez configurer l’accès de sécurité pour l’application de fonction en utilisant l’interface Azure CLI ou le portail Azure. Suivez les étapes correspondant à l’option choisie.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vous pouvez exécuter ces commandes dans [Azure Cloud Shell](https://shell.azure.com) ou dans une [installation locale d’Azure CLI](/cli/azure/install-azure-cli).
Vous pouvez utiliser l’identité managée par le système de l’application de fonction et lui attribuer le rôle _**Propriétaire des données Azure Digital Twins**_ pour votre instance Azure Digital Twins. L'instance sera ainsi autorisée à effectuer des activités de plan de données. Ensuite, rendez l’URL de l’instance Azure Digital Twins accessible à votre fonction en définissant une variable d’environnement.

### <a name="assign-access-role"></a>Attribuer le rôle d’accès

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Le squelette de fonction des exemples précédents nécessite qu’un jeton de porteur lui soit transmis, afin de pouvoir s’authentifier auprès d’Azure Digital Twins. Pour garantir que ce jeton du porteur sera transmis, vous devez configurer des autorisations [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) pour que l’application de fonction accède à Azure Digital Twins. Cette opération ne doit être effectuée qu’une seule fois pour chaque application de fonction.


1. Utilisez la commande suivante pour consulter les détails de l’identité managée par le système pour la fonction. Prenez note du champ _principalId_ dans la sortie.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Si le résultat est vide au lieu d’afficher les détails d’une identité, créez une autre identité managée par le système pour la fonction à l’aide de cette commande :
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > La sortie affiche alors les détails de l’identité, notamment la valeur _principalId_ nécessaire pour la prochaine étape. 

1. Utilisez la valeur _principalId_ dans la commande suivante afin d’attribuer l’identité de l’application de fonction au rôle _Propriétaire des données Azure Digital Twins_ pour votre instance Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Enfin, rendez l’URL de votre instance Azure Digital Twins accessible à votre fonction en définissant une **variable d’environnement**. Pour plus d’informations sur les variables d’environnement, consultez [*Gérer votre application de fonction*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du *nom d’hôte* de votre instance Azure Digital Twins. Pour afficher le nom d’hôte, ainsi que toutes les propriétés de votre instance, vous pouvez exécuter `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Effectuez les étapes suivantes dans le [portail Azure](https://portal.azure.com/).

### <a name="assign-access-role"></a>Attribuer le rôle d’accès

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Une identité managée affectée par le système permet aux ressources Azure de s’identifier auprès des services cloud (comme Azure Key Vault, par exemple), sans stocker les informations d’identification dans le code. Une fois activées, toutes les autorisations nécessaires peuvent être accordées par le biais du contrôle d’accès en fonction du rôle Azure. Le cycle de vie de ce type d’identité managée est lié au cycle de vie de cette ressource. De plus, chaque ressource ne peut avoir qu’une seule identité managée affectée par le système.

1. Dans le [portail Azure](https://portal.azure.com/), recherchez votre application de fonction en tapant son nom dans la barre de recherche. Sélectionnez votre application dans les résultats. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

1. Dans la page de l’application de fonction, sélectionnez _Identité_ dans la barre de navigation située à gauche pour utiliser une identité managée pour la fonction. Dans la page _Affecté par le système_, vérifiez que l’_état_ est défini sur **Activé**. (Si ce n’est pas le cas, définissez-le maintenant, puis *enregistrez* le changement.)

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Capture d’écran du portail Azure : dans la page Identité de l’application de fonction, l’option État est activée." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Sélectionnez le bouton _Attributions de rôles Azure_ qui ouvre la page *Attributions de rôles Azure*.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Capture d’écran du portail Azure : le bouton Attributions de rôle Azure sous Autorisations dans la page Identité de la fonction Azure est mis en évidence." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Sélectionnez _+ Ajouter une attribution de rôle (préversion)_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Capture d’écran du portail Azure : Option + Ajouter une attribution de rôle (préversion) mise en évidence dans la page Attributions de rôles Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Dans la page _Ajouter une attribution de rôle (préversion)_ qui s’ouvre, sélectionnez les valeurs suivantes :

    * **Portée** : groupe de ressources
    * **Abonnement** : sélectionnez votre abonnement Azure
    * **Groupe de ressources** : sélectionnez votre groupe de ressources dans la liste déroulante
    * **Rôle** : sélectionnez _Propriétaire des données Azure Digital Twins_ dans la liste déroulante

    Ensuite, enregistrez vos informations en cliquant sur le bouton _Enregistrer_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Capture d’écran du portail Azure : boîte de dialogue permettant d’ajouter une nouvelle attribution de rôle (préversion). Celle-ci contient les champs Étendue, Abonnement, Groupe de ressources et Rôle.":::

### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Pour rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction, vous pouvez définir une **variable d’environnement** pour celle-ci. Pour plus d’informations sur les variables d’environnement, consultez [*Gérer votre application de fonction*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Les paramètres d’application sont exposés en tant que variables d’environnement pour accéder à l’instance Azure Digital Twins. 

Pour définir une variable d’environnement avec l’URL de votre instance, commencez par obtenir l’URL en recherchant le nom d’hôte de votre instance Azure Digital Twins. Recherchez votre instance dans la barre de recherche du [portail Azure](https://portal.azure.com). Ensuite, sélectionnez _Vue d’ensemble_ dans la barre de navigation de gauche pour afficher le _Nom d’hôte_. Copiez cette valeur.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Capture d’écran du portail Azure : dans la page Vue d’ensemble de l’instance Azure Digital Twins, la valeur du nom d’hôte est mise en évidence.":::

Vous pouvez maintenant créer un paramètre d’application en effectuant les étapes suivantes :

1. Recherchez votre application de fonction dans la barre de recherche du portail, puis sélectionnez-la dans les résultats.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

1. Sélectionnez _Configuration_ dans la barre de navigation située à gauche. Sous l’onglet _Paramètres d’application_, sélectionnez _+ Nouveau paramètre d’application_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Capture d’écran du portail Azure : dans la page Configuration de l’application de fonction, le bouton Nouveau paramètre d’application est mis en évidence.":::

1. Dans la fenêtre qui s’ouvre, utilisez la valeur de nom d’hôte copiée ci-dessus pour créer un paramètre d’application.
    * **Nom** : ADT_SERVICE_URL
    * **Valeur** : https://{nom-hôte-azure-digital-twins}
    
    Sélectionnez _OK_ pour créer un paramètre d’application.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Capture d’écran du portail Azure : les champs Nom et Valeur sont renseignés dans la page Ajouter/modifier le paramètre d’application, et le bouton OK est mis en évidence.":::

1. Une fois le paramètre créé, il doit normalement s’afficher à nouveau sous l’onglet _Paramètres d’application_. Vérifiez qu’*ADT_SERVICE_URL* s’affiche dans la liste, puis enregistrez le nouveau paramètre d’application en sélectionnant le bouton _Enregistrer_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Capture d’écran du portail Azure : le nouveau paramètre ADT_SERVICE_URL est mis en évidence dans la page Paramètres de l’application. Le bouton Enregistrer est également mis en évidence.":::

1. Pour être pris en compte, tout changement apporté aux paramètres d’application nécessite un redémarrage de l’application. Par conséquent, sélectionnez _Continuer_ pour redémarrer votre application quand vous y êtes invité.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Capture d’écran du portail Azure : notification indiquant que toute modification apportée aux paramètres d’application entraînera le redémarrage de votre application. Le bouton Continuer est mis en évidence.":::

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu les étapes permettant de configurer une application de fonction dans Azure en vue d’une utilisation avec Azure Digital Twins.

Dans la suite, vous allez voir comment créer une fonction de base pour ingérer des données IoT Hub dans Azure Digital Twins :
* [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md)
