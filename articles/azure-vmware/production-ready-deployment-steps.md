---
title: Planification du déploiement d’Azure VMware Solution
description: Cet article décrit un workflow de déploiement d’Azure VMware Solution.  Le résultat final est un environnement prêt pour la création et la migration des machines virtuelles.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107242"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planification du déploiement d’Azure VMware Solution

Cet article vous présente le processus de planification permettant d’identifier et de collecter les informations à utiliser pendant le déploiement. À mesure que vous planifiez votre déploiement, veillez à documenter les informations que vous collectez pour les référencer facilement pendant le déploiement.

Les étapes décrites dans ce guide de démarrage rapide vous permettent d’obtenir un environnement prêt pour la production en vue de la création de machines virtuelles et de la migration. 

Pour suivre les données que vous allez collecter, consultez la [check-list de planification de HCX](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/).

> [!IMPORTANT]
> Il est important de demander un quota d’hôte au plus tôt, quand vous vous préparez à créer votre ressource Azure VMware Solution. Comme vous pouvez demander un quota d’hôte maintenant, vous êtes prêt à déployer le cloud privé Azure VMware Solution dès lors que le processus de planification est terminé. Une fois que l’équipe du support technique a reçu votre demande de quota d’hôte, cela prend jusqu’à cinq jours ouvrés pour la confirmer et vous allouer des hôtes. Si vous avez un cloud privé Azure VMware Solution et que vous voulez que davantage d’hôtes vous soient alloués, suivez le même processus. Pour plus d’informations, consultez les liens suivants, en fonction du type d’abonnement que vous avez :
> - [Clients EA](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [Clients CSP](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>Abonnement

Identifiez l’abonnement que vous prévoyez d’utiliser pour déployer Azure VMware Solution.  Vous pouvez soit créer un nouvel abonnement, soit réutiliser un abonnement existant.

>[!NOTE]
>L’abonnement doit être associé à un Contrat Entreprise Microsoft ou à un plan Azure pour fournisseurs de solutions cloud. Pour plus d’informations, voir [Comment activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Resource group

Identifiez le groupe de ressources que vous souhaitez utiliser pour votre Azure VMware Solution.  En règle générale, un groupe de ressources est créé spécifiquement pour Azure VMware Solution, mais vous pouvez utiliser un groupe de ressources existant.

## <a name="region"></a>Région

Identifiez la région dans laquelle vous souhaitez déployer Azure VMware Solution.  Pour plus d’informations, consultez le guide [Disponibilité des produits Azure par région](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nom de la ressource

Définissez le nom de la ressource que vous allez utiliser au cours du déploiement.  Le nom de la ressource est le nom convivial et descriptif que vous utilisez pour votre cloud privé Azure VMware Solution.

>[!IMPORTANT]
>Le nom ne doit pas comporter plus de 40 caractères. Si le nom dépasse cette limite, vous ne pourrez pas créer d’adresses IP publiques à utiliser avec le cloud privé. 

## <a name="size-hosts"></a>Dimensionner les hôtes

Identifiez les tailles d’hôtes que vous souhaitez utiliser lors du déploiement d’Azure VMware Solution.  Pour obtenir une liste complète, consultez la documentation relative aux [clouds privés et clusters Azure VMware Solution](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-clusters-and-hosts"></a>Nombre de clusters et d’hôtes

Le premier déploiement d’Azure VMware Solution est constitué d’un cloud privé contenant un seul cluster. Pour votre déploiement, vous devez définir le nombre d’hôtes que vous souhaitez déployer sur le premier cluster.

>[!NOTE]
>Le nombre minimal d’hôtes par cluster est de trois, et la valeur maximale est de 16. Le nombre maximal de clusters par cloud privé est de quatre. 

Pour plus d’informations, consultez la documentation relative aux [clouds privés et clusters Azure VMware Solution](concepts-private-clouds-clusters.md#clusters).

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segment d’adresse IP pour la gestion du cloud privé

La première étape de la planification du déploiement consiste à planifier la segmentation IP. Azure VMware Solution nécessite un réseau CIDR /22. Cet espace d’adressage est décomposé en segments réseau plus petits (sous-réseaux) et utilisé pour les segments de gestion Azure VMware Solution, notamment les fonctionnalités vCenter, VMware HCX, NSX-T et vMotion. La visualisation ci-dessous met en évidence où ce segment sera utilisé.

Ce bloc d’adresses réseau CIDR /22 ne doit pas empiéter sur un segment réseau figurant déjà dans votre environnement local ou dans Azure.

**Exemple :** 10.0.0.0/22

Pour obtenir une description détaillée de la façon dont le réseau CIDR /22 est décomposé par cloud privé, consultez la [Check-list de planification réseau](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifier : segment d’adresse IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment d’adresse IP pour les charges de travail des machines virtuelles

Comme pour tout environnement VMware, les machines virtuelles doivent se connecter à un segment réseau. Dans Azure VMware Solution, il existe deux types de segments : les segments étendus L2 (abordés plus loin) et les segments réseau NSX-T. À mesure que le déploiement de production d’Azure VMware Solution s’étend, il existe souvent une combinaison de segments étendus L2 locaux et de segments réseau NSX-T locaux. Pour planifier le déploiement initial, dans Azure VMware Solution, identifiez un segment réseau unique (réseau IP). Ce réseau ne doit pas empiéter sur les segments réseau locaux ou sur le reste d’Azure, et il ne doit pas se trouver dans le segment réseau /22 défini plus tôt.

Ce segment réseau est principalement utilisé à des fins de test lors du déploiement initial.

>[!NOTE]
>Ce ou ces réseaux ne seront pas nécessaires pendant le déploiement. Ils sont créés en guise d’étape post-déploiement.
  
**Exemple :** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifier : segment d’adresse IP pour les charges de travail des machines virtuelles" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Joindre le réseau virtuel Azure à Azure VMware Solution

Pour assurer la connectivité à Azure VMware Solution, un circuit ExpressRoute est créé du cloud privé Azure VMware Solution vers une passerelle de réseau virtuel ExpressRoute.

Vous pouvez utiliser une passerelle de réseau virtuel ExpressRoute *existante* OU *nouvelle*.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identité : réseau virtuel Azure pour joindre Azure VMware Solution" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Utiliser une passerelle de réseau virtuel ExpressRoute existante

Si vous prévoyez d’utiliser une passerelle de réseau virtuel ExpressRoute *existante*, le circuit ExpressRoute d’Azure VMware Solution est établi dans le cadre d’une étape post-déploiement. Dans ce cas, laissez vide le champ **Réseau virtuel**.

En guise de recommandation générale, il est acceptable d’utiliser une passerelle de réseau virtuel ExpressRoute existante. À des fins de planification, notez la passerelle de réseau virtuel ExpressRoute que vous utiliserez, puis passez à l’[étape suivante](#vmware-hcx-network-segments).

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Créer une nouvelle passerelle de réseau virtuel ExpressRoute

Lorsque vous créez une *nouvelle* passerelle de réseau virtuel ExpressRoute, vous pouvez utiliser un réseau virtuel Azure existant ou en créer un.  

- Pour un réseau virtuel Azure existant :
   1. Identifiez un réseau virtuel Azure où il n’existe pas de passerelle de réseau virtuel ExpressRoute préexistante.
   2. Avant le déploiement, créez un [sous-réseau de passerelle](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) dans le réseau virtuel Azure.

- Pour un nouveau réseau virtuel Azure et une nouvelle passerelle de réseau virtuel, vous allez le créer pendant le déploiement en sélectionnant le lien **Créer** sous la liste **Réseau virtuel**.  Il est important de définir l’espace d’adressage et les sous-réseaux avant le déploiement. Vous êtes donc prêt à entrer ces informations quand vous effectuez les étapes de déploiement.

L’image suivante montre l’écran de déploiement **Créer un cloud privé** avec le champ **Réseau virtuel** mis en évidence.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Capture d’écran de la fenêtre de déploiement Azure VMware Solution avec le champ Réseau virtuel mis en surbrillance":::

> [!NOTE]
> Un réseau virtuel qui sera utilisé ou créé est visible par votre environnement local et votre solution Azure VMware Solution. Vous devez donc vous assurer que le segment IP que vous utilisez dans ce réseau virtuel et les sous-réseaux ne se chevauchent pas.

## <a name="vmware-hcx-network-segments"></a>Segments réseau VMware HCX

VMware HCX est une technologie qui regroupée avec Azure VMware Solution. Les principaux cas d’utilisation de VMware HCX sont les migrations de charge de travail et la récupération d’urgence. Si vous envisagez d’effectuer l’une ou l’autre, il est préférable de planifier la mise en réseau maintenant. Dans le cas contraire, vous pouvez passer à l’étape suivante.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>(Facultatif) Étendre vos réseaux

Vous pouvez étendre des segments réseau à partir d’un site local vers Azure VMware Solution. Si vous le faites, identifiez ces réseaux maintenant.  

Voici quelques facteurs à prendre en compte :

- Si vous envisagez d’étendre des réseaux en local, ces réseaux doivent se connecter à un [commutateur vDS (vSphere Distributed Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) dans votre environnement VMware local.  
- Les réseaux qui se trouvent sur un [commutateur vSS (vSphere Standard Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) ne peuvent pas être étendus.

>[!NOTE]
>Ces réseaux sont étendus en guise de dernière étape de configuration, et non lors du déploiement.
>
## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez rassemblé et documenté les informations nécessaires, passez à la section suivante pour créer votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer Azure VMware Solution](deploy-azure-vmware-solution.md)
> 
