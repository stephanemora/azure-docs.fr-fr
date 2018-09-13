---
title: Configurer le pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB
description: Didacticiel sur la configuration des flux de création et de mise en production dans Visual Studio Team Services (VSTS) à l’aide de la tâche de génération de l’émulateur Cosmos DB
services: cosmos-db
keywords: Émulateur Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783770"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Configurer un pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB dans Visual Studio Team Services

L’émulateur Azure Cosmos DB fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. L’émulateur vous permet de développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. 

La tâche de génération de l’émulateur Azure Cosmos DB pour Visual Studio Team Services (VSTS) vous permet de faire de même dans un environnement d’intégration continue. Avec la tâche de génération, vous pouvez exécuter des tests sur l’émulateur dans le cadre de vos flux de création et de mise en production. La tâche active un conteneur Docker avec l’émulateur déjà en cours d’exécution et fournit un point de terminaison qui peut être utilisé par le reste de la définition de build. Vous pouvez créer et démarrer autant d’instances de l’émulateur que vous le souhaitez, chacune étant exécutée dans un conteneur distinct. 

Cet article montre comment configurer un pipeline d’intégration continue dans VSTS pour une application ASP.NET qui utilise la tâche de génération d’émulateur Cosmos DB pour exécuter des tests. 

## <a name="install-the-emulator-build-task"></a>Installer la tâche de génération d’émulateur

Pour utiliser la tâche de génération, nous devons d’abord l’installer dans notre organisation VSTS. Recherchez l’extension **Azure Cosmos DB Emulator** sur la [Place de marché](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) et cliquez sur **Obtenir gratuitement.**

![Rechercher la tâche de génération de l’émulateur Azure Cosmos DB dans la Place de marché VSTS et l’installer](./media/tutorial-setup-ci-cd/addExtension_1.png)

Ensuite, choisissez l’organisation dans laquelle installer l’extension. 

> [!NOTE]
> Pour installer une extension dans une organisation VSTS, vous devez être propriétaire de compte ou administrateur de collection de projets. Si vous ne disposez pas des autorisations nécessaires, mais que vous êtes un membre de compte, vous pouvez demander des extensions. [En savoir plus.](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Choisir une organisation VSTS dans laquelle installer une extension](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Créer une définition de build

Maintenant que l’extension est installée, nous devons l’ajouter à une [définition de build.](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav) Vous pouvez modifier une définition de build existante ou en créer une. Si vous disposez déjà d’une définition de build, vous pouvez passer directement à [Ajouter la tâche de génération d’émulateur à une définition de build](#addEmulatorBuildTaskToBuildDefinition).

Pour créer une définition de build, accédez à l’onglet **Build and Release** (Création et mise en production) dans VSTS. Sélectionnez **+Nouveau.**

![Créer une définition de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Sélectionnez le projet d’équipe souhaité, le référentiel et la branche pour activer les builds. 

![Sélectionner le projet d’équipe, le référentiel et la branche pour la définition de build ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Enfin, sélectionnez le modèle souhaité pour la définition de build. Nous allons sélectionner le modèle **ASP.NET** dans ce didacticiel. 

![Sélectionner le modèle de définition de build souhaité ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Nous disposons maintenant d’une définition de build que nous pouvons configurer pour utiliser la tâche de génération d’émulateur Azure Cosmos DB comme ci-dessous. 

![Modèle de définition de build ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Ajouter la tâche à une définition de build

Pour ajouter la tâche de génération d’émulateur, recherchez **cosmos** dans la zone de recherche, puis sélectionnez **Ajouter.** La tâche de génération démarre un conteneur avec une instance de l’émulateur Cosmos DB déjà en cours d’exécution. Par conséquent, la tâche doit être placée avant toutes les autres tâches pour lesquelles l’émulateur doit être en cours d’exécution.

![Ajouter la tâche de génération d’émulateur à la définition de build](./media/tutorial-setup-ci-cd/addExtension_3.png) Dans ce didacticiel, nous allons ajouter la tâche au début de la Phase 1 pour nous assurer que l’émulateur est disponible avant d’exécuter nos tests.
Voici à quoi ressemble désormais la définition de build terminée. 

![Modèle de définition de build ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Configurer des tests pour utiliser l’émulateur
Maintenant, nous allons configurer nos tests pour utiliser l’émulateur. La tâche de génération d’émulateur exporte une variable d’environnement (CosmosDbEmulator.Endpoint) vers laquelle toutes les tâches suivantes du pipeline de build peuvent émettre des requêtes. 

Dans ce didacticiel, nous allons utiliser la [tâche Visual Studio Test](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md) pour exécuter des tests unitaires configurés via un fichier **.runsettings**. Pour en savoir plus sur l’initialisation de test unitaire, consultez la [documentation](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

Voici un exemple de fichier **.runsettings** qui définit les paramètres à transmettre dans les tests unitaires d’une application. Remarquez que la variable `authKey` utilisée est la [clé connue](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) pour l’émulateur. Cette `authKey` est la clé attendue par la tâche de génération d’émulateur Elle doit être définie dans votre fichier **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
Ces paramètres `TestRunParameters` sont référencés via une propriété `TestContext` dans le projet de test de l’application. Voici un exemple de test qui s’exécute sur Cosmos DB. 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Accédez aux options d’exécution dans la tâche de test Visual Studio. Dans l’option **Settings file** (Fichier de paramètres), spécifiez que les tests sont configurés à l’aide du fichier **.runsettings**. Dans l’option **Remplacer les paramètres de série de tests**, ajoutez ` -endpoint $(CosmosDbEmulator.Endpoint)`. Cela configurera la tâche de test pour faire référence au point de terminaison de la tâche de génération d’émulateur, au lieu de celle définie dans le fichier **.runsettings**.  

![Remplacer la variable de point de terminaison par le point de terminaison de la tâche de génération d’émulateur](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Exécutez la génération
Maintenant, enregistrez la génération et mettez-la en file d’attente. 

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/runBuild_1.png)

Une fois que la génération a démarré, vous noterez que la tâche d’émulateur Cosmos DB a commencé une extraction de l’image Docker avec l’émulateur installé. 

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/runBuild_4.png)

Une fois la génération terminée, vous noterez que vos tests réussissent, et qu’ils s’exécutent tous sur l’émulateur Cosmos DB de la tâche de génération !

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation de l’émulateur pour développer en local et tester, consultez [Utilisation de l’émulateur Azure Cosmos DB pour le développement local et le test](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Pour exporter les certificats SSL de l’émulateur, consultez [Exporter les certificats de l’émulateur Azure Cosmos DB pour une utilisation avec Java, Python et Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
