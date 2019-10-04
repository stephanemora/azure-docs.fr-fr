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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177222"
---
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Dans la boîte de dialogue **Publier**, sélectionnez **Microsoft Azure App Service**, choisissez **Créer nouveau**, puis sélectionnez **Publier**.

   ![Choisir une cible de publication](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Dans la boîte de dialogue **Créer App Service**, utilisez les paramètres d’hébergement comme spécifiés dans le tableau sous l’image :

    ![Boîte de dialogue Créer App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. |
    | **Abonnement** | Choisissez votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nom du groupe de ressources où créer votre application de fonction. Choisissez **Nouveau** pour créer un groupe de ressources.|
    | **[Plan d’hébergement](../articles/app-service/overview-hosting-plans.md)** | Plan App Service | Un [plan App Service](../articles/app-service/overview-hosting-plans.md) spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application. Vous pouvez économiser de l’argent lors de l’hébergement de plusieurs applications en configurant les applications web pour partager un plan App Service. Les plans App Service définissent la région, la taille de l’instance, le compte de l’échelle et la référence SKU (gratuite, partagée, de base, standard et premium). Choisissez **Nouveau** pour créer un plan App Service. |

1. Cliquez sur **Créer** pour créer une WebJob (tâche web) et les ressources associées dans Azure avec ces paramètres et déployer votre code de projet.