---
title: Vue d’ensemble d’Azure Functions
description: Découvrez comment utiliser les Azure Functions pour optimiser les charges de travail asynchrones en quelques minutes.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 1cd7b3db92de339b7614a4271f9e6793cdf8f2ad
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292891"
---
# <a name="an-introduction-to-azure-functions"></a>Présentation d’Azure Functions

Azure Functions vous permet d’exécuter de petits morceaux de code (appelés « fonctions ») sans vous préoccuper de l’infrastructure de l’application. Avec Azure Functions, l’infrastructure cloud fournit tous les serveurs à jour dont vous avez besoin pour que votre application continue de s’exécuter à grande échelle.

Une fonction est « déclenchée » par un type d’événement spécifique. [Les déclencheurs pris en charge](./functions-triggers-bindings.md) incluent la réponse à des modifications apportées aux données, la réponse à des messages, l’exécution selon une planification ou le résultat d’une requête HTTP.

Même si vous pouvez toujours programmer directement pour une multitude de services, l’intégration à d’autres services est facilitée par l’utilisation de liaisons. Les liaisons vous offrent [un accès déclaratif à une large gamme de services Azure et de tiers](./functions-triggers-bindings.md).

## <a name="features"></a>Fonctionnalités

Voici quelques fonctionnalités clés d’Azure Functions :

- **Applications serverless** : Functions vous permet de développer des applications [serverless](https://azure.microsoft.com/solutions/serverless/) sur Microsoft Azure.

- **Choix du langage** : Écrivez des fonctions en choisissant parmi les langages [C#, Java, JavaScript, Python et PowerShell](supported-languages.md).

- **Modèle tarifaire avec paiement à l’utilisation** : Payez seulement pour le temps passé à exécuter votre code. Reportez-vous à l’option de plan d’hébergement de consommation dans la [section sur les prix](#pricing).  

- **Apportez vos propres dépendances** : Functions prend en charge NuGet et NPM, ce qui vous donne accès à vos bibliothèques favorites.

- **Sécurité intégrée** : Protégez les fonctions déclenchées par HTTP avec fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Compte Microsoft.

- **Intégration simplifiée** : Effectuez facilement une intégration aux services Azure et aux offres SaaS (Software-as-a-Service).

- **Développement flexible** : Configurez une intégration continue et déployez votre code via [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) et d’autres [outils de développement pris en charge](../app-service/deploy-local-git.md).

- **Architecture serverless avec état** : Orchestrez les applications serverless avec [Durable Functions](durable/durable-functions-overview.md).

- **Open source** : Le runtime Azure Functions est open source et disponible sur [GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Que puis-je faire avec Azure Functions ?

Functions est une solution idéale pour le traitement des données en bloc, l’intégration de systèmes, l’utilisation de l’Internet des objets (IoT) et la création de microservices et d’API simples.

Une série de modèles est disponible pour vous permettre de commencer avec des scénarios clés, notamment :

- **HTTP** : Exécuter du code basé sur des [requêtes HTTP](functions-create-first-azure-function.md)

- **Minuteur** : Planifier l’[exécution du code à des moments prédéfinis](./functions-create-scheduled-function.md)

- **Azure Cosmos DB** : Traiter des [documents Azure Cosmos DB nouveaux et modifiés](./functions-create-cosmos-db-triggered-function.md)

- **Stockage Blob** : Traiter des [objets blob du Stockage Azure nouveaux et modifiés](./functions-create-storage-blob-triggered-function.md)

- **Stockage File d’attente** : Répondre à des [messages d’une file d’attente de Stockage Azure](./functions-create-storage-queue-triggered-function.md)

- **Event Grid** : Répondre à des [événements Azure Event Grid via des abonnements et des filtres](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event Hub** : Répondre à [de gros volumes d’événements Azure Event Hub](./functions-bindings-event-hubs.md)

- **File d’attente Service Bus** : Se connecter à d’autres services Azure ou à des services locaux en [répondant à des messages de file d’attente Service Bus](./functions-bindings-service-bus.md)

- **Rubrique Service Bus** : Se connecter à d’autres services Azure ou à des services locaux en [répondant à des messages de rubrique Service Bus](./functions-bindings-service-bus.md)

## <a name="pricing"></a>Combien coûte Azure Functions ?

Azure Functions propose trois sortes de plans tarifaires. Choisissez celui qui répond le mieux à vos besoins :

- **Plan Consommation** : Azure fournit toutes les ressources de calcul nécessaires. Vous n’avez pas à vous préoccuper de la gestion des ressources et seul le temps pendant lequel votre code s’exécute est facturé.

- **Plan Premium** : Vous spécifiez un nombre d’instances préparées à l’utilisation qui sont toujours en ligne et prêtes à répondre immédiatement. Lorsque votre fonction est exécutée, Azure fournit toutes les ressources de calcul supplémentaires nécessaires. Vous payez les instances chauffées au préalable qui s’exécutent en continu ainsi que toutes les instances supplémentaires dont vous avez besoin à mesure qu’Azure effectue le scale-in/scale-out de votre application.

- **Plan App Service** : Exécutez vos fonctions exactement comme vos applications web. Si vous utilisez App Service pour vos autres applications, vos fonctions peuvent s’exécuter sur le même plan, sans coûts supplémentaires.

Pour plus d’informations sur les plans d’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). Vous trouverez tout le détail des prix dans la [page Tarification de Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première fonction Azure](functions-create-first-function-vs-code.md)  
  Commencez avec [Visual Studio Code](functions-create-first-function-vs-code.md), la [ligne de commande](functions-create-first-azure-function-azure-cli.md) ou utilisez le [portail Azure](functions-create-first-azure-function.md).

- [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  fournit des informations techniques supplémentaires sur l’exécution d’Azure Functions, ainsi qu’une référence pour le codage de fonctions et la définition des déclencheurs et des liaisons.

- [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  Présente les plans de service disponibles pour Azure Functions, dont le plan d’hébergement de consommation, et explique comment choisir le plan adapté à vos besoins.

- [En savoir plus sur Azure App Service](../app-service/overview.md)  
  Azure Functions s’appuie sur Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics.
