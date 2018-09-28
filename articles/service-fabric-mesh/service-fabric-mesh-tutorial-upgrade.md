---
title: Tutoriel - Mettre à niveau une application Azure Service Fabric Mesh | Microsoft Docs
description: Découvrez comment mettre à niveau une application Service Fabric à l'aide de Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: a734eb7b4efecf14a4d47a660c00d561a5fa6677
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971805"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutoriel : Découvrez comment mettre à niveau une application Service Fabric à l'aide de Visual Studio

Ce tutoriel est la quatrième partie d’une série et vous montre comment mettre à niveau une application Azure Service Fabric Mesh directement depuis Visual Studio. La mise à niveau inclut une mise à jour du code et une mise à jour de la configuration. Vous verrez que les étapes de la mise à niveau et de la publication dans Visual Studio sont les mêmes.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Mettre à niveau un service Service Fabric Mesh à l’aide de Visual Studio

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application Service Fabric Mesh dans Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Déboguer une application Service Fabric Mesh qui s’exécute dans votre cluster de développement local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Déployer une application Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Mettre à niveau une application Service Fabric Mesh
> * [Nettoyer les ressources Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas déployé l’application de tâches, suivez les instructions fournies dans [Publier une application web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

# <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Mettre à niveau un service Service Fabric Mesh à l’aide de Visual Studio

Cet article explique comment mettre à niveau de façon indépendante un microservice à l’intérieur d’une application.  Dans cet exemple, nous allons modifier le service `WebFrontEnd` pour afficher une catégorie de tâches. Ensuite, nous allons mettre à niveau le service déployé.

## <a name="modify-the-config"></a>Modifier la configuration

Les mises à niveau peuvent être dues à des modifications du code, des modifications de la configuration ou aux deux.  Pour introduire une modification de la configuration, ouvrez le fichier `service.yaml` du projet `WebFrontEnd` (qui se trouve sous le nœud **Ressources du service**).

Dans la section `resources:`, modifiez `cpu:` en remplaçant 0,5 par 1,0, anticipant sur le fait que le serveur frontal web sera utilisé de façon intensive. Elle doit maintenant ressembler à ceci :

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Modifier le modèle

Pour introduire une modification du code, ajoutez une propriété `Category` à la classe `ToDoItem` dans le fichier `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Ensuite, mettez à jour la méthode `Load()`, dans le même fichier, pour définir la catégorie sur une chaîne par défaut :

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modifier le service

Le projet `WebFrontEnd` est une application ASP.NET Core avec une page web affichant des élément de liste de tâches. Dans le projet `WebFrontEnd`, ouvrez `Index.cshtml` et ajoutez les deux lignes suivantes, indiquées ci-dessous, pour afficher la catégorie de la tâche :

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Générez et exécutez l’application pour vérifier que vous voyez une nouvelle colonne de catégorie dans la page web qui répertorie les tâches.

## <a name="upgrade-the-app-from-visual-studio"></a>Mettre à niveau l’application à partir de Visual Studio

Que vous effectuiez une mise à niveau du code ou une mise à niveau de la configuration (dans ce cas nous effectuons les deux), pour mettre à niveau votre application Service Fabric Mesh sur Azure, cliquez avec le bouton droit sur **todolistapp** dans Visual Studio, et sélectionnez **Publier...**

Ensuite, une boîte de dialogue **Publier une application Service Fabric** s’affiche.

![Boîte de dialogue de publication d’une application Service Fabric MeshVisual studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Sélectionnez votre compte et votre abonnement Azure. Veillez à ce que le paramètre **Emplacement** soit défini sur l’emplacement que vous avez utilisé quand vous avez initialement publié l’application de tâches dans Azure. Dans cet article, nous utilisons la région **USA Est**.

Veillez à ce que le paramètre **Groupe de ressources** soit défini sur le groupe de ressources que vous avez utilisé quand vous avez initialement publié l’application de tâches dans Azure.

Veillez à ce que le paramètre **Registre de conteneurs Azure** soit défini sur le nom du registre de conteneurs Azure que vous avez créé quand vous avez initialement publié l’application de tâches dans Azure.

Dans la boîte de dialogue de publication, appuyez sur le bouton **Publier** pour mettre à niveau l’application de tâches sur Azure.

Vous pouvez surveiller la progression de la mise à niveau en sélectionnant le volet **Service Fabric Tools** dans la fenêtre de **sortie** de Visual Studio. Une fois la mise à niveau terminée, la sortie de **Service Fabric Tools** affiche l’adresse IP et le port de votre application sous la forme d’une URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Ouvrez un navigateur web et accédez à l’URL pour voir le site web en cours d’exécution dans Azure. Vous devez maintenant voir une page web qui contient une colonne de catégorie.

## <a name="next-steps"></a>Étapes suivantes

Cette partie du didacticiel vous a permis de répondre aux questions suivantes :
> [!div class="checklist"]
> * Comment mettre à niveau une application Service Fabric Mesh à l’aide de Visual Studio ?

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Nettoyer les ressources Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)