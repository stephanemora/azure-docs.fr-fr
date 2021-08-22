---
title: Paramètres de port d’Azure Relay | Microsoft Docs
description: Cet article comprend un tableau qui décrit la configuration requise pour les valeurs de port d’Azure Relay.
ms.topic: article
ms.date: 06/23/2021
ms.openlocfilehash: b293d58d2e555b7e0d01ea1a9e421f78cf23105c
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668679"
---
# <a name="azure-relay-port-settings"></a>Paramètres de port d’Azure Relay

Le tableau suivant décrit la configuration requise pour les valeurs de port d’Azure Relay.

## <a name="hybrid-connections"></a>les connexions hybrides

Les connexions hybrides font appel à WebSockets sur le port 443 avec TLS/SSL comme mécanisme de transport sous-jacent, qui utilise uniquement **HTTPS**. 

## <a name="wcf-relays"></a>Relais WCF
  
|Liaison|Sécurité de transport|Port|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Oui|HTTPS| 
|" |Non|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Oui|9351/HTTPS|  
|" |Non|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Vous pouvez soit utiliser|5671/9352/HTTP (9352/9353 en cas d’utilisation hybride)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Oui|9351/HTTPS|  
|" |Non|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Oui|HTTPS|  
|" |Non|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Oui|HTTPS|  
|" |Non|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Relay, consultez les liens suivants :
* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [FAQ Relay](relay-faq.yml)