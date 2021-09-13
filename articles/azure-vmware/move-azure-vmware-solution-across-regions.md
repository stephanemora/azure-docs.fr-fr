---
title: Déplacer des ressources Azure VMware Solution dans les régions
description: Cet article explique comment déplacer des ressources Azure VMware Solution d’une région Azure vers une autre.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534980"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>Déplacer des ressources Azure VMware Solution vers une autre région

>[!IMPORTANT]
>Les étapes décrites dans cet article sont uniquement destinées au déplacement de ressources entre un environnement Azure VMware Solution dans une région (source) et un environnement Azure VMware Solution dans une autre région (cible). 

Vous pouvez déplacer des ressources Azure VMware Solution vers une autre région pour plusieurs raisons. Par exemple, pour déployer des fonctionnalités ou des services disponibles dans des régions spécifiques uniquement ou pour répondre à des besoins de stratégie et de gouvernance ou à des besoins de planification de la capacité. 

Cet article vous permet de planifier et d’effectuer la migration d’Azure VMware Solution entre une région Azure et une autre, par exemple de la région Azure A vers la région Azure B.


Le diagramme illustre la connectivité ExpressRoute recommandée entre les deux environnements Azure VMware Solution.  Un appairage de sites HCX et un maillage de services sont créés entre les deux environnements.  Le trafic de migration HCX et l’extension de couche 2 transitent entre les deux environnements (représenté par la ligne rouge). Pour connaître la planification de HCX recommandée par VMware, consultez [Planning an HCX Migration](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1).

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="Diagramme montrant la communication ExpressRoute Global Reach entre les environnements Azure VMware Solution source et cible." border="false":::

>[!NOTE]
>Vous n’avez pas besoin de migrer de nouveau un workflow en local, car le trafic transite entre les clouds privés (source et cible) :
>
>**Cloud privé Azure VMware Solution (source) > passerelle ExpressRoute (source) > passerelle ExpressRoute (cible) > Cloud privé Azure VMware Solution (cible)**

Le diagramme illustre la connectivité entre les deux environnements Azure VMware Solution. 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="Diagramme illustrant la communication entre les environnements Azure VMware Solution source et cible." border="false":::


Cet article décrit les étapes à effectuer pour : 

> [!div class="checklist"]
> * Préparer et planifier le déplacement vers une autre région Azure
> * Établir une connectivité réseau entre les deux clouds privés Azure VMware Solution
> * Exporter la configuration à partir de l’environnement Azure VMware Solution source
> * Réappliquer les éléments de configuration pris en charge à l’environnement Azure VMware Solution cible
> * Migrer les charges de travail à l’aide de VMware HCX

## <a name="prerequisites"></a>Configuration requise

- [L’appliance VMware HCX est mise à niveau vers le dernier patch](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html), ce qui évite les éventuels problèmes de migration.

- La bibliothèque de contenu locale de la source est une [bibliothèque de contenu publiée](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g).

## <a name="prepare"></a>Préparer

Les étapes suivantes montrent comment préparer votre cloud privé Azure VMware Solution pour passer à un autre cloud privé Azure VMware Solution. 

### <a name="export-the-source-configuration"></a>Exporter la configuration source

1. À partir de la source, [exportez les segments étendus, les règles de pare-feu, les détails des ports et les tables de routage](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html).

1. [Exportez le contenu d’une vue de liste d’inventaire dans un fichier CSV](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html).

1. [Triez les charges de travail par groupes de migration (vague de migration)](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1).


### <a name="deploy-the-target-environment"></a>Déployer l’environnement cible

Avant de pouvoir déplacer la configuration source, vous devez [déployer l’environnement cible](plan-private-cloud-deployment.md).


### <a name="back-up-the-source-configuration"></a>Sauvegarder la configuration source

Sauvegardez la configuration Azure VMware Solution (source) qui comprend les stratégies et les règles de pare-feu, de VC et NSX-T. 

- **Calcul :** exportez la configuration d’inventaire existante. Pour la sauvegarde de l’inventaire, vous pouvez utiliser RVtool (une application open source).

- **Stratégies et règles de réseau et de pare-feu :** sur l’environnement Azure VMware Solution cible, créez les mêmes segments réseau que ceux de l’environnement source.

Azure VMware Solution prend en charge toutes les solutions de sauvegarde. Vous avez besoin de privilèges CloudAdmin pour l’installation, la sauvegarde de données et la restauration des sauvegardes. Pour plus d’informations, consultez [Solutions de sauvegarde pour les machines virtuelles (VM) Azure VMware Solution](ecosystem-back-up-vms.md).

- Sauvegarde des charges de travail de machine virtuelle à l’aide de la solution CommVault :

   - [Créez un client VMware](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm) à partir du centre de commandes pour Azure VMware Solution vCenter.

   - [Créez un groupe de machines virtuelles](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm) avec les machines virtuelles nécessaires pour les sauvegardes.

   - [Exécutez les sauvegardes sur les groupes de machines virtuelles](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm).

   - [Restaurez les machines virtuelles](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm).

- Sauvegarde des charges de travail de machine virtuelle à l’aide de la solution [Veritas NetBackup](https://vrt.as/nb4avs). 

>[!TIP]
>Vous pouvez utiliser [Azure Resource Mover](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) pour vérifier et migrer la liste des ressources prises en charge à déplacer entre régions, qui dépendent d’Azure VMware Solution.

### <a name="locate-the-source-expressroute-circuit-id"></a>Localiser l’ID de circuit ExpressRoute source

1. À partir de la source, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Gérer** > **Connectivité** > **ExpressRoute**.

3. Copiez l’**ID ExpressRoute** de la source.  Vous en aurez besoin pour effectuer l’appairage entre les clouds privés.


### <a name="create-the-targets-authorization-key"></a>Créer la clé d’autorisation de la cible

1. À partir de la cible, connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Gérer** > **Connectivité** > **ExpressRoute**, puis sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Capture d’écran montrant comment demander une clé d’autorisation ExpressRoute" border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. Spécifiez un nom pour la clé, puis sélectionnez **Créer**.

   La création de la clé peut prendre environ 30 secondes. La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé.

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Capture d’écran montrant la clé d’autorisation ExpressRoute Global Reach":::
  
1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous en avez besoin pour effectuer le peering. La clé d’autorisation disparaît après un certain temps. Copiez-la dès qu’elle apparaît.

### <a name="peer-between-private-clouds"></a>Effectuer l’appairage entre les clouds privés

Vous disposez à présent des ID de circuit et des clés d’autorisation ExpressRoute pour les deux environnements. Vous pouvez maintenant appairer la source à la cible. Vous devez utiliser l’ID de ressource et la clé d’autorisation de votre circuit ExpressRoute de cloud privé pour terminer l’appairage.
 
1. À partir de la cible, connectez-vous au [portail Azure](https://portal.azure.com) en utilisant le même abonnement que le circuit ExpressRoute de la source.

1. Sous Gérer, sélectionnez **Connectivité** > **ExpressRoute Global Reach** > **Ajouter**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Capture d’écran montrant l’onglet ExpressRoute Global Reach dans le cloud privé d’Azure VMware Solution":::

1. Collez l’ID de circuit et la clé d’autorisation de la cible ExpressRoute que vous avez créés à l’étape précédente.  Sélectionnez ensuite **Créer** :

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Capture d’écran montrant la boîte de dialogue permettant d’entrer les informations de connexion.":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>Créer un appairage de sites entre les clouds privés

Une fois la connectivité établie, vous allez créer un appairage de sites VMware HCX entre les clouds privés pour faciliter la migration de vos machines virtuelles. Vous pouvez connecter ou appairer le gestionnaire de cloud VMware HCX d’Azure VMware Solution au connecteur VMware HCX de votre centre de données. 

1. Connectez-vous au vCenter de la source, puis, sous **Accueil**, sélectionnez **HCX**.

1. Sous **Infrastructure**, sélectionnez **Appairage de sites**, puis sélectionnez l’option **Se connecter au site distant** (au milieu de l’écran). 

1. Entrez l’adresse IP ou l’URL du gestionnaire de cloud HCX d’Azure VMware Solution que vous avez notée `https://x.x.x.9`, le nom d’utilisateur Azure VMware Solution cloudadmin\@vsphere.local et le mot de passe associé. Sélectionnez **Connecter**.

   > [!NOTE]
   > Pour établir une paire de sites :
   > * Votre connecteur VMware HCX doit pouvoir router vers l’IP de votre gestionnaire de cloud HCX via le port 443.
   >
   > * Utilisez le mot de passe qui vous a servi à vous connecter à vCenter. Vous avez défini ce mot de passe sur l’écran de déploiement initial.

   Un écran s’affiche montrant que votre gestionnaire de cloud VMware HCX d’Azure VMware Solution et votre connecteur HCX local sont connectés (appairés).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Capture d’écran présentant l’appairage du gestionnaire HCX d’Azure VMware Solution et du connecteur HCX.":::

### <a name="create-a-service-mesh-between-private-clouds"></a>Créer un maillage de services entre les clouds privés

> [!NOTE]
> Pour établir correctement une maille de services avec VMware Azure Solution :
>
> * Les ports UDP 500/4500 sont ouverts entre les adresses du profil réseau « liaison montante » de votre connecteur VMware HCX local et les adresses du profil réseau « liaison montante » du cloud HCX d’Azure VMware Solution.
>
> * Veillez à examiner les [ports obligatoires VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage du service** > **Créer le maillage du service**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Capture d’écran des sélections permettant de commencer à créer une maille de services." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Passez en revue les sites préremplis, puis sélectionnez **Continuer**. 

   > [!NOTE]
   > S’il s’agit de votre première configuration de maillage de service, vous ne devez pas modifier cet écran.  

1. Sélectionnez les profils de calcul source et distant dans les listes déroulantes, puis sélectionnez **Continue**.  

   Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Capture d’écran montrant la sélection du profil de calcul source." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Capture d’écran montrant la sélection du profil de calcul distant." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Passez en revue les services que vous souhaitez activer, puis sélectionnez **Continuer**.  

1. Dans **Advanced Configuration - Override Uplink Network profiles** (Configuration avancée - Remplacer les profils réseau de liaison montante), sélectionnez **Continue**.  

   Les profils réseau de liaison montante se connectent au réseau par le biais duquel les appliances d’interconnexion du site distant peuvent être jointes.  
  
1. Dans **Configuration avancée - Scale Out des appliances d’extension réseau**, passez les informations en revue et sélectionnez **Continuer**. 

   Vous pouvez disposer de huit VLAN par appliance au maximum, mais vous pouvez déployer une autre appliance pour en ajouter huit de plus. Vous devez aussi disposer d’un espace IP pour prendre en compte les autres appliances, et il faut compter une IP par appliance.  Pour plus d’informations, consultez [VMware HCX Configuration Limits](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Capture d’écran montrant où augmenter le nombre VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Dans **Advanced Configuration - Traffic Engineering** (Configuration avancée - Ingénierie du trafic), examinez et apportez les modifications que vous estimez nécessaires, puis sélectionnez **Continue**.

1. Passez en revue l’aperçu de la topologie, puis sélectionnez **Continuer**.

1. Entrez un nom convivial pour ce maillage de service et sélectionnez **Terminer**.  

1. Sélectionnez **Afficher les tâches** pour surveiller le déploiement. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Capture d’écran montrant le bouton d’affichage des tâches.":::

   Lorsque le déploiement de la maille de services se termine correctement, les services s’affichent en vert.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Capture d’écran montrant des indicateurs verts sur les services." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Vérifiez l’intégrité de la maille de services en contrôlant l’état de l’appliance. 

1. Sélectionnez **Interconnect** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Capture d’écran affichant les sélections pour la vérification de l’état de l’appliance." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

## <a name="move"></a>Déplacer

Les étapes suivantes montrent comment déplacer vos ressources de cloud privé Azure VMware Solution vers un autre cloud privé Azure VMware Solution dans une région différente. 

Dans cette section, vous allez migrer les éléments suivants :

- Configuration du pool de ressources et création des dossiers

- Modèles de machines virtuelles et balises associées

- Déploiement des segments logiques basé sur les groupes de ports de la source et les VLAN associés 

- Services et groupes de sécurité réseau

- Stratégie et règles de pare-feu de passerelle basées sur les stratégies de pare-feu de la source

### <a name="migrate-the-source-vsphere-configuration"></a>Migrer la configuration vSphere source

Au cours de cette étape, vous allez copier la configuration vSphere de la source et la déplacer vers l’environnement cible. 

1. À partir du vCenter de la source, utilisez la même configuration de pool de ressources et [créez la même configuration de pool de ressources](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4) sur le vCenter de la cible.

2. À partir du vCenter de la source, utilisez le même nom de dossier de machine virtuelle et [créez le même dossier de machine virtuelle](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html) sur le vCenter de la cible sous **Dossiers**.

3. Utilisez VMware HCX pour migrer tous les modèles de machine virtuelle du vCenter de la source vers le vCenter de la cible.

   1. À partir de la source, convertissez les modèles existants en machines virtuelles, puis migrez-les vers la cible.

   2. À partir de la cible, convertissez les machines virtuelles en modèles de machine virtuelle.

4. À partir de l’environnement source, utilisez les mêmes noms de balises de machine virtuelle et [créez les balises sur le vCenter de la cible](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html). 

5. À partir de la bibliothèque de contenu vCenter de la source, utilisez l’option de la bibliothèque abonnée pour copier les modèles ISO, OVF, OVA et de machine virtuelle vers la bibliothèque de contenu cible :

   1. Si la bibliothèque de contenu n’est pas encore publiée, sélectionnez l’option **Activer la publication**.

   2. À partir de la bibliothèque de contenu de la source, copiez l’URL de la bibliothèque publiée.

   3. À partir de la cible, [créez une bibliothèque de contenu abonnée](deploy-vm-content-library.md) avec l’URL de la bibliothèque de la source.

   4. Sélectionnez **Synchroniser maintenant**.


### <a name="configure-the-target-nsx-t-environment"></a>Configurer l’environnement NSX-T cible

Au cours de cette étape, vous allez utiliser la configuration NSX-T source pour configurer l’environnement NSX-T cible.

>[!NOTE]
>Plusieurs fonctionnalités sont configurées sur le NSX-T source. Vous devez donc les copier ou les lire à partir du NXS-T source et les recréer dans le cloud privé cible. Utilisez l’extension L2 pour conserver l’adresse IP et l’adresse MAC de la machine virtuelle lors de la migration entre les clouds privés AVS source et cible pour éviter un temps d’arrêt dû au changement d’IP et à la configuration associée.

1. [Configurez les composants réseau NSX](tutorial-nsx-t-network-segment.md) nécessaires dans l’environnement cible sous la passerelle de niveau 1 par défaut.

1. [Créez la configuration du groupe de sécurité](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [Créez la stratégie et les règles de pare-feu distribuées](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [Créez la stratégie et les règles de pare-feu de passerelle](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html).

1. [Créez le serveur DHCP ou le service de relais DHCP](configure-dhcp-azure-vmware-solution.md). 

1. [Configurez la mise en miroir des ports](configure-port-mirroring-azure-vmware-solution.md).

1. [Configurez le redirecteur DNS](configure-dns-azure-vmware-solution.md).

1. [Configurez une nouvelle passerelle de niveau 1 (autre que celle par défaut)](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html).  Cette configuration est basée sur le NSX-T configuré sur la source. 

### <a name="migrate-the-vms-from-the-source"></a>Migrer les machines virtuelles à partir de la source 

Au cours de cette étape, vous allez utiliser VMware HCX pour migrer les machines virtuelles de la source vers la cible. Vous avez la possibilité d’effectuer une extension de couche 2 à partir de la source et d’utiliser HCX pour déplacer, avec vMotion, les machines virtuelles de la source vers la cible avec une interruption minimale. 

Outre vMotion, d’autres méthodes telles que Bulk et Cold vMotion sont également recommandées.  Pour en savoir plus :

- [Plan an HCX Migration](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [Migrate Virtual Machines with HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>Basculer des réseaux étendus 

Au cours de cette étape, vous allez effectuer un basculement de passerelle final pour mettre fin aux réseaux étendus. Vous allez également déplacer (migrer) les passerelles de l’environnement Azure VMware Solution source vers l’environnement cible.

>[!IMPORTANT]
>Vous devez effectuer le basculement de passerelle après la migration des charges de travail VLAN vers l’environnement Azure VMware Solution cible. De plus, il ne doit y avoir aucune dépendance de machine virtuelle sur les environnements source et cible.

Avant le basculement de passerelle, vérifiez les services et performances de toutes les charges de travail migrées. Après acceptation des performances (sauf concernant d’éventuels problèmes de latence) par les propriétaires d’application et de service web, vous pouvez poursuivre le basculement de passerelle.  Quand vous aurez terminé le basculement, vous devrez modifier les enregistrements DNS A et PTR publics. 

Pour obtenir des recommandations de VMware, consultez [Cutover of extended networks](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9) (Basculement de réseaux étendus).


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>IP publique (NAT de destination) pour les machines virtuelles DMZ migrées

À ce stade, vous avez migré les charges de travail vers l’environnement cible. Ces charges de travail d’application doivent être accessibles à partir de l’Internet public. L’environnement cible offre deux façons d’héberger une application. Les applications peuvent être :

- Hébergées et publiées sous l’équilibreur de charge de la passerelle d’application

- Publiées par le biais de la fonctionnalité d’IP publique dans un WAN virtuel

L’IP publique est généralement la traduction d’adresses réseau (NAT) de destination dans le pare-feu Azure. Avec les règles DNAT, la stratégie de pare-feu traduit les demandes d’adresses IP publiques en adresses privées (serveur web) avec un port. Pour plus d’informations, consultez [Comment utiliser la fonctionnalité d’adresse IP publique dans Azure Virtual WAN](./enable-public-internet-access.md).

>[!NOTE]
>La SNAT est configurée par défaut dans Azure VMware Solution. Vous devez donc l’activer à partir des paramètres de connectivité de cloud privé d’Azure VMware Solution sous l’onglet Gérer.

## <a name="decommission"></a>Désactiver

Pour cette dernière étape, vous allez vérifier que toutes les charges de travail de machine virtuelle ont été correctement migrées, y compris la configuration réseau. S’il n’existe aucune dépendance, vous pouvez déconnecter le maillage de services HCX, l’appairage de sites et la connectivité réseau à partir de l’environnement source. 

>[!NOTE]
>Après avoir désactivé le cloud privé, vous ne pouvez pas annuler l’opération. En effet, la configuration et les données sont perdues.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Prise en charge de l’opération de déplacement pour Microsoft.AVS](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [Conseils sur le déplacement de ressources réseau](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [Conseils pour le déplacement de machines virtuelles](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [Conseils de déplacement de ressources App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
