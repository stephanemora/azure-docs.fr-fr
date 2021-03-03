---
title: Résoudre les problèmes liés à Azure Route Server
description: Découvrez comment résoudre les problèmes liés à Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678531"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Résolution des problèmes liés à Azure Route Server

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bgp-connectivity-issues"></a>Problèmes de connectivité avec BGP

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Pourquoi le peering BGP entre ma NVA et Azure Route Server est-il instable (« bagottement ») ?

Le bagottement peut être dû au paramétrage du minuteur BGP. Par défaut, le minuteur Keep Alive sur Azure Route Server est défini sur 60 secondes et le minuteur de sauvegarde est de 180 secondes.

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Pourquoi puis-je effectuer un test ping depuis ma NVA vers l’adresse IP de pair BGP sur Azure Route Server, alors qu’après avoir configuré le peering BGP entre eux, je ne peux plus effectuer de test ping vers la même adresse IP ? Pourquoi le peering BGP tombe-t-il en panne ?

Dans certaines NVA, vous devez ajouter une route statique pour le sous-réseau d’Azure Route Server. Par exemple, si Azure Route Server est dans la plage 10.0.255.0/27 et que votre NVA se trouve dans la plage 10.0.1.0/24, vous devez ajouter la route suivante à la table de routage dans la NVA :

| Routage | Tronçon suivant |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 est l’adresse IP de passerelle par défaut dans le sous-réseau où votre NVA (ou plus précisément, une des cartes réseau) est hébergée.

## <a name="bgp-route-issues"></a>Problèmes de routes BGP

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Pourquoi ma NVA ne reçoit-elle pas de routes d’Azure Route Server même si le peering BGP est opérationnel ?

Le numéro ASN utilisé par Azure Route Server est 65515. Veillez à configurer un autre numéro ASN pour votre NVA afin qu’une session « eBGP » puisse être établie entre votre NVA et Azure Route Server et que la propagation des routes puisse se produire automatiquement.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>Le peering BGP entre ma NVA et Azure Route Server est opérationnel. Je peux voir des routes correctement échangées entre eux. Pourquoi les routes de la NVA ne sont-elles pas dans la table de routage effective de ma machine virtuelle ? 

* Si votre machine virtuelle se trouve dans le même réseau virtuel que votre NVA et Azure Route Server :

     Azure Route Server expose deux adresses IP de pair BGP, qui sont hébergées sur deux machines virtuelles partageant la responsabilité de l’envoi des routes à toutes les autres machines virtuelles exécutées sur votre réseau virtuel. Chacune de vos NVA doit configurer deux sessions BGP identiques (par exemple, utilisez le même numéro AS, le même chemin AS et publiez le même ensemble de routes) sur les deux machines virtuelles afin que vos machines virtuelles sur le réseau virtuel puissent recevoir des informations de routage cohérentes à partir d’Azure Route Server. Reportez-vous au diagramme ci-dessous.

    ![Diagramme montrant une appliance virtuelle réseau avec Route Server.](./media/faq/network-virtual-appliances.png)

    Si vous avez deux instances de la NVA ou plus, vous *pouvez* publier différents chemins AS pour la même route à partir de différentes instances d’appliance virtuelle réseau si vous souhaitez désigner une instance de NVA comme étant active et une autre comme étant passive.

* Si votre machine virtuelle se trouve sur un autre réseau virtuel que celui qui héberge votre NVA et Azure Route Server. Vérifiez si le peering de réseaux virtuels est activé entre les deux réseaux virtuels *et* si l’option Utiliser le serveur de routage distant est activée sur le réseau virtuel de votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
