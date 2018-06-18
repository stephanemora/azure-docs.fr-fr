---
title: SendGrid | Microsoft Docs
description: Créez des applications logiques avec Azure App Service. Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 0b34a76ecaf4997cbf66c3d026cd770aa8aa080d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295837"
---
# <a name="get-started-with-the-sendgrid-connector"></a>Prise en main du connecteur SendGrid
Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sendgrid"></a>Créer une connexion à SendGrid
Pour créer des applications logiques avec SendGrid, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes : 

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| ApiKey |OUI |Fournir votre clé d’API SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/sendgrid/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).