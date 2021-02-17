---
title: Textures
description: Workflow relatif aux ressources de texture
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594415"
---
# <a name="textures"></a>Textures

Les textures sont une [ressource partagée](../concepts/lifetime.md) immuable. Les textures peuvent être chargées à partir d’une [stockage Blob](../how-tos/conversion/blob-storage.md) et appliquées directement aux modèles comme illustré dans le [Tutoriel : Changement d’environnement et de matériaux](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Cependant, le plus souvent, les textures font partie d’un [modèle converti](../how-tos/conversion/model-conversion.md), dans lequel elles sont référencées par ses [matériaux](materials.md).

## <a name="texture-types"></a>Types de texture

À cas d’usage correspondent des types de texture spécifiques :

* Les **textures 2D** sont principalement utilisées dans les [matériaux](materials.md).
* Les **cubemaps** peuvent être utilisés pour le [ciel](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formats de texture pris en charge

Toutes les textures fournies à ARR doivent être au [format DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface), de préférence avec des mipmaps et la compression de texture.

## <a name="loading-textures"></a>Chargement des textures

Quand vous chargez une texture, vous devez spécifier son type attendu. Si le type ne correspond pas, le chargement de la texture échoue.
Le chargement d’une texture avec le même URI à deux reprises retourne le même objet de texture, car il s’agit d’une [ressource partagée](../concepts/lifetime.md).

Comme pour le chargement de modèles, il existe deux méthodes pour envoyer une ressource de texture dans un stockage Blob source :

* La texture peut être envoyée directement par le biais des paramètres de stockage Blob si le [stockage Blob est lié au compte](../how-tos/create-an-account.md#link-storage-accounts). Dans ce cas, la fonction de chargement appropriée est `LoadTextureAsync` avec le paramètre `LoadTextureOptions`.
* La ressource de texture peut être envoyée par le biais de son URI SAS. La fonction de chargement appropriée est `LoadTextureFromSasAsync` avec le paramètre `LoadTextureFromSasOptions`. Utilisez également cette méthode pour le chargement de [textures intégrées](../overview/features/sky.md#built-in-environment-maps).

L’exemple de code suivant montre comment charger une texture :

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Notez que, dans le cas de l’utilisation de sa variante SAP, seule la fonction/le paramètre de chargement diffère.

Selon la façon dont vous envisagez d’utiliser la texture, son contenu et son type peuvent être soumis à des restrictions. Par exemple, la carte de rugosité d’un [matériau PBR](../overview/features/pbr-materials.md) doit être en nuances de gris.

## <a name="api-documentation"></a>Documentation de l’API

* [Texture, classe C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection.LoadTextureFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Texture, classe C++](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection::LoadTextureAsync()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection::LoadTextureFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Étapes suivantes

* [Matériaux](materials.md)
* [Lumière ambiante](../overview/features/sky.md)