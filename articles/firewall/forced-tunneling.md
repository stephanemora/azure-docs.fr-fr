---
title: Tunneling forcé du pare-feu Azure
description: Vous pouvez configurer un tunneling forcé pour router le trafic Internet vers une appliance virtuelle réseau ou un pare-feu supplémentaire en vue d’un traitement ultérieur.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525795"
---
# <a name="azure-firewall-forced-tunneling"></a>Tunneling forcé du pare-feu Azure

Lorsque vous configurez un nouveau pare-feu Azure, vous pouvez acheminer tout le trafic Internet vers un tronçon suivant désigné au lieu d’accéder directement à Internet. Par exemple, vous pouvez disposer d’un itinéraire par défaut publié via BGP ou utilisant l’itinéraire défini par l’utilisateur (UDR) pour forcer le trafic vers un pare-feu de périphérie local ou une autre appliance virtuelle réseau (NVA) pour traiter le trafic réseau avant qu’il ne soit dirigé vers Internet. Pour prendre en charge cette configuration, vous devez créer un Pare-feu Azure avec la configuration de tunnel forcé activée. Il s’agit d’une exigence obligatoire pour éviter toute interruption de service. S’il s’agit d’un pare-feu préexistant, vous devez recréer le pare-feu en mode Tunnel forcé pour prendre en charge cette configuration. Pour plus d’informations, consultez la [FAQ du Pare-feu Azure](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) sur l’arrêt et le redémarrage d’un pare-feu en mode Tunnel forcé.

## <a name="forced-tunneling-configuration"></a>Configuration de tunneling forcé

Vous pouvez configurer le tunneling forcé lors de la création du pare-feu en activant le mode Tunnel forcé comme indiqué ci-dessous. Pour prendre en charge le tunneling forcé, le trafic du management des services est séparé du trafic client. Un sous-réseau dédié supplémentaire nommé **AzureFirewallManagementSubnet** (taille minimale du sous-réseau /26) ayant sa propre IP publique associée est nécessaire. 

En mode Tunneling forcé, le service Pare-feu Azure intègre le sous-réseau de gestion (AzureFirewallManagementSubnet) à ses fins *opérationnelles*. Par défaut, le service associe une table d’itinéraires fournie par le système au sous-réseau de gestion. Le seul itinéraire autorisé sur ce sous-réseau est un itinéraire par défaut vers Internet et la *propagation des itinéraires de passerelle* doit être désactivée. Évitez d’associer des tables de routage client au sous-réseau de gestion lorsque vous créez le pare-feu. 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="Configurer un tunneling forcé":::

Dans cette configuration, *AzureFirewallSubnet* peut désormais inclure des routes vers n’importe quel pare-feu local ou n’importe quelle appliance virtuelle réseau pour traiter le trafic avant qu’il ne soit dirigé vers Internet. Vous pouvez également publier ces routes via le protocole BGP sur *AzureFirewallSubnet* si l’option **Propager des routes de passerelle** est activée sur ce sous-réseau.

Par exemple, vous pouvez créer une route par défaut sur *AzureFirewallSubnet* avec votre passerelle VPN comme tronçon suivant pour accéder à votre appareil local. Ou vous pouvez activer l’option **Propager des routes de passerelle** pour récupérer les routes appropriées sur le réseau local.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Propagation de la route de la passerelle de réseau virtuel":::

Si vous activez le tunneling forcé, le trafic Internet est « SNATé » vers l'une des adresses IP privées du pare-feu dans AzureFirewallSubnet, ce qui a pour effet de masquer la source à votre pare-feu local.

Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Toutefois, vous pouvez configurer Pare-feu Azure pour qu’il n’effectue **pas** une telle traduction. Pour plus d’informations, consultez [Plages d’adresses IP privées SNAT du Pare-feu Azure](snat-private-range.md).

Une fois que vous avez configuré le pare-feu Azure pour prendre en charge le tunneling forcé, vous ne pouvez pas annuler la configuration. Si vous supprimez toutes les autres configurations IP de votre pare-feu, la configuration IP de gestion est également supprimée et le pare-feu est libéré. L’adresse IP publique attribuée à la configuration IP de gestion ne peut pas être supprimée, mais vous pouvez attribuer une autre adresse IP publique.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide du portail Azure](tutorial-hybrid-portal.md)
