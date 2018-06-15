---
title: Connecteur Outlook.com dans Azure Logic Apps | Documents Microsoft
description: Créez des applications logiques avec Azure App Service. Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 1338b7ce8f72a57cb65f9389e630fd7ebd0417b1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295582"
---
# <a name="get-started-with-the-outlookcom-connector"></a>Prise en main du connecteur Outlook.com
Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-outlookcom"></a>Créer une connexion à Outlook.com
Pour créer des applications logiques avec Outlook.com, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| par jeton |OUI |Fournir des informations d’identification Outlook.com |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/outlook/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).