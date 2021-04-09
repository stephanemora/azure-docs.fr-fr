---
title: Créer un plan Premium Azure Functions dans le portail
description: Découvrez comment utiliser le portail Azure pour créer une application de fonction qui s’exécute dans le plan Premium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676584"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Créer une application de fonction du plan Premium dans le portail Azure

Azure Functions propose un plan Premium évolutif qui offre une connectivité réseau virtuelle, aucun démarrage à froid et du matériel de qualité supérieure. Pour plus d’informations, consultez [Plan Premium Azure Functions](functions-premium-plan.md). 

Dans cet article, vous allez apprendre à utiliser le portail Azure pour créer une application de fonction dans un plan Premium. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. Une application de fonctions vous permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

À ce stade, vous pouvez créer des fonctions dans la nouvelle application de fonction. Ces fonctions peuvent tirer parti des avantages du [plan Premium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une fonction déclenchée par HTTP] (./functions-get-started.md
