---
title: Configurer une fonction dans Azure pour le traitement des données
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction dans Azure qui peut accéder à des jumeaux numériques et être déclenchée par ceux-ci.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ecbf07c1a85c708d4117c83bca546cd8bd6ccd2b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546072"
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

Vous pouvez écrire une fonction en ajoutant le SDK à votre application de fonction. L’application de fonction interagit avec Azure Digital Twins à l’aide du [Kit de développement logiciel (SDK) Azure Digital Twins pour .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Pour utiliser le kit SDK, vous devez inclure les packages suivants dans votre projet. Vous pouvez soit installer les packages à l’aide du gestionnaire de package NuGet de Visual Studio, soit ajouter les packages avec `dotnet` dans un outil en ligne de commande. Suivez les étapes ci-dessous pour la méthode choisie.

**Option 1. Ajouter des packages à l’aide du gestionnaire de package Visual Studio :**
    
Sélectionnez avec le bouton droit votre projet, puis sélectionnez _Gérer les packages NuGet_ dans la liste. Ensuite, dans la fenêtre qui s’ouvre, sélectionnez l’onglet _Parcourir_ et recherchez les packages suivants. Sélectionnez _Installer_ et _Accepter_ pour approuver le contrat de licence et installer les packages.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Option 2. Ajouter des packages à l’aide de `dotnet`l’outil en ligne de commande :**

Vous pouvez également utiliser les commandes `dotnet add` suivantes dans un outil en ligne de commande :

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Ensuite, dans votre Explorateur de solutions Visual Studio, ouvrez le fichier _Function1.cs_ dans lequel se trouve l’exemple de code, et ajoutez les instructions `using` suivantes à votre fonction. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Ajouter du code d’authentification à la fonction

Vous allez à présent déclarer les variables au niveau de la classe, et ajoutez le code d’authentification qui permettra à la fonction d’accéder à Azure Digital Twins. Vous allez ajouter ce qui suit à votre fonction dans le fichier _Function1.cs_.

* Code permettant de lire l’URL du service Azure Digital Twins en tant que variable d’environnement. Il est recommandé de lire l’URL du service à partir d’une variable d’environnement, plutôt que de la coder en dur dans la fonction.

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

## <a name="set-up-security-access-for-the-function-app"></a>Configurer l’accès de sécurité pour l’application de fonction

Vous pouvez configurer l’accès de sécurité pour l’application de fonction en utilisant l’interface Azure CLI ou le portail Azure. Suivez les étapes correspondant à l’option choisie.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Option 1 : Configurer l’accès de sécurité pour l’application de fonction avec l’interface CLI

Le squelette de fonction des exemples précédents nécessite qu’un jeton de porteur lui soit transmis, afin de pouvoir s’authentifier auprès d’Azure Digital Twins. Pour garantir que ce jeton du porteur sera transmis, vous devez configurer [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) pour l’application de fonction. Cette opération ne doit être effectuée qu’une seule fois pour chaque application de fonction.

Vous pouvez créer une identité managée par le système et attribuer l’identité de l’application de fonction au rôle _**Propriétaire des données Azure Digital Twins**_ pour votre instance Azure Digital Twins. L'instance sera ainsi autorisée à effectuer des activités de plan de données. Ensuite, rendez l’URL de l’instance Azure Digital Twins accessible à votre fonction en définissant une variable d’environnement.

Utilisez [Azure Cloud Shell](https://shell.azure.com) pour exécuter les commandes.

Utilisez la commande suivante pour créer l’identité gérée par le système. Prenez note du champ _principalId_ dans la sortie.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Utilisez la valeur _principalId_ dans la commande suivante afin d’attribuer l’identité de l’application de fonction au rôle _Propriétaire des données Azure Digital Twins_ pour votre instance Azure Digital Twins.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Enfin, vous pouvez rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction en définissant une variable d’environnement. Pour plus d’informations sur la définition d’une variable d’environnement, consultez [*Variables d’environnement*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du *nom d’hôte* de votre instance Azure Digital Twins. Pour afficher le nom d’hôte, ainsi que toutes les propriétés de votre instance, vous pouvez exécuter `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Option n°2 : Configurer l’accès de sécurité pour l’application de fonction avec le portail Azure

Une identité managée affectée par le système permet aux ressources Azure de s’identifier auprès des services cloud (comme Azure Key Vault, par exemple), sans stocker les informations d’identification dans le code. Une fois activées, toutes les autorisations nécessaires peuvent être accordées via le contrôle d’accès en fonction du rôle Azure. Le cycle de vie de ce type d’identité managée est lié au cycle de vie de cette ressource. De plus, chaque ressource (Machines virtuelles, par exemple) peut n’avoir qu’une identité managée affectée par le système.

Dans le [portail Azure](https://portal.azure.com/), recherchez _Application de fonction_ dans la barre de recherche, avec le nom de l’application de fonction que vous avez créée précédemment. Sélectionnez l’*application de fonction* dans la liste. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

Dans la fenêtre de l’application de fonction, sélectionnez _Identité_ dans la barre de navigation sur la gauche pour activer l’identité managée.
Sous l’onglet _Affectée par le système_, basculez l’_État_ sur Activé et _Enregistrez_. Une fenêtre contextuelle s’affiche pour _Activer l’identité managée affectée par le système_.
Sélectionnez le bouton _Oui_. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Capture d’écran du portail Azure : dans la page Identité de l’application de fonction, l’option permettant d’activer l’identité managée affectée par le système est définie sur Oui. L’option État est activée.":::

Dans les notifications, vous pouvez vérifier que votre fonction est correctement inscrite auprès d’Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Capture d’écran du portail Azure : liste des notifications apparaissant après la sélection de l’icône en forme de cloche dans la barre supérieure du portail. Une notification indique que l’utilisateur a activé l’identité managée affectée par le système.":::

Notez également l’**ID d’objet** qui figure dans la page _Identité_, car vous en aurez besoin à la section suivante.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Capture d’écran du portail Azure : le champ ID de l’objet dans la page Identité de la fonction Azure est mis en évidence.":::

### <a name="assign-access-roles-using-azure-portal"></a>Affecter des rôles d’accès à l’aide du portail Azure

Sélectionnez le bouton _Attributions de rôles Azure_ qui ouvre la page *Attributions de rôles Azure*. Ensuite, sélectionnez _+ Ajouter une attribution de rôle (préversion)_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Capture d’écran du portail Azure : le bouton Attributions de rôle Azure sous Autorisations dans la page Identité de la fonction Azure est mis en évidence.":::

Dans la page _Ajouter une attribution de rôle (préversion)_ qui s’ouvre, renseignez les champs suivants :

* _Portée_ : groupe de ressources
* _Abonnement_ : sélectionnez votre abonnement Azure
* _Groupe de ressources_ : sélectionnez votre groupe de ressources dans la liste déroulante
* _Rôle_ : sélectionnez _Propriétaire des données Azure Digital Twins_ dans la liste déroulante

Ensuite, enregistrez vos informations en cliquant sur le bouton _Enregistrer_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Capture d’écran du portail Azure : boîte de dialogue permettant d’ajouter une nouvelle attribution de rôle (préversion). Celle-ci contient les champs Étendue, Abonnement, Groupe de ressources et Rôle.":::

### <a name="configure-application-settings-using-azure-portal"></a>Configurer les paramètres de l’application avec le portail Azure

Vous pouvez rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction en définissant une variable d’environnement. Pour plus d’informations, consultez [*Variables d’environnement*](/sandbox/functions-recipes/environment-variables). Les paramètres d’application sont exposés en tant que variables d’environnement pour accéder à l’instance Digital Twins. 

Pour définir une variable d’environnement avec l’URL de votre instance, commencez par obtenir l’URL en recherchant le nom d’hôte de votre instance Azure Digital Twins. Recherchez votre instance dans la barre de recherche du [portail Azure](https://portal.azure.com). Ensuite, sélectionnez _Vue d’ensemble_ dans la barre de navigation de gauche pour afficher le _Nom d’hôte_. Copiez cette valeur.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Capture d’écran du portail Azure : dans la page Vue d’ensemble de l’instance Azure Digital Twins, la valeur du nom d’hôte est mise en évidence.":::

Vous pouvez désormais créer un paramètre d’application en suivant les étapes ci-dessous :

1. Recherchez votre application de fonction dans la barre de recherche du portail, puis sélectionnez-la dans les résultats
1. Sélectionnez _Configuration_ dans la barre de navigation à gauche pour créer un paramètre d’application
1. Sous l’onglet _Paramètres d’application_, sélectionnez _+ Nouveau paramètre d’application_

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Capture d’écran du portail Azure : dans la page Configuration de l’application de fonction, le bouton Nouveau paramètre d’application est mis en évidence.":::

Dans la fenêtre qui s’ouvre, utilisez la valeur de nom d’hôte copiée ci-dessus pour créer un paramètre d’application.
* **Nom** : ADT_SERVICE_URL
* **Valeur** : https://{nom-hôte-azure-digital-twins}

Sélectionnez _OK_ pour créer un paramètre d’application.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Capture d’écran du portail Azure : les champs Nom et Valeur sont renseignés dans la page Ajouter/modifier le paramètre d’application, et le bouton OK est mis en évidence.":::

Vous pouvez afficher les paramètres de votre application, avec le nom de l’application sous le champ _Nom_. Ensuite, enregistrez les paramètres de votre application en sélectionnant le bouton _Enregistrer_.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Capture d’écran du portail Azure : le nouveau paramètre ADT_SERVICE_URL est mis en évidence dans la page Paramètres de l’application. Le bouton Enregistrer est également mis en évidence.":::

Toute modification apportée aux paramètres de l’application nécessite un redémarrage de l’application pour prendre effet. Sélectionnez _Continuer_ pour redémarrer votre application.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Capture d’écran du portail Azure : notification indiquant que toute modification apportée aux paramètres d’application entraînera le redémarrage de votre application. Le bouton Continuer est mis en évidence.":::

Vous pouvez constater que les paramètres de l’application sont mis à jour en sélectionnant l’icône _Notifications_. Si le paramètre de votre application n’est pas créé, vous pouvez essayer de nouveau d’ajouter un paramètre d’application en suivant la procédure décrite ci-dessus.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Capture d’écran du portail Azure : liste des notifications apparaissant après la sélection de l’icône en forme de cloche dans la barre supérieure du portail. Une notification indique que les paramètres de l’application web ont été mis à jour.":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu les étapes permettant de configurer une application de fonction dans Azure en vue d’une utilisation avec Azure Digital Twins.

Dans la suite, vous allez voir comment créer une fonction de base pour ingérer des données IoT Hub dans Azure Digital Twins :
* [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md)
