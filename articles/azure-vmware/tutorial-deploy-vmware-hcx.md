---
title: Didacticiel - Déployer et configurer VMware HCX
description: Découvrez comment déployer et configurer la solution HCX (Hybrid Cloud Extension) VMware pour votre cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578782"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Déployer et configurer VMware HCX

Dans cet article, nous allons suivre les procédures de déploiement et de configuration de VMware HCX pour votre cloud privé de la solution VMWare Azure. VMware HCX permet la migration de vos charges de travail VMware vers Azure VMware Solution et d’autres sites connectés par le biais de différents types de migration.

VMware HCX Advanced (solution prédéployée dans Azure VMware Solution) prend en charge jusqu’à trois connexions de site (de local à cloud ou ce cloud à cloud). Si vous avez besoin de plus de trois connexions de site pour VMware HCX Enterprise, vous avez la possibilité d’activer le module complémentaire [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (qui est actuellement *en préversion*) en soumettant une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) est disponible avec Azure VMware Solution en tant que fonction/service *en préversion*. Bien que la solution VMware HCX EE pour Azure VMware Solution soit *en préversion*, il s’agit d’une fonction ou d’un service gratuit et soumis aux conditions générales du service en préversion. Une fois le service VMware HCX EE mis à la disposition générale, vous recevrez un préavis de 30 jours indiquant que la facturation aura été basculée. Vous avez également la possibilité de désactiver/quitter le service.

Avant de commencer, passez minutieusement en revue les sections [Avant de commencer](#before-you-begin), [Exigences de la version logicielle](#software-version-requirements) et [Prérequis](#prerequisites). 

Ensuite, nous parcourrons pas à pas toutes les procédures nécessaires pour :

> [!div class="checklist"]
> * Déployer VMware HCX OVA localement (connecteur)
> * Activer VMware HCX
> * Connectez votre environnement sur site à votre environnement Azure VMware Solution.
> * Configurez l’interconnexion (profil de calcul, profil(s) réseau et maillage du service).
> * Terminez la configuration en vérifiant l’état de l’appliance.

Une fois l’installation terminée, vous pouvez appliquer les étapes suivantes recommandées à la fin de cet article.  

## <a name="before-you-begin"></a>Avant de commencer
   
* Consultez la [série de tutoriels](tutorial-network-checklist.md) Azure VMware Solution Software Defined Datacenter (SDDC).
* Consultez et référencez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), notamment le guide de l’utilisateur HCX.
* Consultez [Migration des machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation de VMware.
* Consultez éventuellement les [considérations relatives au déploiement de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Consultez éventuellement les ressources VMware associées sur HCX, comme la [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere sur HCX. 
* Vous pouvez également demander l’activation de HCX Enterprise Azure VMware Solution par le biais des canaux de support Azure VMware Solution.
* Pour déployer l’appliance d’interconnexion WAN, des plages CIDR spécifiques sont déjà allouées à partir du client fourni `\22` pour le cloud privé en cours de création.

Le dimensionnement des charges de travail par rapport aux ressources de calcul et de stockage est une étape de planification essentielle. Traitez cette étape de dimensionnement dans le cadre de la planification initiale de l’environnement de cloud privé. 

Vous pouvez également dimensionner les charges de travail en effectuant une [évaluation Azure VMware Solution](../migrate/how-to-create-azure-vmware-solution-assessment.md) dans le portail Azure Migrate.

## <a name="software-version-requirements"></a>Exigences de la version logicielle

Les composants d’infrastructure doivent exécuter la version minimale requise. 
                                                         
| Type de composant    | Exigences de l’environnement source    | Exigences de l’environnement de destination   |
| --- | --- | --- |
| Serveur vCenter   | 5,1<br/><br/>Si vous utilisez 5.5 U1 ou une version antérieure, utilisez l’interface utilisateur HCX autonome pour les opérations HCX.  | 6.0 U2 et versions ultérieures   |
| ESXi   | 5.0    | ESXi 6.0 et versions ultérieures   |
| NSX    | Pour l’extension réseau HCX des commutateurs logiques à la source : NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Pour le routage de proximité HCX : NSXv 6.4+ (le routage de proximité n’est pas pris en charge avec NSX-T) |
| vCloud Director   | Non requis. Aucune interopérabilité avec vCloud Director sur le site source | Lorsque l’environnement de destination est intégré à vCloud Director, la version minimale est 9.1.0.2.  |

## <a name="prerequisites"></a>Prérequis

### <a name="network-and-ports"></a>Réseau et ports

* [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) configuré entre les circuits locaux et ExpressRoute Azure VMware Solution SDDC.

* Tous les ports requis doivent être ouverts pour la communication entre les composants locaux et Azure VMware Solution SDDC (voir [la documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Les appliances HCX IX et NE locales doivent pouvoir accéder à l’infrastructure vCenter et ESXi.

### <a name="ip-addresses"></a>Adresses IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Déployer VMware HCX OVA sur site

>[!NOTE]
>Avant de déployer l’appliance virtuelle sur votre vCenter local, vous devez télécharger le fichier OVA VMware HCX. 

1. Connectez-vous à HCX Manager d’Azure VMware Solution sur `https://x.x.x.9` le port 443 avec les informations d’identification de l’utilisateur **cloudadmin**, puis accédez à **Support**.

   >[!TIP]
   >Pour identifier l’adresse IP de HCX Manager, dans le panneau Azure VMware Solution, accédez à **Gérer** > **Connectivité**, puis sélectionnez l’onglet **HCX**. 
   >
   >Le mot de passe vCenter a été défini lorsque vous avez configuré le cloud privé.

1. Sélectionnez le lien de **téléchargement** pour télécharger le fichier OVA VMware HCX.

1. Accédez au vCenter local et sélectionnez un modèle OVF, soit le fichier OVA téléchargé, à déployer sur votre vCenter local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Sélectionnez un nom et un emplacement, une ressource/un cluster dans lequel vous déployez HCX, puis examinez les détails et les ressources requises.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Passez en revue les termes du contrat de licence et, si vous les acceptez, sélectionnez le stockage et le réseau requis, puis sélectionnez **Suivant**.

1. Dans **Personnaliser le modèle**, entrez toutes les informations requises. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Sélectionnez **Suivant**, vérifiez la configuration, puis sélectionnez **Terminer** pour déployer HCX OVA.
     
   >[!NOTE]
   >En règle générale, VMware HCX Manager que vous déployez maintenant est déployé sur le réseau de gestion du cluster.  
   
   > [!IMPORTANT]
   > Une fois le déploiement terminé, vous devrez peut-être lancer manuellement l’appliance virtuelle.
   > Attendez 10-15 minutes après la mise sous tension de l’appliance HCX pour passer à l’étape suivante.

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - déploiement de l’appliance VMware HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Activer VMware HCX

Après avoir déployé le fichier OVA VMware HCX en local, vous pouvez activer VMware HCX. Avant de pouvoir activer VMware HCX, vous devez récupérer une licence.

1. Dans Azure VMware Solution, accédez à **Gérer** > **Connectivité**, sélectionnez l’onglet **HCX**, puis sélectionnez **Ajouter**.

1. Connectez-vous à VMware HCX Manager en local sur `https://HCXManagerIP:9443` et connectez-vous en utilisant les informations d’identification d’**administrateur** . 

   > [!IMPORTANT]
   > Veillez à inclure le numéro de port `9443` avec l’adresse IP de VMware HCX Manager.

1. Dans **Gestion des licences**, entrez votre **Clé avancée HCX**.  
   
    > [!NOTE]
    > VMware HCX Manager doit avoir un accès Internet ouvert ou un proxy configuré.

1. Dans **Emplacement du centre de données**, indiquez l’emplacement le plus proche où vous installez VMware HCX Manager en local.

1. Modifiez le **Nom du système** ou acceptez le nom par défaut.
   
1. Vous pouvez soit terminer ultérieurement, soit continuer. Sélectionnez l’option **Oui, continuer** pour continuer.
    
1. Dans **Connecter votre vCenter**, fournissez le nom de domaine complet ou l’adresse IP de votre serveur vCenter, ainsi que les informations d’identification appropriées, puis sélectionnez **Continuer**.
   
1. Dans **Configurer SSO/PSC**, indiquez le nom de domaine complet ou l’adresse IP de votre PSC, puis sélectionnez **Continuer**.
   
   >[!NOTE]
   >En règle générale, il s’agit du même nom de domaine complet vCenter/de la même adresse IP.

1. Vérifiez que toutes les entrées sont correctes et sélectionnez **Redémarrer**.
    
   > [!NOTE]
   > Un certain délai s’écoulera après le redémarrage avant que vous soyez invité à passer à l’étape suivante.
   >
   >Après le redémarrage des services, il est essentiel que vCenter apparaisse en vert à l’écran affiché. VCenter et SSO ont les paramètres de configuration appropriés. Ceux-ci doivent être identiques à ceux de l’écran précédent.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - activation de VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-vmware-hcx"></a>Configurer VMware HCX

Vous êtes maintenant prêt à ajouter un appairage de sites, à créer un réseau et un profil de calcul, et à activer des services tels que la migration, l’extension réseau ou la récupération d’urgence. 

### <a name="add-a-site-pairing"></a>Ajouter un appairage de site

Vous pouvez connecter (appairer) le VMware HCX Manager à Azure VMware Solution grâce au VMware HCX Manager dans votre centre de données. 

1. Connectez-vous au vCenter local puis, sous **Accueil**, sélectionnez **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Sous **Infrastructure**, sélectionnez **Appairage de sites**, puis sélectionnez l’option **Connexion au site distant** (au milieu de l’écran). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Entrez l’**URL HCX distante ou l’adresse IP**, le nom d’utilisateur cloudadmin@vsphere.local et le **mot de passe** Azure VMware Solution cloudadmin, puis sélectionnez **Connecter**.

   > [!NOTE]
   > L’**URL HCX distante** est votre HCX Manager de cloud privé Azure VMware Solution, généralement l’adresse « 9 » du réseau de gestion.  Par exemple, si votre vCenter est 192.168.4.2, votre URL HCX sera 192.168.4.9.
   >
   > Le **mot de passe** sera le même mot de passe que celui que vous avez utilisé pour vous connecter à vCenter. Vous avez défini ce mot de passe sur l’écran de déploiement initial.

   Un écran s’affiche montrant votre HCX Manager dans Azure VMware Solution et votre gestionnaire HCX local connecté (appairé).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - appairage de VMware HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Créer des profils réseau

VMware HCX déploie deux appliances virtuelles (automatisées), mais a besoin de plusieurs segments IP.  Lorsque vous créez vos profils réseau, vous définissez les segments IP que vous avez identifiés lors de la [phase de préparation et de planification du prédéploiement des segments de réseau VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Vous allez créer quatre profils réseau :

   - Gestion
   - vMotion
   - Réplication
   - Liaison montante

1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage de service multisite** > **Profils réseau** > **Créer un profil réseau**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pour chaque profil réseau, sélectionnez le réseau ainsi que le groupe de ports, fournissez un nom, créez le pool d’adresses IP pour ce segment précis, puis sélectionnez **Créer**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - créer un profil réseau](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-compute-profile"></a>Créer un profil de calcul

1. Sélectionnez **Profil de calcul** > **Créer un profil de calcul**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Entrez un nom de profil de calcul et sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Sélectionnez les services à activer, comme la migration, l’extension réseau et la récupération d’urgence, puis sélectionnez **Continuer**.
  
   > [!NOTE]
   > En général, rien ne sera modifié ici.

1. Dans **Sélectionner des ressources de service**, sélectionnez une ou plusieurs ressources de service (clusters) pour lesquelles les services HCX sélectionnés doivent être activés.  

1. Lorsque vous voyez les clusters dans votre centre de données local, sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Dans **Sélectionner un magasin de données**, sélectionnez la ressource de stockage du magasin de données pour déployer les appliances d’interconnexion VMware HCX, puis sélectionnez **Continuer**.

   Lorsque plusieurs ressources sont sélectionnées, VMware HCX utilise la première ressource sélectionnée jusqu’à épuisement de sa capacité.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Sélectionnez **Profil réseau de gestion**, le profil de réseau de gestion que vous avez créé au cours des étapes précédentes, puis sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Le profil de réseau de gestion permet aux appliances VMware HCX de communiquer avec vCenter, et les hôtes ESXi peuvent être contactés.

1. Sélectionnez **Profil réseau de liaison montante**, le profil réseau de liaison montante que vous avez créé au cours des étapes précédentes, puis sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Sélectionnez **Profil réseau vMotion**, le profil réseau vMotion que vous avez créé au cours des étapes précédentes, puis sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Choisissez le **Profil réseau de réplication vSphere**, le profil réseau de réplication que vous avez créé au cours des étapes précédentes, puis sélectionnez **Continuer**.

   Dans la plupart des cas, le profil réseau de réplication est le même que le profil réseau d’administration.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Sélectionnez **Commutateurs distribués pour les extensions réseau**, les commutateurs virtuels distribués qui contiennent les machines virtuelles à migrer vers Azure VMware Solution sur un réseau étendu à deux couches, puis sélectionnez **Continuer**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Passez en revue les règles de connexion et sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Sélectionnez **Terminer** pour créer le profil de calcul.

   Un écran similaire à celui ci-dessous s’affiche.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - Créer un profil de calcul VMware HCX](https://www.youtube.com/embed/qASXi5xrFzM).




### <a name="create-service-mesh"></a>Créer le maillage du service

À présent, configurez le maillage du service entre le SDDC local et le SDDC Azure VMware Solution.

1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage du service** > **Créer le maillage du service**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Passez en revue les sites préremplis, puis sélectionnez **Continuer**. 

   >[!NOTE]
   >S’il s’agit de votre première configuration de maillage de service, vous ne devez pas modifier cet écran.  

1. Sélectionnez la liste déroulante pour la source et les profils de calcul distants, puis sélectionnez **Continuer**.  

   Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Passez en revue les services qui seront activés, puis sélectionnez **Continuer**.  

1. Dans **Configuration avancée - Remplacer les profils réseau de liaison montante**, sélectionnez **Continuer**.  Les profils réseau de liaison montante se connectent au réseau via lequel les appliances d’interconnexion du site distant peuvent être atteintes.  
  
1. Dans **Configuration avancée - Scale Out des appliances d’extension réseau**, passez les informations en revue et sélectionnez **Continuer**. 

1. Dans **Configuration avancée - Ingénierie du trafic**, examinez et apportez les modifications nécessaires, puis sélectionnez **Continuer**.

1. Passez en revue l’aperçu de la topologie, puis sélectionnez **Continuer**.

1. Entrez un nom convivial pour ce maillage de service et sélectionnez **Terminer**.  

1. Sélectionnez **Afficher les tâches** pour surveiller le déploiement. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

   Lorsque le déploiement du maillage de service se termine correctement, les services s’affichent en vert.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Vérifiez l’intégrité du maillage de service en vérifiant l’état de l’appliance, sélectionnez **Interconnexion** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - Maillage de service VMware HCX](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Facultatif) Créer une extension réseau

Si vous souhaitez étendre les réseaux de votre environnement local à Azure VMware Solution, procédez comme suit pour étendre ces réseaux.

1. Sous **Services**, sélectionnez **Extension réseau**, puis sélectionnez **Créer une extension réseau**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Sélectionnez chacun des réseaux que vous souhaitez étendre à Azure VMware Solution, puis sélectionnez **Suivant**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

1. Entrez l’adresse IP de la passerelle locale pour chacun des réseaux que vous étendez, puis sélectionnez **Soumettre**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

   L’extension du réseau peut prendre quelques minutes. Dans ce cas, le changement de statut change pour **Extension terminée**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution - extension de réseau VMware HCX](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Étapes suivantes

Si vous avez atteint ce point et que l’état du tunnel d’interconnexion de l’appliance est **Activé** et vert, vous pouvez migrer et protéger des machines virtuelles Azure VMware Solution à l’aide de VMware HCX.  Azure VMware Solution prend en charge les migrations de charge de travail (avec ou sans extension réseau).  Vous pouvez toujours effectuer des migrations de charge de travail dans votre création de réseaux de votre environnement vSphere sur site, avec le déploiement de machines virtuelles sur ces réseaux.  Pour plus d’informations, consultez la [documentation de VMware HCVX](https://docs.vmware.com/en/VMware-HCX/index.html) et [Migration de machines virtuelles vers VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation technique de VMware pour obtenir des informations sur l’utilisation de HCX.
