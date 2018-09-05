---
title: Se connecter à Outlook.com - Azure Logic Apps | Microsoft Docs
description: Gérer les e-mails, les calendriers et les contacts avec les API REST Outlook.com et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8030ab9d317c1deefaf441008b9022c4a26bb17c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746160"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Gérer les e-mails, les calendriers et les contacts dans Outlook.com avec Azure Logic Apps

Cet article explique comment vous pouvez créer et gérer votre compte Outlook.com dans une application logique avec le connecteur Box. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour votre compte Outlook.com, par exemple :

* Envoyer un e-mail. 
* Planifier des réunions.
* Rechercher des contacts. 

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un [compte Outlook.com](https://outlook.live.com/owa/)

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Outlook.com. Pour démarrer votre application logique avec un déclencheur Outlook, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Connexion à Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que les déclencheurs, actions et limites, comme décrit dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/outlook/). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)