---
title: Vidéo Se connecter à Office 365 – Azure Logic Apps | Microsoft Docs
description: Gérer des vidéos avec les API REST de vidéo d’Office 365 et Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 44152101f1a2be6268d4e27430fdf60ef0131fae
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295776"
---
# <a name="get-started-with-the-office365-video-connector"></a>Prise en main du connecteur Office 365 Video
Connexion à Office 365 Video pour obtenir des informations sur une vidéo Office 365, la liste des vidéos, et bien plus encore. Avec Office 365 Video, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Video. 
* Utiliser des actions pour vérifier l’état du portail vidéo, obtenir une liste de toutes les vidéos dans un canal, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Vous pouvez, par exemple, utiliser le connecteur Bing Search pour rechercher des vidéos Office 365, puis utiliser le connecteur Office 365 Video pour obtenir des informations sur ces vidéos. Si la vidéo répond à vos besoins, vous pouvez la publier sur Facebook. 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Créer une connexion au connecteur Office 365 Video
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Video et autoriser les applications logiques à se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Video, comme le nom du client ou l’ID du canal. 


## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).