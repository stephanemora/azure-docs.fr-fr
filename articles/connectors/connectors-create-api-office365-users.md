---
title: Se connecter à des utilisateurs Office 365
description: Automatiser des tâches et des flux de travail qui obtiennent et gèrent des profils dans les profils Office 365 Users à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666854"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Obtenir et gérer des profils dans Office 365 Users à l’aide d’Azure Logic Apps

Connexion à Office 365 Users pour obtenir des profils, rechercher des utilisateurs, et plus encore. Avec Office 365 Users, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Users. 
* Utilisez des actions qui permettent d’obtenir les collaborateurs, le profil utilisateur d’un responsable, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Vous pouvez, par exemple, obtenir les collaborateurs d’une personne, puis prendre ces informations et mettre à jour une base de données SQL Azure. 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Créer une connexion à Office 365 Users

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Users afin qu’Azure Logic Apps puisse se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Users, notamment l’ID client. La section **Informations de référence sur l’API REST** de cet article décrit ces propriétés.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/officeusers/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](apis-list.md)