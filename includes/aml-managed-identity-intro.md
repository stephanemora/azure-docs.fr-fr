---
title: Fichier include
description: Fichier include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147224"
---
 Les clusters de calcul Azure Machine Learning prennent également en charge les [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l'accès aux ressources Azure sans inclure d'informations d'identification dans votre code. Il existe deux types d’identités administrées :

* Une **identité managée attribuée par le système** est activée directement sur le cluster de calcul Azure Machine Learning. Le cycle de vie d'une identité attribuée par le système est directement lié au cluster de calcul. Si le cluster de calcul est supprimé, Azure efface automatiquement les informations d'identification et l'identité dans Azure AD.
* Une **identité managée attribuée par l'utilisateur** est une ressource Azure autonome fournie via le service Azure Managed Identity. Vous pouvez attribuer une identité managée attribuée par l'utilisateur à plusieurs ressources et la conserver aussi longtemps que vous le souhaitez.