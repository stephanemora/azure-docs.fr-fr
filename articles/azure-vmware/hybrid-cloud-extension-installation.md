---
title: Installer HCX (Hybrid Cloud Extension)
description: Configurer la solution HCX (Hybrid Cloud Extension) VMware pour votre cloud privé AVS (Azure VMware Solution)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: dc5f7f82b83c82538b2d5a7b4c87131afb3fcc20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873646"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installer HCX pour Azure VMware Solution

Dans cet article, nous explorons les procédures pour configurer la solution HCX (Hybrid Cloud Extension) VMware pour votre cloud privé AVS (Azure VMware Solution). HCX Advanced (installation par défaut) prend en charge jusqu’à trois sites externes, où chaque site externe nécessite qu’un connecteur ou un gestionnaire HCX Enterprise soit installé et activé.
HCX permet la migration de vos charges de travail VMware vers le cloud et ou d’autres sites connectés par le biais de différents types de migration HCX intégrés pris en charge. Si plus de trois sites sont requis, les clients ont la possibilité d’activer le module complémentaire HCX Enterprise via le Support. HCX Enterprise entraîne des frais supplémentaires pour les clients après la disponibilité générale, mais fournit des [fonctionnalités supplémentaires](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

Commencez par passer en revue minutieusement les sections [Avant de commencer](#before-you-begin), [Exigences de la version logicielle](#software-version-requirements) et [Prérequis](#prerequisites). 

Ensuite, nous allons passer en revue toutes les procédures nécessaires pour :

> [!div class="checklist"]
> * Déployer HCX OVA sur site
> * Activer et configurer HCX
> * Configurer la liaison montante réseau et le maillage du service
> * Terminer la configuration en vérifiant l’état de l’appliance

Une fois la configuration terminée, vous trouverez les étapes suivantes recommandées.

## <a name="before-you-begin"></a>Avant de commencer
    
* Passez en revue la [série de tutoriels](tutorial-network-checklist.md) AVS Software Defined Datacenter (SDDC)
* Consultez et référencez la [documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) y compris le Guide de l’utilisateur HCX
* Consultez les documents VMware [Migration de machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Consultez éventuellement les [considérations relatives au déploiement de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Consultez éventuellement les ressources VMware associées sur HCX, comme la [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere sur HCX. 
* Commandez une activation d’AVS HCX Enterprise via les canaux de support AVS.

Le dimensionnement des charges de travail par rapport aux ressources de calcul et de stockage est une étape de planification essentielle lors de la préparation à l’utilisation de la solution AVS Private Cloud HCX. Cette étape de dimensionnement doit être traitée dans le cadre de la planification initiale de l’environnement de cloud privé. 

## <a name="software-version-requirements"></a>Exigences de la version logicielle
Les composants d’infrastructure doivent exécuter la version minimale requise. 
                                                         
| Type de composant                                                          | Exigences de l’environnement source                                                                   | Exigences de l’environnement de destination                                                                      |
| --- | --- | --- |
| Serveur vCenter                                                          | 5,1<br/><br/>Si vous utilisez 5.5 U1 ou une version antérieure, utilisez l’interface utilisateur HCX autonome pour les opérations HCX.         | 6.0 U2 et versions ultérieures                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 et versions ultérieures                                                                                        |
| NSX                                                                     | Pour l’extension réseau HCX des commutateurs logiques à la source : NSXv 6.2+ ou NSX-T 2.4+              | NSXv 6.2+ ou NSX-T 2.4+<br/><br/Pour le routage de proximité HCX : NSXv 6.4+ (routage de proximité non pris en charge avec NSX-T) |
| vCloud Director                                                         | Non requis. Aucune interopérabilité avec vCloud Director sur le site source | Lorsque l’environnement de destination est intégré à vCloud Director, la version minimale est 9.1.0.2.              |

## <a name="prerequisites"></a>Prérequis

* La portée globale doit être configurée entre les circuits locaux et AVS SDDC ER.

* Tous les ports requis doivent être ouverts entre les circuits locaux et AVS SDDC (voir [la documentation VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Une adresse IP pour HCX Manager sur site et au minimum deux adresses IP pour l’appliance IX (Interconnect, interconnexion) et NE (Network Extension, extension réseau).

* Les appliances HCX IX et NE locales doivent pouvoir accéder à l’infrastructure vCenter et ESXi.

* Pour déployer l’appliance d’interconnexion WAN, en plus du bloc d’adresses réseau CIDR /22 utilisé pour le déploiement SDDC dans le portail Azure, HCX nécessite un bloc /29. Vous devez prendre cela en compte dans la planification de votre réseau.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Déployer VMware HCX OVA sur site

1. Connectez-vous à AVS SDDC vCenter et sélectionnez **HCX**.

    ![Sélectionner HCX dans AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Pour télécharger le fichier VMware HCX OVA, sélectionnez **Administration** > **Mises à jour du système**.

    ![Obtenir les mises à jour du système](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Sélectionnez un modèle OVF à déployer sur vCenter local.  

    ![Sélectionner un modèle OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Sélectionnez un nom et un emplacement, puis sélectionnez une ressource/un cluster où HCX doit être déployé. Examinez ensuite les détails et les ressources requises.  

    ![Examiner les détails du modèle](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Passez en revue les termes du contrat de licence et, si vous les acceptez, sélectionnez le stockage et le réseau requis. Sélectionnez ensuite **Suivant**.

1. Dans **Personnaliser le modèle**, entrez toutes les informations requises. 

    ![Personnaliser le modèle](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Sélectionnez **Suivant**, vérifiez la configuration, puis sélectionnez **Terminer** pour déployer HCX OVA.

## <a name="activate-hcx"></a>Activer HCX

Après l’installation, exécutez les étapes suivantes.

1. Ouvrez HCX Manager à `https://HCXManagerIP:9443` et connectez-vous avec votre nom d’utilisateur et votre mot de passe. 

1. Dans **Gestion des licences**, entrez votre **Clé avancée HCX**.  

    ![Entrer la clé HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager doit avoir un accès Internet ouvert ou un proxy configuré.

1. Configurez vCenter.

    ![Configurer vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Dans **Emplacement du centre de données**, si nécessaire, modifiez l’emplacement du centre de centres.

    ![Emplacement de la base de données](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configurer HCX 

1. Connectez-vous à vCenter local, puis sélectionnez **Accueil** > **HCX**.

    ![HCX dans VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Sélectionnez **Infrastructure** > **Appairage de site** > **Ajouter un appairage de site**.

    ![Ajouter un appairage de site](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Entrez **l’URL HCX à distance**, le **nom d’utilisateur** et le **mot de passe**. Sélectionnez **Connecter**.

   Le système affiche le site connecté.
   
    ![Connexion au site](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Sélectionnez **Interconnexion** > **Maillage de service multisite** > **Profils réseau** > **Créer un profil réseau**.

    ![Créer un profil réseau](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Entrez les plages d’adresses IP HCX IX et NE (un minimum de deux adresses IP est requis pour les appliances IX et NE).
    
   ![Entrer les plages d’adresses IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > L’appliance d’extension réseau (HCX-NE) a une relation un-à-un avec un commutateur virtuel distribué (DVS).  

1. Sélectionnez à présent **Profil de calcul** > **Créer un profil de calcul**.

1. Entrez un nom de profil de calcul et sélectionnez **Continuer**.  

    ![Créer un profil de calcul](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Sélectionnez les services à activer, comme la migration, l’extension réseau et la récupération d’urgence pr. Sélectionnez **Continuer**.

    ![Sélectionner des services](./media/hybrid-cloud-extension-installation/select-services.png)

1. Dans **Sélectionner des ressources de service**, sélectionnez une ou plusieurs ressources de service pour lesquelles les services HCX sélectionnés doivent être activés. Sélectionnez **Continuer**.
    
   ![Sélectionner des ressources de service](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Sélectionnez des clusters spécifiques dans lesquels les machines virtuelles sources sont ciblées pour la migration à l’aide de HCX.

1. Sélectionnez **Magasin de données** et **Continuer**. 
      
    Sélectionnez chaque ressource de calcul et de stockage pour déployer les appliances d’interconnexion HCX. Lorsque plusieurs ressources sont sélectionnées, HCX utilise la première ressource sélectionnée jusqu’à épuisement de sa capacité.  
    
    ![Sélectionner les ressources de déploiement](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Sélectionnez le profil réseau d’administration créé dans **Profils réseau** et sélectionnez **Continuer**.  
      
    Sélectionnez le profil réseau via lequel l’interface d’administration de vCenter et les hôtes ESXi peuvent être atteints. Si vous n’avez pas encore défini de profil réseau, vous pouvez le créer ici.  
    
    ![Sélectionner le profil réseau d’administration](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Sélectionnez **Liaison montante réseau** et **Continuer**.
      
    Sélectionnez un ou plusieurs profils réseau pour que l’une des conditions suivantes soit remplie :  
    * Les appliances d’interconnexion sur le site distant sont accessibles via ce réseau.  
    * Les appliances côté distant peuvent accéder aux appliances d’interconnexion locales via ce réseau.  
    
    Si vous avez des réseaux point à point comme Direct Connect qui ne sont pas partagés entre plusieurs sites, vous pouvez ignorer cette étape, car les profils de calcul sont partagés entre plusieurs sites. Dans ce cas, les profils réseau de liaison montante peuvent être remplacés et spécifiés lors de la création du maillage du service d’interconnexion.  
    
    ![Sélectionner le profil réseau de liaison montante](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Sélectionnez **Profil réseau vMotion** et **Continuer**.
      
    Sélectionnez le profil réseau via lequel l’interface vMotion des hôtes ESXi peut être atteinte. Si vous n’avez pas encore défini de profil réseau, vous pouvez le créer ici. Si vous n’avez pas de réseau vMotion, sélectionnez **Profil réseau d’administration**.  
    
    ![Sélectionner le profil réseau vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Sélectionnez **Profil réseau de réplication vSphere** et **Continuer**.
      
    Sélectionnez un profil réseau via lequel l’interface de réplication vSphere des hôtes ESXi peut être atteinte. Dans la plupart des cas, ce profil est le même que le profil réseau d’administration.  
    
    ![Sélectionner le profil réseau de réplication vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Sélectionnez **Commutateurs distribués pour les extensions réseau** et **Continuer**.  
      
    Sélectionnez les commutateurs virtuels distribués sur lesquels vous avez des réseaux auxquels sont connectées les machines virtuelles à migrer.

    ![Sélectionner les commutateurs virtuels distribués](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Passez en revue les règles de connexion et sélectionnez **Continuer**. Sélectionnez **Terminer** pour créer le profil de calcul.  

    ![Créer un profil de calcul](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>Configurer la liaison montante réseau

À présent, configurez la modification du profil réseau dans AVS SDDC pour la liaison montante réseau.

1. Connectez-vous à SDDC NSX-T pour créer un commutateur logique ou utilisez un commutateur logique existant qui peut être utilisé pour la liaison montante réseau entre le SDDC local et le SDDC AVS.

1. Créez un profil réseau pour la liaison montante HCX dans le SDDC AVS qui peut être utilisé pour la communication de l’environnement local vers le SDDC AVS.  
    
   ![Créer un profil réseau pour la liaison montante](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Entrez un nom pour le profil réseau et au moins 4-5 adresses IP libres en fonction de l’extension réseau L2 requise.  
    
   ![Configurer le profil réseau pour la liaison montante](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Sélectionnez **Créer** pour terminer la configuration du SDDC AVS

## <a name="configure-service-mesh"></a>Configurer le maillage du service

À présent, configurez le maillage du service entre le SDDC local et le SDDC AVS.

1. Connectez-vous à AVS SDDC vCenter et sélectionnez **HCX**.

1. Sélectionnez **Infrastructure** > **Interconnexion** > **Maillage du service** > **Créer un maillage de service**.  Configurez le réseau et les profils de calcul créés dans les étapes précédentes.    
      
    ![Configurer le maillage du service](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. Sélectionnez **Créer un maillage de service** et **Continuer**.  
      
    Sélectionnez les sites associés entre lesquels activer la mobilité hybride.  
    
    ![Sélectionner les sites associés](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. Sélectionnez **Profil de calcul** et **Continuer**.
      
    Sélectionnez un profil de calcul sur chaque site source et distant pour activer les services d’hybridation. Les sélections définissent les ressources, où les machines virtuelles pourront utiliser les services HCX.  
      
    ![Activer les services d’hybridation](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. Sélectionnez les services à activer pour HCX, puis sélectionnez **Continuer**.  
      
    ![Sélectionner les services HCX](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. Dans **Configuration avancée - Remplacer les profils réseau de liaison montante**, sélectionnez **Continuer**.  
      
    Les profils réseau de liaison montante sont utilisés pour se connecter au réseau via lequel les appliances d’interconnexion du site distant peuvent être atteintes.  
      
    ![Remplacer les profils de liaison montante](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. Dans **Configuration avancée - Effectuer un scale-out de l’appliance d’extension réseau**, sélectionnez **Configurer le scale-out de l’appliance d’extension réseau**. 
      
    ![Scale-out de l’extension réseau](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. Entrez le nombre d’appliances correspondant au nombre de commutateurs DVS.  
      
    ![Configurer le nombre d’appliances](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. Dans **Configuration avancée - Ingénierie du trafic**, sélectionnez **Continuer**.  
      
    ![Configurer l’ingénierie du trafic](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. Passez en revue l’aperçu de la topologie, puis sélectionnez **Continuer**. Ensuite, entrez un nom convivial pour ce maillage de service et sélectionnez **Terminer**.  
      
    ![Terminer le maillage du service](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Le maillage du service est déployé et configuré.  
      
    ![Maillage du service déployé](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Vérifier l’état de l’appliance
Pour vérifier l’état de l’appliance, sélectionnez **Interconnexion** > **Appliances**. 
      
![État de l’appliance](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Étapes suivantes

Lorsque l’état d’interconnexion de l’appliance **État du tunnel** est **UP** et vert, vous êtes prêt à migrer et à protéger les machines virtuelles AVS à l’aide de HCX. Consultez [la documentation de VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) et [Migration de machines virtuelles avec VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) dans la documentation technique de VMware.
