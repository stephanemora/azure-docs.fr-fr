---
title: Installer HCX (Hybrid Cloud Extension)
description: Configurer la solution HCX (Hybrid Cloud Extension) VMware pour votre cloud privé Azure VMware Solution
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: fb8497af33b364c1d2ab475233bd2a83ef1befad
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752321"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installer HCX pour Azure VMware Solution

Dans cet article, nous explorons les procédures pour configurer la solution HCX (Hybrid Cloud Extension) VMware pour votre cloud privé Azure VMware Solution. HCX permet la migration de vos charges de travail VMware vers le cloud et d’autres sites connectés par le biais de différents types de migration HCX intégrés pris en charge.

HCX Advanced, l’installation par défaut, prend en charge jusqu’à trois connexions de site (localement ou cloud vers cloud). Si plus de trois connexions de site sont requises, les clients ont la possibilité d’activer le module complémentaire HCX Enterprise via le support, qui est actuellement disponible en préversion. HCX Enterprise entraîne des frais supplémentaires pour les clients après la disponibilité générale, mais fournit des [fonctionnalités supplémentaires](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


Commencez par passer en revue minutieusement les sections [Avant de commencer](#before-you-begin), [Exigences de la version logicielle](#software-version-requirements) et [Prérequis](#prerequisites). 

Ensuite, nous parcourrons pas à pas toutes les procédures nécessaires pour :

> [!div class="checklist"]
> * Déployer HCX OVA localement (connecteur)
> * Activer et configurer HCX
> * Configurer la liaison montante réseau et le maillage du service
> * Terminer la configuration en vérifiant l’état de l’appliance

Une fois l’installation terminée, vous pouvez appliquer les étapes suivantes recommandées à la fin de cet article.  

## <a name="before-you-begin"></a>Avant de commencer
    
* Consultez la [série de tutoriels](tutorial-network-checklist.md) Azure VMware Solution Software Defined Datacenter (SDDC).
* Consultez et référencez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), notamment le guide de l’utilisateur HCX.
* Consultez [Migration des machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation de VMware.
* Consultez éventuellement les [considérations relatives au déploiement de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Consultez éventuellement les ressources VMware associées sur HCX, comme la [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere sur HCX. 
* Demandez l’activation de HCX Enterprise Azure VMware Solution par le biais des canaux de support Azure VMware Solution.

Le dimensionnement des charges de travail par rapport aux ressources de calcul et de stockage est une étape de planification essentielle lorsque vous vous préparez à utiliser la solution Azure VMware Solution Private Cloud HCX. Traitez cette étape de dimensionnement dans le cadre de la planification initiale de l’environnement de cloud privé. 

Vous pouvez également dimensionner les charges de travail en effectuant une évaluation Azure VMware Solution dans le portail Azure Migrate (https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) ).

## <a name="software-version-requirements"></a>Exigences de la version logicielle

Les composants d’infrastructure doivent exécuter la version minimale requise. 
                                                         
| Type de composant    | Exigences de l’environnement source    | Exigences de l’environnement de destination   |
| --- | --- | --- |
| Serveur vCenter   | 5,1<br/><br/>Si vous utilisez 5.5 U1 ou une version antérieure, utilisez l’interface utilisateur HCX autonome pour les opérations HCX.  | 6.0 U2 et versions ultérieures   |
| ESXi   | 5.0    | ESXi 6.0 et versions ultérieures   |
| NSX    | Pour l’extension réseau HCX des commutateurs logiques à la source : NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Pour le routage de proximité HCX : NSXv 6.4+ (le routage de proximité n’est pas pris en charge avec NSX-T) |
| vCloud Director   | Non requis. Aucune interopérabilité avec vCloud Director sur le site source | Lorsque l’environnement de destination est intégré à vCloud Director, la version minimale est 9.1.0.2.  |

## <a name="prerequisites"></a>Prérequis

* ExpressRoute Global Reach doit être configuré entre les circuits locaux et ExpressRoute Azure VMware Solution SDDC.

* Tous les ports requis doivent être ouverts entre les circuits locaux et Azure VMware Solution SDDC (voir [la documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Une adresse IP pour HCX Manager sur site et au minimum deux adresses IP pour l’appliance IX (Interconnect, interconnexion) et NE (Network Extension, extension réseau).

* Les appliances HCX IX et NE locales doivent pouvoir accéder à l’infrastructure vCenter et ESXi.

* Pour déployer l’appliance d’interconnexion WAN, en plus du bloc d’adresses réseau CIDR /22 utilisé pour le déploiement SDDC dans le portail Azure, HCX nécessite un bloc /29. Veillez à tenir compte de cette exigence dans la planification de votre réseau.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Déployer VMware HCX OVA sur site

1. Connectez-vous à Azure VMware Solution SDDC vCenter et sélectionnez **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/avs-vsphere-client.png" alt-text="Connectez-vous à Azure VMware Solution SDDC vCenter et sélectionnez HCX.":::

1. Sous **Administration**, sélectionnez **Mises à jour système**, puis sélectionnez **Demander le lien de téléchargement** pour télécharger le fichier VMware HCX OVA.

   :::image type="content" source="media/hybrid-cloud-extension-installation/administration-updates.png" alt-text="Sous Administration, sélectionnez Mises à jour système, puis sélectionnez Demander le lien de téléchargement pour télécharger le fichier VMware HCX OVA.":::

1. Ensuite, accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Ensuite, accédez au vCenter local et sélectionnez un modèle OVF à déployer sur votre vCenter local.":::

1. Sélectionnez un nom et un emplacement, puis sélectionnez une ressource/un cluster où HCX doit être déployé. Examinez ensuite les détails et les ressources requises.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Sélectionnez un nom et un emplacement, puis sélectionnez une ressource/un cluster où HCX doit être déployé. Examinez ensuite les détails et les ressources requises.":::

1. Passez en revue les termes du contrat de licence et, si vous les acceptez, sélectionnez le stockage et le réseau requis. Sélectionnez ensuite **Suivant**.

1. Dans **Personnaliser le modèle**, entrez toutes les informations requises. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="Dans Personnaliser le modèle, entrez toutes les informations requises.":::

1. Sélectionnez **Suivant**, vérifiez la configuration, puis sélectionnez **Terminer** pour déployer HCX OVA.

## <a name="activate-hcx"></a>Activer HCX

Après l’installation, exécutez les étapes suivantes.

1. Connectez-vous au gestionnaire HCX local à l’adresse `https://HCXManagerIP:9443` et connectez-vous avec votre nom d’utilisateur et votre mot de passe. 

   > [!IMPORTANT]
   > Veillez à inclure le numéro de port `9443` avec l’adresse IP de HCX Manager.

1. Dans **Gestion des licences**, entrez votre **Clé avancée HCX**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="Dans Gestion des licences, entrez votre Clé avancée HCX.":::
    
    > [!NOTE]
    > HCX Manager doit avoir un accès Internet ouvert ou un proxy configuré.

1. Dans **vCenter**, si nécessaire, modifiez les informations de vCenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="Dans vCenter, si nécessaire, modifiez les informations de vCenter.":::

1. Dans **Emplacement du centre de données**, si nécessaire, modifiez l’emplacement du centre de centres.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="Dans Emplacement du centre de données, si nécessaire, modifiez l’endroit du centre de données.":::

## <a name="configure-hcx"></a>Configurer HCX 

1. Connectez-vous au vCenter local, puis sous **Accueil**, sélectionnez **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Connectez-vous au vCenter local puis, sous Accueil, sélectionnez HCX.":::

1. Sous **Infrastructure**, sélectionnez **Appairage de site** > **Ajouter un appairage de site**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="Sous Infrastructure, sélectionnez Appairage de site > Ajouter un appairage de site.":::

1. Entrez l’URL HCX distante ou l’adresse IP, le nom d’utilisateur et le mot de passe Azure VMware Solution cloudadmin, puis sélectionnez **Connecter**.

   Le système affiche le site connecté.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="Le système affiche le site connecté.":::

1. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage de service multisite** > **Profils réseau** > **Créer un profil réseau**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="Sous Infrastructure, sélectionnez Interconnexion > Maillage de service multisite > Profils réseau > Créer un profil réseau.":::

1. Pour le nouveau profil réseau, entrez les plages d’adresses IP HCX IX et NE (un minimum de deux adresses IP est requis pour les appliances IX et NE).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Pour le nouveau profil réseau, entrez les plages d’adresses IP HCX IX et NE (un minimum de deux adresses IP est requis pour les appliances IX et NE).":::
  
   > [!NOTE]
   > L’appliance d’extension réseau (HCX-NE) a une relation un-à-un avec un commutateur virtuel distribué (DVS).  

1. Sélectionnez à présent **Profil de calcul** > **Créer un profil de calcul**.

1. Entrez un nom de profil de calcul et sélectionnez **Continuer**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Entrez un nom de profil de calcul et sélectionnez Continuer.":::

1. Sélectionnez les services à activer, comme la migration, l’extension réseau et la récupération d’urgence, puis sélectionnez **Continuer**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Sélectionnez les services à activer, comme la migration, l’extension réseau ou la reprise d’activité, puis sélectionnez Continuer.":::

1. Dans **Sélectionner des ressources de service**, sélectionnez une ou plusieurs ressources de service pour lesquelles les services HCX sélectionnés doivent être activés. Sélectionnez **Continuer**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="Dans Sélectionner des ressources de service, sélectionnez une ou plusieurs ressources de service pour lesquelles les services HCX sélectionnés doivent être activés. Sélectionnez Continuer.":::
  
   > [!NOTE]
   > Sélectionnez des clusters spécifiques dans lesquels les machines virtuelles sources sont ciblées pour la migration à l’aide de HCX.

1. Sélectionnez **Magasin de données** et **Continuer**. 
      
   Sélectionnez chaque ressource de calcul et de stockage pour déployer les appliances d’interconnexion HCX. Lorsque plusieurs ressources sont sélectionnées, HCX utilise la première ressource sélectionnée jusqu’à épuisement de sa capacité.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Sélectionnez chaque ressource de calcul et de stockage pour déployer les appliances d’interconnexion HCX. Lorsque plusieurs ressources sont sélectionnées, HCX utilise la première ressource sélectionnée jusqu’à épuisement de sa capacité.":::

1. Sélectionnez le profil réseau d’administration créé dans **Profils réseau** et sélectionnez **Continuer**.  
      
   Sélectionnez le profil réseau via lequel l’interface d’administration de vCenter et les hôtes ESXi peuvent être atteints. Si vous n’avez pas encore défini de profil réseau, vous pouvez le créer ici.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Sélectionnez le profil réseau par le biais duquel l’interface d’administration de vCenter et les hôtes ESXi sont accessibles. Si vous n’avez pas encore défini de profil réseau, vous pouvez le créer ici.":::

1. Sélectionnez **Liaison montante réseau** et **Continuer**.
      
   Sélectionnez un ou plusieurs profils réseau pour que l’une des conditions suivantes soit remplie :  
   * Les appliances d’interconnexion sur le site distant sont accessibles via ce réseau.  
   * Les appliances côté distant peuvent accéder aux appliances d’interconnexion locales via ce réseau.  
    
   Si vous avez des réseaux point à point comme Direct Connect qui ne sont pas partagés entre plusieurs sites, vous pouvez ignorer cette étape, car les profils de calcul sont partagés entre plusieurs sites. Dans ce cas, les profils réseau de liaison montante peuvent être remplacés et spécifiés lors de la création du maillage du service d’interconnexion.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Sélectionnez Liaison montante réseau et Continuer.":::

1. Sélectionnez **Profil réseau vMotion** et **Continuer**.
      
   Sélectionnez le profil réseau via lequel l’interface vMotion des hôtes ESXi peut être atteinte. Si vous n’avez pas encore défini de profil réseau, vous pouvez le créer ici. Si vous n’avez pas de réseau vMotion, sélectionnez **Profil réseau d’administration**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Sélectionnez Profil réseau vMotion et Continuer.":::

1. Dans **Sélectionner le profil réseau de réplication vSphere**, sélectionnez un profil réseau dans l’interface de réplication vSphere des hôtes ESXi, puis sélectionnez **Continuer**.
      
   Dans la plupart des cas, ce profil est le même que le profil réseau d’administration.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="Dans Sélectionner le profil réseau de réplication vSphere, sélectionnez un profil réseau dans l’interface de réplication vSphere des hôtes ESXi, puis sélectionnez Continuer.":::

1. Dans **Sélectionner les commutateurs distribués pour les extensions réseau**, sélectionnez le DVS sur lequel vous disposez de réseaux auxquels les machines virtuelles seront intégrées et connectées.  Sélectionnez **Continuer**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="Dans Sélectionner les commutateurs distribués pour les extensions réseau, sélectionnez le DVS sur lequel vous disposez de réseaux auxquels les machines virtuelles seront intégrées et connectées.  Sélectionnez Continuer.":::

1. Passez en revue les règles de connexion et sélectionnez **Continuer**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Passez en revue les règles de connexion et sélectionnez Continuer.":::

1.  Sélectionnez **Terminer** pour créer le profil de calcul.

## <a name="configure-network-uplink"></a>Configurer la liaison montante réseau

À présent, configurez la modification du profil réseau dans Azure VMware Solution SDDC pour la liaison montante réseau.

1. Connectez-vous à SDDC NSX-T pour créer un commutateur logique ou utilisez un commutateur logique existant qui peut être utilisé pour la liaison montante réseau entre le SDDC local et le SDDC Azure VMware Solution.

1. Créez un profil réseau pour la liaison montante HCX dans le SDDC Azure VMware Solution qui peut être utilisé pour la communication de l’environnement local vers le SDDC Azure VMware Solution.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Créez un profil réseau pour la liaison montante HCX dans le SDDC Azure VMware Solution qui peut être utilisé pour la communication de l’environnement local vers le SDDC Azure VMware Solution.":::

1. Entrez un nom pour le profil réseau et au moins 4-5 adresses IP libres en fonction de l’extension réseau L2 requise.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Entrez un nom pour le profil réseau et au moins 4-5 adresses IP libres en fonction de l’extension réseau L2 requise.":::

1. Sélectionnez **Créer** pour terminer la configuration du SDDC Azure VMware Solution.

## <a name="configure-service-mesh"></a>Configurer le maillage du service

À présent, configurez le maillage du service entre le SDDC local et le SDDC Azure VMware Solution.

1. Connectez-vous à Azure VMware Solution SDDC vCenter et sélectionnez **HCX**.

2. Sous **Infrastructure**, sélectionnez **Interconnexion** > **Maillage de service** > **Créer un maillage de service** pour configurer le réseau et les profils de calcul créés dans les étapes précédentes.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="Sous Infrastructure, sélectionnez Interconnexion > Maillage de service > Créer un maillage de service pour configurer le réseau et les profils de calcul créés aux étapes précédentes.":::

3. Sélectionnez les sites associés pour activer la fonctionnalité hybride et sélectionnez **Continuer**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Sélectionnez les sites associés pour activer la fonctionnalité hybride, puis sélectionnez Continuer.":::

4. Sélectionnez la source et les profils de calcul distants pour activer les services d’hybridation, puis sélectionnez **Continuer**.
      
   Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services HCX.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="Les sélections définissent les ressources où les machines virtuelles peuvent consommer des services HCX.":::

5. Sélectionnez les services à activer, puis sélectionnez **Continuer**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Sélectionnez les services à activer, puis sélectionnez Continuer.":::

6. Dans **Configuration avancée - Remplacer les profils réseau de liaison montante**, sélectionnez **Continuer**.  
      
   Les profils réseau de liaison montante sont utilisés pour se connecter au réseau via lequel les appliances d’interconnexion du site distant peuvent être atteintes.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="Les profils réseau de liaison montante sont utilisés pour se connecter au réseau via lequel les appliances d’interconnexion du site distant peuvent être atteintes.":::

7. Sélectionnez **Configurer le scale-out de l’appliance d’extension réseau**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Sélectionnez Configurer le scale-out de l’appliance d’extension réseau.":::

8. Entrez le nombre d’appliances correspondant au nombre de commutateurs DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Entrez le nombre d’appliances correspondant au nombre de commutateurs DVS.":::

9. Sélectionnez **Continuer** pour ignorer.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Sélectionnez Continuer pour ignorer.":::

10. Passez en revue l’aperçu de la topologie, puis sélectionnez **Continuer**. 

11. Entrez un nom convivial pour ce maillage de service et sélectionnez **Terminer**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Terminer le maillage du service":::

   Le maillage du service est déployé et configuré.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Maillage du service déployé":::

## <a name="check-appliance-status"></a>Vérifier l’état de l’appliance
Pour vérifier l’état de l’appliance, sélectionnez **Interconnexion** > **Appliances**. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Vérifiez l’état de l’appliance.":::

## <a name="next-steps"></a>Étapes suivantes

Lorsque l’état d’interconnexion de l’appliance **État du tunnel** est **UP** et vert, vous êtes prêt à migrer et à protéger les machines virtuelles Azure VMware Solution à l’aide de HCX. Consultez [la documentation de VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) et [Migration de machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation technique de VMware.
