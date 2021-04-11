---
title: Tutoriel - Nettoyer des ressources | Azure
description: Dans ce tutoriel, vous allez apprendre à nettoyer les ressources Azure allouées lors de la création de l’application web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: cfb42b82943f03c3633ff69662ab841bf587ede4
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221855"
---
# <a name="tutorial-clean-up-resources"></a>Tutoriel : Nettoyer les ressources

Si vous avez effectué toutes les étapes de ce tutoriel en plusieurs parties, vous avez créé un service d’application, un plan d’hébergement de service d’application et un compte de stockage dans un groupe de ressources. Vous avez également créé une inscription d’application dans Azure Active Directory. Lorsque vous n’en avez plus besoin, supprimez ces ressources et l’inscription d’application afin de ne pas continuer à accumuler des frais.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Supprimer les ressources Azure créées durant le tutoriel.

## <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

Dans le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources** dans le menu du portail, puis sélectionnez le groupe de ressources qui contient votre service d’application et votre plan de service d’application.

Sélectionnez **Supprimer le groupe de ressources** pour supprimer le groupe de ressources et toutes les ressources.

:::image type="content" alt-text="Capture d’écran montrant la suppression du groupe de ressources." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

L’exécution de cette commande peut prendre plusieurs minutes.

## <a name="delete-the-app-registration"></a>Supprimer l’inscription d’application

Dans le menu du portail, sélectionnez **Azure Active Directory** > **Inscriptions d’applications**. Sélectionnez ensuite l’application que vous avez créée.
:::image type="content" alt-text="Capture d’écran montrant la sélection de l’inscription de l’application." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Dans la vue d’ensemble de l’inscription d’application, sélectionnez **Supprimer**.
:::image type="content" alt-text="Capture d’écran montrant la suppression de l’inscription de l’application." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
>
> * Supprimer les ressources Azure créées durant le tutoriel.

Découvrez comment connecter une [application .NET Core](tutorial-dotnetcore-sqldb-app.md), une [application Python](tutorial-python-postgresql-app.md), une [application Java](tutorial-java-spring-cosmosdb.md) ou une [application Node.js](tutorial-nodejs-mongodb-app.md) à une base de données.