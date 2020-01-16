---
title: Se connecter à Google Drive
description: Automatiser des workflows qui créent et gèrent des fichiers pour Google Drive à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666837"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Créer et gérer des fichiers pour Google Drive à l’aide d’Azure Logic Apps

Connectez-vous à Google Drive pour créer des fichiers, obtenir des lignes et plus encore. Avec Google Drive, vous pouvez effectuer les opérations suivantes : 

* Créer votre flux d’activité en fonction des données que vous obtenez pendant votre recherche. 
* Utiliser des actions pour rechercher des images, des actualités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez rechercher une vidéo, puis utiliser Twitter pour la publier dans un flux Twitter.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Créer la connexion à Google Drive

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

Après avoir créé la connexion, vous entrez les propriétés Google Drive, telles que le chemin du dossier ou le nom du fichier. 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont fournis par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence du connecteur](/connectors/googledrive/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](apis-list.md)
