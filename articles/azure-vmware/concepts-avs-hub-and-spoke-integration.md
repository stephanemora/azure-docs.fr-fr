---
title: 'Concept : intégrer un déploiement Azure VMware Solution dans une architecture hub and spoke'
description: Apprenez-en plus sur les recommandations relatives à l’intégration d’un déploiement Azure VMware Solution dans une architecture hub and spoke existante ou nouvelle sur Azure.
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684496"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Intégrer Azure VMware Solution dans une architecture hub and spoke

Dans cet article, nous fournissons des recommandations relatives à l’intégration d’un déploiement Azure VMware Solution dans une [architecture hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/shared-services) existante ou nouvelle sur Azure. 

Le scénario Hub and Spoke suppose un environnement de cloud hybride avec des charges de travail sur :

* Azure natif utilisant les services IaaS ou PaaS
* Azure VMware Solution 
* vSphere local

## <a name="architecture"></a>Architecture

Le *Hub* est un réseau virtuel Azure qui centralise la connectivité à votre cloud privé local et Azure VMware Solution. Les *Spokes* sont des réseaux virtuels appairés avec le Hub pour permettre la communication entre des réseaux virtuels.

Le trafic entre le centre de données local, le cloud privé Azure VMware Solution et le hub passe par des connexions ExpressRoute. Les réseaux virtuels Spoke contiennent généralement des charges de travail IaaS, mais ils peuvent avoir des services PaaS comme [App Service Environment](../app-service/environment/intro.md), qui a une intégration directe avec le réseau virtuel, ou d’autres services PaaS avec [Azure Private Link](../private-link/index.yml) activé. 

Le diagramme illustre un exemple de déploiement hub and spoke dans Azure connecté au cloud local et Azure VMware Solution via ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Déploiement de l’intégration hub and spoke Azure VMware Solution" border="false":::

L’architecture possède les composants majeurs suivants :

-   **Site local :** Un ou plusieurs centres de données client locaux connectés à Azure via une connexion ExpressRoute.

-   **Cloud privé Azure VMware Solution** SDDC Azure VMware Solution formé par un ou plusieurs clusters vSphere, chacun avec un maximum de 16 nœuds.

-   **Passerelle ExpressRoute :** Active la communication entre le cloud privé Azure VMware Solution, le réseau local, les services partagés sur le réseau virtuel Hub et les charges de travail exécutées sur des réseaux virtuels Spoke.

    > [!NOTE]
    > **Considérations S2S VPN :** Pour les déploiements de production Azure VMware Solution, Azure S2S n’est pas pris en charge en raison des exigences de réseau pour HCX. Toutefois, pour un déploiement PoC ou hors production qui ne requiert pas de HCX, il peut être utilisé.

-   **Réseau virtuel Hub :** Joue le rôle de point central de la connectivité pour votre réseau local cloud privé Azure VMware Solution.

-   **Réseau virtuel Spoke**

    -   **Spoke IaaS :** Un spoke IaaS héberge des charges de travail IaaS Azure, y compris des groupes à haute disponibilité de machines virtuelles et des groupes de machines virtuelles identiques, ainsi que les composants réseau correspondants.

    -   **Spoke PaaS :** Un spoke PaaS héberge des services PaaS Azure à l’aide de l’adressage privé grâce au [Point de terminaison privé](../private-link/private-endpoint-overview.md) et à la [Liaison privée](../private-link/private-link-overview.md).

-   **Pare-feu Azure :** Agit en tant qu’élément central pour segmenter le trafic entre les spokes, le local et Azure VMware Solution.

-   **Application Gateway :** Expose et protège des applications web qui s’exécutent sur des machines virtuelles IaaS/PaaS Azure ou Azure VMware Solution. Il s’intègre à d’autres services tels que la Gestion des API.

## <a name="network-and-security-considerations"></a>Considérations relatives au réseau et à la sécurité

Les connexions ExpressRoute permettent au trafic de circuler entre les sites locaux, Azure VMware Solution et l’infrastructure du réseau Azure. Azure VMware Solution utilise le service [Global Reach d’ExpressRoute](../expressroute/expressroute-global-reach.md) pour implémenter cette connectivité.

La connectivité locale peut également utiliser le service Global Reach d’ExpressRoute, mais ce n’est pas obligatoire.

* **Flux de trafic local vers Azure VMware Solution**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Flux de trafic local vers Azure VMware Solution" border="false":::


* **Flux de trafic Azure VMware Solution vers le réseau virtuel hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Flux de trafic Azure VMware Solution vers le réseau virtuel hub" border="false":::


Pour plus d’informations sur la mise en réseau Azure VMware Solution et les concepts d’interconnexion, consultez la [Documentation produit Azure VMware Solution](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentation du trafic

Le [Pare-feu Azure](../firewall/index.yml) est la partie centrale de la topologie Hub and Spoke, déployée sur le réseau virtuel Hub. Utilisez le Pare-feu Azure ou une autre appliance virtuelle réseau prise en charge par Azure pour établir des règles de trafic et segmenter la communication entre les différents Spokes, les charges de travail locales et Azure VMware Solution.

Créez des tables de routage pour diriger le trafic vers le Pare-feu Azure.  Pour les réseaux virtuels Spoke, créez un itinéraire qui définit l’itinéraire par défaut vers l’interface interne du Pare-feu Azure. Ainsi, lorsqu’une charge de travail dans le réseau virtuel doit atteindre l’espace d’adressage Azure VMware Solution, le pare-feu peut l’évaluer et appliquer la règle de trafic correspondante pour l’autoriser ou la refuser.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Créer des tables de routage pour diriger le trafic vers le Pare-feu Azure":::


> [!IMPORTANT]
> Un itinéraire avec le préfixe d’adresse 0.0.0.0/0 sur le paramètre **GatewaySubnet** n’est pas pris en charge.

Définissez des itinéraires pour des réseaux spécifiques sur la table de routage correspondante. Par exemple, les itinéraires pour atteindre les préfixes IP de la gestion et des charges depuis l’environnement local, et vice versa, acheminant tout le trafic de l’environnement local vers le cloud privé Azure VMware Solution par le biais du Pare-feu Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Définir des itinéraires pour des réseaux spécifiques sur la table de routage correspondante":::

Un deuxième niveau de segmentation du trafic utilisant les groupes de sécurité réseau dans les Spokes et le Hub pour créer une stratégie de trafic plus granulaire. 


### <a name="application-gateway"></a>Application Gateway

L’Application Gateway Azure V1 et V2 ont été testées avec des applications web qui s’exécutent sur des machines virtuelles Azure VMware Solution en tant que pool principal. L’Application Gateway est actuellement la seule méthode prise en charge pour exposer des applications web s’exécutant sur des machines virtuelles Azure VMware Solution à Internet. Elle peut également exposer les applications aux utilisateurs internes en toute sécurité.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Deuxième niveau de segmentation du trafic à l’aide des groupes de sécurité réseau" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox et Azure Bastion

Accédez à l’environnement Azure VMware Solution avec JumpBox, qui est une machine virtuelle Windows 10 ou Windows Server déployée dans le sous-réseau de service partagé au sein du réseau virtuel Hub.

Pour des raisons de sécurité, il est recommandé de déployer le service [Microsoft Azure Bastion](../bastion/index.yml) au sein du réseau virtuel Hub. Azure Bastion fournit un accès RDP et SSH transparent aux machines virtuelles déployées sur Azure sans avoir à approvisionner des adresses IP publiques pour ces ressources. Une fois que vous avez configuré le service Azure Bastion, vous pouvez accéder à la machine virtuelle sélectionnée à partir du Portail Azure. Après avoir établi la connexion, un nouvel onglet s’ouvre et affiche le bureau JumpBox et, à partir de ce bureau, vous pouvez accéder au plan de gestion du cloud privé Azure VMware Solution.

> [!IMPORTANT]
> N’attribuez pas d’adresse IP publique à la machine virtuelle JumpBox ou exposez le port 3389/TCP à l’Internet public. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Réseau virtuel Hub Azure Bastion" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Considérations relatives à la résolution Azure DNS

Pour la résolution Azure DNS, deux options sont disponibles :

-   Utilisez les contrôleurs de domaine Azure Active Directory (Azure AD) déployés sur le Hub (décrits dans [Considérations relatives aux identités](#identity-considerations)) en tant que serveurs de noms.

-   Déployez et configurez une zone privée Azure DNS.

La meilleure approche consiste à combiner les deux pour fournir une résolution de noms fiable pour Azure VMware Solution, sur site et Azure.

En guise de suggestion générale, utilisez l’infrastructure Azure DNS existante (dans ce cas, le DNS intégré à Active Directory) déployée sur au moins deux machines virtuelles Azure déployées dans le réseau virtuel Hub et configurées dans les réseaux virtuels Spoke pour utiliser ces serveurs Azure DNS dans les paramètres DNS.

Vous pouvez toujours utiliser un Azure DNS privé lorsque la zone privée Azure DNS est liée aux réseaux virtuels, et que des serveurs DNS sont utilisés en tant que solutions de résolution hybrides avec un transfert conditionnel vers des noms DNS locaux/Azure VMware Solution tirant profit l’infrastructure client d’Azure DNS privé.

Il y a plusieurs considérations à prendre en compte pour les zones privées Azure DNS :

* L’inscription automatique doit être activée pour qu’Azure DNS gère automatiquement le cycle de vie des enregistrements DNS pour les machines virtuelles déployées au sein de réseaux virtuels Spoke.
* Le nombre maximal de zones DNS privées auxquelles un réseau virtuel peut être lié avec l’inscription automatique activée est limité à une.
* Le nombre maximal de zones DNS privées pouvant être liées à un réseau virtuel est de 1 000 sans l’activation de l’inscription automatique.

Vous pouvez configurer des serveurs locaux et Azure VMware Solution avec des redirecteurs conditionnels vers des machines virtuelles de résolution dans Azure pour la zone privée Azure DNS.

## <a name="identity-considerations"></a>Identité - Éléments à prendre en compte

À des fins d’identité, la meilleure approche consiste à déployer au moins un contrôleur de domaine AD sur le Hub, à l’aide du sous-réseau de service partagé, idéalement deux d’entre eux dans le mode distribué dans une zone ou un groupe à haute disponibilité de machines virtuelles. Consultez [Centre des architectures Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) pour étendre votre domaine AD local à Azure.

En outre, déployez un autre contrôleur de domaine sur le côté Azure VMware Solution pour agir en tant qu’identité et source DNS au sein de l’environnement vSphere.

Pour vCenter et SSO, définissez une source d’identité dans le Portail Azure, sur **Gérer \> Identité \> Sources d’identité**.

Comme meilleure pratique recommandée, intégrez [domaine AD avec Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
