---
title: Configurer une fonction dans Azure pour traiter des données
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction dans Azure qui peut accéder à des jumeaux numériques et être déclenchée par ceux-ci.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 03917b332d6ce204cda0ec072d213a0844690b94
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787830"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connecter des applications de fonction dans Azure pour le traitement des données

Les jumeaux numériques peuvent être mis à jour en fonction des données à l’aide de [routes d’événements](concepts-route-events.md) via des ressources de calcul. Par exemple, une fonction créée à l’aide d’[Azure Functions](../azure-functions/functions-overview.md) peut mettre à jour un jumeau numérique en réponse à :
* Des données de télémétrie d’appareil à partir d’Azure IoT Hub.
* La modification d’une propriété ou autres données provenant d’un autre jumeau numérique au sein du graphe jumeau.

Cet article explique comment créer une fonction dans Azure en vue de l’utiliser avec Azure Digital Twins. Pour créer une fonction, vous devez suivre les étapes de base suivantes :

1. Créer un projet Azure Functions dans Visual Studio
2. Écrire une fonction qui a un déclencheur [Azure Event Grid](../event-grid/overview.md) .
3. Ajouter du code d’authentification à la fonction pour pouvoir accéder à Azure Digital Twins.
4. Publier l’application de fonction dans Azure
5. Configurer la [sécurité](concepts-security.md) pour l’application de fonction

## <a name="prerequisite-set-up-azure-digital-twins"></a>Condition préalable : Configurer Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Créer une application de fonction dans Visual Studio

Dans Visual Studio 2019, sélectionnez **Fichier** > **Nouveau** > **Projet**. Recherchez le modèle **Azure Functions**. Sélectionnez **Suivant**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue Nouveau projet. Le modèle de projet Azure Functions est mis en évidence.":::

Spécifiez un nom pour l’application de fonction, puis sélectionnez __Créer__.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue permettant de configurer un nouveau projet. Les paramètres comprennent le nom du projet, l’emplacement de l’enregistrement, l’option Créer une solution et le nom de la solution.":::

Sélectionnez le type d’application de fonction **Déclencheur Event Grid**, puis __Créer__.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Capture d’écran de Visual Studio montrant la boîte de dialogue permettant de créer une application Azure Functions. L’option Déclencheur Event Grid est mise en évidence.":::

Une fois l’application de fonction créée, Visual Studio génère un exemple de code dans un fichier *Function1.cs* dans le dossier de votre projet. Cette courte fonction est utilisée pour journaliser des événements.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Capture d’écran de Visual Studio. La fenêtre de projet pour le nouveau projet s’affiche. Le code d’un exemple de fonction s’affiche dans un fichier nommé Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Écrire une fonction qui a un déclencheur Event Grid

Vous pouvez écrire une fonction en ajoutant le SDK à votre application de fonction. L’application de fonction interagit avec Azure Digital Twins à l’aide du [Kit de développement logiciel (SDK) Azure Digital Twins pour .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Pour utiliser le SDK, vous devez inclure les packages suivants dans votre projet. Installez les packages à l’aide du gestionnaire de package NuGet de Visual Studio. Ou ajoutez les packages à l’aide de `dotnet` dans un outil en ligne de commande.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Ensuite, dans l’Explorateur de solutions Visual Studio, ouvrez le fichier _Function1.cs_ qui comprend votre exemple de code. Ajoutez les instructions `using` suivantes pour les packages.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Ajouter du code d’authentification à la fonction

À présent, déclarez les variables au niveau de la classe, et ajoutez le code d’authentification qui permettra à la fonction d’accéder à Azure Digital Twins. Ajoutez les variables et le code à votre fonction dans le fichier _Function1.cs_ .

* **Code permettant de lire l’URL du service Azure Digital Twins en tant que variable d’environnement.** Il est recommandé de lire l’URL du service à partir d’une variable d’environnement, plutôt que de la coder en dur dans la fonction. Vous définirez la valeur de cette variable d’environnement [plus loin dans cet article](#set-up-security-access-for-the-function-app). Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Une variable statique destinée à contenir une instance HttpClient.** HttpClient est relativement coûteux à créer. Nous voulons donc éviter d’avoir à le créer pour chaque appel de fonction.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Informations d’identification de l’identité managée.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Une variable locale _DigitalTwinsClient_.** Ajoutez la variable à l'intérieur de votre fonction pour contenir votre instance de client Azure Digital Twins. Vous *ne devez pas* rendre cette variable statique dans votre classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Un contrôle de valeur Null pour _adtInstanceUrl_.** Ajoutez le contrôle de valeur Null, puis wrappez la logique de votre fonction dans un bloc try/catch pour intercepter toutes les exceptions.

Une fois ces modifications effectuées, le code de votre fonction doit ressembler à l’exemple suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Maintenant que votre application est écrite, vous pouvez la publier sur Azure.

## <a name="publish-the-function-app-to-azure"></a>Publier l’application de fonction dans Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Vérifier la publication de votre fonction

1. Connectez-vous avec vos informations d’identification dans le [portail Azure](https://portal.azure.com/).
2. Dans la zone de recherche en haut de la fenêtre, recherchez le nom de votre application de fonction, puis sélectionnez-la.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Capture d’écran montrant le portail Azure. Dans le champ de recherche, entrez le nom de l’application de fonction." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Dans la page **Application de fonction** qui s’ouvre, choisissez **Fonctions** dans le menu sur la gauche. Si votre fonction a bien été publiée, son nom apparaît dans la liste.

    > [!Note] 
    > Vous devrez peut-être attendre quelques minutes ou actualiser la page avant que votre fonction s’affiche dans la liste des fonctions publiées.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Affichez les fonctions publiées dans le portail Azure." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Pour accéder à Azure Digital Twins, votre application de fonction devra disposer d’une identité managée par le système avec les autorisations nécessaires pour accéder à votre instance d’Azure Digital Twins. Vous allez maintenant configurer cela.

## <a name="set-up-security-access-for-the-function-app"></a>Configurer l’accès de sécurité pour l’application de fonction

Vous pouvez configurer l’accès de sécurité pour l’application de fonction en utilisant l’interface Azure CLI ou le portail Azure. Suivez les étapes correspondant à l’option choisie.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Exécutez ces commandes dans [Azure Cloud Shell](https://shell.azure.com) ou dans une [installation locale d’Azure CLI](/cli/azure/install-azure-cli).
Vous pouvez utiliser l’identité managée par le système de l’application de fonction et lui attribuer le rôle **Propriétaire des données Azure Digital Twins** pour votre instance Azure Digital Twins. Le rôle donnera à l’application de fonction l’autorisation d’effectuer des activités de plan de données. Ensuite, rendez l’URL de l’instance accessible à votre fonction en définissant une variable d’environnement.

### <a name="assign-an-access-role"></a>Attribuer un rôle d’accès

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Le squelette de fonction dans les exemples précédents nécessite qu'un jeton du porteur lui soit passé. Si le jeton du porteur n’est pas passé, l’application de fonction ne peut pas s’authentifier avec Azure Digital Twins. 

Pour vous assurer que le jeton du porteur est transmis, configurez les autorisations des [identités managées](../active-directory/managed-identities-azure-resources/overview.md) pour que l’application de fonction puisse accéder à Azure Digital Twins. Vous ne configurez ces autorisations qu’une seule fois pour chaque application de fonction.


1. Utilisez la commande suivante pour consulter les détails de l’identité managée par le système pour la fonction. Prenez note du champ `principalId` dans la sortie.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>   
    ```

    >[!NOTE]
    > Si le résultat est vide au lieu d’afficher les détails d’une identité, créez une autre identité managée par le système pour la fonction à l’aide de cette commande :
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>    
    >```
    >
    > La sortie affiche alors les détails de l’identité, notamment la valeur `principalId` nécessaire pour la prochaine étape. 

1. Utilisez la valeur `principalId` dans la commande suivante afin d’affecter l’identité de l’application de fonction au rôle _Propriétaire de données Azure Digital Twins_ pour votre instance Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Rendez l’URL de votre instance accessible à votre fonction en définissant une variable d’environnement. Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du nom d’hôte de votre instance. Pour afficher le nom d’hôte, ainsi que toutes les propriétés de votre instance, exécutez `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Effectuez les étapes suivantes dans le [portail Azure](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Attribuer un rôle d’accès

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Une identité managée affectée par le système permet aux ressources Azure de s’identifier auprès des services cloud (comme Azure Key Vault, par exemple), sans stocker les informations d’identification dans le code. Une fois que vous avez activé l’identité managée affectée par le système, toutes les autorisations nécessaires peuvent être accordées par le biais du contrôle d’accès en fonction du rôle Azure. 

Le cycle de vie de ce type d’identité managée est lié au cycle de vie de cette ressource. De plus, chaque ressource ne peut avoir qu’une seule identité managée affectée par le système.

1. Dans le [portail Azure](https://portal.azure.com/), recherchez votre application de fonction en tapant son nom dans la zone de recherche. Sélectionnez votre application dans les résultats. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

1. Dans la page de l’application de fonction, sélectionnez __Identité__ dans le menu de gauche pour utiliser une identité managée pour la fonction. Sur la page __Attribué par le système__, vérifiez que le __Statut__ est défini sur **Activé**. Si ce n’est pas le cas, modifiez le statut maintenant, puis **enregistrez** la modification.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Capture d’écran du portail Azure : dans la page Identité de l’application de fonction, l’option État est activée." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Sélection d’__Attributions de rôles Azure__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Capture d’écran du portail Azure. Dans la page Identité de la fonction Azure, sous Autorisations, le bouton Attributions de rôle Azure est mis en surbrillance." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Sélectionnez __+ Ajouter une attribution de rôle (préversion)__ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Capture d’écran du portail Azure. Dans la page Attributions de rôle Azure, le bouton Ajouter une attribution de rôle (préversion) est mis en surbrillance." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Dans la page __Ajouter une attribution de rôle (préversion)__ , sélectionnez les valeurs suivantes :

    * **Portée** : _Groupe de ressources_
    * **Abonnement**: Sélectionnez votre abonnement Azure.
    * **Groupe de ressources** : Sélectionnez votre groupe de ressources.
    * **Rôle** : _Propriétaire des données Azure Digital Twins_

    Enregistrez les détails en sélectionnant __Enregistrer__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Capture d’écran du portail Azure, montrant comment ajouter une nouvelle attribution de rôle. La boîte de dialogue affiche les champs de l’étendue, de l’abonnement, du groupe de ressources et du rôle.":::

### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Pour rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction, vous pouvez définir une variable d’environnement pour celle-ci. Les paramètres d’application sont exposés en tant que variables d’environnement pour permettre d’accéder à l’instance Azure Digital Twins. Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Pour définir une variable d'environnement avec l'URL de votre instance, recherchez d'abord le nom d'hôte de votre instance : 

1. Recherchez votre instance dans le [portail Azure](https://portal.azure.com). 
1. Dans le menu de gauche, sélectionnez __Vue d’ensemble__. 
1. Copiez la valeur __Nom d’hôte__.

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text=" Capture d'écran du portail Azure. Sur la page Vue d’ensemble de l'instance, la valeur Nom d'hôte est mise en surbrillance. ":::

Vous pouvez maintenant créer un paramètre d’application :

1. Recherchez votre application de fonction dans la barre de recherche du portail, puis sélectionnez-la dans les résultats.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure. Le nom de l’application de fonction est indiqué dans la barre de recherche du portail. Le résultat de la recherche est mis en évidence.":::

1. Sur la gauche, sélectionnez __Configuration__ Puis, sous l’onglet __Paramètres de l’application__, sélectionnez __+ Nouveau paramètre d’application__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Capture d’écran du portail Azure. Dans l’onglet Configuration de l’application de fonction, le bouton Nouveau paramètre d’application est mis en évidence.":::

1. Dans la fenêtre qui s’ouvre, utilisez la valeur de nom d’hôte copiée pour créer un paramètre d’application.
    * **Nom** : ADT_SERVICE_URL
    * **Valeur** : https://{nom-hôte-azure-digital-twins}
    
    Sélectionnez __OK__ pour créer un paramètre d’application.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Capture d’écran du portail Azure. Dans la page Ajouter/modifier le paramètre d’application, les champs Nom et Valeur sont remplis. Le bouton OK est mis en surbrillance.":::

1. Une fois le paramètre créé, il doit apparaître sous l’onglet __Paramètres de l’application__ . Vérifiez qu’**ADT_SERVICE_URL** apparaît dans la liste. Enregistrez ensuite le nouveau paramètre d’application en sélectionnant __Enregistrer__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Capture d’écran du portail Azure. Dans l’onglet Paramètres de l’application, le nouveau paramètre ADT_SERVICE_URL est mis en surbrillance. Le bouton Enregistrer est également mis en surbrillance.":::

1. Tout changement apporté aux paramètres d’application nécessite un redémarrage de l’application. Par conséquent, sélectionnez __Continuer__ pour redémarrer votre application quand vous y êtes invité.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Capture d’écran du portail Azure. Une notification indique que toute modification apportée aux paramètres d’application entraînera le redémarrage de votre application. Le bouton Continuer est mis en évidence.":::

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez configuré une application de fonction dans Azure en vue d’une utilisation avec Azure Digital Twins. Dans la suite, vous allez voir comment créer une fonction de base pour [ingérer des données IoT Hub dans Azure Digital Twins](how-to-ingest-iot-hub-data.md).
