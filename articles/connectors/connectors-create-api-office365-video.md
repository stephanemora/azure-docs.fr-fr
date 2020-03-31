---
title: Se connecter à une vidéo Office 365
description: Automatiser des tâches et des flux de travail qui gèrent des vidéos dans Office 365 Video à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665783"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Gérer des vidéos dans Office 365 Video à l’aide d’Azure Logic Apps

Connexion à Office 365 Video pour obtenir des informations sur une vidéo Office 365, la liste des vidéos, et bien plus encore. Avec Office 365 Video, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Video. 

* Utiliser des actions pour vérifier l’état du portail vidéo, obtenir une liste de toutes les vidéos dans un canal, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. 

Vous pouvez, par exemple, utiliser le connecteur Bing Search pour rechercher des vidéos Office 365, puis utiliser le connecteur Office 365 Video pour obtenir des informations sur ces vidéos. Si la vidéo répond à vos besoins, vous pouvez la publier sur Facebook.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Se connecter à une Office 365 Video

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Video et autoriser les applications logiques à se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Après avoir créé la connexion, vous entrez les propriétés Office 365 Video, comme le nom du client ou l’ID du canal. 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)