---
title: Configuration d’un projet Unity en partant de zéro
description: Explique comment configurer un projet Unity vide en vue d’une utilisation avec Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678650"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Tutoriel : Configuration d’un projet Unity en partant de zéro

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Configuration d’un projet Unity vide pour ARR
> * Création et arrêt de sessions de rendu
> * Réutilisation de sessions existantes
> * Connexion à une session et déconnexion
> * Chargement de modèles dans une session de rendu
> * Affichage des statistiques de connexion

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, vous avez besoin des éléments suivants :

* Les informations de votre compte (ID du compte, clé du compte, ID de l’abonnement). Si vous n’avez pas de compte, [créez-en un](../../how-tos/create-an-account.md).
* Le SDK Windows 10.0.18362.0 [(télécharger)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Installez la dernière version de Visual Studio 2019 [(télécharger)](https://visualstudio.microsoft.com/vs/older-downloads/)
* Git [(télécharger)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(télécharger)](https://unity3d.com/get-unity/download)
  * Installez ces modules dans Unity :
    * **UWP** - Prise en charge de la génération avec la plateforme Windows universelle
    * **IL2CPP** - Prise en charge de la génération dans Windows (IL2CPP)

> [!TIP]
> Le [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering) contient des projets Unity préparés adaptés à chacun des tutoriels. Vous pouvez utiliser ces projets comme référence.

## <a name="create-a-new-unity-project"></a>Créer un projet Unity

Dans Unity Hub, créez un projet.
Dans cet exemple, nous supposons que le projet est créé dans un dossier nommé `RemoteRendering`.

![Fenêtre New Project (Nouveau projet)](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Configurer le manifeste du projet

Vous devez modifier le fichier `Packages/manifest.json` qui se trouve dans le dossier de votre projet Unity. Ouvrez le fichier dans un éditeur de texte, puis ajoutez les lignes listées ci-dessous :

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Le package de pipeline de rendu universel est facultatif, mais recommandé pour des raisons de performances.
Une fois que vous avez modifié et enregistré le manifeste, Unity s’actualise automatiquement. Vérifiez que les packages ont été chargés dans la fenêtre *Project* (Projet) :

![Vérifier les importations de packages](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Vérifiez que vous avez la version la plus récente du package.

Les étapes suivantes permettent de vérifier que votre projet utilise la version la plus récente du package Remote Rendering.
1. Sélectionnez le package dans la fenêtre Project, puis cliquez sur l’icône de package : ![Sélection de l’icône de package](media/package-icons.png)
1. Dans l’inspecteur (Inspector), cliquez sur « View in Package Manager » (Afficher dans le gestionnaire de package) : ![inspecteur de package](media/package-properties.png)
1. Dans la page Package Manager (Gestionnaire de package), vérifiez que le bouton Update (Mettre à jour) est disponible pour le package Remote Rendering. Si c’est le cas, en cliquant dessus, vous mettez à jour le package vers la dernière version disponible : ![Le package ARR dans le Gestionnaire de package](media/package-manager.png)
1. Parfois, la mise à jour du package peut entraîner des erreurs dans la console. Si cela se produit, essayez de fermer, puis de rouvrir le projet.

## <a name="configure-the-camera"></a>Configurer la caméra

Sélectionner le nœud **Main Camera** (Caméra principale).

1. Sous *Transform*, réinitialisez sa transformation :

    ![Réinitialisation de la transformation de la caméra](media/camera-reset-transform.png)

1. Définissez **Clear flags** (Effacer les indicateurs) sur *Solid Color* (Couleur unie).

1. Définissez **Background** (Arrière-plan) sur *Black* (Noir).

1. Définissez **Clipping Planes** (Plans de découpage) sur *Near = 0.3* (Proche = 0,3) et *Far = 20* (Lointain = 20). Cela signifie que le rendu découpera la géométrie qui est plus proche que 30 cm ou plus lointaine que 20 mètres.

    ![Propriétés de la caméra Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajuster les paramètres du projet

1. Ouvrez *Edit >Project Settings...* (Modifier > Paramètres du projet).
1. Dans la liste à gauche, sélectionnez Quality (Qualité).
1. Définissez **Default Quality Level** (Niveau de qualité par défaut) sur *Low* (Bas).

    ![Modifier les paramètres de qualité du projet](media/settings-quality.png)

1. Sélectionnez **Graphics** (Graphismes) sur la gauche.
1. Définissez le paramètre **Scriptable Rendering Pipeline** (Pipeline de rendu pouvant contenir des scripts) sur *HybridRenderingPipeline*. Ignorez cette étape si vous n’utilisez pas le pipeline de rendu universel.

    ![Modification des paramètres graphiques du projet](media/settings-graphics-lwrp.png) Parfois l’interface utilisateur ne remplit pas la liste des types de pipeline disponibles à partir des packages. Dans ce cas, la ressource *HybridRenderingPipeline* doit être glissée manuellement vers le champ : ![Modification des paramètres graphiques du projet](media/hybrid-rendering-pipeline.png)
1. Sélectionnez **Player** (Lecteur) sur la gauche.
1. Sélectionnez l’onglet **Universal Windows Platform settings** (Paramètres de la plateforme Windows universelle).
1. Modifiez les **paramètres XR** pour prendre en charge Windows Mixed Reality : ![paramètres du lecteur](media/xr-player-settings.png)
1. Sélectionnez les paramètres comme dans la capture d’écran ci-dessus :
    1. Activez **Virtual Reality Supported** (Réalité virtuelle prise en charge).
    1. Définissez **Depth Format**(Format de profondeur) sur *16-Bit Depth* (Profondeur de 16 bits).
    1. Activez **Depth Buffer Sharing** (Partage du tampon de profondeur).
    1. Définissez **Stereo Rendering Mode** (Mode de rendu stéréo) sur *Single Pass Instanced* (Instance à passe unique).

1. Dans la même fenêtre, au-dessus de *XR Settings*, développez **Publishing Settings** (Paramètres de publication).
1. Faites défiler jusqu’à **Capabilities** (Fonctionnalités), puis sélectionnez :
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Facultatif pour le développement : **PrivateNetworkClientServer**

      Cette option est nécessaire si vous souhaitez connecter le débogueur distant Unity à votre appareil.

1. Dans **Supported Device Families** (Familles d’appareils prises en charge), activez **Holographic** (Holographique) et **Desktop** (Bureau).

1. Si vous souhaitez utiliser Mixed Reality Toolkit, consultez la [documentation MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview) pour en savoir plus sur les paramètres et les fonctionnalités recommandés.

## <a name="validate-project-setup"></a>Valider la configuration du projet

Effectuez les étapes suivantes pour vérifier que les paramètres du projet sont corrects.

1. Choisissez l’entrée ValidateProject dans le menu RemoteRendering de la barre d’outils de l’éditeur Unity.
1. Utilisez la fenêtre ValidateProject pour vérifier les paramètres du projet et les corriger si nécessaire.

    ![Validation du projet de l’éditeur Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Créer un script pour initialiser Azure Remote Rendering

Créez un [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) et nommez-le **RemoteRendering**. Ouvrez le fichier de script et remplacez l’intégralité de son contenu par le code ci-dessous :

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Ce script initialise Azure Remote Rendering, il lui indique quel objet de caméra utiliser pour le rendu, puis il place un **Create Session** (Créer une session) dans la fenêtre d’affichage quand le *mode Lecture* (Play Mode) est activé.

> [!CAUTION]
> Si vous modifiez puis enregistrez le script en mode Lecture dans Unity, Unity peut se figer et vous obliger à l’arrêter par le biais du gestionnaire des tâches. Par conséquent, vous devez toujours désactiver le mode Lecture avant de modifier le script *RemoteRendering*.

## <a name="test-azure-remote-rendering-session-creation"></a>Tester la création d’une session Azure Remote Rendering

Créez un GameObject dans la scène, puis ajoutez-y le composant *RemoteRendering*. Renseignez le *domaine*, l’*ID* et la *clé* de votre compte Remote Rendering :

![Propriétés du composant RemoteRendering](media/remote-rendering-component.png)

Démarrez l’application dans l’éditeur (**appuyez sur Play** ou sur CTRL + P). Le bouton **Create Session** (Créer une session) doit s’afficher dans la fenêtre d’affichage. Cliquez dessus pour démarrer votre première session ARR :

![Création d’une première session](media/test-create.png)

En cas d’échec, vérifiez que vous avez correctement entré les détails de votre compte dans les propriétés du composant RemoteRendering. Dans le cas contraire, un message s’affiche dans la fenêtre de console indiquant l’ID de session qui vous est affecté, ainsi que l’état actuel de la session, c’est-à-dire *Starting* (En cours de démarrage) :

![Sortie de démarrage de la session](media/create-session-output.png)

À ce stade, Azure provisionne un serveur et démarre une machine virtuelle de rendu à distance. En général, **cette opération prend de 3 à 5 minutes**. Lorsque la machine virtuelle est prête, le rappel du script Unity `OnSessionStatusChanged` est exécuté et affiche le nouvel état de la session :

![Sortie indiquant que la session est prête](media/create-session-output-2.png)

C’est tout ! Pour l’instant, rien ne se passe. Pour éviter que des frais ne vous soient facturés, vous devez toujours arrêter les sessions quand elles ne sont plus nécessaires. Pour cela, vous pouvez soit cliquer sur le bouton **Stop Session** (Arrêter la session), soit arrêter la simulation Unity. En raison de la propriété **Auto-Stop Session** (Arrêter automatiquement la session) du composant *ARRServiceUnity* qui est activée par défaut, la session est automatiquement arrêtée. Si tout échoue, en raison d’un plantage ou d’un problème de connexion, votre session peut s’exécuter pendant toute la durée définie pour votre *MaxLeaseTime* avant d’être arrêtée par le serveur.

> [!NOTE]
> L’arrêt d’une session prend effet immédiatement et ne peut pas être annulé. Une fois la session arrêtée, vous devez en créer une nouvelle, entraînant les mêmes frais de démarrage.

## <a name="reusing-sessions"></a>Réutilisation des sessions

La création d’une session est malheureusement une opération fastidieuse. Il est donc préférable de créer des sessions le plus rarement possible et de les réutiliser.

Insérez le code suivant dans le script *RemoteRendering* et supprimez les anciennes versions des fonctions dupliquées :

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Avant d’exécuter ce code, veillez à désactiver l’option **Auto-Stop Session** (Arrêter automatiquement la session) dans le composant RemoteRendering. Dans le cas contraire, chaque session que vous créez sera automatiquement arrêtée lorsque vous arrêterez la simulation, et la tentative de réutilisation échouera.

Lorsque vous appuyez sur *Play*, vous disposez maintenant de trois boutons dans la fenêtre d’affichage : **Create Session** (Créer une session), **Query Active Sessions** (Interroger les sessions actives) et **Use Existing Session** (Utiliser une session existante). Le premier bouton crée toujours une nouvelle session. Le deuxième bouton interroge les sessions *actives* existantes. Si vous n’avez pas spécifié manuellement un ID de session à utiliser, cette action sélectionne automatiquement cet ID de session pour une utilisation ultérieure. Le troisième bouton tente une connexion à une session existante. Il s’agit soit de celle que vous avez spécifiée manuellement par le biais de la propriété de composant *Session ID* (ID de session), soit de celle qui a été trouvée lorsque vous avez appuyé sur le bouton *Query Active Sessions* (Interroger les sessions actives).

La fonction **AutoStartSessionAsync** est utilisée pour simuler les appuis sur bouton en dehors de l’éditeur.

> [!TIP]
> Il est possible d’ouvrir des sessions qui ont été arrêtées, qui ont expiré ou qui sont dans un état d’erreur. Même si elles ne peuvent plus être utilisées pour le rendu, vous pouvez interroger les informations qui leur sont associées dans une session inactive. Le code ci-dessus vérifie l’état d’une session dans `ARRService_OnSessionStarted` afin de s’arrêter automatiquement quand la session devient inutilisable.

Avec cette fonctionnalité, vous pouvez désormais créer et réutiliser des sessions, ce qui devrait améliorer considérablement votre workflow de développement.

En règle générale, la création d’une session est déclenchée en dehors de l’application cliente en raison du temps nécessaire pour faire tourner le serveur.

## <a name="connect-to-an-active-session"></a>Se connecter à une session active

Jusqu’à maintenant, nous avons créé et ouvert des sessions. L’étape suivante consiste à *se connecter* à une session. Une fois connecté, le serveur de rendu génère des images et envoie un flux vidéo à notre application.

Insérez le code suivant dans le script *RemoteRendering* et supprimez les anciennes versions des fonctions dupliquées :

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Pour tester cette fonctionnalité :

1. Appuyez sur **Play** dans Unity.
1. Ouvrez une session :
    1. Si vous disposez déjà d’une session, appuyez sur **Query Active Sessions** (Interroger les sessions actives), puis sur **Use Existing session** (Utiliser une session existante).
    1. Dans le cas contraire, appuyez sur **Create Session** (Créer une session).
1. Appuyez sur **Connect** (Se connecter).
1. Après quelques secondes, la sortie de la console doit indiquer que vous êtes connecté.
1. Pour le moment, rien d’autre ne doit se produire.
1. Appuyez sur **Disconnect** (Se déconnecter) ou désactivez le mode Lecture d’Unity.

>[!NOTE]
> Plusieurs utilisateurs peuvent *ouvrir* une session pour interroger les informations qui lui sont associées. Toutefois, un seul utilisateur peut y être *connecté* à la fois. Si un autre utilisateur est déjà connecté, la connexion échoue avec une **erreur d’établissement de liaison**.

## <a name="load-a-model"></a>Charger un modèle

Insérez le code suivant dans le script *RemoteRendering* et supprimez les anciennes versions des fonctions dupliquées :

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

À présent, lorsque vous appuyez sur Play, puis ouvrez une session et vous connectez à celle-ci, le bouton **Load Model** (Charger le modèle) s’affiche. Une fois que vous cliquez dessus, la sortie de la console indique la progression du chargement et, quand celle-ci atteint 100 %, le modèle d’un moteur doit s’afficher :

![Modèle chargé dans l’éditeur](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) est un composant important qui est utilisé pour la [stabilité des hologrammes](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Toutefois, il n’a d’effet que s’il est déployé sur un appareil Mixed Reality.

> [!TIP]
> Si vous avez suivi le [Guide de démarrage rapide : Convertir un modèle pour le rendu](../../quickstarts/convert-model.md), vous savez déjà comment convertir vos propres modèles. Pour l’afficher, vous avez seulement besoin de placer l’URI d’un modèle converti dans la propriété *Model Name* (Nom du modèle).

## <a name="display-frame-statistics"></a>Afficher les statistiques des frames

Azure Remote Rendering effectue le suivi de diverses informations concernant la qualité de la connexion. Pour un moyen rapide d’afficher ces informations, effectuez les étapes suivantes :

Créez un [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) et nommez-le **RemoteFrameStats**. Ouvrez le fichier de script et remplacez l’intégralité de son contenu par le code ci-dessous :

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Créez un GameObject et nommez-le *FrameStats*. Attachez-le en tant que nœud enfant à l’objet *Main Camera* et définissez sa position sur **x = 0, y = 0, z = 0.325**. Ajoutez le composant **RemoteFrameStats** à l’objet.

Ajoutez un objet enfant **UI > Canvas** à l’objet *FrameStats* et définissez ses propriétés de la façon suivante :

![Propriétés du canevas](media/framestats-canvas.png)

Ajoutez un objet **UI > Text** en tant qu’enfant du canevas et définissez ses propriétés de la façon suivante :

![Propriétés du texte](media/framestats-text.png)

Sélectionnez l’objet *FrameStats* et remplissez le **champ FrameStats** en cliquant sur l’icône représentant un cercle, puis en sélectionnant l’objet **Text** :

![Définition de la propriété Text](media/framestats-set-text.png)

Désormais, lorsque vous êtes connecté à la session à distance, le texte doit afficher les statistiques de streaming :

![Sortie concernant les statistiques de frames](media/framestats-output.png)

Le code désactive la mise à jour des statistiques en dehors de l’éditeur, car une zone de texte verrouillée serait gênante. Une implémentation plus sophistiquée est disponible dans le projet de [démarrage rapide](../../quickstarts/render-model.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris toutes les étapes nécessaires à la création d’un projet Unity vide, et vous avez vu comment l’utiliser avec Azure Remote Rendering. Dans le tutoriel suivant, nous verrons plus en détail comment utiliser des entités distantes.

> [!div class="nextstepaction"]
> [Tutoriel : Utilisation d’entités distantes dans Unity](working-with-remote-entities.md)
