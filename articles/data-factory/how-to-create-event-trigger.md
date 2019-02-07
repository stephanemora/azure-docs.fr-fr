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
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: douglasl
ms.openlocfilehash: fdbae70718ad62f96dda85e2d9574c7ec2d5ef23
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561091"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Créer un déclencheur qui exécute un pipeline en réponse à un événement

Cet article décrit les déclencheurs basés sur un événement que vous pouvez créer dans vos pipelines Data Factory.

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d’intégration de données nécessitent souvent des clients Data Factory pour déclencher des pipelines basés sur des événements. Data Factory est désormais intégré à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), qui vous permet de déclencher des pipelines sur un événement.

Pour afficher une présentation de dix minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> L’intégration décrite dans cet article dépend [d’Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Vérifiez que votre abonnement est inscrit auprès du fournisseur de ressources Event Grid. Pour plus d’informations, consultez [Types et fournisseurs de ressources](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

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
| **events** | Type des événements qui entraîne l’activation de ce déclencheur. | Tableau    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Oui, n’importe quelle combinaison de ces valeurs. |
| **blobPathBeginsWith** | Le chemin d’accès de l’objet blob doit commencer par le modèle fourni pour activer le déclencheur. Par exemple, `/records/blobs/december/` active uniquement le déclencheur pour les objets blob du dossier `december` sous le conteneur `records`. | Chaîne   | | Vous devez indiquer une valeur pour l’une de ces propriétés au moins : `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | Le chemin d’accès de l’objet blob doit se terminer par le modèle fourni pour activer le déclencheur. Par exemple, `december/boxes.csv` active uniquement le déclencheur pour les objets blob nommés `boxes` dans un dossier `december`. | Chaîne   | | Vous devez indiquer une valeur pour l’une de ces propriétés au moins : `blobPathBeginsWith` ou `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Exemples de déclencheurs basés sur un événement

Cette section fournit des exemples de paramètres de déclencheur basé sur un événement.

> [!IMPORTANT]
> Vous devez inclure le segment `/blobs/` du chemin, comme indiqué dans les exemples suivants, chaque fois que vous spécifiez conteneur et dossier, conteneur et fichier, ou conteneur, dossier et fichier.

| Propriété | Exemples | Description |
|---|---|---|
| **Blob path begins with** | `/containername/` | Reçoit les événements de tout objet blob du conteneur. |
| **Blob path begins with** | `/containername/blobs/foldername/` | Reçoit les événements de tout objet blob du conteneur `containername` et du dossier `foldername`. |
| **Blob path begins with** | `/containername/blobs/foldername/subfoldername/` | Vous pouvez également référencer un sous-dossier. |
| **Blob path begins with** | `/containername/blobs/foldername/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` dans le dossier `foldername`, sous le conteneur `containername`. |
| **Blob path ends with** | `file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` dans n’importe quel chemin d’accès. |
| **Blob path ends with** | `/containername/blobs/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` sous le conteneur `containername`. |
| **Blob path ends with** | `foldername/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt`, dans le dossier `foldername`, sous n’importe quel conteneur. |

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
