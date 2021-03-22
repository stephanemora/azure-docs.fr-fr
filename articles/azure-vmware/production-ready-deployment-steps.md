---
title: Planification du déploiement d’Azure VMware Solution
description: Cet article décrit un workflow de déploiement d’Azure VMware Solution.  Le résultat final est un environnement prêt pour la création et la migration des machines virtuelles.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462420"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planification du déploiement d’Azure VMware Solution

Cet article vous présente le processus de planification permettant d’identifier et de collecter les informations à utiliser pendant le déploiement. À mesure que vous planifiez votre déploiement, veillez à documenter les informations que vous collectez pour les référencer facilement pendant le déploiement.

Les étapes décrites dans ce guide de démarrage rapide vous permettent d’obtenir un environnement prêt pour la production en vue de la création de machines virtuelles et de la migration. 

>[!IMPORTANT]
>Avant de créer votre ressource Azure VMware Solution, référez-vous à l’article [Guide pratique pour activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md) afin de soumettre un ticket de support pour que vos hôtes soient alloués. Une fois que l’équipe du support technique a reçu votre demande, il lui faut jusqu’à cinq jours ouvrés pour confirmer votre requête et vous allouer des hôtes. Si vous disposez d’un cloud privé Azure VMware Solution et que vous souhaitez que davantage d’hôtes vous soient alloués, vous passerez par le même processus. 


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

Dans Azure VMware Solution, vous allez déployer un cloud privé et créer plusieurs clusters. Pour votre déploiement, vous devez définir le nombre de clusters et les hôtes que vous souhaitez déployer sur chaque cluster. Le nombre minimal d’hôtes par cluster est de trois, et la valeur maximale est de 16. Le nombre maximal de clusters par cloud privé est de quatre. Le nombre maximal de nœuds par cloud privé est de 64.

Pour plus d’informations, consultez la documentation relative aux [clouds privés et clusters Azure VMware Solution](concepts-private-clouds-clusters.md#clusters).

>[!TIP]
>Vous pouvez toujours étendre le cluster ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.

## <a name="vcenter-admin-password"></a>Mot de passe de l’administrateur du vCenter
Définissez le mot de passe d'administrateur vCenter. Pendant le déploiement, vous allez créer un mot de passe d’administrateur vCenter. Le mot de passe est attribué au compte administrateur cloudadmin@vsphere.local lors de la build vCenter. Vous utiliserez ces informations d’identification pour vous connecter à vCenter.

## <a name="nsx-t-admin-password"></a>Mot de passe administrateur NSX-T
Définissez le mot de passe administrateur NSX-T. Pendant le déploiement, vous allez créer un mot de passe d’administrateur NSX-T. Le mot de passe est attribué à l’utilisateur administrateur dans le compte NSX au cours de la build NSX. Vous utiliserez ces informations d’identification pour vous connecter à NSX-T Manager.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segment d’adresse IP pour la gestion du cloud privé

La première étape de la planification du déploiement consiste à planifier la segmentation IP. Azure VMware Solution nécessite un réseau CIDR /22. Cet espace d’adressage le décompose en segments réseau plus petits (sous-réseaux) et utilisés pour les fonctionnalités vCenter, VMware HCX, NSX-T et vMotion.

Ce bloc d’adresses réseau CIDR /22 ne doit pas empiéter sur un segment réseau se trouvant déjà dans votre environnement local ou dans Azure.

**Exemple :** 10.0.0.0/22

Azure VMware Solution se connecte à votre réseau virtuel Microsoft Azure par le biais d’un circuit ExpressRoute Global Reach interne (D-MSEE dans la visualisation ci-dessous). Cette fonctionnalité faisant partie du service Azure VMware Solution, elle n’est pas facturée.

Pour plus d’informations, consultez la [Liste de contrôle de planification du réseau](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifier : segment d’adresse IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment d’adresse IP pour les charges de travail des machines virtuelles

Identifiez un segment IP afin de créer votre premier réseau pour vos charges de travail (segment NSX) dans votre cloud privé. En d’autres termes, vous devrez créer un segment réseau dans Azure VMware Solution afin de pouvoir déployer des machines virtuelles sur Azure VMware Solution.

Même si vous envisagez d’étendre des réseaux locaux à Azure VMware Solution (L2), vous devez toujours créer un segment réseau qui valide l’environnement.

N’oubliez pas que tous les segments IP créés doivent être uniques dans votre espace Azure et votre empreinte locale.
  
**Exemple :** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifier : segment d’adresse IP pour les charges de travail des machines virtuelles" border="false":::     

## <a name="optional-extend-networks"></a>(Facultatif) Étendre des réseaux

Vous pouvez étendre les segments réseau à partir d’un site local vers Azure VMware Solution et, si vous le faites, identifier ces réseaux maintenant.  

N’oubliez pas les points suivants :

- Si vous envisagez d’étendre des réseaux en local, ces réseaux doivent se connecter à un [commutateur vDS (vSphere Distributed Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) dans votre environnement VMware local.  
- Si le ou les réseaux que vous souhaitez étendre se trouvent sur un [commutateur vSS (vSphere Standard Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), ils ne peuvent pas être étendus.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Joindre le réseau virtuel Azure à Azure VMware Solution

Dans cette étape, vous allez identifier une passerelle de réseau virtuel ExpressRoute et le réseau virtuel Azure de prise en charge utilisé pour connecter le circuit ExpressRoute d’Azure VMware Solution.  Le circuit ExpressRoute facilite la connectivité vers et depuis le cloud privé Azure VMware Solution vers d’autres services Azure, ressources Azure et environnements locaux.

Vous pouvez utiliser une passerelle de réseau virtuel ExpressRoute *existante* OU *nouvelle*.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identité : réseau virtuel Azure pour joindre Azure VMware Solution" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Utiliser une passerelle de réseau virtuel ExpressRoute existante

Si vous utilisez une passerelle de réseau virtuel ExpressRoute *existante*, le circuit ExpressRoute d’Azure VMware Solution est établi après le déploiement du cloud privé. Dans ce cas, laissez vide le champ **Réseau virtuel**.  

Prenez note de la passerelle de réseau virtuel ExpressRoute que vous allez utiliser et passez à l’étape suivante.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Créer une nouvelle passerelle de réseau virtuel ExpressRoute

Lorsque vous créez une *nouvelle* passerelle de réseau virtuel ExpressRoute, vous pouvez utiliser un réseau virtuel Azure existant ou en créer un.  

- Pour un réseau virtuel Azure existant :
   1. Vérifiez qu’il n’existe pas de passerelle de réseau virtuel ExpressRoute préexistante dans le réseau virtuel. 
   1. Sélectionnez le réseau virtuel Azure existant dans la liste **Réseau virtuel**.

- Pour un nouveau réseau virtuel Azure, vous pouvez le créer à l’avance ou au cours du déploiement. Sélectionnez le lien **Créer nouveau** sous la liste **Réseau virtuel**.

L’image ci-dessous montre l’écran de déploiement **Créer un cloud privé** avec le champ **Réseau virtuel** mis en surbrillance.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Capture d’écran de la fenêtre de déploiement Azure VMware Solution avec le champ Réseau virtuel mis en surbrillance":::

>[!NOTE]
>Un réseau virtuel qui sera utilisé ou créé est visible par votre environnement local et votre solution Azure VMware Solution. Vous devez donc vous assurer que le segment IP que vous utilisez dans ce réseau virtuel et les sous-réseaux ne se chevauchent pas.

## <a name="vmware-hcx-network-segments"></a>Segments réseau VMware HCX

VMware HCX est une technologie regroupée avec Azure VMware Solution. Les principaux cas d’utilisation de VMware HCX sont les migrations de charge de travail et la récupération d’urgence. Si vous envisagez d’effectuer l’une ou l’autre, il est préférable de planifier la mise en réseau maintenant.   Dans le cas contraire, vous pouvez passer à l’étape suivante.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez rassemblé et documenté les informations nécessaires, passez à la section suivante pour créer votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer Azure VMware Solution](deploy-azure-vmware-solution.md)
