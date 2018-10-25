---
title: Créer des déclencheurs basés sur un événement dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer un déclencheur dans Azure Data Factory qui exécute un pipeline en réponse à un événement.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: douglasl
ms.openlocfilehash: 20ee69654a6b19365c9b7c46e1fa11e102168365
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309345"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Créer un déclencheur qui exécute un pipeline en réponse à un événement

Cet article décrit les déclencheurs basés sur un événement que vous pouvez créer dans vos pipelines Data Factory.

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d’intégration de données nécessitent souvent des clients Data Factory pour déclencher des pipelines basés sur des événements. Data Factory est désormais intégré à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), qui vous permet de déclencher des pipelines sur un événement.

Pour afficher une présentation de dix minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> L’intégration décrite dans cet article dépend [d’Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Vérifiez que votre abonnement est inscrit auprès du fournisseur de ressources Event Grid. Pour plus d’informations, consultez [Types et fournisseurs de ressources](../azure-resource-manager/resource-manager-supported-services.md#portal).

## <a name="data-factory-ui"></a>IU de la fabrique de données

### <a name="create-a-new-event-trigger"></a>Créer un déclencheur d’événement

Un événement type est l’arrivée d’un fichier, ou la suppression d’un fichier, dans votre compte de stockage Azure. Vous pouvez créer un déclencheur qui répond à cet événement dans votre pipeline Data Factory.

> [!NOTE]
> Cette intégration prend en charge uniquement des comptes de stockage de version 2 (Usage général).

![Créer un déclencheur d’événement](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Configurer le déclencheur d’événement

Avec les propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), vous pouvez spécifier les conteneurs, les dossiers et les noms d’objets blob pour lesquels vous souhaitez recevoir des événements. Vous pouvez utiliser divers modèles pour les deux propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), comme indiqué dans les exemples plus loin dans cet article. Au moins une de ces propriétés est requise.

![Configurer le déclencheur d’événement](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Sélectionner le type de déclencheur d’événement

Dès que le fichier arrive dans votre emplacement de stockage et que l’objet blob correspondant est créé, cet événement se déclenche et exécute votre pipeline Data Factory. Vous pouvez créer un déclencheur qui répond à un événement de création d’objet blob, un événement de suppression d’objet blob, ou les deux, dans vos pipelines Data Factory.

![Sélectionner le type de déclencheur en tant qu’événement](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapper des propriétés de déclencheur à des paramètres de pipeline

Quand un déclencheur d’événement est déclenché pour un objet blob spécifique, l’événement capture le chemin de dossier et le nom de fichier de l’objet blob dans les propriétés `@triggerBody().folderPath` et `@triggerBody().fileName`. Pour utiliser les valeurs de ces propriétés dans un pipeline, vous devez mapper les propriétés aux paramètres de pipeline. Après le mappage des propriétés aux paramètres, vous pouvez accéder aux valeurs capturées par le déclencheur à l’aide de l’expression `@pipeline().parameters.parameterName` tout au long du pipeline.

![Mappage des propriétés aux paramètres de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Par exemple, dans la capture d’écran précédente, le déclencheur est configuré pour se déclencher quand un chemin d’objet blob se terminant par `.csv` est créé dans le compte de stockage. Par conséquent, quand un objet blob avec l’extension `.csv` est créé n’importe où dans le compte de stockage, les propriétés `folderPath` et `fileName` capturent l’emplacement du nouvel objet blob. Par exemple, `@triggerBody().folderPath` a une valeur de type `/containername/foldername/nestedfoldername` et `@triggerBody().fileName` a une valeur de type `filename.csv`. Ces valeurs sont mappées dans l’exemple aux paramètres de pipeline `sourceFolder` et `sourceFile`. Vous pouvez les utiliser dans le pipeline comme `@pipeline().parameters.sourceFolder` et `@pipeline().parameters.sourceFile` respectivement.

## <a name="json-schema"></a>Schéma JSON

Le tableau suivant fournit une vue d’ensemble des éléments de schéma associés aux déclencheurs basés sur un événement :

| **Élément JSON** | **Description** | **Type** | **Valeurs autorisées** | **Obligatoire** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID de ressource Azure Resource Manager du compte de stockage. | Chaîne | ID d’Azure Resource Manager | Oui |
| **events** | Type des événements qui entraîne l’activation de ce déclencheur. | Tableau    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Oui, n’importe quelle combinaison. |
| **blobPathBeginsWith** | Le chemin d’accès de l’objet blob doit commencer par le modèle fourni pour activer le déclencheur. Par exemple, '/records/blobs/december/' n’activera le déclencheur que pour les objets blob dans le dossier « december » sous le conteneur « records ». | Chaîne   | | Au moins une de ces propriétés doit être fournie : blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Le chemin d’accès de l’objet blob doit se terminer par le modèle fourni pour activer le déclencheur. Par exemple, 'december/boxes.csv' n’activera le déclencheur que pour les objets blob nommés « boxes » dans un dossier « december ». | Chaîne   | | Au moins une de ces propriétés doit être fournie : blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exemples de déclencheurs basés sur un événement

Cette section fournit des exemples de paramètres de déclencheur basé sur un événement.

-   **Blob path begins with**('/containername/') : reçoit des événements pour tout objet blob dans le conteneur.
-   **Blob path begins with**('/containername/blobs/foldername') : reçoit des événements pour tout objet blob dans le conteneur containername et le dossier foldername. Vous pouvez également référencer un sous-dossier, par exemple, /containername/blobs/foldername/subfoldername/.
-   **Blob path begins with**('/containername/blobs/foldername/file.txt') : reçoit des événements pour un objet blob nommé file.txt dans le dossier foldername sous le conteneur containername.
-   **Blob path ends with**('file.txt') : reçoit des événements pour un objet blob nommé « file.txt » dans n’importe quel chemin d’accès.
-   **Blob path ends with**('/containername/blobs/file.txt') : reçoit des événements pour un objet blob nommé file.txt sous le conteneur containername.
-   **Blob path ends with**('/foldername/file.txt') : reçoit des événements pour un objet blob nommé « file.txt » dans le dossier « foldername » sous n’importe quel conteneur.

> [!NOTE]
> Vous devez inclure le segment `/blobs/` du chemin chaque fois que vous spécifiez conteneur et dossier, conteneur et fichier, ou conteneur, dossier et fichier.

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
