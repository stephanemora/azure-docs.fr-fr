---
title: Modèles
description: Décrit ce qu’est un modèle dans Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593872"
---
# <a name="models"></a>Modèles

Un *modèle* dans Azure Remote Rendering fait référence à une représentation complète de l’objet, composée d’[entités](entities.md) et de [composants](components.md). Les modèles sont le principal moyen d’utiliser des données personnalisées dans le service de rendu distant.

## <a name="model-structure"></a>Structure du modèle

Un modèle a exactement une [entité](entities.md) comme nœud racine. En dessous, il peut avoir une hiérarchie arbitraire d’entités enfants. Lors du chargement d’un modèle, une référence à cette entité racine est retournée.

Chaque entité peut avoir des [composants](components.md) attachés. Dans le cas le plus courant, les entités ont des *MeshComponents*, qui font référence aux [ressources de maillage](meshes.md).

## <a name="creating-models"></a>Création de modèles

La création de modèles pour le runtime s’effectue en [convertissant les modèles d’entrée](../how-tos/conversion/model-conversion.md) à partir de formats de fichiers tels que FBX et GLTF. Le processus de conversion extrait toutes les ressources, telles que les textures, les matériaux et les maillages, et les convertit en formats de runtime optimisés. Il extraira également les informations structurelles et les convertira en structure graphique d’entité ou de composant ARR.

> [!IMPORTANT]
> La [conversion de modèle](../how-tos/conversion/model-conversion.md) est la seule façon de créer des [maillages](meshes.md). Bien que les maillages puissent être partagés entre les entités au moment de l’exécution, il n’existe aucun autre moyen d’obtenir un maillage dans le runtime, autre que le chargement d’un modèle.

## <a name="loading-models"></a>Chargement de modèles

Une fois qu’un modèle est converti, il peut être chargé à partir du stockage blob Azure dans le runtime.

Il existe deux fonctions de chargement distinctes qui diffèrent par la méthode d’adressage de la ressource dans le stockage blob :

* Le modèle peut être adressé directement par le biais des paramètres de stockage blob si le [stockage blob est lié au compte](../how-tos/create-an-account.md#link-storage-accounts). Dans ce cas, la fonction de chargement appropriée est `LoadModelAsync` avec le paramètre `LoadModelOptions`.
* Le modèle peut être adressé par son URI SAS. La fonction de chargement appropriée est `LoadModelFromSasAsync` avec le paramètre `LoadModelFromSasOptions`. Utilisez également cette méthode pour le chargement de [modèles intégrés](../samples/sample-model.md).

Les extraits de code suivants montrent comment charger des modèles avec l’une ou l’autre des fonctions. Pour charger un modèle à l’aide de ses paramètres de stockage blob, utilisez un code similaire à celui ci-dessous :


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Si vous souhaitez charger un modèle à l’aide d’un jeton SAP, utilisez un code similaire à l’extrait de code suivant :

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Par la suite, vous pouvez parcourir la hiérarchie d’entités et modifier les entités et les composants. Le chargement répété du même modèle crée plusieurs instances, chacune avec sa propre copie de la structure des entités et des composants. Étant donné que les maillages, les matériaux et les textures sont des [ressources partagées](../concepts/lifetime.md), leurs données ne seront pas rechargées. Par conséquent, l’instanciation d’un modèle plus d’une fois nécessite relativement peu de mémoire.

## <a name="api-documentation"></a>Documentation de l’API

* [C# RenderingConnection.LoadModelAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection.LoadModelFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection::LoadModelAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection::LoadModelFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Étapes suivantes

* [Entités](entities.md)
* [Maillages](meshes.md)