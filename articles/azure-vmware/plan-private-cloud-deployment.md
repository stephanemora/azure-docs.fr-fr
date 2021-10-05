---
title: Planifier le déploiement d’Azure VMware Solution
description: Découvrez comment planifier votre déploiement Azure VMware Solution.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 09/27/2021
ms.openlocfilehash: 6528727fda867f5f07d7d83b09df1bbf70df6b92
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079463"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Planifier le déploiement d’Azure VMware Solution

La planification de votre déploiement Azure VMware Solution est essentielle pour un environnement prêt pour la production à des fins de création de machines virtuelles et de migration. Pendant le processus de planification, vous allez identifier et recueillir ce qui est nécessaire pour votre déploiement. À mesure que vous planifiez, veillez à documenter les informations que vous collectez pour y accéder facilement pendant le déploiement. Un déploiement réussi génère un environnement prêt pour la production à des fins de création de machines virtuelles et de migration.

Dans ce guide pratique, vous allez :

> [!div class="checklist"]
> * Identifier l’abonnement, le groupe de ressources, la région et le nom de la ressource Azure
> * Identifier les hôtes de taille et déterminer le nombre de clusters et d’hôtes
> * Demander un quota d’hôte pour le plan Azure éligible
> * Identifier le segment IP CIDR /22 pour la gestion du cloud privé
> * Identifier un segment réseau unique
> * Définir la passerelle de réseau virtuel
> * Définir des segments réseau VMware HCX

Une fois que vous avez terminé, effectuez les étapes suivantes recommandées à la fin pour poursuivre ce guide de prise en main.


## <a name="identify-the-subscription"></a>Identifier l’abonnement

Identifiez l’abonnement que vous prévoyez d’utiliser pour déployer Azure VMware Solution.  Vous pouvez créer un nouvel abonnement ou utiliser un abonnement existant.

>[!NOTE]
>L’abonnement doit être associé à un Contrat Entreprise (EA) Microsoft, à un plan Azure pour fournisseurs de solutions cloud (CSP) ou à un Contrat client Microsoft (MCA). Pour plus d’informations, consultez les [critères d’éligibilité](request-host-quota-azure-vmware-solution.md#eligibility-criteria).

## <a name="identify-the-resource-group"></a>Identifier le groupe de ressources

Identifiez le groupe de ressources que vous souhaitez utiliser pour votre Azure VMware Solution.  En règle générale, un groupe de ressources est créé spécifiquement pour Azure VMware Solution, mais vous pouvez utiliser un groupe de ressources existant.

## <a name="identify-the-region-or-location"></a>Identifier la région ou la localisation

Identifiez la [région](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) dans laquelle vous souhaitez déployer Azure VMware Solution. 

## <a name="define-the-resource-name"></a>Définir le nom de la ressource

Le nom de la ressource est le nom convivial et descriptif que vous utilisez pour votre cloud privé Azure VMware Solution, par exemple, **MyPrivateCloud**.

>[!IMPORTANT]
>Le nom ne doit pas comporter plus de 40 caractères. Si le nom dépasse cette limite, vous ne pourrez pas créer d’adresses IP publiques à utiliser avec le cloud privé. 

## <a name="identify-the-size-hosts"></a>Identifier les tailles d’hôtes

Identifiez les tailles d’hôtes que vous souhaitez utiliser lors du déploiement d’Azure VMware Solution.  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>Déterminer le nombre de clusters et d’hôtes

Votre premier déploiement d’Azure VMware Solution est constitué d’un cloud privé contenant un seul cluster. Pour votre déploiement, vous devez définir le nombre d’hôtes que vous souhaitez déployer sur le premier cluster.

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>Pour en savoir plus sur les limites du nombre d’hôtes par cluster, le nombre de clusters par cloud privé et le nombre d’hôtes par cloud privé, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits).

## <a name="request-a-host-quota"></a>Demander un quota d’hôte 

Vous devez demander un quota d’hôte rapidement pour être prêt à déployer votre cloud privé Azure VMware Solution dès que le processus de planification est terminé.
Avant de demander un quota d’hôte, assurez-vous d’avoir identifié l’abonnement, le groupe de ressources et la région Azure. En outre, assurez-vous d’avoir identifié les hôtes de taille et déterminez le nombre de clusters et d’hôtes dont vous aurez besoin.

Une fois que l’équipe du support technique a reçu votre demande de quota d’hôte, cela prend jusqu’à cinq jours ouvrés pour la confirmer et vous allouer des hôtes.

- [Clients EA](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [Clients CSP](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>Définir le segment d’adresse IP pour la gestion du cloud privé

Azure VMware Solution nécessite un réseau CIDR /22, par exemple, `10.0.0.0/22`. Cet espace d’adressage est décomposé en segments réseau plus petits (sous-réseaux) et utilisé pour les segments d’administration Azure VMware Solution, notamment les fonctionnalités vCenter, VMware HCX, NSX-T et vMotion. Le diagramme met en évidence les segments d’adresses IP d’administration d’Azure VMware Solution.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Diagramme illustrant les segments d’adresses IP d’administration d’Azure VMware Solution." border="false":::  

>[!IMPORTANT]
>Le bloc d’adresses réseau CIDR /22 ne doit pas empiéter sur un segment réseau figurant déjà dans votre environnement local ou dans Azure. Pour plus d’informations sur la façon dont le réseau CIDR /22 est décomposé par cloud privé, consultez [Considérations sur le routage et le sous-réseau](tutorial-network-checklist.md#routing-and-subnet-considerations).



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>Définir le segment d’adresse IP pour les charges de travail de machine virtuelle

Comme pour tout environnement VMware, les machines virtuelles doivent se connecter à un segment réseau.  À mesure que le déploiement de production d’Azure VMware Solution s’étend, il existe souvent une combinaison de segments étendus L2 locaux et de segments réseau NSX-T locaux. 

Pour le déploiement initial, identifiez un segment réseau unique (réseau IP), par exemple, `10.0.4.0/24`. Ce segment réseau est principalement utilisé à des fins de test lors du déploiement initial.  Le bloc d’adresse ne doit pas chevaucher des segments réseau dans l’environnement local ou dans Azure et ne doit pas se trouver dans le segment réseau /22 déjà défini. 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifier : segment d’adresse IP pour les charges de travail des machines virtuelles" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>Définir la passerelle de réseau virtuel

Azure VMware Solution nécessite un réseau virtuel Azure et un circuit ExpressRoute. Déterminez si vous voulez utiliser une passerelle de réseau virtuel ExpressRoute *existante* OU *Nouvelle*. Si vous décidez d’utiliser une *nouvelle* passerelle de réseau virtuel, vous allez la créer après avoir créé votre cloud privé. Il est possible d’utiliser une passerelle de réseau virtuel ExpressRoute existante, et à des fins de planification, notez la passerelle de réseau virtuel ExpressRoute que vous allez utiliser. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Diagramme qui montre le réseau virtuel Azure attaché à VMware Azure Solution" border="false":::

>[!IMPORTANT]
>Vous pouvez vous connecter à une passerelle de réseau virtuel dans un réseau Azure Virtual WAN, mais celle-ci sort du cadre du présent guide de démarrage rapide.

## <a name="define-vmware-hcx-network-segments"></a>Définir des segments réseau VMware HCX

VMware HCX est une plateforme de mobilité des applications qui simplifie la migration des applications, le rééquilibrage des charges de travail et la continuité de l’activité dans les centres de données et les clouds. Vous pouvez migrer vos charges de travail VMware vers Azure VMware Solution et d’autres sites connectés par le biais de différents types de migration. 

Le connecteur VMware HCX déploie un sous-ensemble d’appliances virtuelles (automatisées) qui nécessitent plusieurs segments IP. Lorsque vous créez vos profils réseau, vous utilisez les segments IP. Identifiez les éléments suivants pour le déploiement de VMware HCX, qui prend en charge un cas d’utilisation pilote ou de petite taille de produit.  En fonction des besoins de votre migration, modifiez-les si nécessaire. 

- **Réseau de gestion :** quand vous déployez VMware HCX localement, vous devez identifier un réseau de gestion pour VMware HCX.  En règle générale, il s’agit du même réseau de gestion que celui utilisé par votre cluster VMware local.  Au minimum, identifiez **deux** adresses IP sur ce segment réseau pour VMware HCX. Vous aurez peut-être besoin de quantités plus grandes, en fonction de l’échelle de votre déploiement au-delà du cas d’usage pilote ou petit.

  >[!NOTE]
  >Lors de la préparation de grands environnements, au lieu d’utiliser le réseau de gestion spécifié pour le cluster VMware local, créez un réseau /26 et présentez-le comme un groupe de ports à votre cluster VMware local.  Vous pouvez ensuite créer jusqu’à 10 maillages de services et 60 extendeurs réseau (-1 par maillage de service). Vous pouvez étirer **huit** réseaux par extendeur réseau en utilisant des clouds privés Azure VMware Solution.

- **Réseau de liaison montante :** quand vous déployez VMware HCX localement, vous devez identifier un réseau de liaison montante pour VMware HCX. Utilisez le même réseau que celui que vous utiliserez pour le réseau d’administration. 

- **Réseau vMotion :** quand vous déployez VMware HCX localement, vous devez identifier un réseau vMotion pour VMware HCX.  En règle générale, il s’agit du même réseau que celui utilisé pour vMotion par votre cluster VMware local.  Au minimum, identifiez **deux** adresses IP sur ce segment réseau pour VMware HCX. Vous aurez peut-être besoin de quantités plus grandes, en fonction de l’échelle de votre déploiement au-delà du cas d’usage pilote ou petit.

  Vous devez exposer le réseau vMotion sur un commutateur virtuel distribué ou vSwitch0. Si ce n’est pas le cas, modifiez l’environnement en conséquence.

  >[!NOTE]
  >De nombreux environnements VMware utilisent des segments réseau non routés pour vMotion, ce qui ne pose aucun problème.
  
- **Réseau de réplication :** quand vous déployez VMware HCX localement, vous devez définir un réseau de réplication. Utilisez le même réseau que ceux que vous utilisez pour vos réseaux de gestion et de liaison montante.  Si les hôtes du cluster local utilisent un réseau VMkernel de réplication dédié, réservez **deux** adresses IP dans ce segment réseau et utilisez le réseau VMkernel de réplication pour le réseau de réplication.


## <a name="determine-whether-to-extend-your-networks"></a>Déterminer si vous allez étendre vos réseaux

Vous pouvez éventuellement étendre des segments réseau depuis un site local vers Azure VMware Solution. Si vous le faites, identifiez ces réseaux maintenant en suivant ces instructions :

- Les réseaux doivent se connecter à un [commutateur vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) dans votre environnement VMware local.  
- Les réseaux qui se trouvent sur un [commutateur vSS (vSphere Standard Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) ne peuvent pas être étendus.

>[!IMPORTANT]
>Ces réseaux sont étendus en guise de dernière étape de configuration, et non lors du déploiement.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez rassemblé et documenté les informations nécessaires, passez au tutoriel suivant pour créer votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer Azure VMware Solution](deploy-azure-vmware-solution.md)
