---
title: Didacticiel - Déployer et configurer VMware HCX
description: Découvrez comment déployer et configurer une solution VMware HCX (Hybrid Cloud Extension) pour votre cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791407"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Déployer et configurer VMware HCX

Dans cet article, nous allons suivre les procédures de déploiement et de configuration du connecteur VMware HCX local pour votre cloud privé Azure VMware Solution. Avec VMware HCX, vous pouvez migrer vos charges de travail VMware vers Azure VMware Solution et d’autres sites connectés, par le biais de différents types de migration. Étant donné que la solution VMware Azure déploie et configure le Gestionnaire de clouds HCX, vous devez télécharger, activer et configurer le connecteur HCX dans votre centre de données VMware local.

Le connecteur VMware HCX Advanced est prédéployé dans Azure VMware Solution. Il prend en charge jusqu’à trois connexions de site (localement vers cloud, ou cloud vers cloud). Si vous avez besoin de plus de trois connexions de site, soumettez une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support) pour activer le module complémentaire [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) . Le module complémentaire est actuellement en préversion. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) est disponible avec Azure VMware Solution sous forme de service en préversion. Il est gratuit et est soumis aux conditions générales d’un service en préversion. Faisant suite à la mise en disponibilité générale du service VMware HCX EE, vous recevrez un préavis de 30 jours indiquant que la facturation sera basculée. Vous aurez également la possibilité de désactiver ou de quitter le service. Notez qu’il n’existe actuellement aucun chemin de mise à niveau simple de HCX Enterprise vers HCX Advanced et que les clients qui choisissent de rétrograder devront effectuer un redéploiement et subir un temps d’arrêt.

Avant toute chose, commencez par passer soigneusement en revue les sections [Avant de commencer](#before-you-begin), [Exigences de la version logicielle](#software-version-requirements) et [Prérequis](#prerequisites) de cet article. 

Ensuite, nous parcourrons pas à pas toutes les procédures nécessaires pour :

> [!div class="checklist"]
> * Déployer le fichier OVA VMware HCX localement (connecteur HCX).
> * Activer le connecteur VMware HCX.
> * Associer votre connecteur HCX local à votre Gestionnaire de clouds HCX d’Azure VMware Solution.
> * Configurer l’interconnexion (profil réseau, profil de calcul et maille de services).
> * Terminer l’installation en contrôlant l’état de l’appliance et en vérifiant que la migration est possible.

L’installation terminée, vous pouvez appliquer les étapes suivantes recommandées à la fin de cet article.  

## <a name="before-you-begin"></a>Avant de commencer
   
* Consultez la [série de tutoriels](tutorial-network-checklist.md) de base du centre de données défini par logiciel (SDDC) Azure VMware Solution.
* Consultez et référencez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), notamment le guide de l’utilisateur HCX.
* Consultez [Migration des machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation de VMware.
* Consultez éventuellement les [considérations relatives au déploiement de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Consultez éventuellement les ressources VMware connexes sur HCX, comme la [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere. 
* Vous pouvez également demander l’activation de HCX Enterprise Azure VMware Solution par le biais des canaux de support Azure VMware Solution.
* Si vous le souhaitez, [passez en revue les ports réseau nécessaires à HCX](https://ports.vmware.com/home/VMware-HCX).
* BIen que le Gestionnaire de clouds HCX d’Azure VMware Solution soit préconfiguré à partir du réseau /22 fourni pour le cloud privé Azure VMware Solution, vous devez allouer des plages réseau à partir de votre réseau local pour le connecteur HCX local. Ces réseaux et ces plages sont décrits plus loin dans cet article.

Le dimensionnement des charges de travail par rapport aux ressources de calcul et de stockage est une étape de planification essentielle. Traitez cette étape de dimensionnement dans le cadre de la planification initiale d’un environnement de cloud privé. 

Vous pouvez dimensionner les charges de travail en procédant à une [évaluation Azure VMware Solution](../migrate/how-to-create-azure-vmware-solution-assessment.md) dans le portail Azure Migrate.

## <a name="software-version-requirements"></a>Exigences de la version logicielle

Les composants d’infrastructure doivent exécuter la version minimale requise. 
                                                         
| Type de composant    | Exigences de l’environnement source    | Exigences de l’environnement de destination   |
| --- | --- | --- |
| Serveur vCenter   | 5,1<br/><br/>Si vous utilisez 5.5 U1 ou une version antérieure, utilisez l’interface utilisateur HCX autonome pour les opérations HCX.  | 6.0 U2 et versions ultérieures   |
| ESXi   | 5.0    | ESXi 6.0 et versions ultérieures   |
| NSX    | Pour l’extension réseau HCX des commutateurs logiques à la source : NSXv 6.2+ ou NSX-T 2.4+.   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Pour le routage de proximité HCX : NSXv 6.4+. (routage de proximité non pris en charge avec NSX-T.) |
| vCloud Director   | Non obligatoire. Il n’existe aucune interopérabilité avec vCloud Director sur le site source. | Lorsque vous intégrez l’environnement de destination à vCloud Director, la version minimale est 9.1.0.2.  |

## <a name="prerequisites"></a>Prérequis

### <a name="network-and-ports"></a>Réseau et ports

* Configurez [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) entre les circuits locaux et les circuits ExpressRoute du SDDC Azure VMware Solution.

* [Tous les ports obligatoires](https://ports.vmware.com/home/VMware-HCX) doivent être ouverts pour la communication entre les composants locaux et le SDDC Azure VMware Solution.

Pour plus d’informations, consultez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Adresses IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Déployer le fichier OVA de VMware HCX Connector en local

> [!NOTE]
> Avant de déployer l’appliance virtuelle sur votre vCenter local, vous devez télécharger le fichier OVA du connecteur VMware HCX. 

1. Ouvrez une fenêtre de navigateur, connectez-vous au Gestionnaire HCX d’Azure VMware Solution sur le port `https://x.x.x.9` 443 avec les informations d’identification de l’utilisateur **cloudadmin** , puis accédez à **Support**.

   > [!TIP]
   > Notez l’adresse IP du Gestionnaire de clouds HCX dans Azure VMware Solution. Pour identifier l’adresse IP, dans le volet Azure VMware Solution, accédez à **Manage** (Gérer) > **Connectivity** , puis sélectionnez l’onglet **HCX**. 
   >
   >Le mot de passe vCenter a été défini lorsque vous avez configuré le cloud privé.

1. Sélectionnez le lien de **téléchargement** pour télécharger le fichier OVA de VMware HCX Connector.

1. Connectez-vous à votre vCenter local. Sélectionnez un modèle OVF, c’est-à-dire le fichier OVA que vous avez téléchargé, pour déployer le connecteur HCX dans votre vCenter local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Capture d’écran de navigation vers un modèle OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Sélectionnez un nom et un emplacement, puis sélectionnez une ressource/un cluster où déployer le connecteur HCX. Ensuite, examinez les détails et les ressources nécessaires.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Capture d’écran de la révision des détails du modèle." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Passez en revue les termes du contrat de licence. Si vous les acceptez, sélectionnez le stockage et le réseau nécessaires, puis sélectionnez **Next** (Suivant).

1. Dans **Personnaliser le modèle** , entrez toutes les informations requises. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Capture d’écran des zones de personnalisation d’un modèle." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Sélectionnez **Next** (Suivant), vérifiez la configuration, puis sélectionnez **Finish** (Terminer) pour déployer le fichier OVA du connecteur HCX.
     
   > [!NOTE]
   > En règle générale, le connecteur VMware HCX que vous déployez à présent est déployé sur le réseau de gestion du cluster.  
   
   > [!IMPORTANT]
   > Vous devrez peut-être activer manuellement l’appliance virtuelle.  Si c’est le cas, patientez 10 à 15 minutes avant de passer à l’étape suivante.

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Déploiement de l’appliance HCX](https://www.youtube.com/embed/BwSnQeefnso). 


## <a name="activate-vmware-hcx"></a>Activer VMware HCX

Une fois que vous avez déployé le fichier OVA du connecteur VMware HCX en local, et démarré l’appliance, vous êtes prêt à procéder à l’activation. Vous devez tout d’abord vous procurer une clé de licence sur le portail Azure VMware Solution.

1. Dans le portail Azure VMware Solution, accédez à **Manage** (Gérer) > **Connectivity** , sélectionnez l’onglet **HCX** , puis sélectionnez **Add** (Ajouter).

1. Utilisez les informations d’identification de l’ **administrateur** pour vous connecter au Gestionnaire de clouds VMware HCX en local sur `https://HCXManagerIP:9443`. 

   > [!IMPORTANT]
   > Veillez à inclure le numéro de port `9443` avec l’adresse IP de VMware HCX Manager.

1. Dans **Licensing** (Gestion des licences), entrez votre clé pour **HCX Advanced Key** (Clé HCX Advanced).  
   
    > [!NOTE]
    > Le Gestionnaire VMware HCX doit disposer d’un accès Internet ouvert, ou d’un proxy configuré.

1. Dans **Datacenter Location** (Emplacement du centre de données), indiquez l’emplacement le plus proche pour l’installation du Gestionnaire de clouds VMware HCX en local.

1. Dans **System Name** (Nom du système), modifiez ou acceptez le nom par défaut.
   
1. Sélectionnez **Yes, Continue** (Oui, continuer).
    
1. Dans **Connecter votre vCenter** , fournissez le nom de domaine complet ou l’adresse IP de votre serveur vCenter, ainsi que les informations d’identification appropriées, puis sélectionnez **Continuer**.
   
1. Dans **Configure SSO/PSC** , indiquez le nom de domaine complet ou l’adresse IP de votre instance Platform Services Controller (PSC), puis sélectionnez **Continue**.
   
   >[!NOTE]
   >En règle générale, cette entrée est la même que votre nom de domaine complet ou votre adresse IP vCenter.

1. Vérifiez que toutes les entrées sont correctes et sélectionnez **Redémarrer**.
    
   > [!NOTE]
   > Un certain délai s’écoulera après le redémarrage avant que vous soyez invité à passer à l’étape suivante.

Après le redémarrage des services, il est essentiel que vCenter apparaisse en vert à l’écran affiché. VCenter et SSO doivent être dotés des paramètres de configuration appropriés : ceux-ci doivent être identiques à ceux de l’écran précédent.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Capture d’écran du tableau de bord avec l’état vCenter vert." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Activer HCX](https://www.youtube.com/embed/BkAV_TNYxdE).


## <a name="configure-the-vmware-hcx-connector"></a>Configurer le connecteur VMware HCX

Vous êtes maintenant prêt à ajouter un couplage de sites, à créer un réseau et un profil de calcul, et à activer des services tels que la migration, l’extension réseau ou la reprise d’activité. 

### <a name="add-a-site-pairing"></a>Ajouter un appairage de site

Vous pouvez connecter (appairer) le Gestionnaire de clouds VMware HCX d’Azure VMware Solution au connecteur VMware HCX de votre centre de données. 

1. Connectez-vous au vCenter local puis, sous **Accueil** , sélectionnez **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Capture d’écran du client vCenter avec la sélection de HCX parmi les raccourcis." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Sous **Infrastructure** , sélectionnez **Site Pairing** (Couplage de sites), puis sélectionnez l’option **Connect To Remote Site** (Connexion au site distant) (au milieu de l’écran). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Capture d’écran de sélections pour la création d’un site distant." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Entrez l’adresse IP ou l’URL HCX distante que vous avez notée précédemment, le nom d’utilisateur Azure VMware Solution cloudadmin@vsphere.local et le mot de passe associé. Sélectionnez **Connecter**.

   > [!NOTE]
   > Pour établir correctement un couplage de sites, votre connecteur HCX doit être en mesure de router vers l’adresse IP de votre Gestionnaire de clouds HCX sur le port 443.
   >
   > Le mot de passe est le même mot de passe que celui que vous avez utilisé pour vous connecter à vCenter. Vous avez défini ce mot de passe sur l’écran de déploiement initial.

   Un écran s’affiche montrant que votre Gestionnaire de clouds HCX dans Azure VMware Solution et votre connecteur HCX local sont connectés (couplés).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Capture d’écran présentant le couplage du gestionnaire HCX dans Azure VMware Solution avec le connecteur HCX.":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Couplage de sites HCX](https://www.youtube.com/embed/sKizDCRHOko).



### <a name="create-network-profiles"></a>Créer des profils réseau

VMware HCX déploie un sous-ensemble d’appliances virtuelles (automatisées) qui nécessitent plusieurs segments IP. Lorsque vous créez vos profils réseau, vous définissez les segments IP que vous avez identifiés lors de la [phase de préparation et de planification du prédéploiement des segments de réseau VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Vous allez créer quatre profils réseau :

   - Gestion
   - vMotion
   - Réplication
   - Liaison montante

1. Sous **Infrastructure** , sélectionnez **Interconnexion** > **Maillage de service multisite** > **Profils réseau** > **Créer un profil réseau**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Capture d’écran de sélections permettant de commencer à créer un profil réseau." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pour chaque profil réseau, sélectionnez le réseau ainsi que le groupe de ports, puis fournissez un nom et créez le pool d’adresses IP pour ce segment précis. Sélectionnez ensuite **Créer**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Capture d’écran des détails d’un nouveau profil réseau.":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Profil réseau HCX](https://www.youtube.com/embed/NhyEcLco4JY).


### <a name="create-a-compute-profile"></a>Créer un profil de calcul

1. Sélectionnez **Profil de calcul** > **Créer un profil de calcul**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Capture d’écran affichant les sélections qui permettent de commencer à créer un profil de calcul." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Entrez un nom de profil de calcul et sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Capture d’écran affichant l’entrée d’un nom de profil de calcul, et le bouton Continue." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Sélectionnez les services à activer, comme la migration, l’extension réseau ou la reprise d’activité, puis sélectionnez **Continue**.
  
   > [!NOTE]
   > En général, rien ne change ici.

1. Dans **Select Service Resources** (Sélectionner des ressources de service), sélectionnez une ou plusieurs ressources de service (clusters) pour lesquelles les services HCX sélectionnés doivent être activés.  

1. Lorsque vous voyez les clusters dans votre centre de données local, sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Capture d’écran montrant les ressources de service sélectionnées, et le bouton Continue." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Dans **Select Datastore** (Sélectionner un magasin de données), sélectionnez la ressource de stockage du magasin de données pour déployer les appliances d’interconnexion VMware HCX. Sélectionnez **Continuer**.

   Lorsque plusieurs ressources sont sélectionnées, VMware HCX utilise la première ressource sélectionnée jusqu’à épuisement de sa capacité.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Capture d’écran montrant la sélection d’une ressource de stockage de données, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Dans **Select Management Network Profile** , sélectionnez le profil réseau de gestion que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de gestion, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Le profil réseau de gestion permet aux appliances VMware HCX de communiquer avec vCenter. Les hôtes ESXi sont accessibles via ce profil.

1. Dans **Select Uplink Network Profile** , sélectionnez le profil réseau de liaison montante que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de liaison montante, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Dans **Select Motion Network Profile** , sélectionnez le profil réseau vMotion que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau vMotion, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Dans **Select vSphere Replication Network Profile** , sélectionnez le profil réseau de réplication que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   Dans la plupart des cas, le profil réseau de réplication est le même que le profil réseau d’administration.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de réplication, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Dans **Select Distributed Switches for Network Extensions** , sélectionnez les commutateurs virtuels distribués qui contiennent les machines virtuelles à migrer vers Azure VMware Solution sur un réseau étendu à deux couches. Sélectionnez **Continuer**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Capture d’écran montrant la sélection des commutateurs virtuels distribués, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Passez en revue les règles de connexion et sélectionnez **Continuer**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Capture d’écran montrant les règles de connexion et le bouton Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Sélectionnez **Terminer** pour créer le profil de calcul.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Capture d’écran affichant des informations de profil de calcul." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Profil de calcul](https://www.youtube.com/embed/qASXi5xrFzM).

### <a name="create-a-service-mesh"></a>Créer une maille de services.

À présent, configurez une maille de services entre le SDDC local et le SDDC Azure VMware Solution.

   > [!NOTE]
   > Pour établir correctement une maille de services avec VMware Azure Solution :
   >
   > Les ports UDP 500/4500 sont ouverts entre les adresses du profil réseau « liaison montante » définies pour le connecteur HCX local, et les adresses du profil réseau « liaison montante » du cloud HCX d’Azure VMware solution.
   >
   > Veillez à passer en revue les [ports obligatoires HCX](https://ports.vmware.com/home/VMware-HCX).

1. Sous **Infrastructure** , sélectionnez **Interconnexion** > **Maillage du service** > **Créer le maillage du service**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Capture d’écran des sélections permettant de commencer à créer une maille de services." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Passez en revue les sites préremplis, puis sélectionnez **Continuer**. 

   >[!NOTE]
   >S’il s’agit de votre première configuration de maillage de service, vous ne devez pas modifier cet écran.  

1. Sélectionnez les profils de calcul source et distant dans les listes déroulantes, puis sélectionnez **Continue**.  

   Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Capture d’écran montrant la sélection du profil de calcul source." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Capture d’écran montrant la sélection du profil de calcul distant." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Passez en revue les services qui seront activés, puis sélectionnez **Continue**.  

1. Dans **Advanced Configuration - Override Uplink Network profiles** (Configuration avancée - Remplacer les profils réseau de liaison montante), sélectionnez **Continue**.  

   Les profils réseau de liaison montante se connectent au réseau par le biais duquel les appliances d’interconnexion du site distant peuvent être jointes.  
  
1. Dans **Configuration avancée - Scale Out des appliances d’extension réseau** , passez les informations en revue et sélectionnez **Continuer**. 

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

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : La maille de services](https://www.youtube.com/embed/FyZ0d3P_T24).



### <a name="optional-create-a-network-extension"></a>(Facultatif) Créer une extension réseau

Si vous souhaitez étendre un réseau de votre environnement local vers Azure VMware Solution, suivez ces étapes :

1. Sous **Services** , sélectionnez **Extension réseau** , puis sélectionnez **Créer une extension réseau**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Capture d’écran affichant les sélections qui permettent de commencer à créer une extension réseau." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Sélectionnez chaque réseau que vous souhaitez étendre vers Azure VMware Solution, puis sélectionnez **Next** (Suivant).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Capture d’écran présentant la sélection d’un réseau.":::

1. Entrez l’adresse IP de la passerelle locale pour chacun des réseaux que vous étendez, puis sélectionnez **Soumettre**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Capture d’écran montrant l’entrée d’une adresse IP de passerelle.":::

   L’extension d’un réseau peut prendre quelques minutes. Quand l’opération est terminée, l’état change et passe à **Extension complete** (Extension terminée).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Capture d’écran montrant l’état d’extension terminée." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution : Extension de réseau](https://www.youtube.com/embed/cNlp0f_tTr0).


## <a name="next-steps"></a>Étapes suivantes

Si vous avez atteint ce point et que l’état du tunnel d’interconnexion de l’appliance est **Activé** et vert, vous pouvez migrer et protéger des machines virtuelles Azure VMware Solution en utilisant VMware HCX. Azure VMware Solution prend en charge les migrations de charge de travail (avec ou sans extension réseau). Vous pouvez toujours migrer des charges de travail dans votre environnement vSphere, en même temps que la création locale de réseaux et le déploiement de machines virtuelles sur ces réseaux.  

Pour plus d’informations sur l’utilisation de HCX, accédez à la documentation technique de VMware :

* [Documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migration des machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [Ports HCX obligatoires](https://ports.vmware.com/home/VMware-HCX)
