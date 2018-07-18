---
title: Paramètres de port d’Azure Relay | Microsoft Docs
description: Fournit des informations sur les valeurs de port d’Azure Relay.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 56688b6941d58c0ecc8d0ff4ba3c8a8392e71496
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029371"
---
# <a name="azure-relay-port-settings"></a>Paramètres de port d’Azure Relay

Le tableau suivant décrit la configuration requise pour les valeurs de port d’Azure Relay.

## <a name="hybrid-connections"></a>les connexions hybrides

Les connexions hybrides font appel à WebSockets sur le port 443 avec SSL comme mécanisme de transport sous-jacent, qui utilise **HTTPS** uniquement. 

## <a name="wcf-relays"></a>Relais WCF
  
|Liaison|Sécurité de transport|Port|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|OUI|HTTPS| 
|" |Non |HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|OUI|9351/HTTPS|  
|" |Non |9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Vous pouvez soit utiliser|5671/9352/HTTP (9352/9353 en cas d’utilisation hybride)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|OUI|9351/HTTPS|  
|" |Non |9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|OUI|HTTPS|  
|" |Non |HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|OUI|HTTPS|  
|" |Non |HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Relay, consultez les liens suivants :
* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [FAQ Relay](relay-faq.md)