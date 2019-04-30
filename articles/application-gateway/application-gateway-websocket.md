---
title: Prise en charge de WebSocket pour la passerelle Application Gateway | Microsoft Docs
description: Cette page fournit une vue d’ensemble de la prise en charge de WebSocket pour la passerelle Application Gateway.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: 54c34690e678f07d6309a1877b0ca5d0a0b274f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831235"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Vue d’ensemble de la prise en charge de WebSocket dans Application Gateway

Application Gateway prend en charge WebSocket de manière native, quelle que soit la taille de la passerelle. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. 

Le protocole WebSocket, standardisé dans la [RFC6455](https://tools.ietf.org/html/rfc6455), permet une communication en duplex intégral entre le serveur et le client via une connexion TCP de longue durée. Il assure une communication plus interactive entre le serveur web et le client, qui peut être bidirectionnelle sans nécessiter d’interrogations, comme c’est le cas pour les implémentations basées sur le protocole HTTP. Contrairement au protocole HTTP, WebSocket génère une faible surcharge et peut réutiliser la même connexion TCP pour plusieurs demandes/réponses, ce qui entraîne une utilisation plus efficace des ressources. Les protocoles WebSocket sont conçus pour fonctionner sur les ports HTTP traditionnels (80 et 443).

Vous pouvez continuer d’utiliser un élément écouteur HTTP standard sur le port 80 ou 443 pour recevoir le trafic WebSocket. Le trafic WebSocket est ensuite dirigé vers le serveur principal compatible WebSocket à l’aide du pool principal approprié tel que spécifié dans les règles Application Gateway. Le serveur principal doit répondre aux sondes de la passerelle Application Gateway, qui sont décrites dans la section [Vue d’ensemble de la sonde d’intégrité](application-gateway-probe-overview.md) . Les sondes d’intégrité d’Application Gateway n’utilisent que les protocoles HTTP/HTTPS. Chaque serveur principal doit répondre aux sondes HTTP de la passerelle Application Gateway pour acheminer le trafic WebSocket au serveur.

Il est utilisé dans les applications qui bénéficient de communication rapide et en temps réel, telles que la conversation, tableau de bord et applications de jeu.

## <a name="how-does-websocket-work"></a>Comment fonctionne le WebSocket

Pour établir une connexion WebSocket, une négociation basée sur HTTP spécifique est échangée entre le client et le serveur. En cas de réussite, le protocole de couche d’application est « mis à niveau » à partir de HTTP vers WebSockets, à l’aide de la connexion TCP précédemment établie. Une fois que cela se produit, HTTP est complètement hors de l’image ; données peuvent être envoyées ou reçus par le biais du protocole WebSocket par les deux points de terminaison, jusqu'à ce que la connexion WebSocket est fermée. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Élément de configuration d’écouteur

Un écouteur HTTP permet de prendre en charge le trafic WebSocket. Voici un extrait d’un élément HttpListeners provenant d’un exemple de fichier de modèle. Vous auriez besoin d’écouteurs HTTP et HTTPS pour prendre en charge WebSocket et sécuriser le trafic WebSocket. De même que vous pouvez utiliser le portail ou Azure PowerShell pour créer une passerelle d’application avec des écouteurs sur le port 80/443 pour prendre en charge le trafic WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuration des règles de routage, BackendHttpSetting et BackendAddressPool

Un élément BackendAddressPool permet de définir un pool principal avec des serveurs compatibles WebSocket. L’élément backendHttpSetting est défini avec un port principal 80 et 443. Les propriétés de l’affinité basée sur les cookies et de requestTimeouts ne sont pas pertinentes pour le trafic WebSocket. La règle de routage n’est pas modifiée. La règle « De base » sert à lier l’écouteur approprié au pool d’adresses principal correspondant. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Serveur principal compatible WebSocket

Votre serveur principal doit exécuter un serveur web HTTP/HTTPS sur le port configuré (généralement 80/443) pour que WebSocket fonctionne. En effet, le protocole WebSocket impose que la négociation initiale s’effectue en HTTP avec mise à niveau vers le protocole WebSocket comme champ d’en-tête. Voici un exemple d’en-tête :

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Autre raison : la sonde d’intégrité principale d’Application Gateway ne prend en charge que les protocoles HTTP/HTTPS. Si le serveur principal ne répond pas aux sondes HTTP ou HTTPS, il est retiré du pool principal.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert la prise en charge de WebSocket, consultez [Créer une passerelle Application Gateway](quick-create-powershell.md) pour commencer à utiliser une application web compatible avec WebSocket.