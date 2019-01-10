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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742777"
---
# <a name="transforms-and-jobs"></a>Transformations et travaux
 
Utilisez des [transformations](https://docs.microsoft.com/rest/api/media/transforms) pour configurer des tâches courantes de codage ou d'analyse des vidéos. Chaque **transformation** décrit une recette, ou un flux de travail de tâches pour le traitement de vos fichiers vidéo ou audio. 

Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Azure Media Services pour appliquer la **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations comme l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l'emplacement de votre vidéo d'entrée en utilisant : des URL HTTPS, des URL de SAP, ou des [ressources Media Services](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Flux de travail classique

1. Créer une transformation 
2. Envoyer des travaux sous cette transformation 
3. Répertorier les transformations 
4. Supprimer une transformation si vous ne prévoyez pas de l’utiliser ultérieurement. 

Supposons que vous souhaitez extraire la première image de toutes vos vidéos sous la forme d’une image miniature. Vous devrez effectuer les étapes suivantes : 

1. Définir la recette, ou la règle de traitement de vos vidéos, « utiliser la première image de la vidéo comme miniature ». 
2. Pour chaque vidéo, vous devez indiquer au service : 
    1. où trouver cette vidéo ;  
    2. à quel emplacement écrire l’image miniature de sortie. 

Une **transformation** vous permet de créer la recette une seule fois (Étape 1) et d’envoyer des travaux à l’aide de cette recette (Étape 2).

## <a name="transforms"></a>Transformations

Vous pouvez créer des transformations dans votre compte Media Services à l’aide de l’API v3 directement, ou à l’aide des kits de développement logiciel (SDK) publiés. L’API Azure Media Services v3 est pilotée par Azure Resource Manager. Vous pouvez donc utiliser des modèles Resource Manager pour créer et déployer des transformations dans votre compte Media Services. Le contrôle d’accès en fonction du rôle peut être utilisé pour bloquer l’accès aux transformations.

### <a name="transform-definition"></a>Définition de la transformation

Le tableau suivant présente les propriétés de la transformation ainsi que leurs définitions.

|NOM|Description|
|---|---|
|ID|ID de ressource complet pour la ressource.|
|Nom|Nom de la ressource.|
|properties.created |Date et heure UTC de création de la transformation, au format « AAAA-MM-JJThh:mm:ssZ ».|
|properties.description |Description détaillée facultative de la transformation.|
|properties.lastModified |Date et heure UTC de dernière modification de la transformation, au format « AAAA-MM-JJThh:mm:ssZ ».|
|properties.outputs |Tableau d’un ou plusieurs TransformOutputs que la transformation doit générer.|
|Type|Type de la ressource.|

Pour obtenir la définition complète, consultez [Transformations](https://docs.microsoft.com/rest/api/media/transforms).

Comme expliqué ci-dessus, une transformation vous permet de créer une recette ou règle pour le traitement de vos vidéos. Une transformation unique peut appliquer plusieurs règles. Par exemple, une transformation peut spécifier que chaque vidéo est encodée dans un fichier MP4 à une vitesse de transmission donnée, et qu’une image miniature est générée à partir de la première image de la vidéo. Vous devez ajouter une entrée TransformOutput pour chaque règle que vous souhaitez inclure dans votre transformation.

> [!NOTE]
> Bien que la définition de transformations prenne en charge une opération de mise à jour, vous devez vous assurer que tous les travaux en cours sont terminés avant d’apporter une modification. En règle générale, vous mettez à jour une transformation existante pour modifier la description, ou pour modifier les priorités des TransformOutputs sous-jacentes. Si vous souhaitez réécrire la recette, vous devez créer une nouvelle transformation.

## <a name="jobs"></a>Tâches

Lorsqu’une transformation est créée, vous pouvez envoyer des travaux à l’aide des API Media Services ou de l’un des kits de développement logiciel (SDK) publiés. La progression et l’état des travaux peuvent être obtenus en surveillant les événements avec Event Grid. Pour plus d’informations, consultez la section relative à la [surveillance des événements à l’aide d’Event Grid](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Définition des travaux

Le tableau suivant présente les propriétés des travaux ainsi que leurs définitions.

|NOM|Description|
|---|---|
|id|ID de ressource complet pour la ressource.|
|Nom   |Nom de la ressource.|
|properties.correlationData |Le client a fourni de données de corrélation (fixes) qui sont retournées en tant que partie du travail et notifications de modification de l’état JobOutput. Pour plus d’informations, consultez la section relative aux [schémas d’événements](media-services-event-schemas.md)<br/><br/>La propriété peut également être utilisée pour l’utilisation de plusieurs locataires de Media Services. Vous pouvez insérer des ID de locataire dans les travaux. |
|properties.created |Date et heure UTC de création du travail, au format « AAAA-MM-JJThh:mm:ssZ ».|
|properties.description |Description facultative du travail fournie par le client.|
|properties.input|Entrées du travail.|
|properties.lastModified    |Date et heure UTC de dernière modification du travail, au format « AAAA-MM-JJThh:mm:ssZ ».|
|properties.outputs|Sorties du travail.|
|properties.priority    |Priorité avec laquelle le travail doit être traité. Les travaux de priorité plus élevée sont traités avant ceux de priorité plus faible. Si aucune valeur n’est définie, la priorité par défaut est « normale ».|
|properties.state   |État actuel du travail.|
|Type   |Type de la ressource.|

Pour obtenir la définition complète, consultez [Travaux](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> Bien que la définition de travaux prenne en charge une opération de mise à jour, les seules propriétés pouvant être modifiées une fois que le travail est envoyé sont la description et la priorité. En outre, une modification de la priorité ne s’applique que si le travail est toujours dans un état de file d’attente. Si le traitement du travail a commencé, ou est terminé, la modification de la priorité n’a aucun effet.

### <a name="pagination"></a>Pagination

La pagination des travaux est prise en charge dans Media Services v3.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des travaux sont créés ou supprimés pendant la pagination dans la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

L’exemple C# suivant montre comment énumérer tous les travaux dans le compte.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Pour obtenir des exemples REST, consultez [Travaux - Liste](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers vidéo en continu](stream-files-dotnet-quickstart.md)
