---
title: Se connecter à des utilisateurs Office 365
description: Gérer les profils utilisateur avec les API REST Office 365 Users et Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: addb64a9b43c51af8363caa6f0fb3261a618e893
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789515"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Prise en main du connecteur Office 365 Users
Connexion à Office 365 Users pour obtenir des profils, rechercher des utilisateurs, et plus encore. Avec Office 365 Users, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Users. 
* Utilisez des actions qui permettent d’obtenir les collaborateurs, le profil utilisateur d’un responsable, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Vous pouvez, par exemple, obtenir les collaborateurs d’une personne, puis prendre ces informations et mettre à jour une base de données SQL Azure. 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Créer une connexion à Office 365 Users
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Users et autoriser les applications logiques à se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Users, notamment l’ID client. La section **Informations de référence sur l’API REST** de cet article décrit ces propriétés.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/officeusers/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).