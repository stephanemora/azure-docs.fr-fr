---
title: Transformations et travaux dans Azure Media Services | Microsoft Docs
description: Lorsque vous utilisez Media Services, vous devez créer une transformation pour décrire les règles ou les spécifications de traitement de vos vidéos. Cet article explique ce qu’est une transformation et comment l’utiliser.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d621afd682e6040179777f4cd6d991ff31acb5a3
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445489"
---
# <a name="transforms-and-jobs"></a>Transformations et travaux
 
Utilisez des [transformations](https://docs.microsoft.com/rest/api/media/transforms) pour configurer des tâches courantes de codage ou d'analyse des vidéos. Chaque **transformation** décrit une recette, ou un flux de travail de tâches pour le traitement de vos fichiers vidéo ou audio. Une transformation unique peut appliquer plusieurs règles. Par exemple, une transformation peut spécifier que chaque vidéo est encodée dans un fichier MP4 à une vitesse de transmission donnée, et qu’une image miniature est générée à partir de la première image de la vidéo. Vous devez ajouter une entrée TransformOutput pour chaque règle que vous souhaitez inclure dans votre transformation. Vous pouvez créer des transformations dans votre compte Media Services à l’aide de l’API Media Services v3 directement, ou à l’aide des kits de développement logiciel (SDK) publiés. L’API Azure Media Services v3 est pilotée par Azure Resource Manager. Vous pouvez donc utiliser des modèles Resource Manager pour créer et déployer des transformations dans votre compte Media Services. Le contrôle d’accès en fonction du rôle peut être utilisé pour bloquer l’accès aux transformations.

L’opération de mise à jour de l’entité [Transform](https://docs.microsoft.com/rest/api/media/transforms) est destinée à modifier la description ou les priorités des TransformOutputs sous-jacents. Il est recommandé que ces mises à jour soient effectuées lorsque tous les travaux en cours sont terminés. Si vous souhaitez réécrire la recette, vous devez créer une nouvelle transformation.

Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Azure Media Services pour appliquer la **transformation** à un contenu vidéo ou audio d’entrée donné. Lorsque la transformation est créée, vous pouvez envoyer des travaux à l’aide des API Media Services ou de l’un des kits de développement logiciel (SDK) publiés. Le **travail** spécifie des informations comme l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l'emplacement de votre vidéo d'entrée en utilisant : des URL HTTPS, des URL SAS ou des [éléments multimédia](https://docs.microsoft.com/rest/api/media/assets). La progression et l’état des travaux peuvent être obtenus en surveillant les événements avec Event Grid. Pour plus d’informations, consultez la section relative à la [surveillance des événements à l’aide d’Event Grid](job-state-events-cli-how-to.md).

L’opération de mise à jour de l’entité [Job](https://docs.microsoft.com/rest/api/media/jobs) (Travail) permet de modifier les propriétés *description*, et *priority* une fois le travail soumis. Une modification de la propriété *priority* ne s’applique que si le travail est toujours dans un état de file d’attente. Si le traitement du travail a commencé, ou est terminé, la modification de la priorité n’a aucun effet.

Le schéma qui suit présente le flux de travail transformations/travaux.

![Transformations](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Les propriétés de **Transform** et **Job** (Transformation et Travail) de type DateHeure sont toujours au format UTC.

## <a name="typical-workflow"></a>Flux de travail classique

1. Créer une transformation 
2. Envoyer des travaux sous cette transformation 
3. Répertorier les transformations 
4. Supprimer une transformation si vous ne prévoyez pas de l’utiliser ultérieurement. 

### <a name="example"></a>Exemples

Supposons que vous souhaitez extraire la première image de toutes vos vidéos sous la forme d’une image miniature. Vous devrez effectuer les étapes suivantes : 

1. Définir la recette, ou la règle de traitement de vos vidéos, « utiliser la première image de la vidéo comme miniature ». 
2. Pour chaque vidéo, vous devez indiquer au service : 
    1. où trouver cette vidéo ;  
    2. à quel emplacement écrire l’image miniature de sortie. 

Une **transformation** vous permet de créer la recette une seule fois (Étape 1) et d’envoyer des travaux à l’aide de cette recette (Étape 2).

## <a name="job-error-codes"></a>Codes d’erreur des tâches

Consultez [Codes d’erreur](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="paging"></a>Pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="configure-media-reserved-units"></a>Configurer des unités réservées Multimédia

Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des unité réservées Multimédia (MRU) 10 S3. Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).

Pour plus de détails, voir [Mise à l’échelle du traitement multimédia avec l’interface CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET](stream-files-tutorial-with-api.md)
- [Tutoriel : Analyser des vidéos avec Media Services v3 à l’aide de .NET](analyze-videos-tutorial-with-api.md)
