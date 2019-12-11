---
title: Se connecter à Facebook
description: Gérer votre chronologie et la page avec les API REST de Facebook et Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 1aa936f902dc17c9a401959c19824f6c581547b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789853"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gérer votre page et votre chronologie Facebook à l’aide d'Azure Logic Apps

Connectez-vous à Facebook et publiez dans un journal, obtenez un flux de page et bien plus encore. Avec Facebook, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Facebook. 
* Utiliser un déclencheur quand une publication est reçue.
* Utiliser des actions pour publier dans votre journal, obtenir un flux de page et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand il y a une nouvelle publication dans votre journal, vous pouvez la transférer vers votre flux Twitter. 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Créer une connexion à Facebook

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Facebook.

1. Connectez-vous à votre compte Facebook.

2. Sélectionnez **Autoriser**et permettez à vos applications logiques de se connecter à votre compte Facebook et de l’utiliser. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/facebook/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)