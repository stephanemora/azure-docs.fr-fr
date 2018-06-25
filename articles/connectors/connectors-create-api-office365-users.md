---
title: Se connecter à Office 365 Outlook Users - Azure Logic Apps | Microsoft Docs
description: Gérer les profils utilisateur avec les API REST Office 365 Users et Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: f030ac07dc1615c435c1a110836d7a03ab8a8546
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295606"
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