---
title: Reflets du ciel
description: Décrit comment configurer des cartes d’environnement pour les reflets du ciel
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594178"
---
# <a name="sky-reflections"></a>Reflets du ciel

Dans Azure Remote Rendering, une texture de ciel est utilisée pour éclairer les objets de façon réaliste. Dans les applications de réalité augmentée, cette texture doit ressembler à votre environnement réel, pour que les objets paraissent convaincants. Cet article explique comment modifier la texture du ciel.

> [!NOTE]
> La texture du ciel est également appelée *carte d’environnement*. Ces termes sont utilisés ici indifféremment.

## <a name="object-lighting"></a>Éclairage des objets

Azure Remote Rendering utilise le *rendu physique réaliste* (PBR) pour les calculs d’éclairage réaliste. Bien que vous puissiez ajouter des [sources de lumière](lights.md) à votre scène, l’utilisation d’une bonne texture de ciel produit le meilleur des effets.

Les images ci-dessous montrent les résultats de l’éclairage de différentes surfaces, obtenus uniquement avec une texture de ciel :

| Roughness  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Non métallique  | ![Diélectrique, Rugosité=0](media/dielectric-0.png)   | ![Diélectrique, Rugosité=0,25](media/dielectric-0.25.png)  | ![Diélectrique, Rugosité=0,5](media/dielectric-0.5.png)  | ![Diélectrique, Rugosité=0,75](media/dielectric-0.75.png)  | ![Diélectrique, Rugosité=1](media/dielectric-1.png)  |
| Métallique      | ![Métallique, Rugosité=0](media/metallic-0.png)  | ![Métallique, Rugosité=0,25](media/metallic-0.25.png)    | ![Métallique, Rugosité=0,5](media/metallic-0.5.png)    | ![Métallique, Rugosité=0,75](media/metallic-0.75.png)    | ![Métallique, Rugosité=1](media/metallic-1.png)    |

Pour plus d’informations sur le modèle d’éclairage, reportez-vous au chapitre sur les [matériaux](../../concepts/materials.md).

> [!IMPORTANT]
> Azure Remote Rendering utilise la texture du ciel uniquement pour les modèles d’éclairage. Il n’effectue pas le rendu du ciel sous forme d’arrière-plan, car les applications de réalité augmentée disposent déjà d’un arrière-plan approprié : le monde réel.

## <a name="changing-the-sky-texture"></a>Modification de la texture du ciel

Pour modifier la carte d’environnement, il suffit de [charger une texture](../../concepts/textures.md) et modifier l’élément `SkyReflectionSettings` de la session :

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

Notez que la variante `LoadTextureFromSasAsync` est utilisée ci-dessus, car une texture intégrée est chargée. En cas de chargement à partir de [stockages d’objets blob liés](../../how-tos/create-an-account.md#link-storage-accounts), utilisez la variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Types de texture de ciel

Vous pouvez utiliser des *[cartes cubiques](https://en.wikipedia.org/wiki/Cube_mapping)* et des *textures 2D* en tant que cartes d’environnement.

Toutes les textures doivent se présenter dans un [format de texture pris en charge](../../concepts/textures.md#supported-texture-formats). Vous n’avez pas besoin de fournir de mipmaps pour les textures de ciel.

### <a name="cube-environment-maps"></a>Cartes d’environnement en cube

Pour référence, voici une carte cubique non wrappée :

![Carte cubique non wrappée](media/Cubemap-example.png)

Utilisez `RenderingSession.Connection.LoadTextureAsync`/ `LoadTextureFromSasAsync` avec `TextureType.CubeMap` pour charger les textures de carte cubique.

### <a name="sphere-environment-maps"></a>Cartes d’environnement en sphère

Lors de l’utilisation d’une texture 2D comme carte d’environnement, l’image doit se trouver dans un [espace de coordonnées sphériques](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Image de ciel dans des coordonnées sphériques](media/spheremap-example.png)

Utilisez `RenderingSession.Connection.LoadTextureAsync` avec `TextureType.Texture2D` pour charger des cartes d’environnement sphériques.

## <a name="built-in-environment-maps"></a>Cartes d’environnement intégrées

Azure Remote Rendering fournit quelques cartes d’environnement intégrées qui sont toujours disponibles. Toutes les cartes d’environnement intégrées sont des cartes cubiques.

|Identificateur                         | Description                                              | Illustration                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Divers éclairages directs, éclairage intérieur lumineux    | ![Skybox Autoshop utilisée pour éclairer un objet](media/autoshop.png)
|builtin://BoilerRoom               | Intérieur clair, lumineux, plusieurs sources d’éclairage naturel      | ![Skybox BoilerRoom utilisée pour éclairer un objet](media/boiler-room.png)
|builtin://ColorfulStudio           | Lumières de différentes couleurs et intensités, dans un décor intérieur moyennement éclairé  | ![Skybox ColorfulStudio utilisée pour éclairer un objet](media/colorful-studio.png)
|builtin://Hangar                   | Éclairage de grande salle, moyennement lumineux                     | ![Skybox SmallHangar utilisée pour éclairer un objet](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Décor intérieur sombre avec lumière contrastée              | ![Skybox IndustrialPipeAndValve utilisée pour éclairer un objet](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Pièce claire, ambiance de jour, lumière vive provenant d’une fenêtre     | ![Skybox Lebombo utilisée pour éclairer un objet](media/lebombo.png)
|builtin://SataraNight              | Sol sombre et nuit noire avec de nombreuses lumières environnantes   | ![Skybox SataraNight utilisée pour éclairer un objet](media/satara-night.png)
|builtin://SunnyVondelpark          | Lumière du soleil vive et ombre contrastée                      | ![Skybox SunnyVondelpark utilisée pour éclairer un objet](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Ciel clair avec sol modérément éclairé            | ![Skybox Syferfontein utilisée pour éclairer un objet](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Soleil et ombre modérément contrastés                         | ![Skybox TearsOfSteelBridge utilisée pour éclairer un objet](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Soir, lumière de coucher de soleil à l’approche du crépuscule                    | ![Skybox VeniceSunset utilisée pour éclairer un objet](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Sol dans l’ombre ; tonalités vives, vert luxuriant et lumière blanche | ![Skybox WhippleCreekRegionalPark utilisée pour éclairer un objet](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Ambiante diurne, lumière vive et sol clair                 | ![Skybox WinterRiver utilisée pour éclairer un objet](media/winter-river.png)
|builtin://DefaultSky               | Identique à TearsOfSteelBridge                               | ![Skybox DefaultSky utilisée pour éclairer un objet](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Documentation de l’API

* [RenderingConnection.SkyReflectionSettings, propriété C#](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection::SkyReflectionSettings()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>Étapes suivantes

* [Lumières](../../overview/features/lights.md)
* [Matériaux](../../concepts/materials.md)
* [Textures](../../concepts/textures.md)
