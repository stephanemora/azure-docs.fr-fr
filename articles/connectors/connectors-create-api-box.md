---
title: Se connecter à Box
description: Automatiser des tâches et des flux de travail qui créent et gèrent des fichiers dans Box à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666769"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Créer et gérer des fichiers dans Box à l’aide d’Azure Logic Apps

Cet article explique comment vous pouvez créer et gérer vos fichiers dans Box depuis une application logique avec le connecteur Box. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour gérer vos fichiers et d’autres actions, par exemple :

* Créer votre flux d'activité en fonction des données que vous obtenez de Box.

* Déclencher un flux de travail et des tâches automatisés quand un fichier est créé ou mis à jour.

* Exécuter une action qui copie ou supprime un fichier.

  Quand ces actions obtiennent une réponse, elles mettent la sortie à la disposition d’autres actions. 
  Par exemple, quand un fichier est changé dans Box, vous pouvez l’envoyer dans un e-mail à l’aide d’Office 365.

## <a name="prerequisites"></a>Conditions préalables requises

* Un [compte Box](https://www.box.com/home)

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Box. Pour démarrer votre application logique avec un déclencheur Box, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, voir la [page de référence du connecteur](/connectors/box/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)