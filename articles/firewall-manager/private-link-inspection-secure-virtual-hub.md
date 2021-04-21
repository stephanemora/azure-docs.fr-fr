---
title: Sécuriser le trafic destiné aux points de terminaison privés dans Azure Virtual WAN
description: Découvrez comment utiliser les règles de réseau et les règles d’application pour sécuriser le trafic destiné aux points de terminaison privés dans Azure Virtual WAN
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259351"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Sécuriser le trafic destiné aux points de terminaison privés dans Azure Virtual WAN

> [!NOTE]
> Cet article s’applique uniquement au hub virtuel sécurisé. Si vous souhaitez inspecter le trafic destiné à des points de terminaison privés à l’aide du Pare-feu Azure dans un réseau virtuel de hub, consultez [Utiliser le Pare-feu Azure pour inspecter le trafic destiné à un point de terminaison privé](../private-link/inspect-traffic-with-azure-firewall.md).

Le [point de terminaison privé Azure](../private-link/private-endpoint-overview.md) est le composant fondamental d’[Azure Private Link](../private-link/private-link-overview.md). Les points de terminaison privés permettent aux ressources Azure déployées dans un réseau virtuel de communiquer en privé avec des ressources de liaisons privées.

Les points de terminaison privés permettent aux ressources d’accéder au service de liaison privée déployé dans un réseau virtuel. L’accès au point de terminaison privé via l’appairage de réseaux virtuels et les connexions réseau locales permettent d’étendre la connectivité.

Vous devrez peut-être filtrer le trafic des clients localement ou dans Azure destiné aux services exposés via des points de terminaison privés dans un réseau Virtual WAN connecté. Cet article vous guide tout au long de cette tâche en utilisant un [hub virtuel sécurisé](../firewall-manager/secured-virtual-hub.md) avec le service [Pare-feu Azure](../firewall/overview.md) comme fournisseur de sécurité.

Le service Pare-feu Azure filtre le trafic en utilisant l’une de ces méthodes :

* [Nom de domaine complet dans les règles de réseau](../firewall/fqdn-filtering-network-rules.md) pour les protocoles TCP et UDP
* [Nom de domaine complet dans les règles d’application](../firewall/features.md#application-fqdn-filtering-rules) pour HTTP, HTTPS et MSSQL.
* Adresses IP source et de destination, port et protocole utilisant les [règles de réseau](../firewall/features.md#network-traffic-filtering-rules)

Utilisez les règles d’application plutôt que les règles de réseau pour inspecter le trafic destiné aux points de terminaison privés.
Un hub virtuel sécurisé est géré par Microsoft et ne peut pas être lié à une [zone de DNS privé](../dns/private-dns-privatednszone.md). Cela est nécessaire pour résoudre le nom de domaine complet (ou « FQDN ») d’une [ressource de lien privé](../private-link/private-endpoint-overview.md#private-link-resource) vers l’adresse IP correspondant à son point de terminaison privé.

Le filtrage FQDN SQL est pris en charge uniquement en [mode proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Le mode *proxy* peut entraîner une plus grande latence par rapport au mode de *redirection*. Si vous souhaitez continuer à utiliser le mode de redirection, qui est le mode par défaut pour les clients se connectant dans Azure, vous pouvez filtrer l’accès en utilisant le nom de domaine complet dans les règles de réseau du pare-feu.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrer le trafic à l’aide d’un nom de domaine complet dans les règles de réseau et d’application

Pour permettre au service Pare-feu Azure de filtrer le trafic à l’aide du nom de domaine complet dans les règles de réseau et d’application, procédez comme suit :

1. Déployez une machine virtuelle de [redirecteur DNS](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) dans un réseau virtuel connecté au hub virtuel sécurisé et lié aux zones de DNS privé hébergeant les types d’enregistrements A pour les points de terminaison privés.

2. Configurez des [paramètres DNS personnalisés](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) pour pointer vers l’adresse IP de la machine virtuelle du redirecteur DNS et activez le proxy DNS dans la stratégie de pare-feu associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé.

3. Configurez des [serveurs DNS personnalisés](../virtual-network/manage-virtual-network.md#change-dns-servers) pour les réseaux virtuels connectés au hub virtuel sécurisé pour pointer vers l’adresse IP privée associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé.

4. Configurez des serveurs DNS locaux pour transférer les requêtes DNS des zones de DNS public des points de terminaison privés à l’adresse IP privée associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé.

5. Si nécessaire, configurez une [règle d’application](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) ou une [règle réseau](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) dans la stratégie de pare-feu associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé. Le paramètre *Type de destination* de cette règle doit être défini sur Nom de domaine complet et le nom de domaine complet public de la ressource de liaison privée doit être défini sur *Destination*.

6. Dans la stratégie de pare-feu associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé , accédez à *Hubs virtuels sécurisés* et sélectionnez le hub virtuel sécurisé où le filtrage du trafic destiné aux points de terminaison privés sera configuré.

7. Accédez à **Configuration de la sécurité**, puis sous **Trafic Privé**, sélectionnez **Envoyer via le Pare-feu Azure**.

8. Sélectionnez **Préfixes de trafic privé** pour modifier les préfixes CIDR à inspecter via le service Pare-feu Azure dans le hub virtuel sécurisé et ajoutez un préfixe /32 pour chaque point de terminaison privé, comme suit :

   > [!IMPORTANT]
   > Si ces préfixes /32 ne sont pas configurés, le trafic destiné aux points de terminaison privés contournera le service Pare-feu Azure.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuration de la sécurité de Firewall Manager" border="true":::

Cette procédure fonctionne uniquement lorsque les clients et les points de terminaison privés sont déployés dans différents réseaux virtuels connectés au même hub virtuel sécurisé et pour des clients locaux. Si les clients et les points de terminaison privés sont déployés dans le même réseau virtuel, vous devez créer un UDR avec des itinéraires /32 pour les points de terminaison privés. Configurez ces itinéraires en définissant le paramètre **Type de tronçon suivant** sur **Appliance virtuelle** et le paramètre **Adresse de tronçon suivant** sur l’adresse IP privée du service Pare-feu Azure déployé dans le hub virtuel sécurisé. Le paramètre **Propager des itinéraires de passerelle** doit être défini sur **Oui**.

Le diagramme suivant illustre les flux du DNS et des trafics de données des différents clients qui se connectent à un point de terminaison privé déployé dans un réseau Azure Virtual WAN :

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Flux de trafic" border="true":::

## <a name="troubleshooting"></a>Dépannage

Voici les principaux problèmes que vous pouvez rencontrer lorsque vous tentez de filtrer le trafic destiné à des points de terminaison privés via un hub virtuel sécurisé :

- Les clients ne parviennent pas à se connecter aux points de terminaison privés.

- Le service Pare-feu Azure est contourné. Ce symptôme peut être validé par l’absence d’entrées du journal des règles d’application ou de réseau dans le service Pare-feu Azure.

Généralement, ces problèmes sont causés par :

- une résolution de noms DNS incorrecte, ou ;

- une configuration du routage incorrecte.

### <a name="incorrect-dns-name-resolution"></a>Résolution de noms DNS incorrecte

1. Vérifiez que les serveurs DNS du réseau virtuel sont définis sur *Personnalisé* et que l’adresse IP est l’adresse IP privée du service Pare-feu Azure dans le hub virtuel sécurisé.

   Azure CLI :

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Vérifiez que les clients situés dans le même réseau virtuel que l’ordinateur virtuel redirecteur DNS peuvent résoudre le nom de domaine complet public du point de terminaison privé en son adresse IP privée correspondante en interrogeant directement la machine virtuelle configurée en tant que redirecteur DNS.

   Linux :

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Inspectez les entrées du journal du service Pare-feu Azure *AzureFirewallDNSProxy* et validez qu’il peut recevoir et résoudre les requêtes DNS des clients.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Vérifiez que le paramètre *Proxy DNS* a été activé et qu’un serveur DNS *Personnalisé* pointant vers l’adresse IP de la machine virtuelle agissant en tant que redirecteur DNS d’adresse IP a été configuré dans la stratégie de pare-feu associée au service Pare-feu Azure dans le hub virtuel sécurisé.

   Azure CLI :

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Configuration du routage incorrecte

1. Dans la stratégie de pare-feu associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé, vérifiez le paramètre *Configuration de la sécurité*. Assurez-vous que la colonne **TRAFIC PRIVÉ** affiche la valeur **Sécurisé par le service Pare-feu Azure** pour chaque connexion de réseau virtuel et de branche dont vous souhaitez filtrer le trafic.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Trafic privé sécurisé par le service Pare-feu Azure" border="true":::

2. Dans la stratégie de pare-feu associée au service Pare-feu Azure déployé dans le hub virtuel sécurisé, vérifiez le paramètre **Configuration de la sécurité**. Assurez-vous qu’il existe une entrée /32 pour chaque adresse IP privée de point de terminaison privé dont vous souhaitez filtrer le trafic sous **Préfixes de trafic privés**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuration de la sécurité de Firewall Manager – Préfixes de trafic privé" border="true":::

3. Dans le hub virtuel sécurisé sous Virtual WAN, examinez les itinéraires effectifs des tables de routage associées aux connexions de réseaux virtuels et de branches dont vous souhaitez filtrer le trafic. Assurez-vous qu’il existe des entrées /32 pour chaque adresse IP privée de point de terminaison privé dont vous souhaitez filtrer le trafic.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Routes effectives d’un hub virtuel sécurisé" border="true":::

4. Inspectez les itinéraires effectifs sur les cartes réseau jointes aux machines virtuelles déployées dans les réseaux virtuels dont vous souhaitez filtrer le trafic. Assurez-vous qu’il existe des entrées /32 pour chaque adresse IP privée de point de terminaison privé dont vous souhaitez filtrer le trafic.
 
   Azure CLI :

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Examinez les tables de routage de vos appareils de routage locaux. Notez les espaces d’adressage des réseaux virtuels sur lesquels les points de terminaison privés sont déployés.

   Azure Virtual WAN ne publie pas les préfixes configurés sous **Préfixes de trafic privé** dans le paramètre **Configuration de la sécurité** de la stratégie de pare-feu pour le local. Il est prévu que les entrées /32 ne s’affichent pas dans les tables de routage de vos appareils de routage locaux.

6. Inspectez les journaux du service Pare-feu Azure **AzureFirewallApplicationRule** et **AzureFirewallNetworkRule**. Assurez-vous que le trafic destiné aux points de terminaison privés est journalisé.

   Les entrées de journal **AzureFirewallNetworkRule** n’incluent pas d’informations sur le nom de domaine complet. Lorsque vous inspectez les règles de réseau, filtrez les résultats par adresse IP et par port.

   Lors du filtrage du trafic destiné aux points de terminaison privés [Azure Files](../storage/files/storage-files-introduction.md), les entrées de journal **AzureFirewallNetworkRule** sont générées uniquement lorsqu’un client monte le partage de fichiers ou s’y connecte pour la première fois. Le service Pare-feu Azure ne génère pas de journaux pour les opérations [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) pour les fichiers dans le partage de fichiers. Cela est dû au fait que les opérations CRUD sont effectuées sur le canal TCP persistant ouvert lorsque le client monte le partage de fichiers ou s’y connecte pour la première fois.

   Exemple de requête de journal de règle d’application :

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Pare-feu Azure pour inspecter le trafic destiné à un point de terminaison privé](../private-link/inspect-traffic-with-azure-firewall.md)
