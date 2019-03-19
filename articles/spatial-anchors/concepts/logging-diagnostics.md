---
title: Journalisation et diagnostics dans Azure des ancres spatiales | Microsoft Docs
description: Explication détaillée de la génération et de récupérer la journalisation et diagnostics dans Azure des ancres spatiale.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882826"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Journalisation et diagnostics dans Azure des ancres Spatial

Ancres Spatial Azure fournit un mécanisme de journalisation standard qui est utile pour le développement d’applications. Le mode de journalisation de diagnostics ancres Spatial est utile lorsque vous avez besoin de plus d’informations pour le débogage. Journalisation des diagnostics stocke les images de l’environnement.

## <a name="standard-logging"></a>Journalisation standard
Dans l’API ancres spatiale, vous pouvez vous abonner au mécanisme de journalisation pour obtenir des journaux utiles pour le développement d’applications et le débogage. Les API de journalisation standard ne stocker des photos de l’environnement sur le disque de l’appareil. Le SDK fournit ces journaux en tant que les rappels d’événement. C’est à vous permet d’intégrer ces journaux dans le mécanisme de journalisation de l’application.

### <a name="configuration-of-log-messages"></a>Configuration des messages de journal
Il existe deux rappels d’intérêt pour l’utilisateur. L’exemple suivant montre comment configurer la session.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Propriétés et événements

Ces rappels d’événement sont fournis pour traiter les journaux et les erreurs de la session :

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Spécifie le niveau de détail pour les événements à recevoir de l’exécution.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fournit des événements de journal de débogage standard.
- [Erreur](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error) : Fournit des événements de journal que le runtime considère comme erreurs.

## <a name="diagnostics-logging"></a>Journalisation des diagnostics

Outre le mode standard d’opération pour la journalisation, ancres Spatial a également un mode de diagnostic. Mode de diagnostics capture les images de l’environnement et les enregistre sur le disque. Vous pouvez utiliser ce mode pour déboguer certains types de problèmes, tels que la défaillance de manière prévisible localiser un point d’ancrage. Activer l’enregistrement des diagnostics uniquement pour reproduire un problème spécifique. Puis, désactivez-la. Ne pas activer les diagnostics lorsque vous exécutez vos applications normalement.

Pendant une interaction de la prise en charge avec Microsoft, un représentant Microsoft peut demander si vous êtes prêt à envoyer une offre groupée de diagnostics pour un examen approfondi. Dans ce cas, vous pouvez décider activer les diagnostics et de reproduire le problème permet d’envoyer le groupe de diagnostic. 

Si vous envoyez un journal de diagnostic à Microsoft sans accusé de réception préalable d’un représentant de Microsoft, la demande sera transmise sans réponse.

Les sections suivantes montrent comment activer le mode de diagnostics et également comment envoyer les journaux de diagnostic à Microsoft.

### <a name="enable-diagnostics-logging"></a>Activer la journalisation des diagnostics

Lorsque vous activez une session pour la journalisation des diagnostics, toutes les opérations dans la session ont correspondantes journalisation des diagnostics dans le système de fichiers local. Au cours de la journalisation, les images de l’environnement sont enregistrées sur le disque.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Envoyer le groupe de diagnostics

L’extrait de code suivant montre comment envoyer une offre groupée de diagnostic à Microsoft. Cette offre groupée inclut les images de l’environnement capturées par la session une fois que vous activez les diagnostics. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Parties d’un ensemble de diagnostics
Le groupe de diagnostics peut contenir les informations suivantes :

- **Images de l’image clé**: Images de l’environnement capturées pendant la session alors que les diagnostics ont été activés.
- **Journaux** : Journaliser les événements enregistrés par le runtime.
- **Métadonnées de session**: Métadonnées qui identifie la session.
