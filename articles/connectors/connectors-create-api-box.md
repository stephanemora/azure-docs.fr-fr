---
title: Se connecter à Box - Azure Logic Apps
description: Créer et gérer des fichiers avec les API REST Box et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 5fbc64194182c41a70eb27a4049234973c39fffe
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050931"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Créer et gérer des fichiers dans Box avec Azure Logic Apps

Cet article explique comment vous pouvez créer et gérer vos fichiers dans Box depuis une application logique avec le connecteur Box. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour gérer vos fichiers et d’autres actions, par exemple :

* Créer votre flux d'activité en fonction des données que vous obtenez de Box.

* Déclencher un flux de travail et des tâches automatisés quand un fichier est créé ou mis à jour.

* Exécuter une action qui copie ou supprime un fichier.

  Quand ces actions obtiennent une réponse, elles mettent la sortie à la disposition d’autres actions. 
  Par exemple, quand un fichier est changé dans Box, vous pouvez l’envoyer dans un e-mail à l’aide d’Office 365.

## <a name="prerequisites"></a>Prérequis

* Un [compte Box](https://www.box.com/home)

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Box. Pour démarrer votre application logique avec un déclencheur Box, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/box/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)