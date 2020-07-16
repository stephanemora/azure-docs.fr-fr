---
title: Afficher les détails de l’authentification d’Azure Maps
description: Afficher le Portail Azure pour afficher les détails de l’authentification d’Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988367"
---
Vous pouvez afficher les détails de l’authentification du compte Azure Maps dans le Portail Azure. Dans votre compte, dans le menu **Paramètres**, sélectionnez **Authentification**.

![Informations sur l’authentification](../media/how-to-manage-authentication/how-to-view-auth.png)

Une fois qu’un compte Azure Maps a été créé, la valeur `x-ms-client-id` Azure Maps est présente dans la page des détails de l’authentification du Portail Azure. Cette valeur représente le compte qui sera utilisé pour les requêtes d’API REST. Elle doit être stockée dans la configuration de l’application et récupérée avant d’effectuer des requêtes HTTP lors de l’utilisation de l’authentification Azure AD avec Azure Maps.
