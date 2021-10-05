---
title: Configuration de composants réseau NSX avec Azure VMware Solution
description: Découvrez comment utiliser Azure VMware Solution pour configurer des segments de réseau NSX-T.
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700663"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>Configuration de composants réseau NSX avec Azure VMware Solution

Un cloud privé Azure VMware Solution est fourni par défaut avec NSX-T. Il est préprovisionné avec une passerelle NSX-T de niveau 0 en mode **Actif/Actif** et une passerelle NSX-T de niveau 1 par défaut en mode Actif/En attente.  Ces passerelles vous permettent de connecter les segments (commutateurs logiques) et de fournir une connectivité Est-Ouest et Nord-Sud. 

Après avoir déployé Azure VMware Solution, vous pouvez configurer les objets NSX-T nécessaires sur le Portail Azure.  Celui-ci présente un affichage simplifié des opérations NSX-T dont un administrateur VMware a besoin quotidiennement. Cet affichage s’adresse aux utilisateurs qui ne connaissent pas NSX-T Manager.  

Quatre options s’offrent à vous pour configurer les composants NSX-T dans la console Azure VMware Solution :

- **Segments** : créez des segments qui s’affichent dans NSX-T Manager et vCenter. Pour plus d’informations, consultez [Ajouter un segment NSX-T à l’aide du portail Azure](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment).

- **DHCP** : créez un serveur DHCP ou un relais DHCP si vous envisagez d’utiliser DHCP.  Pour plus d’informations, consultez [Utiliser le portail Azure pour créer un serveur DHCP ou un relais](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay).

- **Mise en miroir des ports** : créez une mise en miroir des ports pour aider à résoudre les problèmes réseau. Pour plus d’informations, consultez [Configurer la mise en miroir des ports dans le portail Azure](configure-port-mirroring-azure-vmware-solution.md).

- **DNS** : créez un redirecteur DNS pour envoyer les requêtes DNS à un serveur DNS désigné à des fins de résolution.  Pour plus d’informations, consultez [Configurer un redirecteur DNS dans le portail Azure](configure-dns-azure-vmware-solution.md).

>[!IMPORTANT]
>Vous avez toujours accès à la console NSX-T Manager, où vous pouvez utiliser les paramètres avancés mentionnés et d’autres fonctionnalités NSX-T. 

