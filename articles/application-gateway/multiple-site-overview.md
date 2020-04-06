---
title: Hébergement de plusieurs sites sur Azure Application Gateway
description: Cet article fournit une vue d’ensemble de la prise en charge de sites multiples pour la passerelle Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257408"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hébergement de plusieurs sites Application Gateway

L’hébergement de plusieurs sites vous permet de configurer plusieurs applications web sur le même port d’une passerelle Application Gateway. Cette fonctionnalité vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu’à 100 sites web à une passerelle Application Gateway. Chaque site web peut être dirigé vers son propre pool principal. Dans l’exemple suivant, la passerelle d’application gère le trafic pour `contoso.com` et fabrikam.`fabrikam.com` avec deux pools de serveurs principaux : ContosoServerPool et FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Pour la référence SKU v1, les règles sont traitées dans l’ordre où elles sont répertoriées sur le portail. Pour la référence SKU v2, les correspondances exactes ont une priorité plus élevée. Il est vivement recommandé de configurer des écouteurs multisites avant un écouteur de base.  De cette façon, vous êtes sûr que le trafic est acheminé vers le serveur principal approprié. Si un écouteur de base est indiqué en premier et correspond à une demande entrante, elle est traitée par cet écouteur.

Les requêtes adressées à `http://contoso.com` sont acheminées vers ContosoServerPool, tandis que les requêtes adressées à `http://fabrikam.com` sont acheminées vers FabrikamServerPool.

De même, vous pouvez héberger de nombreux sous-domaines du même domaine parent sur le même déploiement de passerelle d’application. Par exemple, vous pouvez héberger `http://blog.contoso.com` et `http://app.contoso.com` sur un déploiement unique de passerelle d’application.

## <a name="host-headers-and-server-name-indication-sni"></a>En-têtes d’hôte et Indication du nom du serveur (SNI)

Il existe trois mécanismes pour activer l’hébergement de plusieurs sites dans la même infrastructure.

1. Hébergez plusieurs applications web, chacune sur une adresse IP unique.
2. Utilisez le nom d’hôte pour héberger plusieurs applications web sur la même adresse IP.
3. Utilisez des ports différents pour héberger plusieurs applications web sur la même adresse IP.

Actuellement, le service Application Gateway prend en charge une seule IP publique sur laquelle il écoute le trafic. Par conséquent, la prise en charge de plusieurs applications, chacune avec sa propre adresse IP, n’est pas disponible actuellement. 

Application Gateway prend en charge plusieurs applications qui écoutent sur des ports différents, mais ce scénario requiert que les applications acceptent le trafic sur les ports non standard. Il ne s’agit généralement pas de la configuration de votre choix.

Application Gateway se base sur des en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites web sur les mêmes adresses IP et port. Les sites hébergés sur Application Gateway peuvent également prendre en charge le déchargement TLS avec l’extension Indication du nom du serveur (SNI) TLS. Ce scénario signifie que le navigateur du client et la batterie de serveurs web principale doivent prendre en charge HTTP/1.1 et l’extension TLS définie dans RFC 6066.

## <a name="listener-configuration-element"></a>Élément de configuration d’écouteur

Les éléments de configuration HTTPListener existants sont améliorés pour prendre en charge les éléments d’indication du nom d’hôte et du nom de serveur. Ils sont utilisés par Application Gateway pour acheminer le trafic vers le pool principal approprié. 

L’exemple de code suivant est un extrait de l’élément HttpListeners issu d’un modèle de fichier :

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Vous pouvez consulter le [modèle Resource Manager avec l’hébergement de sites multiples](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) pour un déploiement basé sur un modèle de bout en bout.

## <a name="routing-rule"></a>Règle de routage

Aucune modification n’est requise dans la règle de routage. La règle de routage « De base » doit continuer à être choisie pour lier l’écouteur de site approprié au pool d’adresses principales correspondant.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir étudié l’hébergement de sites multiples, accédez à [Créer une passerelle Application Gateway avec l’hébergement de sites multiples](tutorial-multiple-sites-powershell.md) pour créer une passerelle Application Gateway avec prise en charge de plusieurs applications web.

