---
title: Configuration de la connectivité de machines virtuelles à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configuration de la connectivité à partir des machines virtuelles pour utiliser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df60b31ce950cc6c242c8077e59d90c41771e4c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239493"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Connexion de machines virtuelles Azure à de grandes instances HANA

L’article [What ' s SAP HANA sur Azure (grandes Instances) ?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) mentionne que le déploiement minimal de grandes Instances HANA avec la couche d’application SAP dans Azure ressemble à ceci :

![Réseau virtuel Azure connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image1-architecture.png)

Une observation situé sur le côté de réseau virtuel Azure, il est nécessaire pour :

- qu’il est nécessaire de définir un réseau virtuel Azure dans lequel déployer les machines virtuelles de la couche d’application SAP ;
- qu’il est nécessaire de définir un sous-réseau par défaut dans le réseau virtuel Azure qui est réellement celui dans lequel les machines virtuelles sont déployées ;
- que le réseau virtuel Azure créé doit avoir au moins un sous-réseau de machines virtuelles et un sous-réseau de passerelle de réseau virtuel Azure ExpressRoute. Ces sous-réseaux doivent se voir affecter les plages d’adresses IP spécifiées et décrites dans les sections suivantes.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Créer le réseau virtuel Azure pour les grandes Instances HANA

>[!Note]
>Le réseau virtuel Azure pour grandes instances HANA doit être créé à l’aide du modèle de déploiement Azure Resource Manager. L’ancien modèle de déploiement Azure, communément appelé modèle de déploiement Classic, n’est pas pris en charge par la solution de grande instance HANA.

Pour créer le réseau virtuel, vous pouvez vous servir du portail Azure, de PowerShell, d’un modèle Azure ou d’Azure CLI. (Pour plus d’informations, consultez [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Dans l’exemple suivant, le réseau virtuel qui nous intéresse est créé à l’aide du portail Azure.

Lorsque vous faites référence à la **espace d’adressage** dans cette documentation, à l’espace d’adressage du réseau virtuel Azure est autorisé à utiliser. Il s’agit aussi de la plage d’adresses que le réseau virtuel utilise pour la propagation des itinéraires BGP. Cet **espace d’adressage** est illustré ici :

![Espace d’adressage d’un réseau virtuel Azure affiché sur le portail Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Dans l’exemple précédent, avec 10.16.0.0/16, le réseau virtuel Azure a été affecté une plage d’adresses IP au lieu de cela grand et large à utiliser. Par conséquent, toutes les plages d’adresses IP des sous-réseaux suivants au sein de ce réseau virtuel peuvent s’inscrire dans cet espace d’adressage. En règle générale, nous déconseillons l’utilisation d’une plage d’adresses d’une telle ampleur pour un même réseau virtuel dans Azure. Mais penchons-nous sur les sous-réseaux définis dans le réseau virtuel Azure :

![Sous-réseaux de réseau virtuel Azure et leurs plages d’adresses IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Nous voyons ici un réseau virtuel avec un premier sous-réseau de machines virtuelles (appelé « default ») et un sous-réseau appelé « GatewaySubnet ».

Dans les deux graphiques précédentes, le **espace d’adressage de réseau virtuel** couvre à la fois **la plage d’adresses de sous-réseau IP de la machine virtuelle Azure** et celle de la passerelle de réseau virtuel.

Vous pouvez restreindre l’**espace d’adressage de réseau virtuel** aux plages spécifiques utilisées par chaque sous-réseau. Vous pouvez également définir l’**espace d’adressage de réseau virtuel** d’un réseau virtuel comme étant constitué de plusieurs plages spécifiques, comme indiqué ici :

![Espace d’adressage de réseau virtuel Azure avec deux espaces](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Dans ce cas, deux espaces sont définis pour l’**espace d’adressage de réseau virtuel**. Ils sont identiques aux plages d’adresses IP définies pour la plage d’adresses IP de sous-réseau de la machine virtuelle Azure et de la passerelle de réseau virtuel. 

Vous pouvez utiliser n’importe quelle norme d’affectation de noms de votre choix pour ces sous-réseaux locataires (sous-réseaux de machines virtuelles). Cependant, **il doit toujours y avoir un seul et unique sous-réseau de passerelle pour chaque réseau virtuel** qui se connecte au circuit ExpressRoute SAP HANA sur Azure (grandes instances). **Ce sous-réseau de passerelle doit être nommé « GatewaySubnet »** pour vous assurer que la passerelle ExpressRoute est correctement placée.

> [!WARNING] 
> Il est essentiel que le sous-réseau de passerelle soit toujours nommé « GatewaySubnet ».

Vous pouvez utiliser plusieurs sous-réseaux de machines virtuelles et des plages d’adresses non contiguës. Ces plages d’adresses doivent être couverte par l’**espace d’adressage de réseau virtuel** du réseau virtuel. Elles peuvent se présenter sous une forme agrégée. Elles peuvent aussi figurer dans une liste regroupant les plages exactes des sous-réseaux de machines virtuelles et du sous-réseau de passerelle.

Voici un résumé des aspects importants d’un réseau virtuel Azure qui se connecte à de grandes Instances HANA :

- Vous devez envoyer le **espace d’adressage de réseau virtuel** à Microsoft lorsque vous effectuez un déploiement initial de grandes Instances HANA. 
- L’**espace d’adressage de réseau virtuel** peut être une plage plus large couvrant les plages d’adresses IP de sous-réseau des machines virtuelles Azure et de la passerelle de réseau virtuel.
- Vous pouvez aussi soumettre plusieurs plages couvrant les différentes plages d’adresse IP de la ou des plages d’adresses IP du sous-réseau de machines virtuelles et la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel.
- L’**espace d’adressage du réseau virtuel** défini est utilisé pour la propagation du routage BGP.
- Le nom du sous-réseau de passerelle doit être : **« GatewaySubnet »** .
- L’espace d’adressage sert de filtre du côté de la grande instance HANA pour autoriser ou interdire le trafic entre Azure et les unités de grandes instances HANA. Les informations de routage BGP du réseau virtuel Azure doivent correspondre aux plages d’adresses IP configurées pour le filtrage du côté de la grande instance HANA. À défaut, des problèmes de connectivité peuvent survenir.
- La section **Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute** fournit des détails sur le sous-réseau de passerelle, que nous évoquons ci-après.



## <a name="different-ip-address-ranges-to-be-defined"></a>Différentes plages d’adresses IP à définir 

Certaines des plages d’adresses IP qui sont nécessaires pour le déploiement de grandes Instances HANA a été commercialisée déjà. Mais d’autres plages d’adresses IP sont tout aussi importantes. Pas toutes les plages d’adresses IP suivantes doivent être envoyés à Microsoft. En revanche, vous devez les définir avant d’envoyer une demande de déploiement initial :

- **Espace d’adressage du réseau virtuel** : L’**espace d’adressage de réseau virtuel** est constitué par les plages d’adresses IP que vous attribuez à votre paramètre d’espace d’adressage dans les réseaux virtuels Azure. Ces réseaux se connectent à l’environnement de grande Instance SAP HANA. Il est recommandé que ce paramètre d’espace d’adressage soit une valeur multiligne. Il doit être composé de la plage de sous-réseau de la machine virtuelle Azure et des plages de sous-réseau de la passerelle Azure. Cette plage de sous-réseau a été présentée dans les illustrations précédentes. Elle NE doit PAS chevaucher les plages d’adresses du pool d’adresses IP local ou du serveur, ou les plages d’adresses ER-P2P. Comment obtenir ces plages d’adresses IP ? Votre fournisseur d’équipe ou un service de réseau d’entreprise doit fournir une ou plusieurs adresses IP ou les plages qui ne sont pas utilisées à l’intérieur de votre réseau. Par exemple, le sous-réseau de votre machine virtuelle Azure est 10.0.1.0/24 et le sous-réseau de votre sous-réseau de passerelle Azure est 10.0.2.0/28.  Nous recommandons que votre espace d’adressage de réseau virtuel Azure est défini comme : 10.0.1.0/24 et 10.0.2.0/28. Même si les valeurs d’espace d’adresse peuvent être agrégées, nous vous recommandons de les faire correspondre aux plages de sous-réseau. Vous éviterez ainsi une réutilisation accidentelle de plages d’adresses IP non utilisées contenues dans des espaces d’adressage plus larges ailleurs dans votre réseau. **L’espace d’adressage de réseau virtuel est une plage d’adresses IP. Vous devez l’envoyer à Microsoft lorsque vous demandez un déploiement initial**.
- **Plage d’adresses IP de sous-réseau de machine virtuelle Azure :** Cette plage d’adresses IP est celle que vous affectez au paramètre de sous-réseau de réseau virtuel Azure. Ce paramètre se trouve dans votre réseau virtuel Azure et se connecte à l’environnement de grande Instance SAP HANA. Cette plage d’adresses IP est utilisée pour affecter des adresses IP à vos machines virtuelles Azure. Les adresses IP en dehors de cette plage sont autorisées à se connecter aux serveurs de votre grande instance SAP HANA. Si nécessaire, vous pouvez utiliser plusieurs sous-réseaux de machines virtuelles Azure. Nous vous recommandons d’utiliser un bloc CIDR /24 pour chaque sous-réseau de machines virtuelles Azure. Cette plage d’adresses doit faire partie des valeurs utilisées dans l’espace d’adressage de réseau virtuel Azure. Comment obtenir cette plage d’adresses IP ? L’équipe de votre réseau d’entreprise ou votre fournisseur de services doit fournir une plage d’adresses IP non utilisée à l’intérieur de votre réseau.
- **Plage d’adresses IP de sous-réseau de passerelle de réseau virtuel :** Selon les fonctionnalités que vous projetez d’utiliser, la taille recommandée est :
   - Passerelle ExpressRoute ultra-performante : bloc d’adresses /26 (obligatoire pour la classe Type II des références SKU).
   - Coexistence avec un VPN et ExpressRoute en utilisant une passerelle de réseau virtuel ExpressRoute hautes performances (ou plus petite) : bloc d’adresses /27.
   - Toutes les autres situations : bloc d’adresses /28 Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de l’« espace d’adressage de réseau virtuel ». Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de l’espace d’adressage de réseau virtuel Azure que vous soumettez à Microsoft. Comment obtenir cette plage d’adresses IP ? L’équipe réseau de votre entreprise ou votre fournisseur de services doit fournir une plage d’adresses IP qui n’est pas actuellement utilisée à l’intérieur de votre réseau. 
- **Plage d’adresses pour la connectivité ER-P2P :** il s’agit de la plage d’adresses IP utilisée pour votre connexion ExpressRoute (ER) P2P de grande instance SAP HANA. Cette plage d’adresses IP doit correspondre à la plage CIDR /29. Cette plage NE doit PAS chevaucher les plages d’adresses IP locales ou Azure. Elle sert à configurer la connectivité ER entre votre passerelle virtuelle ExpressRoute et les serveurs de grandes instances SAP HANA. Comment obtenir cette plage d’adresses IP ? L’équipe réseau de votre entreprise ou votre fournisseur de services doit fournir une plage d’adresses IP qui n’est pas actuellement utilisée à l’intérieur de votre réseau. **Cette plage est une plage d’adresses IP. Vous devez l’envoyer à Microsoft lorsque vous demandez un déploiement initial**.  
- **Plage d’adresses du pool d’adresses IP des serveurs :** cette plage d’adresses IP est utilisée pour l’affectation de l’adresse IP aux serveurs de grandes instances HANA. La taille de sous-réseau recommandée est un bloc CIDR /24. Elle peut être plus petite, si nécessaire, avec 64 adresses IP. Dans cette plage, les 30 premières adresses IP sont réservées à une utilisation par Microsoft. Veillez à tenir compte de ce point au moment de choisir la taille de la plage. Cette plage NE doit PAS chevaucher les adresses IP locales ou Azure. Comment obtenir cette plage d’adresses IP ? L’équipe réseau de votre entreprise ou votre fournisseur de services doit fournir une plage d’adresses IP qui n’est pas actuellement utilisée à l’intérieur de votre réseau.  **Il s’agit d’une plage d’adresses IP qui doit être soumise à Microsoft au moment de demander un déploiement initial**.

Facultatifs plages d’adresses IP qui finalement avoir besoin d’être envoyée à Microsoft :

- Si vous choisissez d’utiliser [ExpressRoute de portée mondiale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) pour activer le routage direct sur site aux unités de grande Instance HANA, vous devez réserver /29 une autre plage d’adresses IP. Cette plage peut se chevauchent pas avec un des autres plages d’adresses IP que vous avez définies auparavant.
- Si vous choisissez d’utiliser [ExpressRoute de portée mondiale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) pour activer le routage direct d’un locataire de grande Instance HANA dans une région Azure à un autre client de grande Instance HANA dans une autre région Azure, vous devez réserver /29 une autre plage d’adresses IP . Cette plage peut se chevauchent pas avec un des autres plages d’adresses IP que vous avez définies auparavant.

Pour plus d’informations sur l’utilisation de grandes instances HANA et de portée mondiale ExpressRoute, consultez les documents :

- [Architecture de réseau SAP HANA (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Connecter un réseau virtuel aux grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Vous devez définir et planifier les plages d’adresses IP décrites précédemment. Toutefois, vous n’avez pas besoin de les transmettre toutes à Microsoft. Voici les plages d’adresses IP que vous devrez nommer à Microsoft :

- Espace(s) d’adressage de réseau virtuel Azure
- Plage d’adresses pour les connexions ER-P2P
- Plage d’adresses de pool d’adresses IP de serveurs

Si vous ajoutez des réseaux virtuels supplémentaires destinés à se connecter à de grandes instances HANA, vous devez soumettre le nouvel espace d’adressage de réseau virtuel Azure que vous ajoutez à Microsoft. 

Voici un exemple des différentes plages ainsi que certains exemples de plages que vous devez configurer et éventuellement fournir à Microsoft. La valeur pour l’espace d’adressage de réseau virtuel Azure n’est pas agrégée dans le premier exemple. Toutefois, elle est définie à partir de la première plage d’adresses IP de sous-réseau de machines virtuelles Azure et de la plage d’adresses IP de sous-réseau de passerelle de réseau virtuel. 

Vous pouvez utiliser plusieurs sous-réseaux de machines virtuelles dans le réseau virtuel Azure au moment de configurer et de soumettre les autres plages d’adresses IP des sous-réseaux de machines virtuelles supplémentaires dans le cadre de l’espace d’adressage de réseau virtuel Azure.

![Plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Le graphique n’affiche pas l’adresse IP supplémentaire ou les plages qui sont requis pour l’utilisation facultative de portée mondiale ExpressRoute.

Vous pouvez aussi agréger les données que vous soumettez à Microsoft. Dans ce cas, l’espace d’adressage du réseau virtuel Azure comprend un seul espace. En reprenant les plages d’adresses IP de l’exemple précédent, voici à quoi ressemble l’espace d’adressage de réseau virtuel agrégé :

![Deuxième possibilité de plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Dans l’exemple, à la place des deux petites plages qui définissaient l’espace d’adressage du réseau virtuel Azure, nous avons maintenant une plage plus large qui couvre 4 096 adresses IP. Du fait de la grandeur de l’espace d’adressage défini, des plages relativement importantes restent inutilisées. Comme les valeurs d’espace d’adressage de réseau virtuel sont utilisées pour la propagation d’itinéraires BGP, l’utilisation des plages non utilisées en local ou ailleurs dans votre réseau peut occasionner des problèmes de routage. Le graphique n’affiche pas l’adresse IP supplémentaire ou les plages qui sont requis pour l’utilisation facultative de portée mondiale ExpressRoute.

Nous vous recommandons de conserver l’espace d’adressage reste étroitement aligné avec l’espace d’adressage de sous-réseau réel que vous utilisez. Au besoin, vous pouvez toujours ajouter par la suite de nouvelles valeurs d’espace d’adressage sans que cela occasionne de temps d’arrêt sur le réseau virtuel.
 
> [!IMPORTANT] 
> Les plages d’adresses IP de la connectivité ER-P2P, du pool d’adresses IP de serveur et de l’espace d’adressage de réseau virtuel Azure **ne doivent pas** se chevaucher entre elles ni avec une autre plage utilisée dans votre réseau. Chacune doit être discrète. Comme le montrent les deux illustrations précédentes, elles ne peuvent pas non plus être un sous-réseau d’une autre plage. En cas de chevauchement entre les plages, le réseau virtuel Azure risque de ne pas pouvoir se connecter au circuit ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Étapes à suivre après la définition des plages d’adresses
Une fois les plages d’adresses IP définies, voici les prochaines étapes à effectuer :

1. Soumettre les plages d’adresses IP correspondant à l’espace d’adressage du réseau virtuel Azure, à la connectivité ER-P2P et à la plage d’adresses du pool d’adresses IP de serveur, ainsi que les autres informations détaillées au début du présent document. À ce stade, vous pouvez aussi commencer à créer le réseau virtuel et les sous-réseaux de machines virtuelles. 
2. Un circuit Express Route est créé par Microsoft entre votre abonnement Azure et le tampon de grande instance HANA.
3. Un réseau locataire est créé sur le tampon de grande instance par Microsoft.
4. Microsoft configure la mise en réseau dans l’infrastructure de SAP HANA sur Azure (grandes instances) pour accepter les adresses IP de votre espace d’adressage de réseau virtuel Azure qui communiquent avec les grandes instances HANA.
5. Selon la référence SKU spécifique de SAP HANA sur Azure (grandes Instances) que vous avez achetée, Microsoft attribue une unité Compute dans un réseau de locataire. Il alloue et monte un stockage, et installe également le système d’exploitation (SUSE ou Red Hat Linux). Les adresses IP correspondant à ces unités sont retirées de la plage d’adresses du pool d’adresses IP de serveur que vous avez soumise à Microsoft.

À la fin du processus de déploiement, Microsoft vous transmet les données suivantes :
- Informations vous permettant de connecter vos réseaux virtuels Azure au circuit ExpressRoute qui relie les réseaux virtuels Azure aux grandes instances HANA :
     - Clé(s) d’autorisation
     - PeerID ExpressRoute
- Données permettant d’accéder aux grandes instances HANA une fois le circuit ExpressRoute et le réseau virtuel Azure établis.

La séquence de connexion des grandes instances HANA est également indiquée dans le document [Installation de SAP HANA sur Azure (grandes instances)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Bon nombre des étapes suivantes sont illustrées par un exemple de déploiement dans ce document. 

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute](hana-connect-vnet-express-route.md).
