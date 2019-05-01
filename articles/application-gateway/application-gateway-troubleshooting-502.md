---
title: Résoudre les erreurs d’Azure Application passerelle incorrecte (502)
description: Découvrez comment résoudre les erreurs 502 dans Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697158"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Résolution des erreurs de passerelle incorrecte dans Application Gateway

Découvrez comment résoudre les erreurs de passerelle incorrecte (502) reçues lors de l’utilisation d’Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Présentation

Après avoir configuré une passerelle d’application, une des erreurs que vous pouvez rencontrer est « Erreur de serveur : 502 - Le serveur Web a reçu une réponse erronée lors de son utilisation en tant que passerelle ou serveur proxy », Cette erreur peut se produire pour les raisons principales suivantes :

* Groupe de sécurité réseau, UDR ou un DNS personnalisé bloque l’accès aux membres du pool principal.
* Machines virtuelles principales ou les instances de machines virtuelles identiques ne sont pas répondre à la sonde d’intégrité par défaut.
* Configuration non valide ou incorrecte des sondes d’intégrité personnalisées.
* Azure Application Gateway [pool back-end n’est pas configuré ou est vide](#empty-backendaddresspool).
* Aucune des machines virtuelles ou des instances du [groupe de machines virtuelles identiques](#unhealthy-instances-in-backendaddresspool) n’est intègre.
* [Problèmes de connectivité ou d’expiration](#request-time-out) des requêtes d’utilisateur.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problème de groupe de sécurité réseau, de routage défini par l’utilisateur ou de DNS personnalisé

### <a name="cause"></a>Cause :

Si l’accès au serveur principal est bloqué en raison d’un groupe de sécurité réseau, un UDR ou un DNS personnalisé, les instances de passerelle d’application ne peut pas atteindre le pool principal. Cela entraîne des échecs de sonde, ce qui provoque les 502 erreurs.

Le groupe de sécurité réseau/UDR peut être présent dans le sous-réseau de passerelle d’application ou le sous-réseau dans lequel les machines virtuelles d’application sont déployées.

De même, la présence d’un DNS personnalisé dans le réseau virtuel peut également entraîner des problèmes. Un nom de domaine complet utilisé pour les membres du pool principal peut ne pas résoudre correctement par le serveur DNS configuré par l’utilisateur pour le réseau virtuel.

### <a name="solution"></a>Solution

Validez la configuration du groupe de sécurité réseau, du routage défini par l’utilisateur et du DNS en effectuant les étapes suivantes :

* Vérifiez les que groupes de sécurité réseau associé avec le sous-réseau de passerelle d’application. Assurez-vous que la communication vers le serveur principal n’est pas bloquée.
* Vérifiez que le sous-réseau de passerelle d’application associé à UDR. Assurez-vous que l’UDR n’est pas dirigeant le trafic du sous-réseau du serveur principal. Par exemple, vérifier pour le routage aux appliances virtuelles ou des itinéraires par défaut annoncés au sous-réseau de passerelle d’application via ExpressRoute/VPN du réseau.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Vérifiez le groupe de sécurité réseau et le routage vers la machine virtuelle du serveur principal.

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Vérifiez la présence d’un DNS personnalisé dans le réseau virtuel. Le DNS peut être vérifié en examinant les détails des propriétés du réseau virtuel dans la sortie.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Le cas échéant, vérifiez que le serveur DNS peut résoudre correctement les nom de domaine complet du membre du pool principal.

## <a name="problems-with-default-health-probe"></a>Problèmes avec la sonde d’intégrité par défaut

### <a name="cause"></a>Cause :

les 502 erreurs peuvent également être fréquentes indicateurs que la sonde d’intégrité par défaut ne peut pas atteindre les machines virtuelles principales.

Lorsqu’une instance de passerelle d’application est configurée, il configure automatiquement une sonde d’intégrité par défaut pour chaque BackendAddressPool à l’aide des propriétés de BackendHttpSetting. La configuration de cette sonde d’intégrité ne nécessite aucune action de la part de l’utilisateur. Plus précisément, lorsqu’une règle d’équilibrage de charge est configurée, une association est effectuée entre un BackendHttpSetting et un élément BackendAddressPool. Une sonde par défaut est configurée pour chacune de ces associations et la passerelle d’application démarre une connexion de vérification d’intégrité réguliers à chaque instance dans le BackendAddressPool au niveau du port spécifié dans l’élément BackendHttpSetting. 

Le tableau suivant répertorie les valeurs associées à la sonde d’intégrité par défaut :

| Propriétés de la sonde | Valeur | Description |
| --- | --- | --- |
| URL de sonde |`http://127.0.0.1/` |Chemin d'accès de l'URL |
| Interval |30 |Intervalle d’analyse en secondes |
| Délai d’attente |30 |Délai d’expiration de l’analyse en secondes |
| Seuil de défaillance sur le plan de l’intégrité |3 |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

### <a name="solution"></a>Solution

* Assurez-vous qu’un site par défaut est configuré et qu’il écoute sur le port 127.0.0.1.
* Si BackendHttpSetting spécifie un port autre que 80, le site par défaut doit être configuré pour écouter sur ce port.
* L’appel à `http://127.0.0.1:port` doit renvoyer un code de résultat HTTP 200. Cela doit être retourné dans le délai de 30 secondes.
* Assurez-vous que le port configuré est ouvert et qu’il n’y a aucune règles de pare-feu ou les groupes de sécurité réseau Azure, ce qui bloque le trafic entrant ou sortant sur le port configuré.
* Si les machines virtuelles Azure classic ou Service Cloud est utilisé avec un nom de domaine complet ou une adresse IP publique, vérifiez que le correspondantes [point de terminaison](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) est ouvert.
* Si la machine virtuelle est configurée via Azure Resource Manager et est à l’extérieur du réseau virtuel dans lequel la passerelle d’application est déployée, un [groupe de sécurité réseau](../virtual-network/security-overview.md) doit être configuré pour autoriser l’accès sur le port souhaité.

## <a name="problems-with-custom-health-probe"></a>Problèmes avec la sonde d’intégrité personnalisée

### <a name="cause"></a>Cause :

Les sondes d’intégrité personnalisées apportent davantage de flexibilité au comportement de contrôle par défaut. Lorsque vous utilisez des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL, le chemin d’accès à tester et combien de réponses en échec pour accepter avant de marquer l’instance de pool back-end comme étant défectueuse.

Les propriétés supplémentaires suivantes sont ajoutées :

| Propriétés de la sonde | Description |
| --- | --- |
| Nom |Nom de la sonde. Ce nom est utilisé pour désigner la sonde dans les paramètres HTTP du serveur principal. |
| Protocol |Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP du serveur principal |
| Host |Nom d’hôte pour l’envoi de la sonde. S’applique uniquement lorsque plusieurs sites est configurés sur la passerelle d’application. Ce nom est différent du nom d’hôte de la machine virtuelle. |
| path |Chemin relatif de la sonde. Le chemin valide commence par « / ». La sonde est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\> |
| Interval |Intervalle d’analyse en secondes. Il s’agit de l’intervalle de temps qui s’écoule entre deux analyses consécutives. |
| Délai d’attente |Délai d’expiration de l’analyse en secondes. Si aucune réponse valide n’est pas reçue dans le délai imparti, la sonde est marquée comme ayant échoué. |
| Seuil de défaillance sur le plan de l’intégrité |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

### <a name="solution"></a>Solution

Vérifiez que la sonde d’intégrité personnalisée est correctement configurée (voir la table précédente). Outre les étapes de dépannage précédentes, vérifiez également les points suivants :

* Assurez-vous que la sonde est correctement spécifiée suivant les indications du [guide](application-gateway-create-probe-ps.md).
* Si la passerelle d’application est configurée pour un site unique, par défaut l’hôte de nom doit être spécifié en tant que `127.0.0.1`, sauf si l’autre manière dans la sonde personnalisée.
* Assurez-vous qu’un appel à http://\<hôte\>:\<port\>\<chemin d’accès\> retourne un code de résultat HTTP 200.
* Assurez-vous que l’intervalle de délai d’attente et unhealtythreshold se sont dans la plage acceptable.
* Si vous utilisez une sonde HTTPS, vérifiez que le serveur back-end ne nécessite pas SNI en configurant un certificat de secours sur le serveur back-end lui-même.

## <a name="request-time-out"></a>Délai d’attente de la demande

### <a name="cause"></a>Cause :

Lorsqu’une demande de l’utilisateur est reçue, la passerelle d’application applique les règles configurées à la demande et l’achemine vers une instance de pool back-end. Application Gateway observe un temps d’attente (configurable) pour recevoir une réponse de l’instance de serveur principal. Par défaut, cet intervalle est **20** secondes. Si la passerelle d’application ne reçoit pas une réponse à partir de l’application principale dans cet intervalle, la demande de l’utilisateur obtient une erreur 502.

### <a name="solution"></a>Solution

Application Gateway vous permet de configurer ce paramètre via BackendHttpSetting, qui peut ensuite être appliqué à différents pools. Différents pools back-end peuvent avoir des paramètres BackendHttpSetting différents et des délais d’attente configuré.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Pool d’adresses principal vide

### <a name="cause"></a>Cause :

Si la passerelle d’application n’a aucune machine virtuelle ou les machines virtuelles identiques configurés dans le pool d’adresses back-end, il ne peut pas acheminer les demandes client et envoie un message d’erreur passerelle incorrecte.

### <a name="solution"></a>Solution

Assurez-vous que le pool d’adresses back-end n’est pas vide. Vous pouvez pour cela utiliser PowerShell, l’interface de ligne de commande ou le portail.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

L’applet de commande ci-dessus doit renvoyer un pool d’adresses principal non vide. L’exemple suivant montre deux pools retournées qui sont configuré avec un nom de domaine complet ou des adresses IP pour les machines virtuelles principales. L’approvisionnement de BackendAddressPool doit se trouver à l’état « Succeeded » (Réussi).

BackendAddressPoolsText :

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instances non intègres dans BackendAddressPool

### <a name="cause"></a>Cause :

Si toutes les instances de BackendAddressPool sont défectueux, la passerelle d’application n’a pas de tous les serveurs principaux pour acheminer la demande utilisateur. Cela peut également être le cas lorsque les instances de serveur principal sont intègres, mais n’ont pas déployé l’application requise.

### <a name="solution"></a>Solution

Assurez-vous que les instances sont intègres et que l’application est correctement configurée. Vérifiez si les instances de serveur principal peuvent répondre à une commande ping à partir d’une autre machine virtuelle dans le même réseau virtuel. Si configuré avec un point de terminaison public, assurez-vous de qu'une demande de navigateur à l’application web est accessible.

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne résolvent pas le problème, ouvrez un [ticket de support](https://azure.microsoft.com/support/options/).

