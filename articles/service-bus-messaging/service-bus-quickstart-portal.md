---
title: Utiliser le portail Azure pour créer une file d’attente Service Bus
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un espace de noms Service Bus puis une file d’attente dans un espace de noms à l’aide du portail Azure.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 0e5a8698da03ee4d9c7d8b0d4d59eda1be3b1883
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185500"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Utiliser le portail Azure pour créer un espace de noms Service Bus ainsi qu’une file d'attente
Ce guide de démarrage rapide vous montre comment créer un espace de noms Service Bus et une file d’attente à l’aide du [portail Azure][Azure portal]. Il vous montre également comment obtenir les informations d’autorisation qu’une application cliente peut utiliser pour envoyer/recevoir des messages à/de la file d'attente. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous disposez d’un abonnement Azure. Si vous n'avez pas d'abonnement Azure, vous pouvez créer un [compte gratuit][] avant de commencer.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un espace de noms Service Bus ainsi qu’une file d'attente dans celui-ci. Pour apprendre à envoyer/recevoir des messages à/de la file d’attente, consultez l’un des guides de démarrage rapide suivants dans la section **Envoyer et recevoir des messages**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
