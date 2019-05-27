---
title: Fichier Include
description: Fichier Include
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131556"
---
1. Accédez au [portail Azure](https://portal.azure.com).

1. Sélectionnez **+ créer une ressource** sur le côté gauche, puis choisissez **Function app**.

1. Pour **plan d’hébergement**, choisissez **plan App Service**, puis sélectionnez **plan App Service/emplacement**.

    ![Créer une application de fonction](./media/functions-premium-create/create-function-app-resource.png)

1. Sélectionnez **créer**, type une **plan App Service** nom, choisissez un **emplacement** dans un [région](https://azure.microsoft.com/regions/) près de chez vous ou près des autres services vos fonctions accéder à, puis sélectionnez **niveau tarifaire**.

    ![Créer un plan App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Choisissez le **EP1** (élastique Premium) planifier, puis sélectionnez **appliquer**.

    ![Sélectionnez le plan premium](./media/functions-premium-create/hosting-plan.png) 

1. Sélectionnez **OK** pour créer le plan, puis utilisez les paramètres d’application de fonction restants comme spécifié dans le tableau ci-dessous l’image. 

    ![Plan de service d’application finie](./media/functions-premium-create/create-function-app.png)  

    | Paramètre      | Valeur suggérée  | Description                                         |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction. |
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre Function App. Vous pouvez également utiliser la valeur suggérée. |
    | **OS** | Windows | Linux n’est pas pris en charge sur le plan Premium. |
    | **Pile d’exécution** | Langage préféré | Choisissez un runtime qui prend en charge votre langage de programmation de fonction favori. Choisissez **.NET** pour les fonctions C# et F#. Uniquement les langues prises en charge sur choisi **système d’exploitation** sont affichés. |
    | **[Stockage](../articles/storage/common/storage-quickstart-create-account.md)** |  Nom globalement unique |  Créez un compte de stockage utilisé par votre application de fonction. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Default | Crée une ressource Application Insights avec le même *nom de l’application* dans la région prise en charge la plus proche. En développant ce paramètre, vous pouvez changer le **Nouveau nom de ressource** ou choisir un autre **Emplacement** dans une [Zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) où vous voulez stocker vos données. |

1. Une fois que vos paramètres sont validés, sélectionnez **créer**.

1. Cliquez sur l’icône Notification en haut à droite du portail pour voir le message **Le déploiement a été effectué**.

    ![Définir de nouveaux paramètres d’application de fonction](./media/functions-premium-create/function-app-create-notification.png)

1. Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application de fonction. Vous pouvez également sélectionner **Épingler au tableau de bord**. L’épinglage permet de revenir plus facilement à cette ressource d’application de fonction à partir de votre tableau de bord.