---
title: Présentation de la préversion d’Azure Firewall Manager
description: En savoir plus sur les fonctionnalités Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366272"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Présentation de la préversion d’Azure Firewall Manager

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager en préversion est un service de gestion de la sécurité qui fournit une stratégie de sécurité centralisée et la gestion des itinéraires pour les périmètres de sécurité basés sur le cloud. 

Firewall Manager assure la gestion de la sécurité pour deux types d’architecture réseau :

- **hub virtuel sécurisé**

   Un [hub Azure Virtual WAN](../virtual-wan/virtual-wan-about.md#resources) est une ressource managée par Microsoft qui vous permet de créer facilement des architectures « Hub and Spoke ». Lorsque les stratégies de sécurité et de routage sont associées à un hub, elles sont appelées *[hub virtuel sécurisé](secured-virtual-hub.md)* . 
- **réseau virtuel hub**

   Il s’agit d’un réseau virtuel Azure standard que vous créez et gérez vous-même. Quand des stratégies de sécurité sont associées à un hub de ce type, on parle de *réseau virtuel hub*. Pour l’instant, seule la stratégie de pare-feu Azure est prise en charge. Vous pouvez appairer des réseaux virtuels spoke qui contiennent vos services et serveurs de charge de travail. Vous pouvez également gérer les pare-feu dans des réseaux virtuels autonomes qui ne sont associés à aucun réseau spoke.

Pour avoir une comparaison détaillée des architectures de *hub virtuel sécurisé* et de *réseau virtuel hub*, consultez [Quelles sont les options d’architecture d’Azure Firewall Manager ?](vhubs-and-vnets.md)

![firewall-manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Fonctionnalités de la préversion d’Azure Firewall Manager

La préversion d’Azure Firewall Manager offre les fonctionnalités suivantes :

### <a name="central-azure-firewall-deployment-and-configuration"></a>Configuration et déploiement centralisés du Pare-feu Azure

Vous pouvez déployer et configurer de manière centralisée plusieurs instances du Pare-feu Azure qui s’étendent sur différentes régions et différents abonnements Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Stratégies hiérarchiques (mondiales et locales)

Vous pouvez utiliser la préversion d’Azure Firewall Manager pour gérer de manière centralisée les stratégies de Pare-feu Azure sur plusieurs hubs virtuels sécurisés. Vos équipes informatiques centrales peuvent créer des stratégies de pare-feu globales pour appliquer la stratégie de pare-feu à l’échelle de l’organisation et dans différentes équipes. Les stratégies de pare-feu créées localement autorisent un modèle en libre-service DevOps qui offre une meilleure agilité.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Intégration à une sécurité de tiers en tant que service pour une sécurité avancée

En plus du Pare-feu Azure, vous pouvez intégrer des fournisseurs SECaaS (Security as a service) tiers afin de fournir une protection réseau supplémentaire pour vos connexions Internet de réseau virtuel et de succursale.

Cette fonctionnalité est disponible uniquement avec les déploiements de hubs virtuels sécurisés.

- Filtrage du trafic de réseau virtuel à Internet (V2I, VNet to Internet)

   - Filtrez le trafic réseau virtuel sortant avec votre fournisseur de sécurité tiers préféré.
   - Tirez parti de la protection Internet avancée prenant en charge l’utilisateur pour vos charges de travail cloud s’exécutant sur Azure.

- Filtrage du trafic de succursale à Internet (B2I, Branch to Internet)

   Tirez parti de votre connectivité Azure et de votre distribution mondiale pour ajouter facilement un filtrage tiers dans le cadre des scénarios de type « filiale vers Internet ».

Pour plus d’informations sur les fournisseurs de sécurité approuvés, consultez [Présentation des partenaires de sécurité de confiance Azure Firewall Manager (préversion)](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gestion de routes centralisée

Routez facilement le trafic vers votre hub sécurisé pour le filtrage et la journalisation sans qu’il soit nécessaire de configurer manuellement des routes définies par l’utilisateur sur des réseaux virtuels spoke. 

Cette fonctionnalité est disponible uniquement avec les déploiements de hubs virtuels sécurisés.

Vous pouvez utiliser des fournisseurs tiers pour le filtrage du trafic B2I (Branch to Internet), côte à côte avec le Pare-feu Azure pour le filtrage Branch to VNet (B2V), VNet to VNet (V2V) et VNet to Internet (V2I). Vous pouvez également utiliser des fournisseurs tiers pour le filtrage du trafic V2I tant que le Pare-feu Azure n’est pas requis pour le filtrage B2V ou V2V. 

## <a name="region-availability"></a>Disponibilité des régions

Les stratégies de pare-feu Azure sont utilisables dans plusieurs régions. Par exemple, vous pouvez créer une stratégie dans la région USA Ouest et l’utiliser dans la région USA Est. 

## <a name="known-issues"></a>Problèmes connus

Les problèmes connus de la préversion d’Azure Firewall Manager sont les suivants :

|Problème  |Description  |Limitation des risques  |
|---------|---------|---------|
|Limitations du filtrage tiers.|Le filtrage du trafic V2I avec des fournisseurs tiers n’est pas pris en charge avec le Pare-feu Azure B2V et V2V.|Enquête|
|La division du trafic n’est pas prise en charge.|Le fractionnement du trafic Office 365 et du trafic PaaS public Azure n’est pas pris en charge actuellement. Par conséquent, la sélection d’un fournisseur tiers pour V2I ou B2I envoie également tout le trafic PaaS public Azure et le trafic Office 365 via le service partenaire.|Enquête sur la division du trafic au niveau du hub.
|Un seul hub virtuel sécurisé par région.|Vous ne pouvez pas avoir plus d’un hub virtuel sécurisé par région.|Créez plusieurs réseaux étendus virtuels dans une région.|
|Les stratégies de base doivent se trouver dans la même région que la stratégie locale.|Créez toutes vos stratégies locales dans la même région que la stratégie de base. Vous pouvez toujours appliquer une stratégie qui a été créée dans une région sur un hub sécurisé à partir d’une autre région.|Enquête|
|La communication entre les hubs ne fonctionne pas pour les hubs virtuels sécurisés|La communication de hub virtuel sécurisé à hub virtuel sécurisé n’est pas encore prise en charge.|Enquête|
|Tous les hubs virtuels sécurisés partageant le même réseau étendu virtuel doivent se trouver dans le même groupe de ressources.|Ce comportement est aujourd’hui cohérent avec les hubs Virtual WAN.|Créez plusieurs réseaux étendus virtuels pour permettre la création de hubs virtuels sécurisés dans différents groupes de ressources.|
|Les groupes IP ne sont pas pris en charge dans la stratégie de pare-feu.|Les groupes IP sont en préversion publique et sont pris en charge uniquement avec les règles de pare-feu classiques.|Correction en cours.
|Les abonnements de fournisseurs de solutions Cloud ne sont pas pris en charge.|Les [abonnements de fournisseurs de solutions Cloud](https://azure.microsoft.com/offers/ms-azr-0145p/) ne sont pas pris en charge.|Enquête

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble du déploiement d’Azure Firewall Manager en préversion](deployment-overview.md)
- En savoir plus sur les [hubs virtuels sécurisés](secured-virtual-hub.md)