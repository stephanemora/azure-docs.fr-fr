---
title: Se connecter à GitHub - Azure Logic Apps | Microsoft Docs
description: Surveiller les événements GitHub avec les API REST GitHub et Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462565"
---
# <a name="connect-to-github"></a>Se connecter à GitHub

GitHub est un service d’hébergement de référentiel Git basé sur le web. Outre d’autres fonctionnalités, il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git.

Pour bien commencer avec le connecteur GitHub, [commencez par créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub

Pour utiliser le connecteur GitHub dans une application logique, commencez par créer une *connexion*, puis spécifiez les détails des propriétés suivantes : 

| Propriété | Obligatoire | Description | 
| -------- | -------- | ----------- | 
| par jeton | Oui | Indiquez vos informations d’identification GitHub. |

Après avoir créé la connexion, vous pouvez exécuter les actions et rechercher les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Passez en revue les [détails des connecteurs](/connectors/github/) pour connaître les déclencheurs et actions définis dans Swagger ainsi que les éventuelles limites.

## <a name="find-more-connectors"></a>Rechercher d’autres connecteurs

* Passez en revue la [liste des connecteurs](apis-list.md).