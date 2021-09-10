---
title: Gérer les espaces de noms et les entités Azure Service Bus par programme
description: Cet article explique comment approvisionner dynamiquement ou programmatiquement des espaces de noms et des entités Service Bus.
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: b053ff5cce51fbcd0ce56e2bdbfaff39dddb6394
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563517"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Provisionnement dynamique d’espaces de noms et d’entités Service Bus 
Les bibliothèques Azure Service Bus permettent d’approvisionner dynamiquement des entités et des espaces de noms Service Bus. Cela permet des déploiements et des scénarios de messagerie complexes, et rend possible la définition des entités à approvisionner par programmation.

## <a name="overview"></a>Vue d'ensemble
Vous pouvez adopter deux approches programmatiques pour gérer les ressources Azure Service Bus. La première consiste à utiliser les bibliothèques basées sur [Azure Resource Manager](../azure-resource-manager/management/overview.md), qui vous permettent de gérer les espaces de noms, les files d’attente, les rubriques, les abonnements, les règles et les stratégies de signature d’accès partagé. Les bibliothèques basées sur Azure Resource Manager prennent en charge l’authentification par le biais d’Azure Active Directory, et non pas par celui des chaînes de connexion. La deuxième approche consiste à exploiter les mêmes bibliothèques clientes Service Bus que celles que vous utilisez pour envoyer et recevoir des messages. Les bibliothèques clientes fournissent également des API pour vous aider à gérer les files d’attente, les rubriques, les abonnements et les règles dans un espace de noms *existant*. Elles prennent en charge l’authentification avec les chaînes de connexion. Lorsque vous décidez de l’approche à adopter, tenez compte des points suivants. 

Les bibliothèques basées sur Azure Resource Manager offrent les mêmes fonctionnalités que le Portail Azure, l’interface CLI et PowerShell lorsqu’il s’agit de gérer les espaces de noms et les entités Service Bus comme les files d’attente, les sujets, les abonnements, etc. Si vous avez utilisé le Portail Azure, l’interface CLI ou PowerShell pour vos opérations de gestion et que vous souhaitez bénéficier d’un moyen dynamique de le faire, alors ces bibliothèques peuvent être mieux adaptées à vos besoins. 

Toutefois, si vous utilisez déjà une bibliothèque cliente Service Bus pour les opérations spécifiques au service, telles que l’envoi et la réception de messages, et que vous devez également gérer les entités Service Bus, l’utilisation de la même bibliothèque peut être plus pratique pour vous. Les bibliothèques clientes possèdent un `ServiceBusAdministrationClient` (appelé `ServiceBusManagementClient` dans les bibliothèques plus anciennes) qui fournit un sous-ensemble des fonctionnalités de gestion fournies par les bibliothèques basées sur Azure Resource Manager. Il convient de souligner que, si les bibliothèques basées sur Azure Resource Manager vous permettent de gérer à la fois des espaces de noms et des entités Service Bus, les bibliothèques clientes vous permettent uniquement de gérer des entités dans un espace de noms existant, mais  *pas* dans l’espace de noms lui-même.

## <a name="manage-using-azure-resource-manager-based-libraries"></a>Gérer à l’aide de bibliothèques basées sur Azure Resource Manager

Les bibliothèques basées sur Azure Resource Manager vous permettent de gérer les espaces de noms, les files d’attente, les rubriques, les abonnements, les règles et les stratégies de signature d’accès partagé.  Elles prennent en charge l’authentification avec Azure Active Directory (Azure AD) *uniquement* et non pas les chaînes de connexion. 

| Langage | Package | Documentation | Exemples|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[Informations de référence sur l’API Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[Informations de référence sur l’API com.azure-resourcemanager-servicebus](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[Informations de référence sur l’API pour @azure/arm-servicebus](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[Informations de référence sur azure-mgmt-servicebus](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Bibliothèques Fluent .NET et Java
Il existe une version Fluent des bibliothèques basées sur Azure Resource Manager. 

|Langage|Package|Documentation|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[Informations de référence sur l’API Microsoft.Azure.Management.ServiceBus.Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[Informations de référence sur l’API com.azure-resourcemanager-servicebus.fluent](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>Gérer à l’aide des bibliothèques clientes Service Bus 

Les bibliothèques clientes Service Bus qui sont utilisées pour des opérations telles que l’envoi et la réception de messages permettent également de gérer les files d’attente, les rubriques, les abonnements et les règles dans un espace de noms Service Bus *existant*. Cette fonctionnalité est disponible via le `ServiceBusAdministrationClient` dans les bibliothèques les plus récentes et via le `ServiceBusManagementClient` dans les bibliothèques plus anciennes. Nous vous recommandons vivement d’utiliser les bibliothèques les plus récentes.

### <a name="latest-service-bus-libraries"></a>Bibliothèques Service Bus les plus récentes
|Langage|Package|Documentation|Exemples|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient), [ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>Bibliothèques Service Bus héritées
|Langage|Package|Documentation|Exemples|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync), [ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>Étapes suivantes
- Envoyer et recevoir des messages à partir de la file d’attente à l’aide de la dernière bibliothèque Azure Service Bus : [.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages), [Java](./service-bus-java-how-to-use-queues.md), [JavaScript](./service-bus-nodejs-how-to-use-queues.md), [Python](./service-bus-python-how-to-use-queues.md)
- Envoyer des messages à une rubrique et recevoir des messages à partir d’un abonnement à la dernière bibliothèque Service Bus : .[NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md),  [Java](./service-bus-java-how-to-use-topics-subscriptions.md), [JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md), [Python](./service-bus-python-how-to-use-topics-subscriptions.md)