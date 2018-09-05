---
title: Se connecter à Box - Azure Logic Apps | Microsoft Docs
description: Créer et gérer des fichiers avec les API REST Box et Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: b5c8c18c6d02710646560f29d4bc7b5784f730a2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746875"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Créer et gérer des fichiers dans Box avec Azure Logic Apps

Cet article explique comment vous pouvez créer et gérer vos fichiers dans Box depuis une application logique avec le connecteur Box. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour gérer vos fichiers et d’autres actions, par exemple :

* Créer votre flux d'activité en fonction des données que vous obtenez de Box. 

* Déclencher un flux de travail et des tâches automatisés quand un fichier est créé ou mis à jour.

* Exécuter des actions qui copient un fichier, suppriment un fichier et bien plus encore. 

  Quand ces actions obtiennent une réponse, elles mettent la sortie à la disposition d’autres actions. 
  Par exemple, quand un fichier est changé dans Box, vous pouvez l’envoyer dans un e-mail à l’aide d’Office 365.

## <a name="prerequisites"></a>Prérequis

* Un [compte Box](https://www.box.com/home)

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Box. Pour démarrer votre application logique avec un déclencheur Box, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que les déclencheurs, actions et limites, comme décrit dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/box/). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)