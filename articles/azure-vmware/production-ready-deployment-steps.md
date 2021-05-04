---
title: Planifier le déploiement d’Azure VMware Solution
description: Cet article décrit un workflow de déploiement d’Azure VMware Solution.  Le résultat final est un environnement prêt pour la création et la migration des machines virtuelles.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 04/27/2021
ms.openlocfilehash: c9fba2009ec20a613ebf37adc2493145d7471711
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145390"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Planifier le déploiement d’Azure VMware Solution

Ce guide de démarrage rapide vous indique le processus de planification permettant d’identifier et de collecter les informations dont vous avez besoin pour votre déploiement. À mesure que vous planifiez votre déploiement, veillez à documenter les informations que vous collectez pour les référencer facilement pendant le déploiement.

Les étapes décrites vous permettent d’obtenir un environnement prêt pour la production en vue de la création de machines virtuelles et de la migration. 

>[!TIP]
>Pour suivre les données que vous allez collecter, utilisez la [check-list de planification de VMware HCX](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/).


## <a name="request-a-host-quota"></a>Demander un quota d’hôte 

Il est important de demander un quota d’hôte au plus tôt, quand vous vous préparez à créer votre ressource Azure VMware Solution. Comme vous pouvez demander un quota d’hôte maintenant, vous êtes prêt à déployer le cloud privé Azure VMware Solution dès lors que le processus de planification est terminé. Une fois que l’équipe du support technique a reçu votre demande de quota d’hôte, cela prend jusqu’à cinq jours ouvrés pour la confirmer et vous allouer des hôtes. Si vous avez un cloud privé Azure VMware Solution et que vous voulez que davantage d’hôtes vous soient alloués, suivez le même processus. Pour plus d’informations, consultez les liens suivants, en fonction du type d’abonnement que vous avez :
- [Clients EA](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
- [Clients CSP](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="identify-the-subscription"></a>Identifier l’abonnement

Identifiez l’abonnement que vous prévoyez d’utiliser pour déployer Azure VMware Solution.  Vous pouvez soit créer un nouvel abonnement, soit réutiliser un abonnement existant.

>[!NOTE]
>L’abonnement doit être associé à un Contrat Entreprise Microsoft ou à un plan Azure pour fournisseurs de solutions cloud. Pour plus d’informations, voir [Comment activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md).

## <a name="identify-the-resource-group"></a>Identifier le groupe de ressources

Identifiez le groupe de ressources que vous souhaitez utiliser pour votre Azure VMware Solution.  En règle générale, un groupe de ressources est créé spécifiquement pour Azure VMware Solution, mais vous pouvez utiliser un groupe de ressources existant.

## <a name="identify-the-region-or-location"></a>Identifier la région ou la localisation

Identifiez la région dans laquelle vous souhaitez déployer Azure VMware Solution.  Pour plus d’informations, consultez le guide [Disponibilité des produits Azure par région](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="identify-the-resource-name"></a>Identifier le nom de la ressource

Définissez le nom de la ressource que vous allez utiliser au cours du déploiement.  Le nom de la ressource est le nom convivial et descriptif que vous utilisez pour votre cloud privé Azure VMware Solution.

>[!IMPORTANT]
>Le nom ne doit pas comporter plus de 40 caractères. Si le nom dépasse cette limite, vous ne pourrez pas créer d’adresses IP publiques à utiliser avec le cloud privé. 

## <a name="identify-the-size-hosts"></a>Identifier les tailles d’hôtes

Identifiez les tailles d’hôtes que vous souhaitez utiliser lors du déploiement d’Azure VMware Solution.  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>Déterminer le nombre de clusters et d’hôtes

Votre premier déploiement d’Azure VMware Solution est constitué d’un cloud privé contenant un seul cluster. Pour votre déploiement, vous devez définir le nombre d’hôtes que vous souhaitez déployer sur le premier cluster.

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>Définir le segment d’adresse IP pour la gestion du cloud privé

La première étape de la planification du déploiement consiste à planifier la segmentation IP. Azure VMware Solution nécessite un réseau CIDR /22. Cet espace d’adressage est décomposé en segments réseau plus petits (sous-réseaux) et utilisé pour les segments de gestion Azure VMware Solution, notamment les fonctionnalités vCenter, VMware HCX, NSX-T et vMotion. La visualisation ci-dessous met en évidence où ce segment sera utilisé.

Ce bloc d’adresses réseau CIDR /22 ne doit pas empiéter sur un segment réseau figurant déjà dans votre environnement local ou dans Azure.

**Exemple :** 10.0.0.0/22

Pour obtenir une description détaillée de la façon dont le réseau CIDR /22 est décomposé par cloud privé, consultez la [Check-list de planification réseau](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifier : segment d’adresse IP" border="false":::  

## <a name="define-the-ip-address-segment-for-vm-workloads"></a>Définir le segment d’adresse IP pour les charges de travail de machine virtuelle

Comme pour tout environnement VMware, les machines virtuelles doivent se connecter à un segment réseau. Dans Azure VMware Solution, il existe deux types de segments : les segments étendus L2 (abordés plus loin) et les segments réseau NSX-T. À mesure que le déploiement de production d’Azure VMware Solution s’étend, il existe souvent une combinaison de segments étendus L2 locaux et de segments réseau NSX-T locaux. Pour planifier le déploiement initial, dans Azure VMware Solution, identifiez un segment réseau unique (réseau IP). Ce réseau ne doit pas empiéter sur les segments réseau locaux ou sur le reste d’Azure, et il ne doit pas se trouver dans le segment réseau /22 défini plus tôt.

Ce segment réseau est principalement utilisé à des fins de test lors du déploiement initial.

>[!NOTE]
>Ce ou ces réseaux ne seront pas nécessaires pendant le déploiement. Ils sont créés en guise d’étape post-déploiement.
  
**Exemple :** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifier : segment d’adresse IP pour les charges de travail des machines virtuelles" border="false":::     

## <a name="define-the-virtual-network-gateway"></a>Définir la passerelle de réseau virtuel

>[!IMPORTANT]
>Vous pouvez vous connecter à une passerelle de réseau virtuel dans un réseau Azure Virtual WAN, mais celle-ci sort du cadre du présent guide de démarrage rapide.

Un cloud privé Azure VMware Solution nécessite un réseau virtuel Azure et un circuit ExpressRoute.  

Déterminez si vous voulez utiliser une passerelle de réseau virtuel ExpressRoute *existante* OU *Nouvelle*.  Si vous décidez d’utiliser une *nouvelle* passerelle de réseau virtuel, vous allez la créer après avoir créé votre cloud privé. Il est toléré d’utiliser une passerelle de réseau virtuel ExpressRoute existante, et à des fins de planification, notez la passerelle de réseau virtuel ExpressRoute que vous allez utiliser. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Diagramme qui montre le réseau virtuel Azure attaché à VMware Azure Solution" border="false":::



## <a name="define-vmware-hcx-network-segments"></a>Définir des segments réseau VMware HCX

VMware HCX est une technologie qui regroupée avec Azure VMware Solution. Les principaux cas d’utilisation de VMware HCX sont les migrations de charge de travail et la récupération d’urgence. Si vous envisagez d’effectuer l’une ou l’autre, il est préférable de planifier la mise en réseau maintenant. Dans le cas contraire, vous pouvez passer à l’étape suivante.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="determine-whether-to-extend-your-networks"></a>Déterminer si vous allez étendre vos réseaux

Vous pouvez éventuellement étendre des segments réseau depuis un site local vers Azure VMware Solution. 

Si vous le faites, identifiez ces réseaux maintenant en suivant ces instructions :

- Les réseaux doivent se connecter à un [commutateur vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) dans votre environnement VMware local.  
- Les réseaux qui se trouvent sur un [commutateur vSS (vSphere Standard Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) ne peuvent pas être étendus.

>[!IMPORTANT]
>Ces réseaux sont étendus en guise de dernière étape de configuration, et non lors du déploiement.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez rassemblé et documenté les informations nécessaires, passez à la section suivante pour créer votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer Azure VMware Solution](deploy-azure-vmware-solution.md)
> 
