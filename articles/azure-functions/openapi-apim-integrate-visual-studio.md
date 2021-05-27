---
title: Créer des API serverless dans Visual Studio à l’aide d’Azure Functions et de Gestion des API
description: Utilisez Visual Studio pour créer une fonction déclenchée par HTTP avec une définition OpenAPI, qui permet l’intégration de Gestion des API afin que d’autres applications et services puissent appeler votre API basée sur une fonction serverless.
ms.topic: tutorial
ms.date: 05/07/2021
ms.openlocfilehash: b8c6afc64f15df3124ab4052568bf36d209d4fde
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383787"
---
# <a name="create-serverless-apis-in-visual-studio-using-azure-functions-and-api-management-integration-preview"></a>Créer des API serverless dans Visual Studio à l’aide d’Azure Functions et de l’intégration de Gestion des API (préversion)

Les API REST sont souvent décrites à l’aide d’une définition OpenAPI. Ce fichier contient des informations sur les opérations d’une API et sur la manière dont les données de requête et de réponse de l’API doivent être structurées.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un projet de fonction serverless dans Visual Studio
> * Tester les API de fonction localement à l’aide des fonctionnalités OpenAPI intégrées
> * Publier le projet dans une application de fonction dans Azure avec l’intégration de Gestion des API 
> * Obtenir la clé d’accès pour la fonction et la définir dans Gestion des API
> * Télécharger le fichier de définition OpenAPI

La fonction serverless que vous créez fournit une API qui vous permet de déterminer si une réparation d’urgence sur une éolienne est rentable. Étant donné que l’application de fonction et l’instance Gestion des API que vous créez utilisent des plans de consommation, le coût d’exécution de ce didacticiel est minime.

> [!NOTE]
> L’intégration d’OpenAPI et de Gestion des API présentée dans cet article est actuellement en préversion. Cette méthode d’exposition d’une API serverless est prise en charge uniquement pour les fonctions de bibliothèque de classes C# (.NET Core 3.1). Tous les autres runtimes de langage doivent plutôt [utiliser l’intégration de Gestion des API à partir du portail](functions-openapi-definition.md). 

## <a name="prerequisites"></a>Prérequis

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) version 16.10 ou ultérieure. Assurez-vous de sélectionner la charge de travail **Développement Azure** lors de l’installation. 

+ Un [abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) actif, créez un [compte gratuit](https://azure.microsoft.com/free/dotnet/) avant de commencer.

## <a name="create-a-functions-project"></a>Créer un projet Functions

Le modèle de projet Azure Functions dans Visual Studio crée un projet que vous pouvez publier dans une application de fonction dans Azure. Vous allez également créer une fonction déclenchée par HTTP qui prend en charge la génération du fichier de définition OpenAPI (anciennement Swagger).

1. Dans le menu de Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Créer un projet**, entrez *functions* dans la zone de recherche, choisissez le modèle **Azure Functions**, puis sélectionnez  **Suivant**.

1. Dans **Configurer votre nouveau projet**, entrez un **Nom de projet** tel que `TurbineRepair`, puis sélectionnez **Créer**. 

1. Pour les paramètres **Créer une application Azure Functions**, utilisez les valeurs du tableau suivant :

    | Paramètre      | Value  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version de .NET** | **.NET Core 3 (LTS)** | Cette valeur crée un projet de fonction qui utilise le runtime Azure Functions version 3.x. La génération de fichiers OpenAPI est prise en charge uniquement pour la version 3.x du runtime Functions. |
    | **Modèle de fonction** | **Déclencheur HTTP avec OpenAPI** | Cette valeur crée une fonction déclenchée par une requête HTTP, avec la possibilité de générer un fichier de définition OpenAPI.  |
    | **Compte de stockage (AzureWebJobsStorage)**  | **Émulateur de stockage** | Vous pouvez utiliser l’émulateur pour le développement local des fonctions de déclencheur HTTP. Étant donné qu’une application de fonction dans Azure nécessite un compte de stockage, celui-ci est attribué ou créé quand vous publiez votre projet sur Azure. |
    | **Niveau d’autorisation** | **Fonction** | Dans Azure, les clients doivent fournir une clé lors de l’accès au point de terminaison. Pour plus d’informations sur les clés et l’autorisation, consultez la section sur les [clés d’accès de fonction](functions-bindings-http-webhook-trigger.md#authorization-keys). |
    
    ![Paramètres de projet Azure Functions](./media/openapi-apim-integrate-vs/functions-project-settings.png)

1. Sélectionnez **Créer** pour créer le projet de fonction et la fonction de déclencheur HTTP avec prise en charge d’OpenAPI. 

Visual Studio crée un projet et la classe `Function1` qui contient un code réutilisable pour le type de fonction de déclencheur HTTP. Ensuite, vous remplacez ce code de modèle de fonction par votre propre code personnalisé. 

## <a name="update-the-function-code"></a>Mettre à jour le code de fonction

La fonction utilise un déclencheur HTTP qui accepte deux paramètres :

| Nom du paramètre | Description|
| ---- | ---- |
| *hours* | Durée estimée pour la réparation d’une turbine, jusqu’à l’heure entière la plus proche. |
| *capacité* | La capacité de l’éolienne, exprimée en kilowatts. |

La fonction calcule ensuite le coût de la réparation et les revenus engendrés par 24 heures de fonctionnement de l’éolienne. Les paramètres sont fournis dans la chaîne de requête ou dans la charge utile d’une requête POST. 

Dans le fichier de projet Function1.cs, remplacez le contenu du code de la bibliothèque de classes générée par le code suivant :

:::code language="csharp" source="~/functions-openapi-turbine-repair/TurbineRepair/Function1.cs":::

Ce code de fonction retourne un message `Yes` ou `No` pour indiquer si une réparation d’urgence est rentable. Elle retourne également l’opportunité de revenu que représente la turbine et le coût de la réparation.

## <a name="run-and-verify-the-api-locally"></a>Exécuter et vérifier l’API localement

Lorsque vous exécutez la fonction, les points de terminaison OpenAPI facilitent l’essai de la fonction localement à l’aide d’une page générée. Vous n’avez pas besoin de fournir des clés d’accès de fonction lors de l’exécution locale.

1. Appuyez sur F5 pour démarrer le projet. Lorsque le runtime Azure Functions démarre localement, un jeu de points de terminaison OpenAPI et Swagger est affiché dans la sortie, ainsi que le point de terminaison de la fonction.  

1. Dans votre navigateur, ouvrez le point de terminaison RenderSwaggerUI, qui doit ressembler à `http://localhost:7071/api/swagger/ui`. Une page est produite en fonction de vos définitions OpenAPI.

1. Sélectionnez **POST** > **Essayer**, entrez des valeurs pour `hours` et `capacity` en tant que paramètres de requête ou dans le corps de la requête JSON, puis sélectionnez **Exécuter**. 

    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-post.png" alt-text="Interface utilisateur Swagger pour tester l’API TurbineRepair":::

1. Lorsque vous entrez des valeurs entières telles que 6 pour `hours` et 2500 pour `capacity`, vous obtenez une réponse JSON ressemblant à l’exemple suivant :
   
    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-response.png" alt-text="Réponse des données JSON à partir de la fonction TurbineRepair.":::

Vous disposez maintenant d’une fonction qui détermine la rentabilité des réparations d’urgence. Ensuite, vous publiez votre projet et vos définitions d’API dans Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Avant de pouvoir publier votre projet, vous devez disposer d’une application de fonction dans votre abonnement Azure. La publication Visual Studio crée une application de fonction automatiquement la première fois que vous publiez votre projet. Elle peut également créer une instance Gestion des API qui s’intègre à votre application de fonction pour exposer l’API TurbineRepair.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Publier** puis, dans **Cible**, sélectionnez **Azure**, puis **Suivant**.

1. Pour **Cible spécifique**, choisissez **Application de fonction Azure (Windows)** afin de créer une application de fonction s’exécutant sur Windows, puis sélectionnez **Suivant**.

1. Dans **Instance de la fonction**, choisissez **+ Créer une application Azure Function...** .

    :::image type="content" source="media/openapi-apim-integrate-vs/publish-new-resource.png" alt-text="Créer une instance d’application de fonction":::

1. Créez une instance en utilisant les valeurs spécifiées dans le tableau suivant :

    | Paramètre      | Value  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. Acceptez ce nom ou entrez un nouveau nom. Les caractères valides sont `a-z`, `0-9` et `-`. |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser. Acceptez cet abonnement ou sélectionnez-en un nouveau dans la liste déroulante. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** | Nom de votre groupe de ressources |  Groupe de ressources dans lequel créer votre application de fonction. Sélectionnez un groupe de ressources existant dans la liste déroulante, ou choisissez **Créer** pour créer un groupe de ressources.|
    | **[Type de plan](functions-scale.md)** | Consommation | Quand vous publiez votre projet dans une application de fonction qui s’exécute dans un [Plan Consommation](consumption-plan.md), vous payez uniquement pour les exécutions de votre application de fonction. D’autres plans d’hébergement occasionnent des coûts plus élevés. |
    | **Lieu** | Emplacement du service | Choisissez un **Emplacement** dans une [région](https://azure.microsoft.com/regions/) proche de chez vous, ou proche d’autres services auxquels vos fonctions ont accès. |
    | **[Stockage Azure](storage-considerations.md)** | Compte de stockage à usage général | Le runtime Functions exige un compte Stockage Azure. Sélectionnez **Nouveau** pour configurer un compte de stockage universel. Vous pouvez également choisir un compte existant qui répond aux [exigences relatives aux comptes de stockage](storage-considerations.md#storage-account-requirements).  |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-function-app-with-storage.png" alt-text="Créer une application de fonction dans Azure avec Stockage":::

1. Sélectionnez **Créer** pour créer une application de fonction et les ressources associées dans Azure. L’état de la création des ressources est affiché en bas à gauche de la fenêtre. 

1. De retour dans **Instance de fonction**, vérifiez que **Exécuter à partir du fichier de package** est coché. Votre application de fonction est déployée en utilisant [Zip Deploy](functions-deployment-technologies.md#zip-deploy) avec le mode [Exécuter à partir du fichier de package](run-functions-from-deployment-package.md) activé. Cette méthode de déploiement est recommandée pour votre projet Functions, car elle offre de meilleures performances. 

1. Sélectionnez **Suivant**, puis, dans la page **Gestion des API**, choisissez également **+ Create an API Management API** (Créer une API Gestion des API).

1.  Créez une **API dans Gestion des API** à l’aide des valeurs du tableau suivant :

    | Paramètre      | Value  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’API** | TurbineRepair | Nom de l’API. |
    | **Nom d’abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser. Acceptez cet abonnement ou sélectionnez-en un nouveau dans la liste déroulante. |
    | **Groupe de ressources** | Nom de votre groupe de ressources | Sélectionnez le même groupe de ressources que votre application de fonction dans la liste déroulante.   |
    | **Service Gestion des API** | Nouvelle instance | Sélectionnez **Nouveau** pour créer une nouvelle instance Gestion des API dans le niveau serverless.   |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-api-management-api.png" alt-text="Créer une instance Gestion des API avec une API":::

1. Sélectionnez **Créer** pour créer l’instance Gestion des API avec l’API TurbineRepair à partir de l’intégration de la fonction.

1. Sélectionnez **Terminer**, vérifiez la page Publier qui indique **Prêt à publier**, puis sélectionnez **Publier** pour déployer le package contenant vos fichiers de projet vers votre nouvelle application de fonction dans Azure. 

    Une fois le déploiement terminé, l’URL racine de l’application de fonction dans Azure est affichée sous l’onglet **Publier**. 

## <a name="get-the-function-access-key"></a>Obtenir la clé d’accès à une fonction

1. Sous l’onglet **Publier**, sélectionnez les points de suspension ( **...** ) en regard de **Hébergement**, puis sélectionnez **Ouvrir dans le portail Azure**. L’application de fonction que vous avez créée est ouverte dans le Portail Azure dans votre navigateur par défaut. 

1. Sous **Fonctions**, sélectionnez **Fonctions**  >  **TurbineRepair**, puis sélectionnez **Touches de fonction**. 

    :::image type="content" source="media/openapi-apim-integrate-vs/get-function-keys.png" alt-text="Obtenir une clé d’accès pour la fonction TurbineRepair":::

1. Sous **Clés de fonction**, sélectionnez **Par défaut** et copiez la **valeur**. Vous pouvez maintenant définir cette clé dans Gestion des API pour qu’elle puisse accéder au point de terminaison de la fonction.

## <a name="configure-api-management"></a>Configuration de Gestion des API

1. Sous l’onglet **Publier** , sélectionnez les points de suspension ( **...** ) en regard de **Hébergement** , puis sélectionnez **Open API in Azure portal** (Ouvrir l’API dans le portail Azure). L’instance Gestion des API que vous avez créée est ouverte dans le Portail Azure dans votre navigateur par défaut. Cette instance Gestion des API est déjà liée à votre application de fonction. 

1. Sous **API**, sélectionnez **Extension OpenAPI Azure Functions** > **Test** > **POST Run**, puis, sous **Stratégie de trafic entrant**, sélectionnez **Ajouter une stratégie**.

    :::image type="content" source="media/openapi-apim-integrate-vs/apim-add-policy.png" alt-text="Ajouter une stratégie de trafic entrant à l’API":::

1. Dans **Ajouter une stratégie de trafic entrant**, choisissez **Définir les paramètres de la requête**, tapez `code` dans **Name** (Nom), sélectionnez **+Value** (Valeur), collez la touche de fonction copiée, puis sélectionnez **Enregistrer**. La Gestion des API comprend la touche de fonction lors du passage de l’appel au point de terminaison de la fonction. 

Maintenant que la clé de fonction est définie, vous pouvez appeler l’API pour vérifier qu’elle fonctionne lorsqu’elle est hébergée dans Azure.

## <a name="verify-the-api-in-azure"></a>Vérifier l’API dans Azure

1. Dans l’API, sélectionnez l’onglet **Test**, puis **POST Run**, entrez le code suivant dans le **Corps de la demande** > **Raw** (Brut), puis sélectionnez **Envoyer** :

    ```json
    {
        "hours": "6",
        "capacity": "2500"
    }
    ```

    :::image type="content" source="media/openapi-apim-integrate-vs/api-management-test-function-api.png" alt-text="Page de test OpenAPI dans l’API Gestion des API":::

    Comme précédemment, vous pouvez également fournir les mêmes valeurs que les paramètres de requête. 

1. Sélectionnez **Envoyer**, puis affichez la **Réponse HTTP** pour vérifier que les mêmes résultats sont retournés à partir de l’API.

## <a name="download-the-openapi-definition"></a>Télécharger la définition OpenAPI

Si votre API fonctionne comme prévu, vous pouvez télécharger la définition OpenAPI.

1. 1. Sous **API**, sélectionnez **Extension OpenAPI Azure Functions**, sélectionnez les points de suspension ( **...** ) et sélectionnez **Exporter**.
   
   ![Télécharger la définition OpenAPI](media/openapi-apim-integrate-vs/download-definition.png)

2. Choisissez les moyens d’exportation d’API, y compris les fichiers OpenAPI dans différents formats. Vous pouvez également [exporter des API depuis Gestion des API Azure vers Power Platform](../api-management/export-api-power-platform.md). 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources.
 
Dans le menu ou la **page d’accueil** du portail Azure, sélectionnez **Groupes de ressources**. Ensuite, dans la page **Groupes de ressources**, sélectionnez le groupe que vous avez créé.

Dans la page **myResourceGroup**, assurez-vous que les ressources répertoriées sont bien celles que vous souhaitez supprimer.

Sélectionnez **Supprimer le groupe de ressources**, tapez le nom de votre groupe dans la zone de texte pour confirmer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio 2019 pour créer une fonction qui est auto-documentée en raison de l’[extension OpenAPI](https://github.com/Azure/azure-functions-openapi-extension) et qui est intégrée à Gestion des API. Vous pouvez maintenant affiner la définition à l’aide de la Gestion des API dans le portail. Vous pouvez aussi [en savoir plus sur la Gestion des API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Modifier la définition OpenAPI dans la Gestion des API](../api-management/edit-api.md)
