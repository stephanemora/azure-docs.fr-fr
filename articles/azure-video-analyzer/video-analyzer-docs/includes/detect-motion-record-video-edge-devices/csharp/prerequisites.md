---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: 7f82c55260f13c9f9016dcef03f35cded7c4f771
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385852"
---
* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > Vous aurez besoin d’un abonnement Azure dans lequel vous avez accès au rôle [Contributeur](../../../../../role-based-access-control/built-in-roles.md#contributor) et au rôle [Administrateur de l’accès utilisateur](../../../../../role-based-access-control/built-in-roles.md#user-access-administrator). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de compte pour qu’il vous les octroie.
* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    > [!TIP]
    > Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. N’hésitez pas à ignorer cette invite.    
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).         

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d'ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/detect-motion-record-video-edge-devices/overview.png" alt-text="Publier des événements d’inférence associés sur le hub IoT Edge":::

Le diagramme précédent montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module périphérique](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP qui héberge un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](./../../../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud du [processeur de détection de mouvement](./../../../pipeline.md#motion-detection-processor). La source RTSP envoie les mêmes images vidéo à un nœud [processeur de porte de signal](./../../../pipeline.md#signal-gate-processor), qui reste fermé jusqu’à ce qu’il soit déclenché par un événement.

Quand le processeur de détection de mouvement détecte des mouvements dans la vidéo, il envoie un événement au nœud processeur de porte de signal, ce qui le déclenche. La porte s’ouvre pendant la durée configurée, et les images vidéo sont envoyées au nœud [récepteur de fichiers](./../../../pipeline.md#file-sink). Ce nœud récepteur enregistre la vidéo au format de fichier MP4 sur le système de fichiers local de votre appareil de périphérie. Le fichier est enregistré à l’emplacement configuré.

Dans ce guide de démarrage rapide, vous allez :

1. Créer et déployer le pipeline.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l’environnement de développement
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
