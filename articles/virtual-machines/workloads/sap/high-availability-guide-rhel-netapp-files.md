---
title: Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux avec Azure NetApp Files | Microsoft Docs
description: Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503786"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux avec Azure NetApp Files pour les applications SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Cet article décrit comment déployer et configurer les machines virtuelles, installer l’infrastructure de cluster et installer un système SAP NetWeaver 7.50 à haute disponibilité à l’aide d’[Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Dans les exemples de configuration, commandes d’installation, et ainsi de suite, L’instance ASCS correspond au numéro 00, l’instance ERS au numéro 01, l’instance d’application principale (PAS) au numéro 02 et l’instance d’application (AAS) au numéro 03. L’assurance qualité d’ID de système SAP est utilisée. 

La couche de base de données n’est pas couverte en détail dans cet article.  

Commencez par lire les notes et publications SAP suivantes :

* [Documentation Azure NetApp Files][anf-azure-doc] 
* Note SAP [1928533], qui contient :
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure

* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2002167] recommande des paramètres de système d’exploitation pour Red Hat Enterprise Linux
* La note SAP [2009879] conseille sur SAP HANA pour Red Hat Enterprise Linux
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [SAP Netweaver dans le cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentation RHEL générale
  * [Vue d’ensemble des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Référence des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuration d’ASC/ERS pour SAP Netweaver avec des ressources autonomes dans RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configure SAP S/4HANA ASCS/ERS with Standalone Enqueue Server 2 (ENSA2) in Pacemaker on RHEL ](https://access.redhat.com/articles/3974941) (Configurer SAP S/4HANA ASC/ERS avec le serveur de file d’attente autonome 2 (ENSA2) dans Pacemaker sur RHEL)
* Documentation RHEL spécifique à Azure :
  * [Stratégies de prise en charge des clusters à haute disponibilité RHEL - Machines virtuelles Microsoft Azure en tant que membres du cluster](https://access.redhat.com/articles/3131341)
  * [Installation et configuration d’un cluster à haute disponibilité Red Hat Enterprise Linux 7.4 (et versions ultérieures) sur Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Applications SAP NetApp su Microsoft Azure avec Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Vue d'ensemble

La haute disponibilité pour les services centraux SAP Netweaver nécessite un stockage partagé.
Pour y parvenir sur Red Hat Linux, il était jusqu'à présent nécessaire de générer un cluster GlusterFS hautement disponible distinct. 

Maintenant, il est possible d’atteindre la haute disponibilité SAP Netweaver à l’aide d’un stockage partagé, déployé sur Azure NetApp Files. L’utilisation d’Azure NetApp Files pour le stockage partagé élimine le besoin de [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs) supplémentaire. Pacemaker est toujours nécessaire pour la haute disponibilité des services (ASCS/SCS) centraux de SAP Netweaver.

![Vue d’ensemble de la haute disponibilité SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS et la base de données SAP HANA utilisent un nom d’hôte virtuel et des adresses IP virtuelles. Sur Azure, un équilibreur de charge est nécessaire pour utiliser une adresse IP virtuelle. La liste suivante illustre la configuration de l’équilibreur de charge avec des adresses IP frontales séparées pour les instances (A)SCS et ERS.

> [!IMPORTANT]
> Le clustering multi-SID de SAP ASC/ERS avec Red Hat Linux comme système d’exploitation invité des machines virtuelles Azure n’est **PAS pris en charge**. Le clustering multi-SID décrit l’installation de plusieurs instances de SAP ASCS/ERS avec des SID différents dans un cluster Pacemaker.

### <a name="ascs"></a>(A)SCS

* Configuration du frontend
  * Adresse IP 192.168.14.9
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster (A)SCS/ERS
* Port de la sonde
  * Port 620<strong>&lt;nr&gt;</strong>
* Règles d’équilibrage de charge
  * TCP 32<strong>&lt;nr&gt;</strong>
  * TCP 36<strong>&lt;nr&gt;</strong>
  * TCP 39<strong>&lt;nr&gt;</strong>
  * TCP 81<strong>&lt;nr&gt;</strong>
  * TCP 5<strong>&lt;nr&gt;</strong>13
  * TCP 5<strong>&lt;nr&gt;</strong>14
  * TCP 5<strong>&lt;nr&gt;</strong>16

### <a name="ers"></a>ERS

* Configuration du frontend
  * Adresse IP 192.168.14.10
* Configuration du backend
  * Connecté aux interfaces réseau principales de toutes les machines virtuelles qui doivent faire partie du cluster (A)SCS/ERS
* Port de la sonde
  * Port 621<strong>&lt;nr&gt;</strong>
* Règles d’équilibrage de charge
  * TCP 32<strong>&lt;nr&gt;</strong>
  * TCP 33<strong>&lt;nr&gt;</strong>
  * TCP 5<strong>&lt;nr&gt;</strong>13
  * TCP 5<strong>&lt;nr&gt;</strong>14
  * TCP 5<strong>&lt;nr&gt;</strong>16

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configuration de l’infrastructure Azure NetApp Files 

SAP NetWeaver nécessite un stockage partagé pour le répertoire de transport et de profil.  Avant de poursuivre la configuration de l’infrastructure Azure NetApp Files, familiarisez-vous avec la [documentation Azure NetApp Files][anf-azure-doc]. Vérifiez si la région Azure sélectionnée est compatible avec Azure NetApp Files. Le lien suivant indique la disponibilité d’Azure NetApp Files pour chaque région Azure : [Disponibilité d’Azure NetApp Files pour chaque région Azure][anf-avail-matrix].

Azure NetApp Files est disponible dans plusieurs [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Avant de déployer Azure NetApp Files, demandez l’intégration à Azure NetApp Files, en suivant les [instructions d’inscription à Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Déployer des ressources Azure NetApp Files  

Les étapes supposent que vous avez déjà déployé un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Les ressources Azure NetApp Files et les machines virtuelles, où les ressources Azure NetApp Files seront montées, doivent être déployées dans le même réseau virtuel Azure ou dans des réseaux virtuels homologués Azure.  

1. Si ce n’est pas encore fait, demandez l’[intégration à Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Créez le compte de NetApp dans la région Azure sélectionnée en suivant les [instructions de création de compte NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurez le pool de capacité Azure NetApp Files en suivant les [instructions de configuration du pool de capacité Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
L’architecture de SAP Netweaver présentée dans cet article utilise un seul pool de capacité Azure NetApp Files, référence SKU Premium. Nous vous recommandons la référence SKU Premium de Azure NetApp Files pour la charge de travail applicative de SAP Netweaver sur Azure.  
4. Déléguez un sous-réseau pour Azure NetApp Files, comme décrit dans les [instructions de délégation d’un sous-réseau pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Déployez des volumes Azure NetApp Files en suivant les [instructions de création de volume pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Déployez les volumes dans le [sous-réseau](https://docs.microsoft.com/rest/api/virtualnetwork/subnets) Azure NetApp Files désigné. Gardez à l’esprit que les ressources Azure NetApp Files et les machines virtuelles Azure doivent être dans le même réseau virtuel Azure ou dans des réseaux virtuels homologués Azure. Dans cet exemple, nous utilisons deux volumes Azure NetApp Files : sap<b>QAS</b> et transSAP. Les chemins d’accès de fichier montés sur les points de montage correspondants sont /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, etc.  

   1. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
Dans cet exemple, nous avons utilisé Azure NetApp Files pour tous les systèmes de fichiers SAP Netweaver afin d’illustrer comment Azure NetApp Files peut être utilisé. Les systèmes de fichiers SAP qui n’ont pas besoin d’être montés par le biais de NFS peuvent également être déployés en tant que [stockage sur disque Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd). Dans cet exemple <b>a-e</b> doit se trouver sur Azure NetApp Files et <b>f-g</b> (autrement dit, /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) peut être déployé en tant que stockage sur disque Azure. 

### <a name="important-considerations"></a>Points importants à prendre en compte

Lorsque vous envisagez d’utiliser Azure NetApp Files pour SAP Netweaver sur une architecture à haute disponibilité SUSE, tenez compte des considérations importantes suivantes :

- La taille de pool de capacité minimale est de 4 Tio. La taille de pool de capacité doit être un multiple de 4 Tio.
- Le volume minimal est de 100 Gio.
- Azure NetApp Files et toutes les machines virtuelles, où les volumes Azure NetApp Files seront montés, doivent être déployés dans le même réseau virtuel Azure ou dans des [réseaux virtuels homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) de la même région. L’accès à Azure NetApp Files via l’homologation de réseaux virtuels dans la même région est maintenant pris en charge. L’accès à Azure NetApp via l’homologation globale n’est pas encore pris en charge.
- Le réseau virtuel sélectionné doit avoir un sous-réseau délégué à Azure NetApp Files.
- Azure NetApp Files ne prend actuellement en charge que NFSv3. 
- Azure NetApp Files propose une [stratégie d’exportation](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) : vous pouvez contrôler les clients autorisés, le type d’accès (lecture et écriture, lecture seule, etc.). 
- La fonctionnalité Azure NetApp Files ne tient pas encore compte des zones. Actuellement, la fonctionnalité Azure NetApp Files n’est pas déployée dans toutes les zones de disponibilité d’une région Azure. Méfiez-vous de l’impact potentiel sur les temps de latence dans certaines régions Azure. 

## <a name="setting-up-ascs"></a>Configuration de (A)SCS

Dans cet exemple, les ressources ont été déployées manuellement le [portail Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Déployer manuellement Linux via le portail Azure

Vous devez d’abord créer les volumes Azure NetApp Files. Déployez les machines virtuelles. Par la suite, vous créez un équilibreur de charge et utilisez les machines virtuelles dans les pools principaux.

1. Créer un équilibrage de charge (interne)  
   1. Créer les adresses IP de serveurs frontaux
      1. Adresse IP 192.168.14.9 pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner le pool d’adresses IP frontal et cliquer sur Ajouter
         1. Entrer le nom du nouveau pool d’adresses IP frontal (par exemple, **frontend.QAS.ASCS**)
         1. Définir l’affectation sur Statique et entrer l’adresse IP (par exemple, **192.168.14.9**)
         1. Cliquez sur OK
      1. Adresse IP 192.168.14.10 pour les instances ASCS ERS
         * Répéter les étapes du point « a » afin de créer une adresse IP pour l’instance ERS (par exemple, **192.168.14.10** et **frontend.QAS.ERS**)
   1. Créer les pools principaux
      1. Créer un pool principal pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner les pools principaux et cliquer sur Ajouter
         1. Entrer le nom du nouveau pool principal (par exemple, **backend.QAS**)
         1. Cliquer sur Ajouter une machine virtuelle
         1. Sélectionner le groupe à haute disponibilité créé précédemment pour l’instance ASCS 
         1. Sélectionner les machines virtuelles du cluster (A)SCS
         1. Cliquez sur OK
   1. Créer les sondes d’intégrité
      1. Port 620**00** pour l’instance ASCS
         1. Ouvrir l’équilibrage de charge, sélectionner les sondes d’intégrité et cliquer sur Ajouter
         1. Entrer le nom de la nouvelle sonde d’intégrité (par exemple, **health.QAS.ASCS**).
         1. Sélectionner le protocole TCP et le port 620**00**, et conserver un intervalle de 5 et un seuil de défaillance sur le plan de l’intégrité de 2
         1. Cliquez sur OK
      1. Port 621**01** pour les instances ASCS ERS
            * Répéter les étapes du point « c » afin de créer une sonde d’intégrité pour l’instance ERS (par exemple, 621**01** et **health.QAS.ERS**)
   1. Règles d’équilibrage de charge
      1. TCP 32**00** pour l’instance ASCS
         1. Ouvrir l’équilibreur de charge, sélectionner les règles d’équilibrage de charge et cliquer sur Ajouter
         1. Entrer le nom de la nouvelle règle d’équilibrage de charge (par exemple, **lb.QAS.ASCS.3200**).
         1. Sélectionner l’adresse IP du serveur frontal pour l’instance ASCS, le pool principal et la sonde d’intégrité créés précédemment (par exemple, **frontend.QAS.ASCS**)
         1. Conserver le protocole **TCP** et indiquer le port **3200**
         1. Augmenter le délai d’inactivité à 30 minutes
         1. **Veiller à activer IP flottante**
         1. Cliquez sur OK
      1. Ports supplémentaires pour l’instance ASCS
         * Répéter les étapes du point « d » pour les ports 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 et TCP pour l’instance ASCS
      1. Ports supplémentaires pour les instances ASCS ERS
         * Répéter les étapes du point « d » pour les ports 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 et TCP pour les instances ASCS ERS


> [!IMPORTANT]
> N’activez pas les timestamps TCP sur des machines virtuelles Azure placées derrière Azure Load Balancer. L’activation des timestamps TCP entraîne l’échec des sondes d’intégrité. Définissez le paramètre **net.ipv4.tcp_timestamps** sur **0**. Pour plus d’informations, consultez [Load Balancer health probes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) (Sondes d’intégrité Load Balancer).

### <a name="create-pacemaker-cluster"></a>Créer le cluster Pacemaker

Suivez les étapes décrites sur la page [Configuration de Pacemaker sur Red Hat Enterprise Linux dans Azure](high-availability-guide-rhel-pacemaker.md) pour créer un cluster Pacemaker de base pour ce serveur (A)SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Préparer l’installation de SAP NetWeaver

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Configurer la résolution de nom d’hôte

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes

    ```
    sudo vi /etc/hosts
    ```

   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  Créer des répertoires SAP dans le volume Azure NetApp Files.  
   Montez temporairement le volume Azure NetApp Files sur l’une des machines virtuelles et créez les répertoires SAP (chemins d’accès de fichier).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS sudo chattr +i /usr/sap/trans sudo chattr +i /usr/sap/QAS/SYS sudo chattr +i /usr/sap/QAS/ASCS00 sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Plug-ins chargés : langpacks, product-id, search-disabled-repos
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Les données du référentiel datent de plus de 2 semaines. Installer yum-cron ? Ou exécuter : yum makecache fast
   # <a name="installed-packages"></a>Packages installés
   # <a name="name---------resource-agents-sap"></a>Nom        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Version     : 3.9.5
   # <a name="release------124el7"></a>Release     : 124.el7
   # <a name="size---------100-k"></a>Taille        : 100 k
   # <a name="repo---------installed"></a>Référentiel : installé
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Référentiel source : rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Résumé     : Agents de ressources de cluster SAP et script de connecteur
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL         : https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licence     : GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Description : Agents de ressources SAP et interface de script de connecteur avec
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Pacemaker pour permettre la gestion des instances SAP dans un cluster
   #          <a name="-environment"></a>: environnement.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Ajouter les lignes suivantes à fstab, enregistrer et quitter
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3  192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3  192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Définir la propriété ResourceDisk.EnableSwap sur y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Créer et utiliser le fichier d’échange sur le disque de ressources.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Définir la taille du fichier d’échange avec propriété ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>L’espace libre sur le disque de ressources varie selon la taille de la machine virtuelle. Veillez à ne pas définir une valeur trop élevée. Vous pouvez vérifier l’espace d’échange avec la commande swapon
   # <a name="size-of-the-swapfile"></a>Taille du fichier d’échange.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl2-standby"></a>Nœud anftstsapcl2 : en attente
   # <a name="online--anftstsapcl1-"></a>En ligne : [ anftstsapcl1 ]
   #
   # <a name="full-list-of-resources"></a>Liste complète des ressources :
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré
   #  <a name="resource-group-g-qasascs"></a>Groupe de ressources : g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Autoriser l'accès à SWPM. Cette règle n’est pas permanente. Si vous redémarrez l’ordinateur, vous devez réexécuter la commande.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo pcs node unstandby anftstsapcl2 sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl1-standby"></a>Nœud anftstsapcl1 : en attente
   # <a name="online--anftstsapcl2-"></a>En ligne : [ anftstsapcl2 ]
   #
   # <a name="full-list-of-resources"></a>Liste complète des ressources :
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl2 démarré
   #  <a name="resource-group-g-qasascs"></a>Groupe de ressources : g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré<
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré
   #  <a name="resource-group-g-qasaers"></a>Groupe de ressources : g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Autoriser l'accès à SWPM. Cette règle n’est pas permanente. Si vous redémarrez l’ordinateur, vous devez réexécuter la commande.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Remplacer la commande de redémarrage par une commande de démarrage
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>Ajouter le paramètre de connexion persistante
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Remplacer la commande de redémarrage par une commande de démarrage
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>supprimer le démarrage automatique du profil ERS
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Modifier la configuration du système Linux
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>Sur le nœud où vous avez installé l’instance ASCS, commentez la ligne suivante
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>Sur le nœud où vous avez installé l’instance ERS, commentez la ligne suivante
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000  sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1  sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000  sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pcs status
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>En ligne : [ anftstsapcl1 anftstsapcl2 ]
    #
    # <a name="full-list-of-resources"></a>Liste complète des ressources :
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl2 démarré
    #  <a name="resource-group-g-qasascs"></a>Groupe de ressources : g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
    #  <a name="resource-group-g-qasaers"></a>Groupe de ressources : g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Port de sonde d’ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent sudo firewall-cmd --zone=public --add-port=62000/tcp sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent sudo firewall-cmd --zone=public --add-port=3200/tcp sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent sudo firewall-cmd --zone=public --add-port=3600/tcp sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent sudo firewall-cmd --zone=public --add-port=3900/tcp sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent sudo firewall-cmd --zone=public --add-port=8100/tcp sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent sudo firewall-cmd --zone=public --add-port=50013/tcp sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent sudo firewall-cmd --zone=public --add-port=50014/tcp sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent sudo firewall-cmd --zone=public --add-port=50016/tcp
   # <a name="probe-port-of-ers"></a>Port de sonde d’ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent sudo firewall-cmd --zone=public --add-port=62101/tcp sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent sudo firewall-cmd --zone=public --add-port=3301/tcp sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent sudo firewall-cmd --zone=public --add-port=50113/tcp sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent sudo firewall-cmd --zone=public --add-port=50114/tcp sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>Adresse IP de la configuration de serveur frontal de l’équilibrage de charge pour l’instance  ASCS SAP NetWeaver
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>Adresse IP de la configuration de serveur frontal de l’équilibrage de charge pour l’instance  ASCS ERS SAP NetWeaver
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Ajouter les lignes suivantes à fstab, enregistrer et quitter
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Ajouter la ligne suivante à fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Monter
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Ajouter la ligne suivante à fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Monter
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Définir la propriété ResourceDisk.EnableSwap sur y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Créer et utiliser le fichier d’échange sur le disque de ressources.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Définir la taille du fichier d’échange avec propriété ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>L’espace libre sur le disque de ressources varie selon la taille de la machine virtuelle. Veillez à ne pas définir une valeur trop élevée. Vous pouvez vérifier l’espace d’échange avec la commande swapon
   # <a name="size-of-the-swapfile"></a>Taille du fichier d’échange.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   Liste hdbuserstore
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT   ENV : 192.168.14.4:30313   USER: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - qasadm hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Supprimer les actions ayant échoué pour l’instance ERS dans le cadre de la migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   En ligne : [ anftstsapcl1 anftstsapcl2 ]
   
   Liste complète des ressources :
   
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   
   Actions ayant échoué :
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm) :      anftstsapcl1 démarré  Groupe de ressources : g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem) :    anftstsapcl1 démarré      nc_QAS_ASCS        (ocf::heartbeat:azure-lb) :      anftstsapcl1 démarré      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2) :       anftstsapcl1 démarré      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance) :   anftstsapcl1 démarré  Groupe de ressources : g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem) :    anftstsapcl2 démarré      nc_QAS_AERS        (ocf::heartbeat:azure-lb) :      anftstsapcl2 démarré      vip_QAS_AERS       (ocf::heartbeat:IPaddr2) :       anftstsapcl2 démarré      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance) :   anftstsapcl2 démarré
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
