---
title: Liaison graphique
description: Configuration de liaisons graphiques et cas d’utilisation
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593906"
---
# <a name="graphics-binding"></a>Liaison graphique

Pour pouvoir utiliser Azure Remote Rendering dans une application personnalisée, vous devez l’intégrer dans le pipeline de rendu de l’application. Cette intégration est la responsabilité de la liaison graphique.

Une fois configurée, la liaison graphique donne accès à différentes fonctions qui agissent sur l’image rendue. Ces fonctions peuvent être divisées en deux catégories : des fonctions générales qui sont toujours disponibles et des fonctions spécifiques qui ne sont pertinentes que pour le `Microsoft.Azure.RemoteRendering.GraphicsApiType` sélectionné.

## <a name="graphics-binding-in-unity"></a>Liaison graphique dans Unity

Dans Unity, la liaison entière est gérée par le struct `RemoteUnityClientInit` passé dans `RemoteManagerUnity.InitializeManager`. Pour définir le mode graphique, le champ `GraphicsApiType` doit être défini sur la liaison choisie. Le champ est renseigné automatiquement selon qu’un XRDevice est présent ou non. Le comportement peut être substitué manuellement par les comportements suivants :

* **HoloLens 2** : la liaison graphique [Windows Mixed Reality](#windows-mixed-reality) est toujours utilisée.
* **Application de bureau UWP plate** : la [simulation](#simulation) est toujours utilisée.
* **Éditeur Unity** : la [simulation](#simulation) est toujours utilisée à moins qu’un casque de réalité virtuelle WMR soit connecté, auquel cas ARR est désactivé pour permettre le débogage des parties non-ARR de l’application. Voir aussi la [communication à distance holographique](../how-tos/unity/holographic-remoting.md).

La seule autre partie pertinente pour Unity est l’accès à la [liaison de base](#access), toutes les autres sections ci-dessous peuvent être ignorées.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuration de la liaison graphique dans des applications personnalisées

Pour sélectionner une liaison graphique, effectuez les deux étapes suivantes : Tout d’abord, la liaison graphique doit être initialisée de manière statique lorsque le programme est initialisé :

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

L’appel ci-dessus est nécessaire pour initialiser Azure Remote Rendering dans les API holographiques. Cette fonction doit être appelée avant l’appel d’une API holographique et avant d’accéder à d’autres API Remote Rendering. De même, la fonction de-init correspondante `RemoteManagerStatic.ShutdownRemoteRendering();` doit être appelée lorsqu’aucune API holographique n’est plus appelée.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Accès aux liaisons graphiques

Une fois qu’un client est configuré, la liaison graphique de base est accessible à l’aide du getter `RenderingSession.GraphicsBinding`. Par exemple, les statistiques des dernières images peuvent être récupérées comme suit :

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>API graphiques

Il existe actuellement deux API graphiques qui peuvent être sélectionnées, `WmrD3D11` et `SimD3D11`. Une troisième (`Headless`) existe, mais n’est pas encore pris en charge côté client.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` est la liaison par défaut à exécuter sur HoloLens 2. Elle crée la liaison `GraphicsBindingWmrD3d11`. Dans ce mode, Azure Remote Rendering est directement branché aux API holographiques.

Pour accéder aux liaisons graphiques dérivées, la `GraphicsBinding` de base doit être castée.
Vous devez effectuer deux opérations pour utiliser la liaison WMR :

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informer Remote Rendering du système de coordonnées utilisé

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Où le `ptr` ci-dessus doit être un pointeur vers un objet `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` natif qui définit le système de coordonnées de l’espace universel dans lequel les coordonnées de l’API sont exprimées.

#### <a name="render-remote-image"></a>Afficher l’image distante

Au début de chaque image, l’image distante doit être affichée dans la mémoire tampon d’arrière-plan. Pour ce faire, appelez `BlitRemoteFrame`, qui remplit à la fois les informations de couleur et de profondeur des yeux dans la cible de rendu actuellement liée. Par conséquent, il est important de le faire après avoir lié la mémoire tampon d’arrière-plan complète en tant que cible de rendu.

> [!WARNING]
> Après que l’image distante a été intégrée dans la mémoire tampon d’entrée, le contenu local doit être rendu à l’aide d’une technique de rendu stéréo simple passe, par exemple à l’aide de **SV_RenderTargetArrayIndex**. L’utilisation d’autres techniques de rendu stéréo, telles que le rendu de chaque œil dans une passe distincte, doit être évitée car elle peut entraîner une dégradation importante des performances ou des artefacts graphiques.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulation

`GraphicsApiType.SimD3D11` est la liaison de simulation et, si elle est sélectionnée, elle crée la liaison graphique `GraphicsBindingSimD3d11`. Cette interface est utilisée pour simuler le mouvement de la tête, par exemple dans une application de bureau, et affiche une image monoscopique.

Pour implémenter la liaison de simulation, il est important de comprendre la différence entre la caméra locale et l'image distante, comme décrit sur la page [caméra](../overview/features/camera.md).

Deux caméras sont nécessaires :

* **Caméra locale** : cette caméra correspond à la position actuelle de la caméra pilotée par la logique d'application.
* **Caméra proxy** : cette caméra correspond à l'*image distante* actuelle qui a été envoyée par le serveur. Comme il y a un délai entre la demande de l'image par le client et l'arrivée de celle-ci, l'*image distante* est toujours un peu en retard par rapport au déplacement de la caméra locale.

L'approche de base est la suivante : l'image distante et le contenu local sont rendus dans une cible hors écran à l'aide de la caméra proxy. L'image proxy est ensuite reprojetée dans l'espace de la caméra locale. Pour plus d'informations, consultez [Reprojection en phase tardive](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` prend également en charge le rendu stéréoscopique, qui doit être activé au cours de l’appel d’installation `InitSimulation` ci-dessous. La configuration est un peu plus complexe et fonctionne comme suit :

#### <a name="create-proxy-render-target"></a>Créer un proxy de cible de rendu

Le contenu local et distant doit être affiché sur une cible de rendu des couleurs et de la profondeur hors écran appelée « proxy » à l’aide des données de la caméra proxy fournies par la fonction `GraphicsBindingSimD3d11.Update`.

Le proxy doit correspondre à la résolution de la mémoire tampon d'arrière-plan, et doit être au format *DXGI_FORMAT_R8G8B8A8_UNORM* ou *DXGI_FORMAT_B8G8R8A8_UNORM*. Dans le cas d’un rendu stéréoscopique, la texture de couleur du proxy et, si la profondeur est utilisée, la texture de profondeur du proxy doivent avoir deux couches de tableau au lieu d’une. Une fois qu’une session est prête, `GraphicsBindingSimD3d11.InitSimulation` doit être appelée avant de l’y connecter :

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

La fonction init doit être fournie avec des pointeurs vers l’appareil D3D natif, ainsi que vers la texture de couleur et de profondeur du proxy de la cible de rendu. Une fois initialisées, `RenderingSession.ConnectAsync` et `Disconnect` peuvent être appelées plusieurs fois, mais, lors du basculement vers une autre session, `GraphicsBindingSimD3d11.DeinitSimulation` doit d’abord être appelée sur l’ancienne session avant que `GraphicsBindingSimD3d11.InitSimulation` puisse être appelée sur une autre session.

#### <a name="render-loop-update"></a>Mise à jour de la boucle de rendu

La mise à jour de la boucle de rendu se compose de plusieurs étapes :

1. Pour chaque image et avant tout rendu, `GraphicsBindingSimD3d11.Update` est appelée avec la transformation de caméra actuelle qui est envoyée au serveur pour être affichée. En même temps, la transformation du proxy retourné doit être appliquée à la caméra proxy pour s’afficher dans le proxy de la cible de rendu.
Si la mise à jour du proxy retournée `SimulationUpdate.frameId` a la valeur Null, il n’y a pas encore de données distantes. Dans ce cas, au lieu d’être affiché dans le proxy de la cible de rendu, tout contenu local doit être affiché directement dans la mémoire tampon d’arrière-plan à l’aide des données de caméra actuelles et les deux étapes suivantes sont ignorées.
1. L’application doit maintenant lier le proxy de la cible de rendu et appeler `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. Cela remplit les informations distantes de couleur et de profondeur dans le proxy de la cible de rendu. Tout contenu local peut maintenant être affiché sur le proxy à l’aide de la transformation de caméra proxy.
1. Ensuite, la mémoire tampon d’arrière-plan doit être liée en tant que cible de rendu et `GraphicsBindingSimD3d11.ReprojectProxy` appelée, ce qui permet de présenter la mémoire tampon d’arrière-plan.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Structures de mise à jour de la simulation

Pour chaque image, la **mise à jour de la boucle de rendu** dans la section précédente vous oblige à entrer une plage de paramètres de caméra correspondant à la caméra locale et retourne un ensemble de paramètres de caméra qui correspondent à la caméra de l’image disponible suivante. Ces deux ensembles sont capturés dans `SimulationUpdateParameters` et dans les structures de `SimulationUpdateResult`, respectivement :

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

La signification des membres des structures est la suivante :

| Membre | Description |
|--------|-------------|
| FrameId | Identificateur d’image continue. Nécessaire pour l’entrée SimulationUpdateParameters et doit être incrémenté continuellement pour chaque nouvelle image. A pour valeur 0 dans SimulationUpdateResult si aucune donnée d’image n’est encore disponible. |
| ViewTransform | Paire stéréoscopique gauche/droite des matrices de transformation de la vue caméra de l’image. Pour le rendu monoscopique, seul le membre `Left` est valide. |
| FieldOfView | Paire stéréoscopique gauche/droite des champs de vision de la caméra de l’image dans la [convention du champ de vision OpenXR](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). Pour le rendu monoscopique, seul le membre `Left` est valide. |
| NearPlaneDistance | Distance du plan proche utilisée pour la matrice de projection de l’image distante actuelle. |
| FarPlaneDistance | Distance du plan lointain utilisée pour la matrice de projection de l’image distante actuelle. |

Les paires stéréoscopiques `ViewTransform` et `FieldOfView` permettent de définir les deux valeurs de caméra oculaire en cas d’activation du rendu stéréoscopique. Sinon, les membres `Right` sont ignorés. Comme vous pouvez le voir, seule la transformation de la caméra est transmise en tant que matrices de transformation 4x4 standard alors qu’aucune matrice de projection n’est spécifiée. Les matrices réelles sont calculées en interne par Azure Remote Rendering à l’aide des champs de vision spécifiés et des plans proche et lointain actuels définis sur l’[ API CameraSettings](../overview/features/camera.md).

Étant donné que vous pouvez changer le plan proche et le plan lointain sur [CameraSettings](../overview/features/camera.md) pendant l’exécution comme vous le souhaitez et que le service applique ces paramètres de manière asynchrone, chaque SimulationUpdateResult contient également le plan proche et le plan lointain spécifiques utilisés lors du rendu de l’image correspondante. Vous pouvez utiliser ces valeurs de plan afin d’adapter vos matrices de projection pour le rendu des objets locaux en fonction du rendu de l’image distante.

Enfin, tandis que l’appel de la **mise à jour de la simulation** nécessite que le champ de vision respecte la convention OpenXR, pour des raisons de standardisation et de sécurité algorithmique, vous pouvez utiliser les fonctions de conversion illustrées dans les exemples de remplissage de structure suivants :

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Ces fonctions de conversion permettent un basculement rapide entre la spécification de champ de vision et une matrice de projection de perspective 4x4 standard, suivant vos besoins en matière de rendu local. Ces fonctions de conversion contiennent une logique de vérification et retournent des erreurs, sans définir de résultat valide, si les matrices de projection d’entrée ou les champs de vision d’entrée ne sont pas valides.

## <a name="api-documentation"></a>Documentation de l’API

* [RemoteManagerStatic.StartupRemoteRendering() C#](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [Classe GraphicsBinding C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [Classe GraphicsBindingWmrD3d11 C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [Classe GraphicsBindingSimD3d11 C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Struct RemoteRenderingInitialization C++](/cpp/api/remote-rendering/remoterenderinginitialization)
* [Classe GraphicsBinding C++](/cpp/api/remote-rendering/graphicsbinding)
* [Classe GraphicsBindingWmrD3d11 C++](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [Classe GraphicsBindingSimD3d11 C++](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Étapes suivantes

* [Appareil photo](../overview/features/camera.md)
* [Reprojection en phase tardive](../overview/features/late-stage-reprojection.md)
* [Tutoriel : Affichage de modèles rendus à distance](../tutorials/unity/view-remote-models/view-remote-models.md)