---
title: Changement d’environnement et de matériaux
description: Tutoriel qui explique comment modifier la carte d’environnement et les matériaux d’une scène Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678680"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Tutoriel : Changement d’environnement et de matériaux

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Changer la carte d’environnement d’une scène
> * Modifier les paramètres des matériaux
> * Charger des textures personnalisées

## <a name="prerequisites"></a>Prérequis

Ce tutoriel part du principe que vous connaissez le [Tutoriel : Utilisation d’entités distantes dans Unity](working-with-remote-entities.md). Cela dit, vous avez juste besoin d’un projet Unity qui vous permette de vous connecter à des sessions et de charger un modèle, comme indiqué dans le [Tutoriel : Configuration d’un projet Unity en partant de zéro](project-setup.md).

> [!TIP]
> Le [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering) contient des projets Unity préparés pour tous les tutoriels du dossier *Unity*, que vous pouvez utiliser comme référence.

## <a name="change-the-environment-map"></a>Changer la carte d’environnement

Azure Remote Rendering prend en charge l’utilisation des [skybox](../../overview/features/sky.md) (parfois appelées « cartes d’environnement ») pour simuler la lumière ambiante. Cela s’avère particulièrement utile lorsque vos objets utilisent un *[rendu physique](../../overview/features/pbr-materials.md)* , comme le font nos exemples de modèles. ARR est également fourni avec différentes textures de carte intégrées, que nous utiliserons dans ce tutoriel.

Créez un script appelé **RemoteSky** et ajoutez-le à un nouveau GameObject. Ouvrez le fichier de script et remplacez son contenu par le code suivant :

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Notez que le variant `LoadTextureFromSASAsync` est utilisé ci-dessus, car les textures intégrées sont chargées. En cas de chargement à partir d’un [stockage Blob lié](../../how-tos/create-an-account.md#link-storage-accounts), utilisez le variant `LoadTextureAsync`. Vous trouverez un exemple d’utilisation de ces modèles dans la [section relative au chargement des modèles](../../concepts/models.md#loading-models).

Lorsque vous exécutez le code et passez d’une carte à l’autre, vous pouvez voir une lumière radicalement différente sur votre modèle. Toutefois, l’arrière-plan reste noir et vous ne pouvez pas voir la texture réelle de la carte. C’est intentionnel, car le rendu d’un arrière-plan serait gênant sur un appareil de réalité augmentée. Dans une application adaptée, vous devez utiliser des textures de carte similaires à votre environnement réel, car de cette façon, les objets paraissent plus réels.

## <a name="modify-materials"></a>Modifier les matériaux

Dans le tutoriel précédent, nous avons utilisé des [composants de remplacement d’état](../../overview/features/override-hierarchical-state.md) pour changer la couleur de teinte des objets sélectionnés. À présent, nous souhaitons obtenir un effet similaire, mais en modifiant le [matériau](../../concepts/materials.md) d’un objet.

Tout d’abord, nous avons besoin d’un script pour sélectionner les objets, comme nous l’avons fait dans le [deuxième tutoriel](working-with-remote-entities.md). Si vous n’avez pas encore de script **RemoteRaycaster**, créez-le maintenant. Remplacez son contenu par le code suivant :

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }
}
```

Ajoutez le composant à votre objet de jeu *RemoteRendering*. Il est chargé de sélectionner les objets avec la souris et d’ajouter des composants *RemoteModelEntity* aux objets sélectionnés. C’est dans cette classe de composant que nous implémentons la fonctionnalité de changement des matériaux.

Si vous n’avez pas encore de script **RemoteModelEntity**, créez-le et remplacez son contenu par le code suivant :

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Lorsque vous exécutez ce code, les objets sur lesquels vous pointez avec la souris sont mis en surbrillance. L’effet est similaire à celui du deuxième tutoriel, mais la façon dont nous l’obtenons est différente. Ici, nous obtenons la liste des matériaux de l’objet sélectionné, puis nous modifions le premier pour avoir une couleur d’albédo différente.

> [!IMPORTANT]
> Pour que cette méthode permette de mettre en surbrillance les bonnes parties d’un modèle, vous devez faire attention à la façon dont vous créez le modèle. Cette méthode fonctionne parfaitement si chaque objet utilise un seul matériau. Si ce n’est pas le cas, le code naïf ci-dessus ne permet pas d’obtenir le résultat souhaité.

## <a name="use-a-different-texture"></a>Utiliser une autre texture

Les [textures](../../concepts/textures.md) font généralement partie d’un modèle source. Pendant la [conversion d’un modèle](../../quickstarts/convert-model.md), toutes les textures sont converties au format d’exécution nécessaire et sont empaquetées dans le fichier de modèle final. Pour remplacer une texture au moment de l’exécution, vous devez l’enregistrer au [format de fichier DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) et la charger dans le stockage Blob Azure. Pour savoir comment créer un conteneur d’objets blob Azure, consultez [ce guide de démarrage rapide](../../quickstarts/convert-model.md). Une fois que vous avez un conteneur d’objets blob, vous pouvez l’ouvrir dans l’Explorateur Stockage Azure et charger votre fichier par glisser-déposer.

Côté runtime, vous pouvez envoyer une ressource de texture vers le stockage Blob de deux manières :

* Envoyez la texture à l’aide de son URI SAS. Pour cela, cliquez avec le bouton droit sur le fichier chargé, puis sélectionnez **Obtenir la signature d’accès partagé** dans le menu contextuel. Utilisez cet URI SAS avec le variant de fonction `LoadTextureFromSASAsync` (voir l’exemple de code ci-dessous).
* Si le [stockage Blob est lié au compte](../../how-tos/create-an-account.md#link-storage-accounts), envoyez la texture directement à l’aide des paramètres du stockage Blob. Dans le cas présent, la fonction de chargement adaptée est `LoadTextureAsync`.

Ouvrez le script **RemoteModelEntity**, ajoutez le code suivant et supprimez les fonctions en double :

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Exécutez ce code et pointez sur votre modèle. Si votre modèle a des coordonnées UV appropriées, vous devriez voir votre texture. Dans le cas contraire, vous ne constaterez qu’un changement de couleur.

## <a name="next-steps"></a>Étapes suivantes

Cela conclut notre série sur l’utilisation d’Azure Remote Rendering avec Unity. À présent, vous devez vous familiariser avec certains concepts fondamentaux d’ARR, tels que les [sessions](../../concepts/sessions.md), les [entités](../../concepts/entities.md) et les [modèles](../../concepts/models.md) afin d’en obtenir une compréhension plus approfondie. Il existe également différentes fonctionnalités, telles que la [lumière](../../overview/features/lights.md), le [rendu en mode contour](../../overview/features/outlines.md), le [remplacement d’état hiérarchique](../../overview/features/override-hierarchical-state.md) et les [matériaux](../../concepts/materials.md), que vous devez explorer plus en détail.

> [!div class="nextstepaction"]
> [Composants et objets de jeu Unity](../../how-tos/unity/objects-components.md)
