---
title: Montée en charge SAP HANA avec HSR et Pacemaker sur SLES | Microsoft Docs
description: Montée en charge SAP HANA avec HSR et Pacemaker sur SLES.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 49c4c579d75b964a4b4c37c8a44bddf1ad08c62b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142816"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Haute disponibilité pour système scale-out SAP HANA avec montée en puissance parallèle avec HSR sur SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Cet article explique comment déployer un système SAP HANA à haut niveau de disponibilité dans une configuration de montée en charge avec réplication de système HANA (HSR) et Pacemaker sur les machines virtuelles Azure SUSE Linux Enterprise Server. Les systèmes de fichiers partagés dans l’architecture présentée sont fournis par [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) et sont montés sur NFS.  

Dans les exemples de configuration, de commandes d’installation, etc., l’instance HANA est **03** et l’ID de système HANA est **HN1**. Les exemples sont basés sur HANA 2.0 SP4 et SUSE Linux Enterprise Server 12 SP5. 

Avant de commencer, reportez-vous aux notes et documents SAP suivants :

* [Documentation Azure NetApp Files][anf-azure-doc] 
* La note SAP [1928533] comprend :  
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure
* Note SAP [2015553] : répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure
* Note SAP [2205917] : contient les paramètres de système d’exploitation recommandés pour SUSE Linux Enterprise Server pour les applications SAP
* Note SAP [1944799] : contient des instructions SAP pour SUSE Linux Enterprise Server pour les applications SAP
* Note SAP [2178632] : contient des informations détaillées sur toutes les métriques de supervision rapportées pour SAP dans Azure
* Note SAP [2191498] : contient la version requise de l’agent hôte SAP pour Linux dans Azure
* Note SAP [2243692] : contient des informations sur les licences SAP sur Linux dans Azure
* Note SAP [1984787] : contient des informations générales sur SUSE Linux Enterprise Server 12
* Note SAP [1999351] : contient des informations supplémentaires relatives à la résolution des problèmes pour l’extension d’analyse Azure améliorée pour SAP
* Note SAP [1900823] : contient des informations sur les exigences de stockage SAP HANA
* [Wiki de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) : contient toutes les notes SAP requises pour Linux
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [Guides sur les meilleures pratiques de haute disponibilité SAP pour SUSE][suse-ha-guide] : contient toutes les informations nécessaires pour configurer la haute disponibilité NetWeaver et la réplication de système SAP HANA localement (à utiliser comme ligne de base générale ; elles fournissent des informations beaucoup plus détaillées)
* [Notes de publication de SUSE High Availability Extension 12 SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [La gestion du partage NFS échoue lors de la réplication d’un cluster haute disponibilité SUSE pour système HANA](https://www.suse.com/support/kb/doc/?id=000019904)
* [Applications SAP NetApp su Microsoft Azure avec Azure NetApp Files][anf-sap-applications-azure]
* [Volumes NFS v4.1 sur Azure NetApp Files pour SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Vue d’ensemble

Une méthode pour obtenir une haute disponibilité HANA pour les installations de montée en charge HANA consiste à configurer la réplication de système HANA et à protéger la solution avec le cluster Pacemaker afin d’autoriser le basculement automatique. En cas de défaillance d’un nœud actif, le cluster bascule les ressources HANA vers l’autre site.  
La configuration présentée montre trois nœuds HANA sur chaque site, ainsi qu’un nœud de créateur de majorité pour empêcher le scénario de split-brain. Les instructions peuvent être adaptées pour inclure plus de machines virtuelles en tant que nœuds de base de données HANA.  

Le système de fichiers partagé HANA `/hana/shared` dans l’architecture présentée est fourni par [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Il est monté via NFSv4.1 sur chaque nœud HANA du même site de réplication de système HANA. Les systèmes de fichiers `/hana/data` et `/hana/log` sont des systèmes de fichiers locaux et ne sont pas partagés entre les nœuds de base de données HANA. SAP HANA sera installé en mode non partagé. 

> [!TIP]
> Pour des configurations de stockage recommandées SAP HANA, consultez [Configurations de stockage SAP HANA des machines virtuelles Azure](./hana-vm-operations-storage.md).   

[![Scale-out SAP HANA avec HSR et cluster Pacemaker sur SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

Dans le diagramme ci-dessus, trois sous-réseaux sont représentés au sein d’un réseau virtuel Azure, qui suit les recommandations de réseau SAP HANA : 
* pour la communication client - `client` 10.23.0.0/24  
* pour la communication interne HANA entre les nœuds - `inter` 10.23.1.128/26  
* pour la réplication de système HANA - `hsr` 10.23.1.192/26  

Comme `/hana/data` et `/hana/log` sont déployés sur des disques locaux, il n’est pas nécessaire de déployer un sous-réseau distinct et des cartes réseau virtuelles distinctes pour la communication au stockage.  

Les volumes Azure NetApp sont déployés dans un sous-réseau distinct, [délégué à Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> La réplication système vers un 3e site n’est pas prise en charge. Pour plus d’informations, consultez la section « Conditions préalables importantes » dans [Scénario d’optimisation des performances scale-out de réplication des systèmes SLES-SAP HANA](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Configurer l’infrastructure

Les instructions suivantes supposent que vous avez déjà créé le groupe de ressources, le réseau virtuel Azure avec les trois sous-réseaux de réseau Azure : `client`, `inter` et `hsr`.

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Déployer des machines virtuelles Linux via le Portail Azure
1. Déployer les machines virtuelles Azure.  
Pour la configuration présentée dans ce document, déployez sept machines virtuelles : 
   - trois machines virtuelles à servir de nœuds de base de données HANA pour le site de réplication HANA 1 : **hana-s1-db1**, **hana-s1-db2** et **hana-s1-db3**  
   - trois machines virtuelles à servir de nœuds de base de données HANA pour le site de réplication HANA 2 : **hana-s2-db1**, **hana-s2-db2** et **hana-s2-db3**  
   - une petite machine virtuelle à servir de *créateur de majorité* : **hana-s-mm**

   Les machines virtuelles, déployées en tant que nœuds HANA de base de données SAP doivent être certifiées par SAP pour HANA, telles qu’elles sont publiées dans le [Répertoire matériel SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Lors du déploiement des nœuds de base de données HANA, assurez-vous que [Accelerated Network](../../../virtual-network/create-vm-accelerated-networking-cli.md) (réseau accélérée) est sélectionné.  
  
   Pour le nœud de créateur de majorité, vous pouvez déployer une petite machine virtuelle, car cette machine virtuelle n’exécute aucune des ressources SAP HANA. La machine virtuelle de créateur de majorité est utilisée dans la configuration du cluster pour obtenir un nombre impair de nœuds de cluster dans un scénario de split-brain. La machine virtuelle de créateur de majorité n’a besoin que d’une seule interface réseau virtuelle dans le sous-réseau `client` dans cet exemple.        

   Déployer des disques managés locaux pour `/hana/data` et `/hana/log`. La configuration de stockage minimale recommandée pour `/hana/data` et `/hana/log` est décrite dans [Configurations de stockage SAP HANA des machines virtuelles Azure](./hana-vm-operations-storage.md).

   Déployez l’interface réseau principale pour chaque machine virtuelle dans le sous-réseau du réseau virtuel `client`.  
   Lorsque la machine virtuelle est déployée via le Portail Azure, le nom de l’interface réseau est généré automatiquement. Dans ces instructions, par soucis de simplicité, nous allons faire référence aux interfaces réseau principales générées automatiquement, qui sont attachées au sous-réseau `client` du réseau virtuel Azure, en tant que **hana-s1-db1-client**, **hana-s1-db2-client**, **hana-s1-db3-client**, etc.  


   > [!IMPORTANT]
   > Vérifiez que le système d’exploitation que vous sélectionnez est certifié SAP pour SAP HANA sur les types de machine virtuelle spécifiques que vous utilisez. Pour obtenir la liste des types de machines virtuelles certifiés SAP HANA et des versions de système d’exploitation correspondants, accédez au site [SAP HANA Certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Cliquez sur les détails du type de machine virtuelle répertorié pour obtenir la liste complète des versions de système d’exploitation prises en charge par SAP HANA pour ce type.  
  

2. Créez six interfaces réseau, une pour chaque machine virtuelle de base de données HANA, dans le sous-réseau `inter` du réseau virtuel (dans cet exemple, **hana-s1-db1-inter**, **hana-s1-db2-inter**, **hana-s1-db3-inter**, **hana-s2-db1-inter**, **hana-s2-db2-inter**, et **hana-s2-db3-inter**).  

3. Créez six interfaces réseau, une pour chaque machine virtuelle de base de données HANA, dans le sous-réseau `hsr` du réseau virtuel (dans cet exemple, **hana-s1-db1-hsr**, **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr**, et **hana-s2-db3-hsr**).  

4. Attachez les interfaces réseau virtuelles nouvellement créées aux machines virtuelles correspondantes :  

    a. Accédez à la machine virtuelle sur le [Portail Azure](https://portal.azure.com/#home).  

    b. Dans le volet gauche, sélectionnez **Machines virtuelles**. Filtrez sur le nom de la machine virtuelle (par exemple, **hana-s1-db1**), puis sélectionnez la machine virtuelle.  

    c. Dans le volet **Vue d’ensemble**, sélectionnez **Arrêter** pour libérer la machine virtuelle.  

    d. Sélectionnez **Mise en réseau**, puis attachez l’interface réseau. Dans la liste déroulante sous **Attacher l’interface réseau**, sélectionnez les interfaces réseau déjà créées pour les sous-réseaux `inter` et `hsr`.  
    
    e. Sélectionnez **Enregistrer**. 
 
    f. Répétez les étapes b à e pour les machines virtuelles restantes (dans notre exemple,  **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2** et **hana-s2-db3**).
 
    g. Laissez les machines virtuelles dans l’état arrêté pour l’instant. Ensuite, nous allons activer [Mise en réseau accélérée](../../../virtual-network/create-vm-accelerated-networking-cli.md) pour toutes les interfaces réseau qui viennent d’être attachées.  

5. Activez la mise en réseau accélérée pour les interfaces réseau supplémentaires pour les sous-réseaux `inter` et `hsr` en procédant comme suit :  

    a. Ouvrez [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) dans le [Portail Azure](https://portal.azure.com/#home).  

    b. Exécutez les commandes suivantes pour activer la mise en réseau accélérée pour les interfaces réseau supplémentaires, qui sont attachées aux sous-réseaux `inter` et `hsr`.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Démarrer les machines virtuelles de base de données HANA

### <a name="deploy-azure-load-balancer"></a>Déployer Azure Load Balancer

1. Nous vous recommandons d’utiliser l’équilibreur de charge standard. Suivez ces étapes de configuration pour déployer l’équilibreur de charge standard :
   1. Commencez par créer un pool d’adresses IP frontales :

      1. Ouvrez l’équilibrage de charge, sélectionnez le **Pool d’adresses IP frontales**, puis cliquez sur **Ajouter**.
      1. Entrez le nom du nouveau pool d’adresses IP frontales (par exemple **hana-frontend**).
      1. Définissez l’**Affectation** sur **Statique** et entrez l’adresse IP (par exemple, **10.23.0.27**).
      1. Sélectionnez **OK**.
      1. Une fois le pool d’adresses IP frontal créé, notez son adresse IP.

   1. Ensuite, créez un pool de back-ends et ajoutez toutes les machines virtuelles du cluster au pool de back-ends :

      1. Ouvrez l’équilibrage de charge, sélectionnez le **Pool d’adresses IP frontales**, puis cliquez sur **Ajouter**.
      1. Entrer le nom du nouveau pool principal (par exemple **hana-backend**).
      1. Cliquez sur **Ajouter une machine virtuelle**.
      1. Sélectionnez **Machine virtuelle**.
      1. Sélectionnez les machines virtuelles du cluster SAP HANA et leurs adresses IP pour le sous-réseau `client`.
      1. Sélectionnez **Ajouter**.

   1. Créez ensuite une sonde d’intégrité :

      1. Ouvrez l’équilibrage de charge, sélectionnez les **sondes d’intégrité**, puis cliquez sur **Ajouter**.
      1. Entrez le nom de la nouvelle sonde d’intégrité (par exemple **hana-hp**).
      1. Sélectionnez **TCP** pour le protocole et le port 625 **03**. Consersez la valeur **Intervalle** à 5, et la valeur **Seuil de défaillance** à 2.
      1. Sélectionnez **OK**.

   1. Ensuite, créez les règles d’équilibrage de charge :
   
      1. Ouvrez l’équilibrage de charge, sélectionnez **Règles d’équilibrage de charge**, puis cliquez sur **Ajouter**.
      1. Entrez le nom de la nouvelle règle d’équilibrage de charge (par exemple, **hana-lb**).
      1. Sélectionnez l’adresse IP frontale, le pool principal et la sonde d’intégrité que vous avez créés (par exemple,**hana-frontend**, **hana-backend** et **hana-hp**).
      1. Sélectionnez **Ports HA**.
      1. Veillez à **activer l’IP flottante** .
      1. Sélectionnez **OK**.

   > [!IMPORTANT]
   > Une adresse IP flottante n’est pas prise en charge sur une configuration IP secondaire de carte réseau pour des scénarios d’équilibrage de charge. Pour plus d’informations, consultez [Limitations d’équilibreur de charge Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Si vous avez besoin d’une adresse IP supplémentaire pour la machine virtuelle, déployez une deuxième carte réseau.    
   
   > [!Note]
   > Lorsque des machines virtuelles sans adresse IP publique sont placées dans le pool principal d’Azure Standard Load Balancer interne (aucune adresse IP publique), il n’y a pas de connectivité Internet sortante, sauf si une configuration supplémentaire est effectuée pour autoriser le routage vers des points de terminaison publics. Pour savoir plus en détails comment bénéficier d’une connectivité sortante, voir [Connectivité des points de terminaison publics pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > N’activez pas les timestamps TCP sur des machines virtuelles Azure placées derrière Azure Load Balancer. L’activation des timestamps TCP entraîne l’échec des sondes d’intégrité. Définissez le paramètre **net.ipv4.tcp_timestamps** sur **0**. Pour plus d’informations, consultez [Load Balancer health probes](../../../load-balancer/load-balancer-custom-probe-overview.md) (Sondes d’intégrité Load Balancer).
   > Voir aussi la note SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Déployer l’infrastructure Azure NetApp Files 

Déployez les volumes ANF pour le système de fichiers `/hana/shared`. Vous aurez besoin d’un volume `/hana/shared` distinct pour chaque site de réplication de système HANA. Pour plus d’informations, consultez [Configurer l’infrastructure Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

Dans cet exemple, les volumes Azure NetApp Files suivants ont été utilisés : 

* volume **HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)
* volume **HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)


## <a name="operating-system-configuration-and-preparation"></a>Configuration et préparation du système d’exploitation

Les instructions des sections suivantes sont précédées de l’une des abréviations suivantes :
* **[A]**  :      applicable à tous les nœuds
* **[AH]**  :     applicable à tous les nœuds de base de données HANA
* **[M]**  :      applicable au nœud de créateur de majorité
* **[AH1]**  :    applicable à tous les nœuds de base de données HANA sur le SITE 1
* **[AH2]**  :    applicable à tous les nœuds de base de données HANA sur le SITE 2
* **[1]**  :      applicable uniquement au nœud de base de données HANA 1, le SITE 1
* **[2]**  :      applicable uniquement au nœud de base de données HANA 1, le SITE 2

Configurez et préparez votre système d’exploitation en procédant comme suit :

1. **[A]** Conservez les fichiers hôtes sur les machines virtuelles. Incluez des entrées pour tous les sous-réseaux. Les entrées suivantes ont été ajoutées à `/etc/hosts` pour cet exemple.  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE offre des agents de ressource spéciaux pour SAP HANA et par les agents par défaut pour SAP HANA ScaleUp installés. Désinstallez les packages pour ScaleUp, s’ils sont installés, puis installez les packages pour le scénario SAP HANAScaleOut. L’étape doit être effectuée sur toutes les machines virtuelles du cluster, y compris le créateur de majorité.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[AH]** Préparez les machines virtuelles : appliquez les paramètres recommandés par la note SAP [2205917] pour SUSE Linux Enterprise Server pour les applications SAP.  

## <a name="prepare-the-file-systems"></a>Préparer les systèmes de fichiers
### <a name="mount-the-shared-file-systems"></a>Monter les systèmes de fichiers partagés

Dans cet exemple, les systèmes de fichiers HANA partagés sont déployés sur Azure NetApp Files et montés sur NFSv4.  

1. **[AH]** Créez des points de montage pour les volumes de base de données HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** Vérifiez le paramètre de domaine NFS. Assurez-vous que le domaine est configuré en tant que domaine par défaut Azure NetApp Files, autrement dit, **`defaultv4iddomain.com`** et que le mappage est défini sur **nobody**.  
   Cette étape n’est nécessaire que si vous utilisez Azure NetAppFiles NFSv4.1.  

    > [!IMPORTANT]
    > Veillez à définir le domaine NFS dans `/etc/idmapd.conf` sur la machine virtuelle pour qu’elle corresponde à la configuration de domaine par défaut sur Azure NetApp Files : **`defaultv4iddomain.com`** . En cas d’incompatibilité entre la configuration de domaine sur le client NFS (c’est-à-dire la machine virtuelle) et le serveur NFS, par exemple la configuration Azure NetApp, les autorisations pour les fichiers sur les volumes Azure NetApp montés sur les machines virtuelles s’affichent en tant que `nobody`.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** Vérifiez `nfs4_disable_idmapping`. Cette option doit avoir la valeur **Y**. Pour créer la structure de répertoire où se trouve `nfs4_disable_idmapping`, exécutez la commande de montage. Vous ne serez pas en mesure de créer manuellement le répertoire sous /sys/modules, car l’accès est réservé pour le noyau/les pilotes.  
   Cette étape n’est nécessaire que si vous utilisez Azure NetAppFiles NFSv4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Montez les volumes Azure NetApp Files partagés sur les machines virtuelles du SITE 1 de base de données HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Montez les volumes Azure NetApp Files partagés sur les machines virtuelles du SITE 2 de base de données HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[AH]** Vérifiez que les systèmes de fichiers `/hana/shared/` correspondants sont montés sur toutes les machines virtuelles de base de données HANA avec la version de protocole NFS **NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Préparer les données et enregistrer les systèmes de fichiers locaux
Dans la configuration présentée, les systèmes de fichiers `/hana/data` et `/hana/log` sont déployés sur un disque managé et sont attachés localement à chaque machine virtuelle de base de données HANA. Vous devez exécuter les étapes pour créer les volumes de données et de fichier journal locaux sur chaque machine virtuelle de base de données HANA. 

Configurez la disposition du disque avec **Logical Volume Manager (LVM)** . L’exemple suivant part du principe que chaque machine virtuelle HANA dispose de trois disques de données joints qui sont utilisés pour utilisés deux volumes.

1. **[AH]** Répertoriez tous les disques disponibles :
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Exemple de sortie :

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** Créez des volumes physiques pour tous les disques que vous souhaitez utiliser :
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** Créez un groupe de volume pour les fichiers de données. Utilisez un groupe de volume pour les fichiers journaux et un autre pour le répertoire partagé de SAP HANA :
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** Créez les volumes logiques. 
   Un volume linéaire est créé lorsque vous utilisez `lvcreate` sans le commutateur `-i`. Nous vous suggérons de créer un volume agrégé par bandes pour obtenir de meilleures performances d’E/S, et d’aligner les tailles des bandes sur les valeurs décrites dans [Configurations de stockage de machines virtuelles SAP HANA](./hana-vm-operations-storage.md). L’argument `-i` doit indiquer le nombre de volumes physiques sous-jacents et l’argument `-I` la taille de bande. Dans ce document, deux volumes physiques sont utilisés pour le volume de données. Par conséquent, l’argument de commutateur `-i` est défini sur **2**. La taille de bande pour le volume de données est de **256 Kio**. Un volume physique étant utilisé pour le volume du fichier journal, aucun commutateur `-i` ou `-I` n’est utilisé explicitement pour les commandes de volume du fichier journal.  

   > [!IMPORTANT]
   > Utilisez le commutateur `-i` et définissez sa valeur sur le nombre de volumes physiques sous-jacents lorsque vous utilisez plusieurs volumes physiques pour chaque volume de données ou de journal. Utilisez le commutateur `-I` pour spécifier la taille de bande lors de la création d’un volume agrégé par bandes.  
   > Pour connaître les configurations de stockage recommandées, notamment les tailles de bande et le nombre de disques, consultez [Configurations de stockage de machines virtuelles SAP HANA](./hana-vm-operations-storage.md).  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** Créez les répertoires de montage et copiez l’UUID de tous les volumes logiques :
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** Créez des entrées `fstab` pour les volumes logiques et le montage :
    ```bash
    sudo vi /etc/fstab
    ```

   Insérez la ligne suivante dans le fichier `/etc/fstab` :

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Montez les nouveaux volumes :

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Créez un cluster Pacemaker

Suivez les étapes décrites à la page [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](high-availability-guide-suse-pacemaker.md) pour créer un cluster Pacemaker de base pour ce serveur HANA.
Incluez toutes les machines virtuelles, y compris le créateur de majorité dans le cluster.  

> [!IMPORTANT]
> Ne définissez pas `quorum expected-votes` à 2, car il ne s’agit pas d’un cluster à deux nœuds.  
> Assurez-vous que la propriété de cluster `concurrent-fencing` est activée, afin que le délimiteur de nœud soit désérialisé.   

## <a name="installation"></a>Installation  

Dans cet exemple, pour le déploiement de SAP HANA dans une configuration de montée en charge avec HSR sur des machines virtuelles Azure, nous avons utilisé HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Préparer l’installation de HANA

1. **[AH]** Avant l’installation de HANA, définissez le mot de passe racine. Vous pouvez désactiver le mot de passe racine une fois l’installation terminée. Exécutez en tant que commande `root``passwd`.  

2. **[1,2]** Changez les autorisations sur `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Vérifiez que vous pouvez vous connecter via SSH sur les machines virtuelles de base de données HANA dans ce site **hana-s1-db2** et **hana-s1-db3** sans être invité à entrer un mot de passe. Si ce n’est pas le cas, échangez les clés SSH comme décrit dans [Activer l’accès SSH via la clé publique](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Vérifiez que vous pouvez vous connecter via SSH sur les machines virtuelles de base de données HANA dans ce site **hana-s2-db2** et **hana-s2-db3** sans être invité à entrer un mot de passe.  
   Si ce n’est pas le cas, échangez les clés SSH.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** Installez des packages supplémentaires, qui sont nécessaires pour HANA 2.0 SP4. Pour plus d’informations, consultez la note SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) pour votre version de SLES. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>Installation de HANA sur le premier nœud de chaque site

1. **[1]** Installez SAP HANA en suivant les instructions fournies dans le [Guide d’installation et de mise à jour de SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Dans les instructions qui suivent, nous présentons l’installation de SAP HANA sur le premier nœud sur le SITE 1.   

   a. Démarrez le programme **hdblcm** en tant que `root` à partir du répertoire du logiciel d’installation HANA. Utilisez le paramètre `internal_network` et transmettez l’espace d’adressage pour le sous-réseau, qui est utilisé pour la communication interne HANA entre les nœuds.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. À l’invite, entrez les valeurs suivantes :

     * Pour **Choisir une action** : entrer **1** (pour l’installation)
     * Pour **Composants supplémentaires pour l’installation** : entrer **2, 3**
     * Pour le chemin d’accès de l’installation : appuyer sur Entrée (utilise par défaut /hana/shared)
     * Pour **Nom d’hôte local** : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Voulez-vous ajouter des hôtes au système ?**  : entrer **n**
     * Pour **ID système SAP HANA** : entrer **HN1**
     * Pour **Numéro d’instance** [00] : entrer **03**
     * Pour **Groupe Worker hôte local** [par défaut] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Sélectionner Utilisation du système/Entrer l’index [4]**  : entrer **4** (pour personnalisé)
     * Pour **Emplacement des volumes de données** [/hana/data/HN1] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Emplacement des volumes de journaux** [/hana/log/HN1] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Restreindre l’allocation de mémoire maximale ?** [n] : entrer **n**
     * Pour **Nom d’hôte du certificat pour l’hôte hana-s1-db1** [hana-s1-db1] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Mot de passe (sapadm) utilisateur de l’agent hôte SAP** : entrer le mot de passe
     * Pour **Confirmer le mot de passe (sapadm) utilisateur de l’agent hôte SAP** : entrer le mot de passe
     * Pour **Mot de passe de l’administrateur système (hn1adm)**  : entrer le mot de passe
     * Pour **Répertoire de base de l’administrateur système**[/usr/sap/HN1/home] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Environnement de connexion de l’administrateur système** [/bin/sh] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **ID utilisateur de l’administrateur système** [1001] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Entrer l’ID du groupe d’utilisateurs (sapsys)** [79] : appuyrz sur Entrée pour accepter la valeur par défaut
     * Pour **Mot de passe utilisateur de la base de données système (système)**  : entrer le mot de passe du système
     * Pour **Confirmer le mot de passe utilisateur de la base de données système (système)**  : entrer le mot de passe du système
     * Pour **Redémarrer le système après le redémarrage de la machine ?** [n] : entrer **n** 
     * Pour **Voulez-vous continuer (y/n)**  : valider le résumé et si tout semble correct, entrer **y**

2. **[2]** Répétez l’étape précédente pour installer SAP HANA sur le premier nœud sur le SITE 2.   

3. **[1,2]** Vérifiez global. ini  

   Affichez global.ini et assurez-vous que la configuration de la communication interne SAP HANA entre les nœuds est en place. Vérifiez la section **communication**. Elle doit comporter un espace d’adressage pour le sous-réseau `inter` et `listeninterface` doit être défini sur `.internal`. Vérifiez la section **internal_hostname_resolution**. Elle doit comporter les adresses IP des machines virtuelles HANA qui appartiennent au sous-réseau `inter`.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1, 2]** Préparez `global.ini` pour l’installation dans un environnement non partagé, comme décrit dans la note SAP [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** Redémarrez SAP HANA pour activer les modifications.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** Vérifiez que l’interface client utilise les adresses IP du sous-réseau `client` pour les communications.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Pour plus d’informations sur la vérification de la configuration, consultez la note SAP [2183363 – Configuration du réseau interne SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[AH]** Modifiez les autorisations sur les répertoires de données et de journaux pour éviter une erreur d’installation de HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** Installez les nœuds HANA secondaires. Les exemples d’instructions de cette étape sont pour le SITE 1.  
   a. Démarrez le programme **hdblcm** résident comme `root`.    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. À l’invite, entrez les valeurs suivantes :

     * Pour **Choisir une action** : entrer **2** (pour ajouter des hôtes)
     * Pour **Entrer les noms d’hôte séparés par une virgule à ajouter** : hana-s1-db2, hana-s1-db3
     * Pour **Composants supplémentaires pour l’installation** : entrer **2, 3**
     * Pour **Entrer le nom d’utilisateur racine [root]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Sélectionner des rôles pour l’hôte 'hana-s1-db2' [1]**  : 1 (pour le Worker)
     * Pour **Entrer le groupe de basculement hôte pour l’hôte 'hana-s1-db2' [par défaut]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Entrer le numéro de partition de stockage pour l’hôte 'hana-s1-db2' [<<assign automatically>>]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Entrer le groupe de basculement hôte pour l’hôte 'hana-s1-db2' [par défaut]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Sélectionner des rôles pour l’hôte 'hana-s1-db3' [1]**  : 1 (pour le Worker)
     * Pour **Entrer le groupe de basculement hôte pour l’hôte 'hana-s1-db3' [par défaut]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Entrer le numéro de partition de stockage pour l’hôte 'hana-s1-db3' [<<assign automatically>>]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Entrer le groupe de Worker hôte pour l’hôte 'hana-s1-db3' [par défaut]**  : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Mot de passe de l’administrateur système (hn1adm)**  : entrer le mot de passe
     * Pour **Entrer le mot de passe (sapadm) utilisateur de l’agent hôte SAP** : entrer le mot de passe
     * Pour **Confirmer le mot de passe (sapadm) utilisateur de l’agent hôte SAP** : entrer le mot de passe
     * Pour **Nom d’hôte du certificat pour l’hôte hana-s1-db2** [hana-s1-db2] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Nom d’hôte du certificat pour l’hôte hana-s1-db3** [hana-s1-db3] : appuyer sur Entrée pour accepter la valeur par défaut
     * Pour **Voulez-vous continuer (y/n)**  : valider le résumé et si tout semble correct, entrer **y**

9. **[2]** Répétez l’étape précédente pour installer les nœuds SAP HANA secondaires sur le SITE 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Configurer la réplication de système SAP HANA 2.0

1. **[1]** Configurez la réplication de système sur le SITE 1 :

   Sauvegardez les bases de données en tant que **hn1** adm :

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Copiez les fichiers d’infrastructure à clé publique du système sur le site secondaire :

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Créez le site principal :

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Configurez la réplication de système sur le SITE 2 :
    
   Inscrivez le second site pour démarrer la réplication de système. Exécutez la commande suivante en tant que <hanasi\>adm :

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Vérifier l’état de la réplication

   Vérifiez l’état de la réplication et attendez que toutes les bases de données soient synchronisées.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** Modifiez la configuration HANA afin que la communication pour la réplication de système HANA si elle est dirigée par le biais des interface réseau virtuelles de réplication de système HANA.   
   - Arrêter HANA sur les deux sites
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Modifiez global. ini pour ajouter le mappage d’hôte pour la réplication de système HANA : utilisez les adresses IP du sous-réseau `hsr`.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Démarrer HANA sur les deux sites
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Pour plus d’informations, consultez [Résolution de noms d’hôtes pour la réplication de système](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Créer des ressources de système de fichiers

Créez une ressource de cluster de système de fichiers factice, qui analyse et signale les échecs, en cas de problème d’accès au système de fichiers monté sur NFS `/hana/shared`. Cela permet au cluster de déclencher le basculement en cas de problème d’accès à `/hana/shared`. Pour plus d’informations, consultez [Gestion du partage NFS échoue lors de la réplication d’un cluster haute disponibilité SUSE pour système HANA](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** Placez Pacemaker en mode maintenance, en vue de la création des ressources de cluster HANA.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1,2]** Créez le répertoire sur le volume ANF /hana/shared, qui sera utilisé dans la ressource de surveillance du système de fichiers spécial. Les répertoires doivent être créés sur les deux sites.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[AH]** Créez le répertoire qui sera utilisé pour monter la ressource de surveillance du système de fichiers spécial. Le répertoire doit être créé sur tous les nœuds de cluster HANA.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** Créez les ressources de cluster du système de fichiers.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   L’attribut `OCF_CHECK_LEVEL=20` est ajouté à l’opération d’analyse afin que les opérations d’analyse effectuent un test en lecture/écriture sur le système de fichiers. Sans cet attribut, l’opération d’analyse vérifie uniquement que le système de fichiers est monté. Cela peut être un problème, car, en cas de perte de connectivité, le système de fichiers peut rester monté bien qu’il soit inaccessible.  

   L’attribut `on-fail=fence` est également ajouté à l’opération d’analyse. Avec cette option, si l’opération d’analyse échoue sur un nœud, ce dernier est immédiatement isolé.   

## <a name="create-sap-hana-cluster-resources"></a>Créer les ressources de cluster SAP HANA

1. **[1,2]** Installez le « hook de réplication de système » HANA. Le hook doit être installé sur un nœud de base de données HANA sur chaque site de réplication de système.         

   1. Préparer le hook en tant que `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Arrêtez HANA sur les deux sites de réplication de système. Exécutez en tant que <sid\>adm :
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Ajuster `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[AH]** Le cluster nécessite une configuration de sudoers sur le nœud de cluster pour <sid\>adm. Dans cet exemple, il est possible de créer un nouveau fichier. Exécutez les commandes en tant que `root`.    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1,2]** Démarrez SAP HANA sur les deux sites de réplication. Exécutez en tant que <sid\>adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Vérifiez l’installation de hook. Exécutez en tant que <sid\>adm sur le site de réplication de système HANA actif.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** Créez les ressources de cluster HANA. Exécutez les commandes suivantes en tant que `root`.    
   1. Assurez-vous que le cluster est déjà en mode maintenance.  
    
   2. Ensuite, créez la ressource de topologie HANA.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Ensuite, créez la ressource d’instance HANA.  
      > [!NOTE] 
      > Cet article contient des références aux termes *maître* et *esclave*, termes que Microsoft n’utilise plus. Lorsque ces termes seront supprimés du logiciel, nous les supprimerons de cet article.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Nous vous recommandons, comme meilleure pratique, de définir AUTOMATED_REGISTER uniquement sur **non**, tout en effectuant des tests de basculement détaillés, afin d’éviter l’échec de l’inscription automatique de l’instance principale comme secondaire. Une fois les tests de basculement terminés, définissez AUTOMATED_REGISTER sur **oui**, afin qu’après la réplication du système de prise en charge, la réplication puisse reprendre automatiquement. 

   4. Créez une adresse IP virtuelle et les ressources associées.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Créer les contraintes de cluster  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** Configurer des propriétés de cluster supplémentaires   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** Vérifier la communication entre le hook et le cluster
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** Placez le cluster en mode maintenance. Vérifiez que l’état du cluster est OK et que toutes les ressources sont démarrées.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Les délais d’expiration de la configuration ci-dessus sont simplement des exemples et doivent être adaptés à la configuration HANA spécifique. Par exemple, vous devrez peut-être augmenter le délai de démarrage, si le démarrage de la base de données SAP HANA prend plus de temps.
  

## <a name="test-sap-hana-failover"></a>Test de basculement SAP HANA 

> [!NOTE]
> Cet article contient des références aux termes *maître* et *esclave*, termes que Microsoft n’utilise plus. Lorsque ces termes seront supprimés du logiciel, nous les supprimerons de cet article.

1. Avant de commencer un test, vérifiez l’état du cluster et de la réplication de système SAP HANA.    

   a. Vérifier qu’il n’y a pas d’actions de cluster ayant échoué  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Vérifier que la réplication de système SAP HANA est synchronisée

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Nous vous recommandons de valider minutieusement la configuration du cluster SAP HANA, en effectuant les tests documentés dans [Haute disponibilité pour SAP HANA sur les machines virtuelles Azure sur SLES](./sap-hana-high-availability.md#test-the-cluster-setup) et dans le [Scénario d’optimisation des performances de réplication SLES](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster).

3. Vérifiez la configuration du cluster pour un scénario d’échec lorsqu’un nœud perd l’accès au partage NFS (`/hana/shared`).  

   Les agents de ressource SAP HANA dépendent de binaires, qui sont stockés sur `/hana/shared` pour effectuer des opérations pendant le basculement. Le système de fichiers `/hana/shared` est monté sur NFS dans la configuration présentée. Un test qui peut être effectué consiste à créer une règle de pare-feu temporaire pour bloquer l’accès au volume ANF `/hana/shared` et sur une des machines virtuelles du site principal. Cette approche valide le basculement du cluster en cas de perte de l’accès à `/hana/shared` sur le site de réplication de système actif.  

   **Résultat attendu** : Quand vous bloquez l’accès au volume ANF `/hana/shared` sur l’une des machines virtuelles du site principal, l’opération d’analyse qui effectue une opération de lecture/écriture sur le système de fichiers échoue, car elle n’est pas en mesure d’accéder au système de fichiers et déclenche le basculement de ressource HANA. Le même résultat est attendu lorsque votre nœud HANA perd l’accès au partage NFS.  
     
   Vous pouvez vérifier l’état des ressources de cluster en exécutant `crm_mon` ou `crm status`. État des ressources avant le début du test :
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Pour simuler l’échec de `/hana/shared`, commencez par confirmer l’adresse IP du volume ANF `/hana/shared` sur le site principal. Pour ce faire, exécutez `df -kh|grep /hana/shared`. 
   Ensuite, configurez une règle de pare-feu temporaire pour bloquer l’accès à l’adresse IP du volume ANF `/hana/shared` en exécutant la commande suivante sur l’une des machines virtuelles du site de réplication de système HANA principal.
   Dans cet exemple, la commande a été exécutée sur hana-s1-db1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Les ressources de cluster seront migrées vers l’autre site de réplication de système HANA.    
              
   Si vous définissez AUTOMATED_REGISTER="false", vous devrez configurer la réplication de système SAP HANA sur le site secondaire. Dans ce cas, vous pouvez exécuter ces commandes pour reconfigurer SAP HANA comme secondaire.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   État des ressources, après le test : 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Étapes suivantes

* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* [Volumes NFS v4.1 sur Azure NetApp Files pour SAP HANA](./hana-vm-operations-netapp.md)
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha].