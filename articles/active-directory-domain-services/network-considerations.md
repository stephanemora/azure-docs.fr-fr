---
title: Planification et connexions réseau pour Azure AD Domain Services | Microsoft Docs
description: Apprenez-en davantage sur certaines considérations et ressources relatives à la conception du réseau virtuel utilisées pour la connectivité lorsque vous exécutez Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: iainfou
ms.openlocfilehash: 1a6fb12311fe4474f03c22c91d9b478220adf5d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425530"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Considérations relatives à la conception du réseau virtuel et options de configuration pour Azure AD Domain Services

Azure Active Directory Domain Services (AD DS) fournissant des services d’authentification et de gestion à d’autres applications et charges de travail, la connectivité réseau est un composant clé. Si les ressources de réseau virtuel ne sont pas correctement configurées, les applications et les charges de travail ne peuvent pas communiquer avec les fonctionnalités fournies par Azure AD DS ni les utiliser. Si vous planifiez correctement votre réseau virtuel, vous vous assurez qu’Azure AD DS soit en mesure de traiter vos applications et vos charges selon vos besoins.

Cet article décrit les considérations et les exigences relatives à la conception qui sont requises pour un réseau virtuel Azure prenant en charge Azure AD DS.

## <a name="azure-virtual-network-design"></a>Conception du réseau virtuel Azure

Pour assurer la connectivité réseau et autoriser les applications et les services à s’authentifier auprès d’Azure AD DS, vous devez utiliser un réseau virtuel et un sous-réseau Azure. Dans l’idéal, Azure AD DS doit être déployé dans son propre réseau virtuel. Vous pouvez inclure un sous-réseau d’application distinct dans le même réseau virtuel pour héberger votre machine virtuelle de gestion ou vos charges de travail pour les petites applications. Un réseau virtuel distinct pour les charges de travail d’application plus volumineuses ou complexes homologuées au réseau virtuel Azure AD DS, est généralement la conception la plus appropriée. Si vous respectez les conditions décrites dans les sections suivantes pour le réseau virtuel et le sous-réseau, vous pouvez choisir d’autres conceptions.

Tout au long de votre conception du réseau virtuel pour Azure AD DS, les considérations suivantes s’appliquent :

* Azure AD DS doit être déployé dans la même région Azure que votre réseau virtuel.
    * À ce stade, vous ne pouvez déployer qu’un seul domaine managé Azure AD DS par locataire Azure AD. Le domaine managé Azure AD DS est déployé sur une seule région. Assurez-vous de créer ou de sélectionner un réseau virtuel dans [une région qui prend en charge Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Tenez compte de la proximité des autres régions Azure et des réseaux virtuels qui hébergent les charges de travail de votre application.
    * Pour réduire la latence, gardez vos applications principales à proximité du sous-réseau du réseau virtuel, à dans la même région que celui-ci, pour votre domaine managé par Azure AD DS. Vous pouvez utiliser le peering du réseau virtuel ou les connexions de réseau privé virtuel (VPN) entre les réseaux virtuels Azure.
* Le réseau virtuel ne peut pas s'appuyer sur des services DNS autres que ceux fournis par Azure AD DS.
    * Azure AD DS fournit son propre service DNS. Le réseau virtuel doit être configuré pour utiliser ces adresses de service DNS. La résolution de noms pour des espaces de noms supplémentaires peut être accomplie à l’aide de redirecteurs conditionnels.
    * Vous ne pouvez pas utiliser les paramètres de serveur DNS personnalisés pour diriger des requêtes en provenance d’autres serveurs DNS, y compris sur des machines virtuelles. Les ressources du réseau virtuel doivent utiliser le service DNS fourni par Azure AD DS.

> [!IMPORTANT]
> Vous ne pouvez pas déplacer Azure AD DS vers un autre réseau virtuel une fois le service activé.

Un domaine managé Azure AD DS se connecte à un sous-réseau dans un réseau virtuel Azure. Concevez ce sous-réseau pour Azure AD DS en prenant en compte les considérations suivantes :

* Azure AD DS doit être déployé dans son propre sous-réseau. N’utilisez pas un sous-réseau existant ou un sous-réseau de passerelle.
* Un groupe de sécurité réseau est créé pendant le déploiement d’un domaine managé Azure AD DS. Ce groupe de sécurité réseau contient les règles requises pour permettre une communication de service appropriée.
    * Ne créez et n’utilisez pas un groupe de sécurité réseau existant avec vos propres règles personnalisées.
* Azure AD DS nécessite 3 à 5 adresses IP. Assurez-vous que la plage d’adresses IP de votre sous-réseau peut fournir ce nombre d’adresses.
    * La restriction des adresses IP disponibles peut empêcher Azure AD Domain Services de gérer deux contrôleurs de domaine.

L’exemple de diagramme suivant présente une conception valide dans laquelle Azure AD DS possède son propre sous-réseau. Le sous-réseau de passerelle pour la connectivité externe et les charges de travail d’application sont dans un sous-réseau connecté du réseau virtuel :

![Conception de sous-réseau recommandée](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Connexions au réseau virtuel Azure AD DS

Comme indiqué dans la section précédente, vous pouvez uniquement créer un domaine managé Azure AD Domain Services dans un réseau virtuel unique dans Azure et un seul domaine managé par locataire Azure AD. Si l’on se base sur cette architecture, vous devrez peut-être connecter un ou plusieurs réseaux virtuels qui hébergent les charges de travail de votre application sur votre réseau virtuel Azure AD DS.

Vous pouvez connecter des charges de travail d’application hébergées sur d’autres réseaux virtuels Azure en utilisant l’une des méthodes suivantes :

* Peering de réseau virtuel
* Réseau privé virtuel (VPN)

### <a name="virtual-network-peering"></a>Peering de réseaux virtuels

VNET Peering est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Vous pouvez connecter des réseaux virtuels dans différentes régions à l’aide du peering de réseaux virtuels mondiaux. Une fois homologués, les deux réseaux virtuels permettent aux ressources, telles que les machines virtuelles, de communiquer directement entre elles à l’aide d’adresses IP privées. Le peering de réseaux virtuels vous permet de déployer un domaine managé Azure AD DS géré avec les charges de travail d’applications déployées dans d’autres réseaux virtuels.

![Connexion entre des réseaux virtuels à l’aide d’un peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Pour plus d’informations, consultez la page [Vue d’ensemble du peering du réseau virtuel Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking"></a>Réseau privé virtuel

Vous pouvez connecter deux réseaux virtuels (connexion de réseau virtuel à réseau virtuel) de la même manière que vous pouvez configurer un réseau virtuel à un emplacement de site local. Ces deux connexions font appel à une passerelle VPN pour créer un tunnel sécurisé utilisant Ipsec/IKE. Ce modèle de connexion vous permet de déployer Azure AD DS dans un réseau virtuel Azure, puis de connecter des emplacements locaux ou d’autres clouds.

![Connexion entre des réseaux virtuels à l’aide d’une passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Pour plus d’informations sur l’utilisation de réseaux privés virtuels, consultez la page [Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Résolution de noms lors de la connexion de réseaux virtuels

Les réseaux virtuels connectés au réseau virtuel Azure AD Domain Services disposent généralement de leurs propres paramètres DNS. Lorsque vous connectez des réseaux virtuels, cela ne permet pas de configurer automatiquement la résolution de noms du réseau virtuel en cours de connectés afin de résoudre les services fournis par le domaine managé Azure AD DS. La résolution de noms de réseaux virtuels en cours de connexion doit être configurée pour permettre aux charges de travail d’application de localiser Azure AD Domain Services.

Vous pouvez activer la résolution de noms à l’aide de redirecteurs DNS conditionnels sur le serveur DNS qui prend en charge les réseaux virtuels connectés, ou en utilisant les mêmes adresses IP DNS à partir du réseau virtuel Azure AD Domain Service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Ressources réseau utilisées par Azure AD DS

Un domaine managé Azure AD DS crée des ressources réseau au cours du déploiement. Ces ressources sont nécessaires au bon fonctionnement et à la bonne gestion du domaine managé Azure AD DS et ne doivent pas être configurées manuellement.

| Ressource Azure                          | Description |
|:----------------------------------------|:---|
| Cartes d'interface réseau                  | Azure AD DS héberge le domaine managé sur deux contrôleurs de domaine (DC) qui s’exécutent sur Windows Server en tant que machines virtuelles Azure. Chaque machine virtuelle dispose d’une interface réseau virtuelle qui se connecte à votre sous-réseau de réseau virtuel. |
| Adresse IP publique standard dynamique         | Azure AD DS communique avec le service de synchronisation et de gestion à l’aide d’une adresse IP publique de référence SKU standard. Pour plus d’informations sur les adresse IP publique, consultez la page [Types d’adresses IP et méthodes d’allocation dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Azure Standard Load Balancer               | Azure AD DS utilise un équilibreur de charge SKU standard pour la traduction d’adresses réseau (NAT) et l’équilibrage de charge (en cas d’utilisation avec le protocole LDAP sécurisé). Pour plus d’informations sur les équilibreurs de charge Azure, consultez [Qu’est-ce que Azure Load Balancer ?](../load-balancer/load-balancer-overview.md) |
| Règles de traduction d’adresses réseau (NAT) | Azure AD DS crée et utilise trois règles NAT sur l’équilibreur de charge : une règle pour le trafic HTTP sécurisé et deux règles pour une communication à distance PowerShell sécurisé. |
| Règles d'équilibrage de charge                     | Lorsqu’un domaine managé Azure AD DS est configuré pour le LDAP sécurisé sur le port TCP 636, trois règles sont créées et utilisées sur un équilibreur de charge pour répartir le trafic. |

> [!WARNING]
> Ne supprimez aucune des ressources réseau créées par Azure AD DS. Si vous supprimez une des ressources réseau, le service Azure AD DS est interrompu.

## <a name="network-security-groups-and-required-ports"></a>Groupes de sécurité réseau et ports requis

Un [groupe de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contient la liste des règles qui autorisent ou rejettent le trafic réseau vers le trafic d’un réseau virtuel Azure. Un groupe de sécurité réseau est créé lorsque vous déployez Azure AD DS qui contient un ensemble de règles permettant au service de fournir des fonctions d’authentification et de gestion. Ce groupe de sécurité réseau par défaut est associé au sous-réseau de réseau virtuel dans lequel votre domaine managé Azure AD DS est déployé.

Les règles de groupe de sécurité réseau suivantes sont requises pour permettre à Azure AD DS de fournir des services d’authentification et de gestion. Ne modifiez pas et ne supprimez pas ces règles de groupe de sécurité réseau pour le sous-réseau de réseau virtuel dans lequel votre domaine managé Azure AD DS est déployé.

| Numéro de port | Protocol | Source                             | Destination | Action | Obligatoire | Objectif |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Quelconque         | Allow  | Oui      | Synchronisation avec votre locataire Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Quelconque         | Allow  | Oui      | Gestion de votre domaine. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Quelconque         | Allow  | Oui      | Gestion de votre domaine. |
| 636         | TCP      | Quelconque                                | Quelconque         | Allow  | Non       | Activé uniquement lorsque vous configurez le LDAP sécurisé (LDAPS). |

> [!WARNING]
> Ne modifiez pas manuellement ces configurations et ressources réseau. Lorsque vous associez un groupe de sécurité réseau mal configuré ou une table d’itinéraire définie par l’utilisateur au sous-réseau dans lequel Azure AD DS est déployé, vous risquez de perturber la capacité de Microsoft à traiter et à gérer le domaine. La synchronisation entre votre locataire Azure AD et votre domaine managé Azure AD DS est également interrompue.
>
> Règles par défaut pour *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* et *DenyAllOutBound* existent également pour le groupe de sécurité réseau. Ne modifiez pas et ne supprimez pas ces règles par défaut.
>
> Le SLA Azure ne s’applique pas aux déploiements dans lesquels un groupe de sécurité réseau mal configuré et/ou des tables d’itinéraire définies par l’utilisateur ont été appliqués et empêchent Azure AD DS de mettre à jour et de gérer votre domaine.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 – synchronisation avec Azure AD

* Utilisé pour synchroniser votre locataire Azure AD avec votre domaine managé Azure AD DS.
* Sans accès à ce port, votre domaine managé Azure AD DS ne peut pas être synchronisé avec votre locataire Azure AD. Les utilisateurs risquent de ne pas pouvoir se connecter, car les modifications apportées à leurs mots de passe ne sont pas synchronisées avec votre domaine managé Azure AD DS.
* L’accès entrant à ce port aux adresses IP est limité par défaut à l'aide de la balise de service **AzureActiveDirectoryDomainServices**.
* Ne limitez pas l’accès sortant à partir de ce port.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 – gestion à l’aide du bureau à distance

* Utilisé pour connecter le bureau à distance aux contrôleurs de domaine dans votre domaine managé Azure AD DS.
* La règle de groupe de sécurité réseau par défaut utilise la balise de service *CorpNetSaw* pour restreindre davantage le trafic.
    * Cette balise de service autorise uniquement les stations de travail à accès sécurisé sur le réseau d’entreprise Microsoft à utiliser le bureau à distance pour le domaine managé Azure AD DS.
    * L’accès est autorisé uniquement avec la justification métier, par exemple pour les scénarios de gestion ou de résolution des problèmes.
* Cette règle peut être définie sur *Refuser* et définie uniquement sur *Autoriser* quand cela est nécessaire. La plupart des tâches de gestion et de surveillance sont effectuées à l’aide de la communication à distance PowerShell. Le RDP n'est utilisé que dans les rares cas où Microsoft a besoin de se connecter à distance à votre domaine managé pour une résolution des problèmes avancée.

> [!NOTE]
> Vous ne pouvez pas sélectionner manuellement la balise de service *CorpNetSaw* dans le portail si vous essayez de modifier cette règle de groupe de sécurité réseau. Vous devez utiliser Azure PowerShell ou Azure CLI pour configurer manuellement une règle qui utilise la balise de service *CorpNetSaw*.

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 – -gestion à l’aide de la communication à distance PowerShell

* Utilisé pour effectuer des tâches de gestion à l’aide de la communication à distance PowerShell sur votre domaine managé Azure AD DS.
* Sans accès à ce port, votre domaine managé Azure AD DS ne peut pas être mis à jour, configuré, sauvegardé ou surveillé.
* Pour les domaines managés Azure AD DS qui utilisent un réseau virtuel basé sur gestionnaire des ressources, vous pouvez restreindre l’accès entrant à ce port à la balise de service *AzureActiveDirectoryDomainServices*.
    * Pour les domaines managés Azure AD DS hérités utilisant un réseau virtuel classique, vous pouvez restreindre l’accès entrant à ce port aux adresses IP sources suivantes : *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, et *104.40.87.209*.

## <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur

Les itinéraires définis par l’utilisateur ne sont pas créés par défaut et ne sont pas nécessaires au bon fonctionnement d’Azure AD DS. Si vous devez utiliser des tables d’itinéraire, évitez de modifier l’itinéraire *0.0.0.0*. Les modifications apportées à cet itinéraire peuvent perturber Azure AD Domain Services.

Vous devez également acheminer le trafic entrant à partir des adresses IP incluses dans les balises de service Azure respectives vers le sous-réseau Azure AD Domain Services. Pour plus d’informations sur les balises de service et leur adresse IP associée , consultez la page [Plages et balises de service Azure IP – Cloud public](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Ces plages IP de centre de données Azure peuvent être modifiées sans préavis. Vérifiez que vous disposez des processus vous permettant de valider que vous disposez des dernières adresses IP.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur certaines ressources réseau et options de connexion utilisées par Azure AD DS, consultez les articles suivants :

* [Peering de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md)
* [Passerelles VPN Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Groupes de sécurité réseau Azure](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->
