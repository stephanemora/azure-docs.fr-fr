---
title: Configuration de la connectivité de machines virtuelles à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configuration de la connectivité de machines virtuelles à SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167623"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Connexion de machines virtuelles Azure à de grandes instances HANA

Comme mentionné précédemment dans l’article [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), le déploiement minimal de grandes instances HANA avec la couche d’application SAP dans Azure se présente comme suit :

![Réseau virtuel Azure connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

En regardant de plus près du côté du réseau virtuel Azure, on s’aperçoit :

- qu’il est nécessaire de définir un réseau virtuel Azure dans lequel déployer les machines virtuelles de la couche d’application SAP ;
- que cela signifie automatiquement qu’un sous-réseau par défaut est défini dans le réseau virtuel Azure et que c’est ce sous-réseau qui sera effectivement utilisé pour le déploiement des machines virtuelles ;
- que le réseau virtuel Azure créé doit avoir au moins un sous-réseau de machines virtuelles et un sous-réseau de passerelle ExpressRoute. Ces sous-réseaux doivent se voir affecter les plages d’adresses IP spécifiées et décrites dans les sections suivantes.

Nous allons donc étudier d’un peu plus près la création du réseau virtuel Azure pour les grandes instances HANA

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Création du réseau virtuel Azure pour les grandes instances HANA

>[!Note]
>Le réseau virtuel Azure pour une grande instance HANA doit être créé à l’aide du modèle de déploiement Azure Resource Manager. L’ancien modèle de déploiement Azure, communément appelé modèle de déploiement Classic, n’est pas pris en charge avec la solution de grande instance HANA.

Le réseau virtuel peut être créé à l’aide du portail Azure, de PowerShell, d’un modèle Azure ou de l’interface de ligne de commande Azure (voir [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Dans l’exemple suivant, nous allons examiner un réseau virtuel créé via le portail Azure.

Dans les définitions d’un réseau virtuel Azure via le portail Azure, examinons de plus près certaines définitions et leur lien avec les éléments de la liste des différentes plages d’adresses IP. La notion **d’espace d’adressage** dont nous parlons désigne l’espace d’adressage que le réseau virtuel Azure est autorisé à utiliser. Il s’agit également de la plage d’adresses que le réseau virtuel utilise pour la propagation des itinéraires BGP. Cet **espace d’adressage** est illustré ici :

![Espace d’adressage du réseau virtuel Azure affiché dans le portail Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Dans le cas ci-dessus, avec 10.16.0.0/16, le réseau virtuel Azure a été affecté à une plage d’adresses IP relativement importante. Cela signifie que toutes les plages d’adresses IP des sous-réseaux suivants au sein de ce réseau virtuel peuvent s’inscrire dans cet espace d’adressage. Généralement, nous ne recommandons pas une plage d’adresses aussi volumineuse pour un seul réseau virtuel dans Azure. Mais allons plus loin et observons les sous-réseaux définis dans le réseau virtuel Azure :

![Sous-réseaux de réseaux virtuels Azure et plages d’adresses IP associées](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Comme vous pouvez le voir, on obtient un réseau virtuel avec un premier sous-réseau de machines virtuelles (appelé « default ») et un sous-réseau appelé « GatewaySubnet ».
Dans la section suivante, nous faisons référence à la plage d’adresses IP du sous-réseau appelé « default » dans les graphiques en tant que **plage d’adresses IP du sous-réseau de machines virtuelles Azure**. Dans les sections suivantes, nous faisons référence à la plage d’adresses IP du sous-réseau de passerelle en tant que **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**. 

Dans le cas illustré dans les deux graphiques ci-dessus, vous pouvez voir que **l’espace d’adressage de réseau virtuel** couvre à la fois la **plage d’adresses IP du sous-réseau de machines virtuelles Azure** et la **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**. 

Dans les autres cas où vous devez vous montrer prudent ou spécifique avec vos plages d’adresses IP, vous pouvez limiter **l’espace d’adressage de réseau virtuel** d’un réseau virtuel aux plages spécifiques utilisées par chaque sous-réseau. Dans ce cas, vous pouvez définir **l’espace d’adressage de réseau virtuel** d’un réseau virtuel spécifique sous forme de plusieurs plages, comme indiqué ici :

![Espace d’adressage de réseau virtuel Azure avec deux espaces](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Dans ce cas, deux espaces sont définis pour **l’espace d’adressage de réseau virtuel**. Ceux-ci sont identiques aux plages d’adresses IP définies pour la **plage d’adresses IP du sous-réseau de machines virtuelles Azure** et la **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**.

Vous pouvez utiliser n’importe quelle norme d’affectation de noms de votre choix pour ces sous-réseaux locataires (sous-réseaux de machines virtuelles). Toutefois, **il doit toujours y avoir un seul et unique sous-réseau de passerelle pour chaque réseau virtuel** qui se connecte au circuit ExpressRoute SAP HANA sur Azure (grandes instances). Et **ce sous-réseau de passerelle doit toujours être nommé « GatewaySubnet »** pour garantir le positionnement correct de la passerelle ExpressRoute.

> [!WARNING] 
> Il est essentiel que le sous-réseau de passerelle soit toujours nommé « GatewaySubnet ».

Plusieurs sous-réseaux de machine virtuelle peuvent être utilisés, même s’ils utilisent des plages d’adresses non contiguës. Mais comme indiqué précédemment, ces plages d’adresses doivent être couvertes par **l’espace d’adressage de réseau virtuel** du réseau virtuel, soit sous forme agrégée, soit dans une liste des plages exactes des sous-réseaux de machine virtuelle et du sous-réseau de passerelle.

Voici en résumé ce qu’il faut retenir d’un réseau virtuel Azure qui se connecte à de grandes instances HANA :

- Vous devez envoyer à Microsoft **l’espace d’adressage du réseau virtuel** lorsque vous effectuez le déploiement initial de grandes instances HANA. 
- **L’espace d’adressage du réseau virtuel** peut être une plus grande plage qui couvre la ou les plages d’adresses IP du sous-réseau de machine virtuelle Azure et la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel.
- Vous pouvez aussi envoyer en tant **qu’espace d’adressage du réseau virtuel** plusieurs plages couvrant les différentes plages d’adresse IP de la ou des plages d’adresses IP du sous-réseau de machine virtuelle et de la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel.
- **L’espace d’adressage du réseau virtuel** défini est utilisé pour la propagation du routage BGP.
- Le sous-réseau de passerelle doit être nommé **GatewaySubnet**.
- **L’espace d’adressage du réseau virtuel** est utilisé comme filtre du côté de la grande instance HANA afin d’autoriser ou d’interdire le trafic entre Azure et les unités de grandes instances HANA. Vous pouvez rencontrer des problèmes de connectivité si les informations d’itinéraire BGP du sous-réseau Azure et des plages d’adresses IP configurées pour le filtrage du côté de la grande instance HANA ne correspondent pas.
- La section « Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute » fournit quelques détails supplémentaires sur le sous-réseau de passerelle.



## <a name="different-ip-address-ranges-to-be-defined"></a>Différentes plages d’adresses IP à définir 

Nous avons déjà évoqué dans des sections précédentes certaines des plages d’adresses IP nécessaires pour pouvoir déployer de grandes instances HANA. Mais d’autres plages d’adresses IP sont également importantes. Examinons-les plus en détail. Les adresses IP suivantes, qu’il n’est pas nécessaire de toutes envoyer à Microsoft, doivent être définies avant d’envoyer une demande de déploiement initial :

- **Espace d’adressage de réseau virtuel :** comme nous l’avons évoqué précédemment, il s’agit des plages d’adresses IP que vous avez affectées (ou que vous prévoyez d’affecter) à votre paramètre d’espace d’adressage dans les réseaux virtuels Azure qui se connectent à l’environnement de grande instance HANA. Il est recommandé d’utiliser ce paramètre d’espace d’adressage sous la forme d’une valeur multiligne composée des plages de sous-réseau de machines virtuelles Azure et de la plage du sous-réseau de passerelle Azure, comme illustré dans les graphismes ci-dessus. Cette plage NE doit PAS chevaucher les plages d’adresses du pool IP local ou de votre serveur ou encore les plages d’adresses ER-P2P. Comment obtenir ces plages d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doivent vous fournir une ou plusieurs plages d’adresses IP qui ne sont pas utilisées à l’intérieur de votre réseau. Exemple : si votre sous-réseau de machines virtuelles Azure (voir ci-dessus) utilise 10.0.1.0/24 et si votre sous-réseau de passerelles Azure (voir ci-dessous) utilise 10.0.2.0/28, alors votre espace d’adressage de réseau virtuel Azure devrait se trouver sur deux lignes : 10.0.1.0/24 et 10.0.2.0/28. Bien que les valeurs d’espace d’adressage peuvent être agrégées, il est recommandé de les mettre en correspondance avec les plages de sous-réseau afin d’éviter une réutilisation accidentelle de plages d’adresses IP inutilisées dans des espaces d’adressage plus grands ailleurs dans votre réseau à l’avenir. **L’espace d'adressage du réseau virtuel est un plage d’adresses IP qui doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**

- **Plage d’adresses IP du sous-réseau de machines virtuelles Azure :** cette plage d’adresses IP, déjà décrite précédemment, est celle que vous avez affectée (ou que vous prévoyez d’affecter) au paramètre du sous-réseau de réseau virtuel Azure dans votre réseau virtuel Azure qui se connecte à l’environnement de grande instance HANA. Cette plage d’adresses IP est utilisée pour affecter des adresses IP à vos machines virtuelles Azure. Les adresses IP en dehors de cette plage sont autorisées à se connecter aux serveurs de votre grande instance SAP HANA. Si nécessaire, plusieurs sous-réseaux de machines virtuelles Azure peuvent être utilisés. Microsoft recommande d’utiliser un bloc CIDR /24 pour chaque sous-réseau de machines virtuelles Azure. Cette plage d’adresses doit faire partie des valeurs utilisées dans l’espace d’adressage de réseau virtuel Azure. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau.

- **Plage d’adresses IP du sous-réseau de passerelle de réseau virtuel :** selon les fonctionnalités que vous prévoyez d’utiliser, voici la taille recommandée :
   - Passerelle ExpressRoute ultra-performante : bloc d’adresse /26 - obligatoire pour la classe de Type II des références SKU
   - Coexistence avec VPN et ExpressRoute à l’aide d’une passerelle ExpressRoute hautes performances (ou plus petites) : bloc d’adresses /27
   - Toutes les autres situations : bloc d’adresses /28 Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de « l’espace d’adressage de réseau virtuel ». Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de l’espace d’adressage de réseau virtuel Azure que vous devez soumettre à Microsoft. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. 

- **Plage d’adresses pour la connectivité ER-P2P :** il s’agit de la plage d’adresses IP utilisée pour votre connexion ExpressRoute (ER) P2P de grande instance HANA. Cette plage d’adresses IP doit correspondre à la plage CIDR /29. Cette plage NE doit PAS chevaucher les plages d’adresses IP locales ou Azure. Elle est utilisée pour configurer la connectivité ER entre votre passerelle ExpressRoute de réseau virtuel Azure et les serveurs de grandes instances HANA. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. **Cette plage d’adresses IP doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**
  
- **Plage d’adresses du pool d’adresses IP de serveurs :** cette plage d’adresses IP est utilisée pour affecter l’adresse IP aux serveurs de grandes instances HANA. La taille de sous-réseau recommandée est un bloc CIDR /24, mais il est possible d’utiliser un bloc plus petit pourvu qu’il fournisse au minimum 64 adresses IP. Dans cette plage, les 30 premières adresses IP sont réservées à une utilisation par Microsoft. Veillez à en tenir compte lors du choix de la taille de la plage. Cette plage NE doit PAS chevaucher les adresses IP locales ou Azure. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. Un bloc CIDR unique /24 (recommandé) à utiliser pour affecter les adresses IP spécifiques nécessaires à SAP HANA sur Azure (grandes instances). **Cette plage d’adresses IP doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**
 
Bien qu’il soit nécessaire de définir et planifier les plages d’adresses IP ci-dessus, vous n’êtes pas tenu de toutes les transmettre à Microsoft. En résumé, voici les plages d’adresses IP que vous devrez nommer à Microsoft :

- Espace(s) d’adressage du réseau virtuel Azure
- Plage d’adresses pour les connexions ER-P2P
- Plage d’adresses du pool d’adresses IP de serveur

Si vous ajoutez d’autres réseaux virtuels qui doivent se connecter à des grandes instances HANA, vous devrez soumettre à Microsoft le nouvel espace d’adressage de réseau virtuel Azure que vous ajoutez. 

Voici un exemple des différentes plages ainsi que certains exemples de plages que vous devez configurer et éventuellement fournir à Microsoft. Comme vous pouvez le voir, la valeur de l’espace d’adressage de réseau virtuel Azure n’est pas agrégée dans le premier exemple, mais définie à partir des plages de la première plage d’adresses IP de sous-réseau de machines virtuelles Azure et de la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel. Si vous utilisez plusieurs sous-réseaux de machines virtuelles dans le réseau virtuel Azure, vous devrez de la même manière configurer et soumettre les autres plages d’adresses IP des sous-réseaux de machines virtuelles supplémentaires dans le cadre de l’espace d’adressage de réseau virtuel Azure.

![Plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Vous avez également la possibilité d’agréger les données que vous envoyez à Microsoft. Dans ce cas, l’espace d’adressage du réseau virtuel Azure comprend un seul espace. Reprenons les plages d’adresses IP utilisées dans l’exemple précédent. Cet espace d’adressage de réseau virtuel agrégé pourrait ressembler à ceci :

![Deuxième possibilité de plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Comme vous pouvez le voir ci-dessus, au lieu des deux petites plages qui définissaient l’espace d’adressage du réseau virtuel Azure, nous avons maintenant une plus grande plage qui couvre les adresses IP 4096. En définissant un espace d’adressage aussi large, des plages relativement importantes restent inutilisées. Étant donné que les valeurs de l’espace d’adressage du réseau virtuel sont utilisées pour la propagation du routage BGP, l’utilisation des plages inutilisées en local ou ailleurs dans votre réseau peut provoquer des problèmes de routage. Il est donc recommandé de faire en sorte que l’espace d’adressage reste étroitement aligné avec l’espace d’adressage de sous-réseau réellement utilisé. Au besoin, vous pouvez toujours ajouter par la suite de nouvelles valeurs d’espace d’adressage sans que cela occasionne de temps d'arrêt sur le réseau virtuel.
 
> [!IMPORTANT] 
> Les plages d’adresses IP de l’espace d’adressage ER-P2P, du pool d’adresses IP du serveur et du réseau virtuel Azure **NE PEUVENT PAS** se chevaucher ou chevaucher d’autres plages utilisées ailleurs sur votre réseau. Chacune doit être isolée et, comme l’indiquent les deux graphiques ci-dessus, ne peut pas être un sous-réseau d’une autre plage. En cas de chevauchement entre les plages, le réseau virtuel Azure risque de ne pas pouvoir se connecter à un circuit ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Étapes à suivre après la définition des plages d’adresses
Une fois les plages d’adresses IP définies, vous devez procéder comme suit :

1. Envoyer les plages d’adresses IP correspondant à l’espace d’adressage du réseau virtuel Azure, à la connectivité ER-P2P et à la plage d’adresses du pool d’adresses IP du serveur, ainsi que les autres informations détaillées au début du présent document. À ce stade, vous pouvez également commencer à créer le réseau virtuel et les sous-réseaux de machines virtuelles. 
2. Un circuit Express Route est créé par Microsoft entre votre abonnement Azure et le tampon de grande instance HANA.
3. Un réseau locataire est créé sur le tampon de grande instance par Microsoft.
4. Microsoft configure la mise en réseau dans l’infrastructure de SAP HANA sur Azure (grandes instances) pour accepter les adresses IP de votre espace d’adressage de réseau virtuel Azure qui communiquent avec des grandes instances HANA.
5. En fonction de la référence SAP HANA sur Azure (grandes instances) spécifique achetée, Microsoft attribue une unité de calcul dans un réseau de locataire, allouez et montez le stockage et installez le système d’exploitation (SUSE ou Red Hat Linux). Les adresses IP correspondant à ces unités sont retirées de la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée à Microsoft.

À la fin du processus de déploiement, Microsoft vous transmet les données suivantes :
- Informations nécessaires pour connecter vos réseaux virtuels Azure au circuit ExpressRoute qui relie les réseaux virtuels Azure aux grandes instances HANA :
     - Clé(s) d’autorisation
     - PeerID ExpressRoute
- Données d’accès aux grandes instances HANA une fois le circuit ExpressRoute et le réseau virtuel Azure établis.

Vous trouverez également la séquence de connexion de grandes instances HANA dans le document [Installation de bout en bout des grandes instances SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Bon nombre des étapes suivantes sont illustrées par un exemple de déploiement dans ce document. 

**Étapes suivantes**

- Voir [Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute](hana-connect-vnet-express-route.md).