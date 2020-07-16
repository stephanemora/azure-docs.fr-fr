---
title: 'Concept : intégrer un déploiement Azure VMware Solution (AVS) dans une architecture hub and spoke'
description: Apprenez-en plus sur les recommandations relatives à l’intégration d’un déploiement Azure VMware Solution (AVS) dans une architecture hub and spoke existante ou nouvelle sur Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374958"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Intégrer Azure VMware Solution (AVS) dans une architecture hub and spoke

Dans cet article, nous fournissons des recommandations relatives à l’intégration d’un déploiement Azure VMware Solution (AVS) dans une [architecture Hub and Spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) existante ou nouvelle sur Azure. 

Le scénario Hub and Spoke suppose un environnement de cloud hybride avec des charges de travail sur :

* Azure natif utilisant les services IaaS ou PaaS
* AVS 
* vSphere local

## <a name="architecture"></a>Architecture

Le *Hub* est un réseau virtuel Azure qui centralise la connectivité à votre cloud privé local et AVS. Les *Spokes* sont des réseaux virtuels appairés avec le Hub pour permettre la communication entre des réseaux virtuels.

Le trafic entre le centre de données local, le cloud privé AVS et le Hub passe par des connexions ExpressRoute. Les réseaux virtuels Spoke contiennent généralement des charges de travail IaaS, mais ils peuvent avoir des services PaaS comme [App Service Environment](../app-service/environment/intro.md), qui a une intégration directe avec le réseau virtuel, ou d’autres services PaaS avec [Azure Private Link](https://docs.microsoft.com/azure/private-link/) activé. 

Le diagramme illustre un exemple de déploiement Hub and Spoke dans Azure connecté au cloud local et AVS via ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Déploiement de l’intégration AVS Hub and Spoke":::




L’architecture possède les composants majeurs suivants :

-   **Site local :** Un ou plusieurs centres de données client locaux connectés à Azure via une connexion ExpressRoute.

-   **Cloud privé AVS :** SDDC AVS formé par un ou plusieurs clusters vSphere, chacun avec un maximum de 16 nœuds.

-   **Passerelle ExpressRoute :** Active la communication entre le cloud privé AVS, le réseau local, les services partagés sur le réseau virtuel Hub et les charges de travail exécutées sur des réseaux virtuels Spoke.

    > [!NOTE]
    > **Considérations S2S VPN :** Pour les déploiements de production AVS, Azure S2S n’est pas pris en charge en raison des exigences de réseau pour HCX. Toutefois, pour un déploiement PoC ou hors production qui ne requiert pas de HCX, il peut être utilisé.

-   **Réseau virtuel Hub :** Joue le rôle de point central de la connectivité pour votre réseau local cloud privé AVS.

-   **Réseau virtuel Spoke**

    -   **Spoke IaaS :** Un spoke IaaS héberge des charges de travail IaaS Azure, y compris des groupes à haute disponibilité de machines virtuelles et des groupes de machines virtuelles identiques, ainsi que les composants réseau correspondants.

    -   **Spoke PaaS :** Un spoke PaaS héberge des services PaaS Azure à l’aide de l’adressage privé grâce au [Point de terminaison privé](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) et à la [Liaison privée](https://docs.microsoft.com/azure/private-link/private-link-overview).

-   **Pare-feu Azure :** Agit en tant qu’élément central pour segmenter le trafic entre les Spokes, le local et AVS.

-   **Application Gateway :** Expose et protège des applications web qui s’exécutent sur des machines virtuelles IaaS/PaaS Azure ou AVS. Il s’intègre à d’autres services tels que la Gestion des API.

## <a name="network-and-security-considerations"></a>Considérations relatives au réseau et à la sécurité

Les connexions ExpressRoute permettent au trafic de circuler entre les sites locaux, AVS et l’infrastructure du réseau Azure. AVS utilise le service [Global Reach d’ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) pour implémenter cette connectivité.

La connectivité locale peut également utiliser le service Global Reach d’ExpressRoute, mais ce n’est pas obligatoire.

* **Flux de trafic local vers AVS**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Flux de trafic local vers AVS":::


* **Flux de trafic VNET AVS vers Hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Flux de trafic de réseau virtuel AVS vers Hub":::


Pour plus d’informations sur la mise en réseau AVS et les concepts d’interconnexion, consultez la [Documentation produit AVS](https://docs.microsoft.com/azure/azure-vmware/concepts-networking).

### <a name="traffic-segmentation"></a>Segmentation du trafic

Le [Pare-feu Azure](https://docs.microsoft.com/azure/firewall/) est la partie centrale de la topologie Hub and Spoke, déployée sur le réseau virtuel Hub. Utilisez le Pare-feu Azure ou une autre appliance virtuelle réseau prise en charge par Azure pour établir des règles de trafic et segmenter la communication entre les différents Spokes, les charges de travail locales et AVS.

Créez des tables de routage pour diriger le trafic vers le Pare-feu Azure.  Pour les réseaux virtuels Spoke, créez un itinéraire qui définit l’itinéraire par défaut vers l’interface interne du Pare-feu Azure. Ainsi, lorsqu’une charge de travail dans le réseau virtuel doit atteindre l’espace d’adressage AVS, le pare-feu peut l’évaluer et appliquer la règle de trafic correspondante pour l’autoriser ou la refuser.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Créer des tables de routage pour diriger le trafic vers le Pare-feu Azure":::


> [!IMPORTANT]
> Un itinéraire avec le préfixe d’adresse 0.0.0.0/0 sur le paramètre **GatewaySubnet** n’est pas pris en charge.

Définissez des itinéraires pour des réseaux spécifiques sur la table de routage correspondante. Par exemple, les itinéraires pour atteindre les préfixes IP de la gestion et des charges depuis l’environnement local, et vice versa, acheminant tout le trafic de l’environnement local vers le cloud privé AVS par le biais du Pare-feu Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Définir des itinéraires pour des réseaux spécifiques sur la table de routage correspondante":::

Un deuxième niveau de segmentation du trafic utilisant les groupes de sécurité réseau dans les Spokes et le Hub pour créer une stratégie de trafic plus granulaire. 


### <a name="application-gateway"></a>Application Gateway

L’Application Gateway Azure V1 et V2 ont été testées avec des applications web qui s’exécutent sur des machines virtuelles AVS en tant que pool principal. L’Application Gateway est actuellement la seule méthode prise en charge pour exposer des applications web s’exécutant sur des machines virtuelles AVS à Internet. Elle peut également exposer les applications aux utilisateurs internes en toute sécurité.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Deuxième niveau de segmentation du trafic à l’aide des groupes de sécurité réseau":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox et Azure Bastion

Accédez à l’environnement AVS avec JumpBox, qui est une machine virtuelle Windows 10 ou Windows Server déployée dans le sous-réseau de service partagé au sein du réseau virtuel Hub.

Pour des raisons de sécurité, il est recommandé de déployer le service [Microsoft Azure Bastion](https://docs.microsoft.com/azure/bastion/) au sein du réseau virtuel Hub. Azure Bastion fournit un accès RDP et SSH transparent aux machines virtuelles déployées sur Azure sans avoir à approvisionner des adresses IP publiques pour ces ressources. Une fois que vous avez configuré le service Azure Bastion, vous pouvez accéder à la machine virtuelle sélectionnée à partir du Portail Azure. Après avoir établi la connexion, un nouvel onglet s’ouvre et affiche le bureau JumpBox et, à partir de ce bureau, vous pouvez accéder au plan de gestion du cloud privé AVS.

> [!IMPORTANT]
> N’attribuez pas d’adresse IP publique à la machine virtuelle JumpBox ou exposez le port 3389/TCP à l’Internet public. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Réseau virtuel Hub Azure Bastion":::


## <a name="azure-dns-resolution-considerations"></a>Considérations relatives à la résolution Azure DNS

Pour la résolution Azure DNS, deux options sont disponibles :

-   Utilisez les contrôleurs de domaine Azure Active Directory (Azure AD) déployés sur le Hub (décrits dans [Considérations relatives aux identités](#identity-considerations)) en tant que serveurs de noms.

-   Déployez et configurez une zone privée Azure DNS.

La meilleure approche consiste à combiner les deux pour fournir une résolution de noms fiable pour AVS, sur site et Azure.

En guise de suggestion générale, utilisez l’infrastructure Azure DNS existante (dans ce cas, le DNS intégré à Active Directory) déployée sur au moins deux machines virtuelles Azure déployées dans le réseau virtuel Hub et configurées dans les réseaux virtuels Spoke pour utiliser ces serveurs Azure DNS dans les paramètres DNS.

Vous pouvez toujours utiliser un Azure DNS privé lorsque la zone privée Azure DNS est liée aux réseaux virtuels, et que des serveurs DNS sont utilisés en tant que solutions de résolution hybrides avec un transfert conditionnel vers des noms DNS locaux/AVS tirant profit l’infrastructure client d’Azure DNS privé.

Il y a plusieurs considérations à prendre en compte pour les zones privées Azure DNS :

* L’inscription automatique doit être activée pour qu’Azure DNS gère automatiquement le cycle de vie des enregistrements DNS pour les machines virtuelles déployées au sein de réseaux virtuels Spoke.
* Le nombre maximal de zones DNS privées auxquelles un réseau virtuel peut être lié avec l’inscription automatique activée est limité à une.
* Le nombre maximal de zones DNS privées pouvant être liées à un réseau virtuel est de 1 000 sans l’activation de l’inscription automatique.

Vous pouvez configurer des serveurs locaux et AVS avec des redirecteurs conditionnels vers des machines virtuelles de résolution dans Azure pour la zone privée Azure DNS.

## <a name="identity-considerations"></a>Identité - Éléments à prendre en compte

À des fins d’identité, la meilleure approche consiste à déployer au moins un contrôleur de domaine AD sur le Hub, à l’aide du sous-réseau de service partagé, idéalement deux d’entre eux dans le mode distribué dans une zone ou un groupe à haute disponibilité de machines virtuelles. Consultez [Centre des architectures Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) pour étendre votre domaine AD local à Azure.

En outre, déployez un autre contrôleur de domaine sur le côté AVS pour agir en tant qu’identité et source DNS au sein de l’environnement vSphere.

Pour vCenter et SSO, définissez une source d’identité dans le Portail Azure, sur **Gérer \> Identité \> Sources d’identité**.

Comme meilleure pratique recommandée, intégrez [domaine AD avec Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


