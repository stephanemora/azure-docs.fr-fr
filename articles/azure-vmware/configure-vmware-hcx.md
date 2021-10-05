---
title: Configurer VMware HCX dans Azure VMware Solution
description: Configurez le connecteur VMware HCX local pour votre cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/07/2021
ms.openlocfilehash: 1fbcd8cc2cc596395f3736949f54f30ac224d060
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755821"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>Configurer le connecteur VMware HCX local

Une fois que vous avez [installé le module complémentaire VMware HCX](install-vmware-hcx.md), vous êtes prêt à configurer le connecteur VMware HCX local pour votre cloud privé Azure VMware Solution.  

Dans ce guide pratique, vous allez :

* Associer votre connecteur VMware HCX local à votre Gestionnaire de clouds HCX d’Azure VMware Solution
* Configurer le profil réseau, le profil de calcul et la maille de services
* Vérifier l’état de l’appliance et valider que la migration est possible

Une fois ces étapes terminées, vous disposerez d’un environnement prêt pour la production pour la création de machines virtuelles et la migration. 

## <a name="prerequisites"></a>Prérequis

- [VMware HCX Connector](install-vmware-hcx.md) a été installé.

- Si vous envisagez d’utiliser VMware HCX Enterprise, vérifiez que vous avez activé le module complémentaire [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) par le biais d’une [demande de support](https://portal.azure.com/#create/Microsoft.Support). Il s’agit d’un essai gratuit de 12 mois dans Azure VMware Solution.

- Si vous envisagez d’[activer VMware HCX MON](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html), veillez à disposer des éléments suivants :  

   - NSX-T ou VDS localement pour l’extension réseau HCX (pas de commutateur standard)

   - Un ou plusieurs segments de réseau étendu actifs

- Les [exigences de la version du logiciel VMware](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) ont été respectées.

- Votre environnement vSphere local (environnement source) répond à la [configuration minimale demandée](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

- [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) est configuré entre les circuits locaux et les circuits ExpressRoute du cloud privé Azure VMware Solution.

- [Tous les ports obligatoires](https://ports.vmware.com/home/VMware-HCX) sont ouverts pour permettre la communication entre les composants locaux et le cloud privé Azure VMware Solution.

- [Définir des segments réseau VMware HCX](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  Les principaux cas d’utilisation de VMware HCX sont les migrations de charge de travail et la récupération d’urgence.

- [Consultez la documentation de VMware HCX](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html) pour plus d’informations sur l’utilisation de HCX.

## <a name="add-a-site-pairing"></a>Ajouter un appairage de site

Vous pouvez connecter ou appairer le gestionnaire de cloud VMware HCX d’Azure VMware Solution au connecteur VMware HCX de votre centre de données.

> [!IMPORTANT]
> Bien que l’outil VMware Configuration Maximum décrive un nombre maximal de 25 paires de sites entre le connecteur HCX local et HXC Cloud Manager, le système de gestion des licences limite ce nombre à trois pour HCX Advanced et à 10 pour HCX Enterprise Edition.

1. Connectez-vous au vCenter local puis, sous **Accueil**, sélectionnez **HCX**.

1. Sous **Infrastructure**, sélectionnez **Site Pairing** (Appairage de sites), puis sélectionnez l’option **Connect To Remote Site** (Connexion au site distant) (au milieu de l’écran).

1. Saisissez l’URL ou l’adresse IP d’Azure VMware Solution HCX Cloud Manager que vous avez notée précédemment `https://x.x.x.9` et les informations d’identification d’un utilisateur qui détient le rôle CloudAdmin dans votre cloud privé. Sélectionnez **Connecter**.

   > [!NOTE]
   > Pour établir une paire de sites :
   > * Votre connecteur VMware HCX doit pouvoir router vers l’IP de votre gestionnaire de cloud HCX via le port 443.
   >
   > * Un compte de service de votre source d’identité externe, par exemple Active Directory, est recommandé pour les connexions de jumelage de sites. Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](./concepts-identity.md).

   Un écran s’affiche montrant que votre gestionnaire de cloud VMware HCX d’Azure VMware Solution et votre connecteur HCX local sont connectés (appairés).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Capture d’écran montrant l’appairage du gestionnaire HCX d’Azure VMware Solution et du connecteur VMware HCX.":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Couplage de sites HCX](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

## <a name="create-network-profiles"></a>Créer des profils réseau

Le connecteur VMware HCX déploie un sous-ensemble d’appliances virtuelles (automatisées) qui nécessitent plusieurs segments IP. Lorsque vous créez vos profils réseau, vous utilisez les segments IP que vous avez identifiés lors de la phase de [Planification](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  Vous allez créer quatre profils réseau :

   - Gestion
   - vMotion
   - Réplication
   - Liaison montante

1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage de service multisite** > **Profils réseau** > **Créer un profil réseau**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Capture d’écran montrant où créer un profil réseau dans le client vSphere." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pour chaque profil réseau, sélectionnez le réseau ainsi que le groupe de ports, puis fournissez un nom et créez le pool d’adresses IP du segment. Sélectionnez ensuite **Créer**.

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Capture d’écran montrant les détails d’un nouveau profil réseau." lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Profil réseau HCX](https://www.youtube.com/embed/O0rU4jtXUxc).

## <a name="create-a-compute-profile"></a>Créer un profil de calcul

1. Sous **Infrastructure**, sélectionnez **Interconnect** (Interconnexion) > **Compute Profiles**(Profils de calcul) > **Create Compute Profile (Créer un profil de calcul)** .

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

1. Dans **Select Management Network Profile**, sélectionnez le profil réseau de gestion que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de gestion, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Dans **Select Uplink Network Profile**, sélectionnez le profil réseau de liaison montante que vous avez créé au cours de la procédure précédente. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de liaison montante, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Dans **Select Motion Network Profile**, sélectionnez le profil réseau vMotion que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau vMotion, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Dans **Select vSphere Replication Network Profile**, sélectionnez le profil réseau de réplication que vous avez créé au cours des étapes précédentes. Sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Capture d’écran montrant la sélection d’un profil réseau de réplication, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Dans **Select Distributed Switches for Network Extensions**, sélectionnez les commutateurs qui contiennent les machines virtuelles à migrer vers Azure VMware Solution sur un réseau étendu de couche 2. Sélectionnez **Continuer**.

   > [!NOTE]
   > Si vous ne migrez pas de machines virtuelles sur des réseaux étendus de couche 2 (L2), vous pouvez ignorer cette étape.

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Capture d’écran montrant la sélection des commutateurs virtuels distribués, et celle du bouton Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Passez en revue les règles de connexion et sélectionnez **Continuer**.

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Capture d’écran montrant les règles de connexion et le bouton Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Sélectionnez **Terminer** pour créer le profil de calcul.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Capture d’écran affichant des informations de profil de calcul." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : Profil de calcul](https://www.youtube.com/embed/e02hsChI3b8).

## <a name="create-a-service-mesh"></a>Créer une maille de services.

>[!IMPORTANT]
>Vérifiez que les ports UDP 500/4500 sont ouverts entre les adresses du profil réseau « liaison montante » de votre connecteur VMware HCX local et les adresses du profil réseau « liaison montante » du cloud HCX d’Azure VMware Solution.


1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage du service** > **Créer le maillage du service**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Capture d’écran des sélections permettant de commencer à créer une maille de services." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Passez en revue les sites préremplis, puis sélectionnez **Continuer**.

   > [!NOTE]
   > S’il s’agit de votre première configuration de maillage de service, vous ne devez pas modifier cet écran.

1. Sélectionnez les profils de calcul source et distant dans les listes déroulantes, puis sélectionnez **Continue**.

   Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services VMware HCX.

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Capture d’écran montrant la sélection du profil de calcul source." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Capture d’écran montrant la sélection du profil de calcul distant." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Passez en revue les services qui seront activés, puis sélectionnez **Continue**.

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

   >[!NOTE]
   >Après avoir établi la maille de services, vous remarquerez peut-être un nouveau magasin de données et un nouvel hôte dans votre cloud privé. Il s’agit d’un comportement parfaitement normal après l’établissement d’une maille de services.
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="Capture d’écran montrant le service le magasin de données de maille HCX et l’hôte." lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

L’état du tunnel d’interconnexion HCX doit indiquer **UP** (Actif) et être en vert. Vous êtes prêt à migrer et à protéger les machines virtuelles Azure VMware Solution à l’aide de VMware HCX. Azure VMware Solution prend en charge les migrations de charge de travail (avec ou sans extension réseau). Ainsi, vous pouvez toujours migrer des charges de travail dans votre environnement vSphere, en même temps que la création locale de réseaux et le déploiement de machines virtuelles sur ces réseaux. Pour plus d’informations, consultez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html). 



Pour une vue d’ensemble de bout en bout de cette procédure, consultez la vidéo [Azure VMware Solution : La maille de services](https://www.youtube.com/embed/COY3oIws108).


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré le connecteur HCX, vous pouvez également vous intéresser aux points suivants :

- [Créer une extension réseau HCX](configure-hcx-network-extension.md)

- [Guide Mobility Optimized Networking (MON) pour VMware HCX](vmware-hcx-mon-guidance.md)

