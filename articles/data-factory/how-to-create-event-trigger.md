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
ms.date: 06/27/2018
ms.author: douglasl
ms.openlocfilehash: a9c15b239ee0bd0dde0b1f11691565b2676e3d07
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062119"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Créer un déclencheur qui exécute un pipeline en réponse à un événement

Cet article décrit les déclencheurs basés sur un événement que vous pouvez créer dans vos pipelines Data Factory.

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d’intégration de données nécessitent souvent des clients Data Factory pour déclencher des pipelines basés sur des événements. Data Factory est désormais intégré à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), qui vous permet de déclencher des pipelines sur un événement.

## <a name="data-factory-ui"></a>IU de la fabrique de données

### <a name="create-a-new-event-trigger"></a>Créer un déclencheur d’événement

Un événement type est l’arrivée d’un fichier, ou la suppression d’un fichier, dans votre compte de stockage Azure. Vous pouvez créer un déclencheur qui répond à cet événement dans votre pipeline Data Factory.

> [!NOTE]
> Cette intégration prend en charge uniquement des comptes de stockage de version 2 (Usage général).

![Créer un déclencheur d’événement](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Sélectionner le type de déclencheur d’événement

Dès que le fichier arrive dans votre emplacement de stockage et que l’objet blob correspondant est créé, cet événement se déclenche et exécute votre pipeline Data Factory. Vous pouvez créer un déclencheur qui répond à un événement de création d’objet blob, un événement de suppression d’objet blob, ou les deux, dans vos pipelines Data Factory.

![Sélectionner le type de déclencheur en tant qu’événement](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Configurer le déclencheur d’événement

Avec les propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), vous pouvez spécifier les conteneurs, les dossiers et les noms d’objets blob pour lesquels vous souhaitez recevoir des événements. Vous pouvez utiliser divers modèles pour les deux propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), comme indiqué dans les exemples plus loin dans cet article. Au moins une de ces propriétés est requise.

![Configurer le déclencheur d’événement](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>Schéma JSON

Le tableau suivant fournit une vue d’ensemble des éléments de schéma associés aux déclencheurs basés sur un événement :

| **Élément JSON** | **Description** | **Type** | **Valeurs autorisées** | **Obligatoire** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID de ressource Azure Resource Manager du compte de stockage. | Chaîne | ID d’Azure Resource Manager | OUI |
| **events** | Type des événements qui entraîne l’activation de ce déclencheur. | Tableau    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Oui, n’importe quelle combinaison. |
| **blobPathBeginsWith** | Le chemin d’accès de l’objet blob doit commencer par le modèle fourni pour activer le déclencheur. Par exemple, '/records/blobs/december/' n’activera le déclencheur que pour les objets blob dans le dossier « december » sous le conteneur « records ». | Chaîne   | | Au moins une de ces propriétés doit être fournie : blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Le chemin d’accès de l’objet blob doit se terminer par le modèle fourni pour activer le déclencheur. Par exemple, 'december/boxes.csv' n’activera le déclencheur que pour les objets blob nommés « boxes » dans un dossier « december ». | Chaîne   | | Au moins une de ces propriétés doit être fournie : blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exemples de déclencheurs basés sur un événement

Cette section fournit des exemples de paramètres de déclencheur basé sur un événement.

-   **Blob path begins with**('/containername/') : reçoit des événements pour tout objet blob dans le conteneur.
-   **Blob path begins with**('/containername/blobs/foldername') : reçoit des événements pour tout objet blob dans le conteneur containername et le dossier foldername.
-   **Blob path begins with**('/containername/blobs/foldername/file.txt') : reçoit des événements pour un objet blob nommé file.txt dans le dossier foldername sous le conteneur containername.
-   **Blob path ends with**('file.txt') : reçoit des événements pour un objet blob nommé file.txt dans n’importe quel chemin.
-   **Blob path ends with**('/containername/blobs/file.txt') : reçoit des événements pour un objet blob nommé file.txt sous le conteneur containername.
-   **Blob path ends with**('/foldername/file.txt') : reçoit des événements pour un objet blob nommé file.txt dans le dossier foldername sous n’importe quel conteneur.

> [!NOTE]
> Vous devez inclure le segment `/blobs/` du chemin chaque fois que vous spécifiez conteneur et dossier, conteneur et fichier, ou conteneur, dossier et fichier.

## <a name="using-blob-events-trigger-properties"></a>Utilisation de propriétés de déclencheur d’événements blob

Lors de l’activation d’un déclencheur d’événements blob, deux variables sont mises à disposition de votre pipeline : *folderPath* et *fileName*. Pour accéder à ces variables, utilisez les expressions `@triggerBody().fileName` ou `@triggerBody().folderPath`.

Par exemple, considérez un déclencheur configuré pour se déclencher quand un objet blob est créé avec `.csv` comme valeur de `blobPathEndsWith`. Quand un fichier .csv est déposé dans le compte de stockage, *folderPath* et *fileName* décrivent l’emplacement du fichier .csv. Par exemple, *folderPath* a la valeur `/containername/foldername/nestedfoldername` et *fileName* a la valeur `filename.csv`.

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
