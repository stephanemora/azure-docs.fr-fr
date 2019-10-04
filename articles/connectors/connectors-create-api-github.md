---
title: Se connecter à GitHub - Azure Logic Apps
description: Surveiller les événements GitHub avec les API REST GitHub et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050894"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Se connecter à GitHub à partir d'Azure Logic Apps

GitHub est un service d’hébergement de référentiel Git basé sur le web. Outre d’autres fonctionnalités, il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git.

Pour bien commencer avec le connecteur GitHub, [commencez par créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub

Pour utiliser le connecteur GitHub dans une application logique, commencez par créer une *connexion*, puis spécifiez les détails des propriétés suivantes : 

| Propriété | Obligatoire | Description | 
| -------- | -------- | ----------- | 
| par jeton | OUI | Indiquez vos informations d’identification GitHub. |

Après avoir créé la connexion, vous pouvez exécuter les actions et rechercher les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence du connecteur](/connectors/github/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)