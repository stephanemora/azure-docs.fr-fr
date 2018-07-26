---
title: Guide pratique pour gérer la connectivité et la messagerie fiable à l’aide des kits Azure IoT Hub device SDK
description: Découvrez comment améliorer la connectivité et la messagerie de vos appareils lorsque vous utilisez les kits Azure IoT Hub device SDK
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a318a1ef8b13b8fcb4f4401ac4d0e45037958d63
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127475"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Guide pratique pour gérer la connectivité et la messagerie fiable à l’aide des kits Azure IoT Hub device SDK

Ce guide fournit des instructions détaillées pour concevoir des applications d’appareil résilientes, en tirant parti des fonctionnalités de connectivité et de messagerie fiable fournies dans les kits Azure IoT device SDK. L’objectif de cet article est d’aider à répondre à des questions et à gérer les scénarios suivants :

- gestion d’une connexion réseau supprimée
- gestion du basculement entre différentes connexions réseau
- gestion de la reconnexion en raison d’erreurs de connexion temporaires aux services

Les détails d’implémentation peuvent varier selon le langage. Consultez la documentation de l’API associée ou le kit SDK spécifique pour plus d’informations.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Conception pour la résilience

Les appareils IoT s’appuient souvent sur des connexions réseau non continues et/ou instables telles que des connexions GSM ou satellite. De plus, lors de l’interaction avec les services cloud, des erreurs peuvent se produire en raison de conditions temporaires telles que des défauts intermittents d’indisponibilité de service ou des erreurs au niveau de l’infrastructure (communément appelées erreurs temporaires). Une application qui s’exécute sur un appareil a besoin de gérer les mécanismes de connexion et reconnexion, ainsi que la logique de nouvelle tentative pour l’envoi et la réception de messages. De plus, les exigences de stratégie de nouvelles tentatives dépendent fortement du scénario IoT auquel participe l’appareil, ainsi que du contexte et des fonctionnalités de l’appareil.

Les kits Azure IoT Hub device SDK ont pour but de simplifier la connexion et la communication de cloud-à-appareil et d’appareil-à-cloud en fournissant un moyen fiable et complet d’établir une connexion et d’envoyer/recevoir des messages vers et depuis Azure IoT Hub. Les développeurs peuvent également modifier l’implémentation existante pour développer la stratégie de nouvelles tentatives appropriée pour un scénario donné.

Les fonctionnalités SDK pertinentes qui prennent en charge la connectivité et la messagerie fiable sont décrites dans les sections suivantes.

## <a name="connection-and-retry"></a>Connexion et nouvelle tentative

Cette section fournit une vue d’ensemble des modèles de reconnexion et de nouvelles tentatives disponibles lors de la gestion des connexions, des conseils d’implémentation pour l’utilisation d’une stratégie de nouvelles tentatives différente dans votre application d’appareil, et des API appropriées pour les kits SDK d’appareil.

### <a name="error-patterns"></a>Modèles d’erreur
Des échecs de connexion peuvent se produire à de nombreux niveaux :

-  Erreurs réseau telles qu’un socket déconnecté et des erreurs de résolution de nom
- Erreurs au niveau du protocole pour le transport HTTP, AMQP et MQTT, telles que des liens détachés ou des sessions expirées
- Erreurs au niveau de l’application qui résultent d’erreurs locales, telles que des informations d’identification non valides, ou du comportement des services, tel que le dépassement d’un quota ou d’une limitation

Les kits SDK d’appareil détectent les erreurs dans les trois niveaux.  Les erreurs liées au système d’exploitation et les erreurs matérielles ne sont pas détectées ni gérées par les kits SDK d’appareil.  La conception est basée sur le [Guide de gestion des erreurs temporaires](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) fourni dans le Centre des architectures Azure.

### <a name="retry-patterns"></a>Modèles de nouvelle tentative

Le processus global de nouvelle tentative lors de la détection d’erreurs de connexion est : 
1. Le kit SDK détecte l’erreur et l’erreur associée dans le réseau, le protocole ou l’application.
2. Selon le type d’erreur, le kit SDK utilise le filtre d’erreur pour décider si une nouvelle tentative doit être effectuée.  Si une **erreur irrécupérable** est identifiée par le kit SDK, les opérations (connexion et envoi/réception) sont arrêtées et le kit SDK en informe l’utilisateur. Une erreur irrécupérable est une erreur que le kit SDK peut identifier et pour laquelle il détermine qu’elle ne peut pas être récupérée, par exemple, une erreur d’authentification ou de point de terminaison défectueux.
3. Si une **erreur récupérable** est identifiée, le kit SDK commence à réessayer d’utiliser la stratégie de nouvelles tentatives spécifiée jusqu'à l’expiration d’un délai donné.
4. Lorsque le délai imparti expire, le kit SDK arrête d’essayer d’établir la connexion ou l’envoi, et en informe l’utilisateur.
5.  Le kit SDK permet à l’utilisateur de joindre un rappel pour recevoir les modifications de l’état de la connexion. 

Trois stratégies de nouvelles tentatives sont fournies :
- **Interruption exponentielle avec instabilité** : Il s’agit de la stratégie de nouvelles tentatives par défaut appliquée.  Elle a tendance à être agressive au début, ralentit, puis atteint un délai maximal qui n’est pas dépassé.  La conception est basée sur le [Guide sur les nouvelles tentatives fourni dans le Centre des architectures Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Nouvelle tentative personnalisée** : Vous pouvez implémenter une stratégie de nouvelles tentatives personnalisée et l’injecter dans la stratégie de nouvelles tentatives selon le langage que vous choisissez. Vous pouvez concevoir une stratégie de nouvelles tentatives adaptée à votre scénario.  Cela n’est pas disponible dans le kit SDK C.
- **Aucune nouvelle tentative** : Il est possible de définir la stratégie de nouvelles tentatives sur « aucune nouvelle tentative », ce qui désactive la logique de nouvelle tentative.  Le kit SDK tente de se connecter une fois et d’envoyer un message une fois, en supposant que la connexion est établie. Cette stratégie doit généralement être utilisée en cas de problèmes de bande passante ou de coût.   Si cette option est choisie, les messages qui ne peuvent pas être envoyés sont perdus et ne peuvent pas être récupérés. 

### <a name="retry-policy-apis"></a>API de stratégie de nouvelles tentatives

   | SDK | Méthode SetRetryPolicy | Implémentations de stratégie | Conseils d’implémentation |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Par défaut** : [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personnalisée :** utiliser la stratégie [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponible<BR>**Aucune nouvelle tentative :** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implémentation de C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Par défaut**: [classe ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personnalisée :** implémenter l’[interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Aucune nouvelle tentative :** [classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implémentation Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[Kit de développement logiciel (SDK) .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Par défaut** : [classe ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personnalisée :** implémenter l’[interface IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Aucune nouvelle tentative :** [classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implémentation C#]() |
   | Nœud| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Par défaut** : [classe ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personnalisée :** implémenter l’[interface RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Aucune nouvelle tentative :** [classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implémentation Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Les exemples de code ci-dessous illustrent ce flux. 

#### <a name="net-implementation-guidance"></a>Conseils d’implémentation .NET

L’exemple de code ci-dessous montre comment définir et paramétrer la stratégie de nouvelles tentatives par défaut :

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Pour éviter une utilisation élevée du processeur, les nouvelles tentatives sont limitées si le code échoue immédiatement (par exemple, lorsqu’il n’existe aucun réseau ni itinéraire vers la destination), afin que le temps minimal pour exécuter la tentative suivante soit de 1 seconde. 

Si le service répond avec une erreur de limitation, la stratégie de nouvelles tentatives est différente et ne peut pas être modifiée via l’API publique :

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Le mécanisme de nouvelle tentative s’arrête après `DefaultOperationTimeoutInMilliseconds`, qui est actuellement défini sur 4 minutes.

#### <a name="other-languages-implementation-guidance"></a>Conseils d’implémentation pour d’autres langages
Pour d’autres langages, consultez la documentation d’implémentation ci-dessous.  Exemples illustrant l’utilisation des API de stratégie de nouvelles tentatives fournies dans le dépôt.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

