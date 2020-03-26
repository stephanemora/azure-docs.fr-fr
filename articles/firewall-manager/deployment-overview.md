---
title: Vue d’ensemble du déploiement d’Azure Firewall Manager Preview
description: Découvrez les étapes de déploiement de haut niveau requises pour Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443123"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Vue d’ensemble du déploiement d’Azure Firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview peut être déployé de différentes manières, mais la procédure générale suivante est recommandée.

## <a name="general-deployment-process"></a>Processus général de déploiement

### <a name="hub-virtual-networks"></a>Réseaux virtuels hub

1.  Créer une stratégie de pare-feu

    - Créer une nouvelle stratégie
<br>*or*<br>
    - Dérivez une stratégie de base et personnalisez une stratégie locale
<br>*or*<br>
    - Importez des règles à partir d’un pare-feu Azure existant. N’oubliez pas de supprimer les règles NAT des stratégies qui doivent être appliquées sur plusieurs pare-feu
1. Créer une architecture hub and spoke
   - Créez un réseau virtuel hub avec Azure Firewall Manager et associez-lui des réseaux virtuels spoke à l’aide du peering de réseaux virtuels
<br>*or*<br>
    - Créez un réseau virtuel, ajoutez des connexions de réseau virtuel et associez au nouveau réseau virtuel des réseaux virtuels spoke à l’aide du peering de réseaux virtuels

3. Sélectionnez les fournisseurs de sécurité auxquels vous associez la stratégie de pare-feu. Actuellement, le Pare-feu Azure est le seul fournisseur pris en charge.

   - Effectuez cette opération en même temps que vous créez un réseau virtuel hub
<br>*or*<br>
    - Convertissez un réseau virtuel existant en réseau virtuel hub. Il est également possible de convertir plusieurs réseaux virtuels.

4. Configurer les routes définies par l’utilisateur pour acheminer le trafic vers le pare-feu de votre réseau virtuel hub.


### <a name="secured-virtual-hubs"></a>Hubs virtuels sécurisés

1. Créer une architecture hub and spoke

   - Créez un hub virtuel sécurisé à l’aide d’Azure Firewall Manager et ajoutez des connexions de réseau virtuel.<br>*or*<br>
   - Créez un hub Virtual WAN et ajoutez des connexions de réseau virtuel.
2. Sélectionner les fournisseurs de sécurité

   - Effectué lors de la création du hub virtuel sécurisé.<br>*or*<br>
   - Convertissez un hub Virtual WAN existant en hub virtuel sécurisé.
3. Créer une stratégie de pare-feu et l’associer à votre hub

   - Applicable uniquement si vous utilisez le Pare-feu Azure.
   - Les stratégies SECaaS (security as a service) tierces sont configurées via l’expérience de gestion des partenaires.
4. Configurer les paramètre de routage pour acheminer le trafic vers votre hub sécurisé

   - Acheminez facilement le trafic vers votre hub sécurisé pour le filtrage et la journalisation sans itinéraires définis par l’utilisateur sur des réseaux virtuels spoke à l’aide de la page Paramètre de routage du hub virtuel sécurisé.

> [!NOTE]
> - Seul un hub par WAN virtuel par région est autorisé. Vous pouvez cependant ajouter plusieurs WAN virtuels dans la région.
> - Les espaces d’adresses IP des hubs ne doivent pas se chevaucher dans un vWAN.
> - Vos connexions de réseau virtuel de hub doivent se trouver dans la même région que le hub.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Sécuriser votre réseau cloud avec Azure Firewall Manager Preview à l’aide du portail Azure](secure-cloud-network.md)