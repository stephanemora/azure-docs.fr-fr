---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 07/17/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 75ff9ec8cdf4cd2747ab00f2c23de509607a5429
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40100104"
---
1. Cliquez sur le bouton **Nouveau** en haut à gauche du portail Azure, puis sélectionnez **Calcul** > **Application de fonction**. 

    ![Créer une application de fonction sur le Portail Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Utilisez les paramètres d’application de fonction comme indiqué dans le tableau sous l’image.

    ![Définir de nouveaux paramètres d’application de fonction](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction. | 
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre Function App. | 
    | **SE** | Windows | L’hébergement serverless est disponible uniquement avec une exécution sur Windows. Pour l’hébergement sur Linux, consultez [Créer votre première fonction exécutée sur Linux à l’aide d’Azure CLI](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Plan d’hébergement](../articles/azure-functions/functions-scale.md)** | Plan de consommation | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Dans la valeur par défaut **Plan de consommation**, les ressources sont ajoutées dynamiquement comme requis par vos fonctions. Avec cet hébergement [serverless](https://azure.microsoft.com/overview/serverless-computing/), vous payez uniquement pour la durée d’exécution de vos fonctions. Si vous exécutez dans un plan App Service, vous devez gérer la [mise à l’échelle de votre application de fonction](../articles/azure-functions/functions-scale.md).  |
    | **Lieu** | Europe Ouest | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |
    | **[Compte de stockage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Nom globalement unique |  Nom du nouveau compte de stockage utilisé par votre Function App. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](../articles/azure-functions/functions-scale.md#storage-account-requirements). |

3. Sélectionnez **Créer** pour configurer et déployer l’application de fonction.

4. Cliquez sur l’icône Notification en haut à droite du portail pour voir le message **Le déploiement a été effectué**. 

    ![Définir de nouveaux paramètres d’application de fonction](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application de fonction.

> [!TIP]
> Si vous avez des difficultés à trouver vos applications de fonction dans le portail, essayez [d’ajouter des applications de fonction à vos favoris dans le portail Azure](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite).   
