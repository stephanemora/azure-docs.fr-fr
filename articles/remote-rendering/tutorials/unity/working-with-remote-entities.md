---
title: Utilisation d’entités distantes dans Unity
description: Tutoriel expliquant comment utiliser des entités ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310209"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Tutoriel : Utilisation d’entités distantes dans Unity

[Tutoriel : Configuration d’un projet Unity en partant de zéro](project-setup.md) a montré comment configurer un nouveau projet Unity pour qu’il fonctionne avec Azure Remote Rendering. Dans ce tutoriel, nous allons nous intéresser aux fonctionnalités les plus courantes dont chaque utilisateur ARR a besoin.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Sélectionner des objets à l’aide du raycasting
> * Remplacer les états d’un objet tels que la couleur de la teinte, l’état de la sélection ou la visibilité
> * Supprimer des entités distantes
> * Déplacer des entités distantes
> * Utiliser des plans de coupe pour effectuer des recherches dans les objets

## <a name="prerequisites"></a>Prérequis

* Ce tutoriel s’appuie sur le [Tutoriel : Configuration d’un projet Unity en partant de zéro](project-setup.md).

> [!TIP]
> Le [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering) contient des projets Unity préparés pour tous les tutoriels du dossier *Unity*, que vous pouvez utiliser comme référence.

## <a name="pick-objects"></a>Sélectionner des objets

Nous voulons interagir avec des objets. La première chose à faire est donc de sélectionner des objets avec le curseur de la souris.

Créez un [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) que vous nommerez **RemoteRaycaster**, puis remplacez l’intégralité de son contenu par le code ci-dessous :

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

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Ajoutez ce composant à l’objet *RemoteRendering* dans votre scène.

> [!WARNING]
>
> Le composant *RemoteRaycaster* nécessite que le composant *ARRServiceUnity* soit attaché au même objet. *ARRServiceUnity* est une classe d’assistance permettant d’accéder plus facilement à certaines fonctionnalités ARR. Toutefois, il ne peut y avoir qu’une seule instance de ce composant dans la scène. Par conséquent, veillez à ajouter tous les composants qui nécessitent *ARRServiceUnity* au même GameObject.
> Si vous souhaitez accéder aux fonctionnalités ARR à partir de plusieurs objets de jeu, ajoutez le composant *ARRServiceUnity* uniquement à l’un d’eux. Ensuite, référencez-le dans les autres scripts ou accédez directement aux fonctionnalités ARR.

Appuyez sur Play, connectez-vous à une session, puis chargez un modèle. Pointez sur des objets de la scène et regardez la sortie de la console. Celle-ci doit afficher le nom d’objet de chaque partie que vous pointez.

## <a name="highlight-objects"></a>Mettre en surbrillance des objets

Dans l’étape qui suit, nous voulons fournir un retour visuel qui indique sur quelles parties du modèle pointe l’utilisateur. Pour cela, nous attachons [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) à l’entité que nous avons choisie. Ce composant peut être utilisé pour activer ou désactiver diverses fonctionnalités dans un objet. Ici, nous l’utilisons pour définir une couleur de teinte et pour activer le [rendu en mode contour](../../overview/features/outlines.md).

Créez un autre fichier de script appelé **RemoteModelEntity** et remplacez son contenu par le code suivant :

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> N’assignez pas ce script à un objet de jeu, car il sera assigné programmatiquement par le code ci-dessous.

Ensuite, nous devons étendre *RemoteRaycaster* pour ajouter le composant *RemoteModelEntity* à l’objet que nous venons de choisir.

Ajoutez le code suivant à l’implémentation de **RemoteRaycaster** et supprimez les fonctions en double :

```csharp
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
        focusedModel = null;
    }
```

Exécutez votre projet et pointez sur un modèle. Vous devriez voir qu’il a désormais une teinte rouge et un contour de sélection blanc.

## <a name="isolate-the-selected-object"></a>Isoler l’objet sélectionné

Vous pouvez également utiliser [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) pour remplacer la visibilité. Cela vous permet d’isoler un objet sélectionné du reste du modèle. Ouvrez le script **RemoteModelEntity**, ajoutez le code suivant et supprimez les fonctions en double :

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Ce code repose sur la présence d’un composant de remplacement d’état au niveau de l’objet situé en haut de la hiérarchie, qui rend tous les objets invisibles. Il remplace ensuite la visibilité de l’objet sélectionné pour rendre cet objet visible. Nous devons donc créer un composant de remplacement d’état au niveau de l’objet racine.

Ouvrez le script **RemoteRendering** et insérez le code ci-dessous en haut de la fonction *LoadModel* :

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Enfin, nous avons besoin d’un moyen d’activer ou de désactiver la visibilité. Ouvrez le script **RemoteRaycaster** et remplacez la fonction *Update* :

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Exécutez le code et cliquez avec le bouton droit sur une partie du modèle. Le reste du modèle disparaît et seule la partie en surbrillance reste visible.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Supprimer les instances GameObject d’entités distantes

Vous avez peut-être remarqué que le code continue à créer des objets, mais qu’il ne les nettoie jamais. Vous observerez la même chose dans le panneau de la hiérarchie d’objets. Lorsque vous développez la hiérarchie d’objets distants pendant la simulation, vous pouvez voir de plus en plus d’objets s’afficher chaque fois que vous pointez sur une nouvelle partie du modèle.

Un trop grand nombre d’objets dans une scène a un impact négatif sur les performances. Vous devez toujours nettoyer les objets dont vous n’avez plus besoin.

Insérez le code ci-dessous dans le script **RemoteRaycaster** et supprimez les fonctions en double :

```csharp
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
```

## <a name="move-objects"></a>Déplacement d’objets

Dans l’étape qui suit, nous voulons déplacer un objet sélectionné. Dans le script **RemoteRaycaster**, insérez ce code et supprimez la fonction en double :

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Si vous exécutez ce code, vous remarquerez que rien ne se passe. Cela est dû au fait que la modification de la transformation d’un objet ne synchronise pas automatiquement la modification de l’état sur le serveur, et ce, pour des raisons de performances. Vous devez donc envoyer (push) manuellement cette modification d’état au serveur ou activer **SyncEveryFrame** dans le composant *RemoteEntitySyncObject*.

Ouvrez le script **RemoteModelEntity** et ajoutez la ligne suivante :

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

En exécutant de nouveau le code, vous devriez pouvoir cliquer sur un objet et le faire glisser.

## <a name="add-a-cut-plane"></a>Ajouter un plan de coupe

La dernière fonctionnalité que nous voulons essayer dans ce tutoriel est l’utilisation des [plans de coupe](../../overview/features/cut-planes.md). Un plan de coupe permet de couper certaines parties des objets rendus, afin de pouvoir voir à l’intérieur.

Créez un nouveau GameObject dans la scène **CutPlane**. Créez un script et nommez-le **RemoteCutPlane**. Ajoutez le composant au nouveau GameObject.

Ouvrez le fichier de script et remplacez son contenu par le code suivant :

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

À présent, quand vous exécutez votre code, vous devriez voir que le modèle a été découpé. Vous pouvez sélectionner l’objet *CutPlane*, le déplacer et le faire pivoter dans la fenêtre *Scene*. Vous pouvez activer ou désactiver le plan de coupe en désactivant l’objet CutPlane.

## <a name="next-steps"></a>Étapes suivantes

Vous connaissez maintenant les fonctionnalités les plus importantes pour interagir avec les objets distants. Dans le tutoriel suivant, nous verrons comment personnaliser l’apparence d’une scène.

> [!div class="nextstepaction"]
> [Tutoriel : Changement d’environnement et de matériaux](changing-environment-and-materials.md)
