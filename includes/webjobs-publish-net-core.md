---
title: Fichier Include
description: Fichier Include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751289"
---
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Dans le **publier** boîte de dialogue, sélectionnez **Microsoft Azure App Service**, choisissez **créer un nouveau**, puis sélectionnez **publier**.

   ![Choix de publication cible](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Dans le **créer App Service** boîte de dialogue, utilisez les paramètres d’hébergement comme indiqués dans le tableau ci-dessous l’image :

    ![Boîte de dialogue Créer App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. |
    | **Abonnement** | Choisissez votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nom du groupe de ressources où créer votre application de fonction. Choisissez **Nouveau** pour créer un groupe de ressources.|
    | **[Plan d’hébergement](../articles/app-service/overview-hosting-plans.md)** | Plan App Service | Un [plan App Service](../articles/app-service/overview-hosting-plans.md) spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application. Vous pouvez économiser de l’argent lors de l’hébergement de plusieurs applications en configurant les applications web pour partager un plan App Service. Plans App Service définissent la région de taille d’instance, comptage et référence (SKU) (gratuit, partagé, Basic, Standard ou Premium). Choisissez **New** pour créer un nouveau plan App Service. |

1. Cliquez sur **créer** pour créer une tâche Web et les ressources associées dans Azure avec ces paramètres et de déployer votre code de projet.