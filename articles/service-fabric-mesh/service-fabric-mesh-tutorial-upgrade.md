---
title: Tutoriel - Mettre à niveau une application Azure Service Fabric Mesh | Microsoft Docs
description: Découvrez comment mettre à niveau une application Service Fabric à l'aide de Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806729"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutoriel : Découvrez comment mettre à niveau une application Service Fabric à l'aide de Visual Studio

Ce tutoriel est la quatrième partie d’une série et vous montre comment mettre à niveau une application Azure Service Fabric Mesh directement depuis Visual Studio. La mise à niveau inclut une mise à jour du code et une mise à jour de la configuration. Vous verrez que les étapes de la mise à niveau et de publication à partir de Visual Studio sont les mêmes.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Mettre à niveau un service Service Fabric Mesh à l’aide de Visual Studio

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application Service Fabric Mesh dans Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Déboguer une application Service Fabric Mesh qui s’exécute dans votre cluster de développement local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Déployer une application Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Mettre à niveau une application Service Fabric Mesh
> * [Nettoyer des ressources Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce tutoriel :

* Si vous n’avez pas déployé l’application To-Do, suivez les instructions fournies dans [Publier une application web Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Mettre à niveau un service Service Fabric Mesh à l’aide de Visual Studio

Cet article explique comment mettre à niveau un microservice au sein d’une application. Dans cet exemple, nous allons modifier le `WebFrontEnd` service pour afficher une catégorie de tâche et augmenter la quantité d’UC qui lui est attribué. Ensuite, nous allons mettre à niveau le service déployé.

## <a name="modify-the-config"></a>Modifier la configuration

Lorsque vous créez une application de maillage de Service Fabric, Visual studio ajoute un **parameters.yaml** fichier pour chaque environnement de déploiement (cloud et local). Dans ces fichiers, vous pouvez définir des paramètres et leurs valeurs qui peuvent ensuite être référencés à partir de vos fichiers de *.yaml MAILLE tels que service.yaml ou network.yaml.  Visual Studio fournit certaines variables, telles que l’UC que le service peut utiliser.

Nous mettrons à jour le `WebFrontEnd_cpu` paramètre pour mettre à jour les ressources de processeur à `1.5` en prévision qui le **WebFrontEnd** service sera plus largement utilisé.

1. Dans le **todolistapp** de projet, sous **environnements** > **Cloud**, ouvrez le **parameters.yaml** fichier. Modifier le `WebFrontEnd_cpu`, valeur `1.5`. Le nom du paramètre commence par le nom du service `WebFrontEnd_` recommandé pour le différencier des paramètres du même nom qui s’appliquent à différents services.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Ouvrez le **WebFrontEnd** du projet **service.yaml** de fichiers sous **WebFrontEnd** > **Service ressources**.

    Notez que le dans `resources:` section, `cpu:` est défini sur `"[parameters('WebFrontEnd_cpu')]"`. Si le projet est généré pour le cloud, la valeur de `'WebFrontEnd_cpu` seront prises dans le **environnements** > **Cloud** > **parameters.yaml** de fichiers et sera `1.5`. Si le projet est généré pour s’exécuter localement, vous allez prendre la valeur à partir de la **environnements** > **Local** > **parameters.yaml** fichier, et « 0,5 » sera.

> [!Tip]
> Par défaut, le fichier de paramètre qui est un homologue du fichier profile.yaml sera être utilisé pour fournir les valeurs pour ce fichier profile.yaml.
> Par exemple, les environnements > Cloud > parameters.yaml fournit les valeurs de paramètre pour les environnements > Cloud > profile.yaml.
>
> Vous pouvez remplacer cela en ajoutant le code suivant au fichier profile.yaml :`parametersFilePath=”relative or full path to the parameters file”` Par exemple, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou `parametersFilePath=”..\CommonParameters.yaml”`.

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

Si vous effectuez une mise à niveau du code, ou une mise à niveau de configuration (dans ce cas nous faisons à la fois), la mise à niveau de votre application de maillage de Service Fabric sur Azure en cliquant sur **todolistapp** dans Visual Studio, puis sélectionnez **publier...**

Ensuite, une boîte de dialogue **Publier une application Service Fabric** s’affiche.

Utiliser le **profil cible** liste déroulante pour sélectionner le fichier profile.yaml à utiliser pour ce déploiement. Nous mettons à jour de l’application dans le cloud et nous sélectionnons le **cloud.yaml** dans la liste déroulante, qui utilisera le `WebFrontEnd_cpu` la valeur 1.0 défini dans ce fichier.

![Boîte de dialogue Publier Service Fabric mesh dans Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Sélectionnez votre compte et votre abonnement Azure. Définir le **emplacement** à l’emplacement que vous avez utilisé quand vous avez initialement publié l’application de tâches vers Azure. Dans cet article, nous utilisons la région **USA Est**.

Définissez **groupe de ressources** au groupe de ressources que vous avez utilisé quand vous avez initialement publié l’application de tâches vers Azure.

Définissez **Azure Container Registry** pour le nom du Registre de conteneur azure que vous avez créé quand vous avez initialement publié l’application de tâches vers Azure.

Dans la boîte de dialogue de publication, appuyez sur le bouton **Publier** pour mettre à niveau l’application de tâches sur Azure.

Surveiller la progression de la mise à niveau en sélectionnant le **outils Service Fabric** volet dans Visual Studio **sortie** fenêtre. 

Une fois que l’image est généré et envoyé vers le Registre de conteneur Azure, un **état** lien s’affiche dans la sortie que vous pouvez cliquer pour surveiller le déploiement dans le portail Azure.

Une fois la mise à niveau terminée, la sortie de **Service Fabric Tools** affiche l’adresse IP et le port de votre application sous la forme d’une URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Ouvrez un navigateur web et accédez à l’URL pour voir le site web en cours d’exécution dans Azure. Vous devez maintenant voir une page web qui contient une colonne de catégorie.

## <a name="next-steps"></a>Étapes suivantes

Cette partie du didacticiel vous a permis de répondre aux questions suivantes :
> [!div class="checklist"]
> * Comment mettre à niveau une application Service Fabric Mesh à l’aide de Visual Studio ?

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Nettoyer des ressources Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)