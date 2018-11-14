---
title: Vue d’ensemble du streaming en direct à l’aide d’Azure Media Services | Microsoft Docs
description: Cette rubrique fournit une vue d’ensemble du streaming en direct à l’aide d’Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: c8e4e84d7ae0defdb053108dc668956062c47ea5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962382"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming en direct avec Azure Media Services v3

La diffusion d’événements en flux continu avec Azure Media Services implique généralement les composants suivants :

* Une caméra utilisée pour diffuser un événement.
* Un encodeur vidéo dynamique qui convertit les signaux de la caméra (ou d’un autre appareil, tel qu’un ordinateur portable) en flux de données qui sont envoyés vers le service de streaming en direct. Les signaux peuvent également inclure des messages d’annonce SCTE-35 et des signaux d’annonce. 
* Le service de streaming en direct Media Services vous permet d’ingérer, de prévisualiser, d’empaqueter, d’enregistrer, de chiffrer et de diffuser du contenu pour vos clients ou dans un CDN en vue d’une diffusion ultérieure.

Cet article fournit une description détaillée du service et inclut des schémas des principaux composants impliqués dans le streaming en direct avec Media Services.

## <a name="overview-of-main-components"></a>Présentation des principaux composants

Dans Media Services, les [événements en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) sont responsables du traitement du contenu vidéo en flux continu. Un événement en temps réel fournit un point de terminaison d’entrée (URL de réception) que vous transmettez ensuite à un encodeur live local. Il reçoit des flux de données entrants en continu d’un encodeur live au format RTMP ou Smooth Streaming et les rend disponibles en diffusion via un ou plusieurs [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) (point de terminaison de streaming). Une [sortie en temps réel](https://docs.microsoft.com/rest/api/media/liveoutputs) vous permet de contrôler la publication, l’enregistrement et les paramètres de la fenêtre DVR du flux temps réel. Ces événements en temps réel fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour obtenir un aperçu et valider votre flux avant tout traitement et remise ultérieurs. 

Media Services assure **l’empaquetage dynamique** qui vous permet de prévisualiser et de diffuser votre contenu aux formats de streaming MPEG DASH, HLS et Smooth Streaming sans avoir à effectuer de ré-empaquetage manuel dans ces formats. Vous pouvez lire le contenu sur tout lecteur compatible avec HLS, DASH ou Smooth Streaming. Vous pouvez également utiliser le [Lecteur multimédia Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) pour tester votre flux.

Media Services vous permet de transmettre votre contenu chiffré dynamiquement (**Chiffrement dynamique**) avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Si vous le souhaitez, vous pouvez également appliquer un **filtrage dynamique** pour contrôler le nombre de pistes, les formats et les débits binaires envoyés aux lecteurs. Media Services prend également en charge l’insertion de publicité.

### <a name="new-live-encoding-improvements"></a>Nouvelles améliorations de l’encodage en direct

La dernière version a fait l’objet des améliorations suivantes.

- Nouveau mode de faible latence pour le direct (10 secondes de bout en bout).
- Prise en charge améliorée de RTMP (stabilité accrue et meilleure prise en charge de l’encodeur source).
- Ingestion sécurisée RTMPS.

    Quand vous créez un événement en direct, vous obtenez maintenant 4 URL d’ingestion. Les 4 URL d’ingestion sont presque identiques, ont le même jeton de streaming (AppId) ; seule la partie du numéro de port est différente. Il existe deux URL principales et de secours pour RTMPS.   
- Prise en charge d’un transcodage de 24 heures. 
- Prise en charge améliorée de la signalisation des annonces dans RTMP via SCTE35.

## <a name="liveevent-types"></a>Types d’événements en temps réel

On distingue deux types [d’événement en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) : l’encodage en temps réel et la transmission directe. 

### <a name="live-encoding-with-media-services"></a>L’encodage en temps réel avec Media Services

![encodage en temps réel](./media/live-streaming/live-encoding.png)

Un encodeur live local envoie un flux à débit unique vers l’événement en temps réel activé pour effectuer un encodage en temps réel avec Media Services dans l’un des formats suivants : RTMP ou Smooth Streaming (MP4 fragmenté). L’événement en temps réel procède ensuite à l’encodage en temps réel du flux à débit unique entrant en flux vidéo multidébit (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients.

Lorsque vous créez ce type d’événement en temps réel, spécifiez le paramètre **Basic** (De base) (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Requête directe

![transmission directe](./media/live-streaming/pass-through.png)

La transmission directe est optimisée pour les flux temps réel de longue durée ou l’encodage live linéaire 24h/24, 7j/7 à l’aide d’un encodeur live local. L’encodeur local envoie à l’événement en temps réel un paquet **RTMP** ou **Smooth Streaming** (MP4 fragmenté) multidébit, configuré pour une transmission **directe**. La transmission **directe** correspond aux flux reçus qui transitent par les **événements en temps réel** sans traitement supplémentaire. 

Les événements en temps réel à transmission directe peuvent prendre en charge une résolution allant jusqu’à 4K et des flux HEVC en transmission directe lorsqu’ils sont utilisés avec le protocole de réception Smooth Streaming. 

Lorsque vous créez ce type d’événement en temps réel, spécifiez le paramètre **None** (Aucun) (LiveEventEncodingType.None).

> [!NOTE]
> La méthode pass-through est le moyen le plus économique de diffuser des vidéos en continu si plusieurs événements vous concernent sur une longue période, et si vous avez déjà investi dans des encodeurs locaux. Consultez les détails de la [tarification](https://azure.microsoft.com/pricing/details/media-services/) .
> 

## <a name="liveevent-types-comparison"></a>Comparaison des types d’événements en temps réel 

Le tableau suivant compare les fonctionnalités des deux types d’événements en temps réel.

| Fonctionnalité | Événement en temps réel à transmission directe | Événement en temps réel de base |
| --- | --- | --- |
| L’entrée à débit binaire unique est encodée en plusieurs débits binaires dans le cloud |Non  |Oui |
| Résolution maximale, nombre de couches |4Kp30  |720p, 6 couches, 30 i/s |
| Protocoles d’entrée |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Prix |Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/) et cliquez sur l’onglet « Vidéo en direct » |Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/) |
| Durée maximale |24 x 7 |24 x 7 |
| Prise en charge de l’insertion d’ardoises |Non  |Oui |
| Prise en charge de la signalisation des annonces via l’API|Non  |Oui |
| Prise en charge de la signalisation des annonces via le protocole SCTE35 intrabande|Oui |Oui |
| Légendes CEA 608/708 pass-through |Oui |Oui |
| Capacité de récupération suite à de brèves interruptions du flux de contribution |Oui |Non (défaillance de l’événement en temps réel après plus de six secondes sans données d’entrée)|
| Prise en charge des groupes d’images d’entrée non uniformes |Oui |Non. L’entrée doit être constituée de groupes d’images fixes de 2 secondes |
| Prise en charge de l’entrée à fréquence d’images variable |Oui |Non. L’entrée doit avoir une fréquence d’images fixe.<br/>Les variations mineures sont tolérées, par exemple pendant les scènes à mouvement élevé. Cependant, l’encodeur ne doit pas descendre à 10 images par seconde. |
| Auto-fermeture des événements en temps réel en cas de perte du flux d’entrée |Non  |Après 12 heures si aucune sortie en temps réel n’est en cours d’exécution |

## <a name="liveevent-states"></a>États des événements en temps réel 

État actuel d’un événement en temps réel. Les valeurs possibles incluent :

|État|Description|
|---|---|
|**Arrêté**| Ceci est l’état initial de l’événement en temps réel après sa création (sauf si le démarrage automatique a été sélectionné). Aucune facturation ne survient dans cet état. Dans cet état, les propriétés de l’événement en temps réel peuvent être mises à jour, mais le streaming n’est pas autorisé.|
|**Démarrage en cours**| L’événement en temps réel est en cours de démarrage. Aucune facturation ne survient dans cet état. Aucune mise à jour ni aucun streaming ne sont autorisés durant cet état. Si une erreur se produit, l’événement en temps réel retourne à l’état Arrêté.|
|**Exécution**| L’événement en temps réel est capable de traiter des flux en direct. Il facture désormais l'utilisation. Vous devez arrêter l’événement en temps réel pour empêcher toute facturation supplémentaire.|
|**En cours d’arrêt**| L’événement en temps réel est en cours d’arrêt. Aucune facturation ne survient dans cet état de transition. Aucune mise à jour ni aucun streaming ne sont autorisés durant cet état.|
|**Suppression en cours**| L’événement en temps réel est en cours de suppression. Aucune facturation ne survient dans cet état de transition. Aucune mise à jour ni aucun streaming ne sont autorisés durant cet état.|

## <a name="liveoutput"></a>Sortie en temps réel

Une [sortie en temps réel](https://docs.microsoft.com/rest/api/media/liveoutputs) vous permet de contrôler la publication, l’enregistrement et les paramètres de la fenêtre DVR du flux temps réel. La relation entre un événement en temps réel et une sortie en temps réel est similaire au contenu multimédia traditionnel où un canal (événement en temps réel) a un flux de contenu constant et un programme (sortie en temps réel) est limité à un événement minuté sur cet événement en temps réel.
Vous pouvez spécifier le nombre d’heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour la sortie en temps réel en définissant la propriété **ArchiveWindowLength**. La valeur **ArchiveWindowLength** est un intervalle de temps ISO 8601 représentant la longueur de la fenêtre d’archive (magnétoscope numérique ou DVR). Cette valeur peut être comprise entre 5 minutes et 25 heures. 

La propriété **ArchiveWindowLength** détermine également la durée maximale que les clients peuvent rechercher en arrière à partir de la position dynamique actuelle. Les sorties en temps réel peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque sortie en temps réel est associée à une [ressource](https://docs.microsoft.com/rest/api/media/assets) et enregistre les données dans un conteneur du stockage Azure associé au compte Media Services. Pour publier la sortie en temps réel, vous devez créer un [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) (localisateur de streaming) pour la ressource associée. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un événement en temps réel prend en charge jusqu’à trois sorties en temps réel exécutées simultanément, ce qui rend possible la création de plusieurs archives du même flux entrant. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Par exemple, votre entreprise doit diffuser un flux linéaire en direct 24h/24, 7j/7, mais vous souhaitez créer des « enregistrements » de programmes tout au long de la journée qui seront proposés aux utilisateurs comme contenu à la demande dans le cadre d’un service de replay.  Dans ce scénario, vous commencez par créer une sortie en temps réel principale, avec une fenêtre d’archive courte d’1 heure ou moins que les utilisateurs pourront visionner en tant que flux en direct principal. Vous créez ensuite un localisateur de streaming pour cette sortie en temps réel et la publiez sur votre application ou site web en tant que flux « en direct ».  Alors que le flux est en cours d’exécution, vous pouvez créer par programmation une deuxième sortie en temps réel simultanée au début d’une émission (ou 5 minutes avant pour fournir des descripteurs à supprimer ultérieurement). Cette deuxième sortie en temps réel peut être arrêtée 5 minutes après la fin du programme ou de l’événement, et vous pouvez ensuite créer un nouveau localisateur de streaming pour publier ce programme en tant que ressource à la demande dans le catalogue de votre application.  Vous pouvez répéter plusieurs fois ce processus pour les autres limites ou points forts du programme que vous souhaitez partager immédiatement à la demande, tandis que le flux « en direct » de la première sortie en temps réel continue de diffuser le flux linéaire.  En outre, vous pouvez tirer parti de la prise en charge du filtre dynamique pour découper le début et la fin de l’archive à partir de la sortie en temps réel que vous avez créée afin d’éviter le chevauchement des programmes et de délimiter de manière plus précise le début et la fin du contenu. Le contenu archivé peut également être soumis à une [transformation](https://docs.microsoft.com/rest/api/media/transforms) de codage ou à un sous-découpage précis des images dans plusieurs formats de sortie à des fins de syndication dans les autres services.

## <a name="streamingendpoint"></a>StreamingEndpoint

Une fois que le flux transite dans l’événement en temps réel, vous pouvez commencer l’événement de streaming en créant une ressource, une sortie en temps réel et un localisateur de streaming. Le flux est alors archivé et mis à la disposition des utilisateurs via le [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Une fois votre compte Media Services créé, un point de terminaison de streaming par défaut est ajouté à votre compte à l’état Arrêté. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état En cours d’exécution.

## <a name="billing"></a>Facturation

La facturation d’un événement en temps réel commence dès que son état passe à « En cours d’exécution ». Pour mettre fin à la facturation de l’événement en temps réel, vous devez arrêter ce dernier.

> [!NOTE]
> Lorsque la valeur **LiveEventEncodingType** de votre [événement en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) est définie sur Basic (De base), Media Services mettra automatiquement fin aux événements en temps réel « en cours d’exécution » 12 heures après la perte du flux d’entrée et l’absence de sorties en temps réel en cours d’exécution. Toutefois, vous serez toujours facturé pour la durée pendant laquelle l’événement en temps réel était à l’état « En cours d’exécution ».
>

Le tableau suivant montre comment les états des événements en temps réel sont mappés au mode de facturation.

| État des événements en temps réel | Existe-t-il une facturation ? |
| --- | --- |
| Démarrage en cours |Aucun (état transitoire) |
| Exécution |OUI |
| En cours d’arrêt |Aucun (état transitoire) |
| Arrêté |Non  |

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
