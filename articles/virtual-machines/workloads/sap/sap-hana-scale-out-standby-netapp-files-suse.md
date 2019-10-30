---
title: Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server | Microsoft Docs
description: Guide de haute disponibilité pour SAP NetWeaver sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 11b0746c3e9e137775b2466af776b4cd9ba1e5df
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791669"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Cet article explique comment déployer un système HANA à haut niveau de disponibilité dans une configuration Scale-out avec nœud de secours des machines virtuelles Azure avec [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) pour les volumes de stockage partagés.  
Dans les exemples de configuration, les commandes d’installation, etc., l’instance HANA est **03** et l’ID de système HANA est **HN1**. Les exemples sont basés sur HANA 2.0 SP4 et SUSE Linux Enterprise Server pour SAP 12 SP4. 

Commencez par lire les notes et publications SAP suivantes :

* [Documentation Azure NetApp Files][anf-azure-doc] 
* Note SAP [1928533], qui contient :  
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure
* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2205917] contient des paramètres de système d’exploitation recommandés pour SUSE Linux Enterprise Server pour les applications SAP
* La note SAP [1944799] contient des instructions SAP pour SUSE Linux Enterprise Server pour les applications SAP
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1984787] contient des informations sur SUSE Linux Enterprise Server 12.
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* La note SAP [1900823] contient des informations sur les exigences de stockage SAP HANA
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [Guides sur les meilleures pratiques pour SUSE SAP HA][suse-ha-guide] Les guides contiennent toutes les informations nécessaires pour configurer la réplication locale des systèmes Netweaver HP et SAP HANA. Utilisez ces guides comme planning de référence. Ils fournissent des informations beaucoup plus détaillées.
* [Notes de publication de SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Applications SAP NetApp su Microsoft Azure avec Azure NetApp Files][anf-sap-applications-azure]
* [SAP HANA sur systèmes NetApp avec NFS](https://www.netapp.com/us/media/tr-4435.pdf). Le Guide de configuration de contient des informations sur la configuration de SAP HANA à l’aide de NFS fourni par Azure NetApp Files.


## <a name="overview"></a>Vue d'ensemble

L’une des méthodes permettant d’obtenir une haute disponibilité HANA est le basculement automatique de l’hôte. Pour configurer le basculement automatique de l’hôte, une ou plusieurs machines virtuelles sont ajoutées au système HANA et configurées pour être des nœuds de secours. En cas de défaillance d’un nœud actif, un nœud de secours prend automatiquement le relais. Dans la configuration présentée avec les machines virtuelles Azure, cela est effectué par [NFS sur Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Le nœud de secours doit avoir accès à tous les volumes de base de données. Les volumes HANA doivent être montés en tant que volumes NFSv4. Le mécanisme amélioré de verrouillage basé sur les baux de fichiers dans le protocole NFSv4 est utilisé pour la délimitation `I/O`. 

> [!IMPORTANT]
> Il est obligatoire de déployer les volumes de données et de journaux HANA en tant que volumes NFSv 4.1 et de les monter à l’aide du protocole NFSv 4.1, afin d’obtenir une configuration prise en charge. La configuration du basculement automatique de l’hôte HANA avec le nœud de secours n’est pas prise en charge avec NFSv3.

![Vue d’ensemble de la haute disponibilité SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Conformément aux recommandations en matière de réseau SAP HANA, trois sous-réseaux ont été créés au sein d’un réseau virtuel Azure : pour la communication avec le système de stockage, pour la communication entre nœuds HANA interne et pour la communication client. Les volumes Azure NetApp se trouvent dans un sous-réseau distinct, [délégué à Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Pour cet exemple de configuration, les sous-réseaux sont les suivants :  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configuration de l’infrastructure Azure NetApp Files 

Avant de poursuivre la configuration de l’infrastructure Azure NetApp Files, familiarisez-vous avec la [documentation Azure NetApp Files][anf-azure-doc]. Azure NetApp Files est disponible dans plusieurs [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Vérifiez si la région Azure sélectionnée est compatible avec Azure NetApp Files.  

Le lien suivant indique la disponibilité d’Azure NetApp Files pour chaque région Azure : [Disponibilité d’Azure NetApp Files pour chaque région Azure][anf-avail-matrix].  
Avant de déployer Azure NetApp Files, demandez l’intégration à Azure NetApp Files, en suivant les [instructions d’inscription à Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Déployer des ressources Azure NetApp Files  

Les étapes supposent que vous avez déjà déployé un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Les ressources Azure NetApp Files et les machines virtuelles, où les ressources Azure NetApp Files seront montées, doivent être déployées dans le même réseau virtuel Azure ou dans des réseaux virtuels homologués Azure.  

1. Si ce n’est pas encore fait, demandez l’[intégration à Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Créez le compte de NetApp dans la région Azure sélectionnée en suivant les [instructions de création de compte NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Configurez le pool de capacité Azure NetApp Files en suivant les [instructions de configuration du pool de capacité Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
L’architecture HANA présentée dans cet article utilise un seul pool de capacité Azure NetApp Files, le niveau de service Ultra. Nous vous recommandons d’utiliser le [niveau de service](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Ultra ou Premium d’Azure NetApp Files pour les charges de travail HANA sur Azure.  

4. Déléguez un sous-réseau pour Azure NetApp Files, comme décrit dans les [instructions de délégation d’un sous-réseau pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Déployez des volumes Azure NetApp Files en suivant les [instructions de création de volume pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Veillez à sélectionner la version **NFSv 4.1**, lors du déploiement des volumes. Actuellement, l’accès à NFSv 4.1 nécessite une mise en liste verte supplémentaire. Déployez les volumes dans le [sous-réseau](https://docs.microsoft.com/rest/api/virtualnetwork/subnets) Azure NetApp Files désigné. Gardez à l’esprit que les ressources Azure NetApp Files et les machines virtuelles Azure doivent être dans le même réseau virtuel Azure ou dans des réseaux virtuels homologués Azure. Par exemple <b>HN1</b>-data-mnt00001, <b>HN1</b>-log-mnt00001, etc. sont les noms de volumes et nfs://10.23.1.5/<b>HN1</b>-data-mnt00001, nfs://10.23.1.4/<b>HN1</b>-log-mnt00001, etc. sont les chemin d’accès de fichiers pour les volumes Azure NetApp Files.  

   1. volume <b>HN1</b>-data-mnt00001 (nfs://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. volume <b>HN1</b>-data-mnt00002 (nfs://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. volume <b>HN1</b>-log-mnt00001 (nfs://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. volume <b>HN1</b>-log-mnt00002 (nfs://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. volume <b>HN1</b>-shared (nfs://10.23.1.4/<b>HN1</b>-shared)
   
   Dans cet exemple, nous avons utilisé des Azure NetApp Files distincts pour chaque volume de données et de journaux HANA. Pour une configuration à coût optimisé sur des systèmes plus petits ou non productifs, il est possible de placer tous les montages de données et tous les montages de journaux sur un volume unique.  

### <a name="important-considerations"></a>Points importants à prendre en compte

Lorsque vous envisagez d’utiliser Azure NetApp Files pour SAP Netweaver sur une architecture à haute disponibilité SUSE, tenez compte des considérations importantes suivantes :

- La taille de pool de capacité minimale est de 4 Tio.  
- La taille de volume minimale est de 100 Gio
- Azure NetApp Files et toutes les machines virtuelles, où les volumes Azure NetApp Files seront montés, doivent être déployés dans le même réseau virtuel Azure ou dans des [réseaux virtuels homologués](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) de la même région.  
- Le réseau virtuel sélectionné doit avoir un sous-réseau délégué à Azure NetApp Files.
- Le débit d’un volume NetApp Azure est une fonction du quota de volume et du niveau de service, comme décrit dans [Niveau de service pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Lors du dimensionnement de volumes HANA Azure NetApp, assurez-vous que le débit obtenu répond à la configuration système requise pour HANA.  
- Azure NetApp Files propose une [stratégie d’exportation](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) : vous pouvez contrôler les clients autorisés, le type d’accès (lecture et écriture, lecture seule, etc.). 
- La fonctionnalité Azure NetApp Files ne tient pas encore compte des zones. Actuellement, la fonctionnalité Azure NetApp Files n’est pas déployée dans toutes les zones de disponibilité d’une région Azure. Méfiez-vous de l’impact potentiel sur les temps de latence dans certaines régions Azure.  
- Il est important que les machines virtuelles soient déployées à proximité du stockage Azure NetApp pour des raisons de faible latence. Pour les charges de travail SAP HANA, une latence faible est critique. Collaborez avec votre représentant Microsoft pour vous assurer que les machines virtuelles et les volumes Azure NetApp Files soient déployés à proximité.  
- L’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` sur les machines virtuelles doivent correspondre à la configuration dans Azure NetApp Files. 

> [!IMPORTANT]
> Pour les charges de travail SAP HANA, une latence faible est critique. Collaborez avec votre représentant Microsoft pour vous assurer que les machines virtuelles et les volumes Azure NetApp Files soient déployés à proximité.  

> [!IMPORTANT]
> En cas d’incompatibilité entre l’ID d’utilisateur pour <b>sid</b>adm et l’ID de groupe pour `sapsys` entre la machine virtuelle et la configuration Azure NetApp, les autorisations pour les fichiers sur les volumes NetApp Azure, montés sur les machines virtuelles, s’afficheront en tant que `nobody`. Veillez à spécifier l’ID d’utilisateur correct pour <b>sid</b>adm et l’ID de groupe pour `sapsys`, lors de l’[intégration d’un nouveau système](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) à Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionnement de la base de données HANA sur Azure NetApp Files

Le débit d’un volume NetApp Azure est une fonction de la taille de volume et du niveau de service, comme décrit dans [Niveau de service pour Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Lorsque vous concevez l’infrastructure pour SAP dans Azure, vous devez connaître certaines exigences de stockage minimales de SAP, qui se traduisent par des caractéristiques de débit minimales :

- Activer la lecture/l’écriture sur /hana/log à un débit de 250 Mo/s pour des tailles d’E/S de 1 Mo  
- Activer l’activité de lecture à un débit de 400 Mo/s au minimum pour /hana/data pour des tailles d’E/S de 16 Mo et 64 Mo  
- Activer l’activité d’écriture à un débit de 250 Mo/s au minimum pour /hana/data avec des tailles d’E/S de 16 Mo et 64 Mo  

Les [limites de débit Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) pour un quota de volume de 1 Tio sont les suivantes :
- Niveau de stockage Premium - 64 Mio/s  
- Niveau de stockage Ultra - 128 Mio/s  

Pour respecter les exigences de débit minimal SAP pour les données et le journal, et conformément aux instructions pour `/hana/shared`, les tailles recommandées ressemblent à ceci :

| Volume | Size<br /> Niveau de stockage Premium | Size<br /> Niveau de stockage Ultra |
| --- | --- | --- |
| /hana/log/ | 4 Tio | 2 Tio |
| /hana/data | 6,3 Tio | 3,2 Tio |
| /hana/shared | Max (512 Go, 1xRAM) par 4 nœuds Worker | Max (512 Go, 1xRAM) par 4 nœuds Worker |

La configuration SAP HANA pour la disposition présentée dans cet article, à l’aide du niveau de stockage Azure NetApp Files Ultra ressemblerait à ceci :

| Volume | Size<br /> Niveau de stockage Ultra |
| --- | --- |
| /hana/log/mnt00001 | 2 Tio |
| /hana/log/mnt00002 | 2 Tio |
| /hana/data/mnt00001 | 3,2 Tio |
| /hana/data/mnt00002 | 3,2 Tio |
| /hana/shared | 2 Tio |

> [!NOTE]
> Les recommandations relatives aux tailles Azure NetApp Files qui sont indiquées ici ciblent une configuration minimale pour le protocole SAP équivalente à celles de leurs fournisseurs d’infrastructure. Dans les déploiements de clients et les scénarios de charge de travail réels, cela peut ne pas suffire. Prenez ces recommandations en tant que point de départ et adaptez-les en fonction des exigences de votre charge de travail spécifique.  

> [!TIP]
> Vous pouvez redimensionner les volumes Azure NetApp Files de manière dynamique, sans avoir à `unmount` les volumes, à arrêter les machines virtuelles ou à arrêter SAP HANA. Cela permet de répondre aux exigences à la fois attendues et imprévues de votre application en matière de débit.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Déployer des machines virtuelles Linux via le Portail Azure

Vous devez d’abord créer les volumes Azure NetApp Files. Créez les [sous-réseaux du réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dans le [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Déployez les machines virtuelles. Créez les interfaces réseau supplémentaires et attachez les interfaces réseau aux machines virtuelles correspondantes. Chaque machine virtuelle a trois interfaces réseau, correspondant aux trois sous-réseaux du réseau virtuel Azure (`storage`, `hana` et `client`).  Voir le [Guide de création d’une machine virtuelle Linux dans Azure avec plusieurs cartes d’interface réseau](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Pour les charges de travail SAP HANA, une latence faible est critique. Collaborez avec votre représentant Microsoft pour vous assurer que les machines virtuelles et les volumes Azure NetApp Files soient déployés à proximité pour atteindre une faible latence. Soumettez les informations nécessaires lors de l’[intégration du nouveau système SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), qui utilise SAP HANA Azure NetApp Files.  
> 
Les étapes suivantes supposent que vous avez déjà créé le groupe de ressources, le réseau virtuel Azure et les trois sous-réseaux du réseau virtuel Azure : `storage`, `hana` et `client`.  Lors du déploiement des machines virtuelles, sélectionnez le sous-réseau de stockage, afin que l’interface réseau de stockage soit l’interface principale sur les machines virtuelles. Si ce n’est pas possible, un itinéraire explicite vers le sous-réseau délégué Azure NetApp via la passerelle de sous-réseau de stockage doit être configuré. 

> [!IMPORTANT]
> Vérifiez que le système d’exploitation que vous sélectionnez est certifié SAP pour SAP HANA sur les types de machine virtuelle spécifiques que vous utilisez. La liste des types de machine virtuelle certifiés SAP HANA et des systèmes d’exploitation correspondants peut être consultée dans la page [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Cliquez sur les détails du type de machine virtuelle listé pour obtenir la liste complète des versions de système d’exploitation SAP HANA prises en charge pour le type de machine virtuelle spécifique.  

1. Créez un groupe à haute disponibilité pour SAP HANA. Veillez à définir un domaine de mise à jour maximal.  

2. Créer trois machines virtuelles (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Utilisez une image SLES4SAP de la galerie Azure prise en charge pour SAP HANA. Dans cet exemple, nous avons utilisé une image SLES4SAP 12 SP4.  
   - Sélectionnez le groupe à haute disponibilité créé précédemment pour SAP HANA.  
   - Sélectionnez le sous-réseau du réseau virtuel de stockage Azure. Sélectionnez [Mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Lorsque vous déployez les machines virtuelles, le nom de l’interface réseau est généré automatiquement. Nous allons faire référence aux interfaces réseau, associées au sous-réseau du réseau virtuel Azure de stockage en tant que **hanadb1-storage**, **hanadb2-storage** et **hanadb3-storage**. 
3. Créez trois interfaces réseau, une pour chaque machine virtuelle, pour le sous-réseau du réseau virtuel `hana`. Dans cet exemple : **hanadb1-hana**, **hanadb2-hana** et **hanadb3-hana**.  
4. Créez trois interfaces réseau : une pour chaque machine virtuelle, pour le sous-réseau du réseau virtuel du **client**. Dans cet exemple : **hanadb1-client**, **hanadb2-client** et **hanadb3-client**.  
5. Attachez les interfaces réseau virtuelles nouvellement créées aux machines virtuelles correspondantes  

    1. Accédez à la machine virtuelle présente sur le [Portail Azure](https://portal.azure.com/#home).  
    2. Sélectionnez Machines Virtuelles dans le volet de navigation de gauche. Filtrez sur le nom de la machine virtuelle, par exemple **hanadb1**. Cliquez sur la machine virtuelle.  
    3. Comme vous êtes dans la vue d’ensemble, sélectionnez Arrêter pour libérer la machine virtuelle.  
    4. Sélectionnez Mise en réseau, Attacher l’interface réseau. Sélectionnez dans la liste déroulante sous « Attacher l’interface réseau » les interfaces réseau déjà créées pour **`hana`** et les sous-réseaux **clients**.  Enregistrez. 
    5. Répétez cette opération pour les autres machines virtuelles. Dans notre exemple, **hanadb2** et **hanadb3**.
    6. Laissez les machines virtuelles dans l’état arrêté pour l’instant. Ensuite, nous allons activer [Réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pour toutes les interfaces réseau qui viennent d’être attachées.  

6. Activez la mise en réseau accélérée pour les interfaces réseau supplémentaires pour **`hana`** et les sous-réseaux **`client`** .  

    1. Ouvrez [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) dans le [Portail Azure](https://portal.azure.com/#home)  
    2. Exécutez les commandes suivantes pour activer la mise en réseau accélérée pour les interfaces réseau supplémentaires, attachées à **`hana`** et aux sous-réseaux **`client`** .  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Démarrer les machines virtuelles  

    1. Sélectionnez Machines Virtuelles dans le volet de navigation de gauche. Filtrez sur le nom de la machine virtuelle, par exemple **hanadb1**. Cliquez sur la machine virtuelle.  
    2. Comme vous êtes dans la vue d’ensemble, sélectionnez Démarrer.  

## <a name="operating-system-configuration-and-preparation"></a>Configuration et préparation du système d’exploitation

Les éléments suivants sont précédés de **[A]** - applicable à tous les nœuds, de **[1]** - applicable uniquement au nœud 1, **[2]** - applicable uniquement au nœud 2 ou **[3]** - applicable uniquement au nœud 3.

1. **[A]** Conservez les fichiers hôtes sur les machines virtuelles. Incluez des entrées pour tous les sous-réseaux. Les entrées suivantes ont été ajoutées à `/etc/hosts` pour cet exemple.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Modifiez les paramètres de configuration DHCP et de cloud pour éviter les modifications involontaires du nom d’hôte.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** Préparez le système d’exploitation pour l’exécution de SAP HANA sur des systèmes NetApp avec NFS, comme décrit dans le [Guide de configuration SAP HANA sur systèmes NetApp AFF avec NFS](https://www.netapp.com/us/media/tr-4435.pdf). Créez un fichier de configuration pour les paramètres de configuration de NetApp : `/etc/sysctl.d/netapp-hana.conf`.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Créez un fichier de configuration avec les paramètres de configuration Microsoft Azure : `/etc/sysctl.d/ms-az.conf`.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** Ajustez les paramètres sunrpc comme recommandé dans le [Guide de configuration SAP HANA sur systèmes NetApp AFF avec NFS](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Monter les volumes Azure NetApp Files

1. **[A]** Créez des points de montage pour les volumes de base de données HANA.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Créez des répertoires spécifiques aux nœuds pour `/usr/sap` sur partages **HN1**.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Vérifiez le paramètre de domaine NFS. Assurez-vous que le domaine est configuré en tant que **`localdomain`** et que le mappage est défini sur **nobody**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Désactivez le mappage d’ID NFSv4. Exécutez la commande de montage pour créer la structure de répertoire, où se trouve `nfs4_disable_idmapping`.  Vous ne serez pas en mesure de créer manuellement le répertoire sous/sys/modules, car l’accès est réservé pour le noyau/les pilotes.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** Créez manuellement le groupe et l’utilisateur SAP HANA. Les ID pour le groupe sapsys et l’utilisateur **hn1**ADM doivent être définis sur les mêmes ID, fournis lors de l’intégration. Dans cet exemple, les ID sont définis sur **1001**. Dans le cas contraire, il ne sera pas possible d’accéder aux volumes.  Les ID pour le groupe sapsys et les comptes d'utilisateurs **hn1**adm et sapadm, doivent être identiques sur toutes les machines virtuelles.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Montez les volumes Azure NetApp Files partagés.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Montez les volumes spécifiques aux nœuds sur **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Montez les volumes spécifiques aux nœuds sur **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Montez les volumes spécifiques aux nœuds sur **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Vérifiez que tous les volumes HANA sont montés avec la version de protocole NFS **NFSv4**.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Installation  

Dans cet exemple, pour le déploiement de SAP HANA dans une configuration Scale-out avec un nœud de secours avec Azure, nous avons utilisé HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Préparer l’installation de HANA

1. **[A]** Définissez le mot de passe racine, avant l’installation de HANA (vous pouvez désactiver le mot de passe racine une fois l’installation terminée). Exécutez en tant que commande `root` `passwd`.  

2. **[1]** Vérifiez que vous pouvez utiliser ssh sur **hanadb2** et **hanadb3**, sans être invité à entrer un mot de passe.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Installez des packages supplémentaires, requis pour HANA 2.0 SP4. Consultez la note SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) pour plus d’informations. 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Modifiez la propriété de SAP HANA des répertoires `data` et `log` pour **hn1**adm.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Installation de HANA

1. **[1]** Installez SAP HANA, en suivant les instructions fournies dans [Guide d’installation et de mise à jour de SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Dans cet exemple, nous installons le Scale-out SAP HANA avec un maître, un Worker et un nœud de secours.  
   Démarrez le programme **hdblcm** à partir du répertoire du logiciel d’installation HANA. Utilisez le paramètre `internal_network` et transmettez l’espace d’adressage pour le sous-réseau, utilisé pour la communication entre nœuds HANA interne.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Entrez les valeurs suivantes à l’invite.

     * Choisir une action :  Entrer **1** (pour l’installation)
     * Select additional components for installation : Entrer **2, 3**
     * Entrer le chemin d’installation :  Appuyer sur Entrée (utilise par défaut /hana/shared)
     * Entrer le nom d’hôte local : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Voulez-vous ajouter des hôtes au système ? Entrez **y**
     * Entrer les noms d’hôte séparés par un virgule à ajouter : **hanadb2, hanadb3**
     * Entrer le nom d’utilisateur racine [root] : appuyer sur Entrée pour accepter la valeur par défaut
     * Entrer le mot de passe de l’utilisateur racine : Entrer le mot de passe de la racine
     * Sélectionnez des rôles pour l’hôte hanadb2 : Entrer **1** (pour Worker)
     * Entrer le groupe de basculement d’hôte pour l’hôte hanadb2 [par défaut] :  Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le numéro de la partition de stockage pour l’hôte hanadb2 [<<assign automatically>>] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le groupe Worker pour l’hôte hanadb2 [par défaut] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Sélectionner des rôles pour l’hôte hanadb3 : Entrer **2** (pour la mise en veille)
     * Entrer le groupe de basculement d’hôte pour l’hôte hanadb3 [par défaut] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le groupe Worker pour l’hôte hanadb3 [par défaut] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Enter SAP HANA System ID : Entrer **HN1**
     * Entrer le numéro d’instance [00] : Entrer **03**
     * Entrer le groupe Worker de l’hôte local [par défaut] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Sélectionner l’utilisation du système / Entrer l’index [4] : Entrer **4** (pour personnalisé)
     * Enter Location of Data Volumes [/hana/data/HN1] :  Appuyer sur Entrée pour utiliser la valeur par défaut
     * Enter Location of Log Volumes [/hana/log/HN1] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Restrict maximum memory allocation? [n] : Entrer **n**
     * Entrer le nom d’hôte du certificat pour l’hôte hanadb1 [hanadb1] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le nom d’hôte du certificat pour l’hôte hanadb2 [hanadb2] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le nom d’hôte du certificat pour l’hôte hanadb3 [hanadb3] : Appuyer sur Entrée pour utiliser la valeur par défaut
     * Entrer le mot de passe de l’administrateur système (hn1adm) : Entrer le mot de passe
     * Entrer le mot de passe de l’utilisateur de la base de données système (SYSTEM) : Entrer le mot de passe de SYSTEM
     * Confirmer le mot de passe de l’utilisateur de la base de données système (SYSTEM) : Entrer le mot de passe de SYSTEM
     * Restart system after machine reboot? [n] : Entrer **n** 
     * Voulez-vous continuer (o/n) : Valider le résumé et si tout semble correct, entrer **y**


2. **[1]** vérifier global. ini  

   Affichez global. ini et assurez-vous que la configuration de la communication interne SAP HANA entre les nœuds est en place. Vérifiez la section **communication**. Elle doit comporter un espace d’adressage pour le sous-réseau **`hana`** et `listeninterface` doit être définie sur `.internal`. Vérifiez la section **internal_hostname_resolution**. Elle doit comporter les adresses IP des machines virtuelles HANA qui appartiennent au sous-réseau **`hana`** .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Ajoutez le mappage de l’hôte pour vous assurer que les adresses IP du client sont utilisées pour les communications des clients. Ajoutez la section `public_host_resolution` et ajoutez les adresses IP correspondantes à partir du sous-réseau client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Redémarrez SAP HANA pour activer les modifications.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Vérifiez que l’interface client utilise les adresses IP du sous-réseau **client** pour les communications.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Pour plus d’informations sur la vérification de la configuration, consultez la note SAP [2183363 - Configuration du réseau interne SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Pour optimiser SAP HANA pour le stockage de fichiers NetApp Azure sous-jacent, définissez les paramètres SAP HANA suivants :

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks` **all**

   Pour plus d’informations, consultez le [Guide de configuration SAP HANA sur les systèmes NetApp AFF avec NFS](https://www.netapp.com/us/media/tr-4435.pdf). 

   À partir des systèmes SAP HANA 2.0, vous pouvez définir les paramètres dans `global.ini`. Consultez la note SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   Pour les systèmes SAP HANA 1.0, les versions jusqu’à SPS12, ces paramètres peuvent être définis lors de l’installation, comme décrit dans la note SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Le stockage utilisé par Azure NetApp Files présente une limite de taille de fichier de 16 To. SAP HANA n’est pas implicitement au courant de la limitation de stockage et ne crée pas automatiquement de nouveau fichier de données, lorsque la limite de taille de fichier de 16 To est atteinte. Comme SAP HANA tente d’augmenter le fichier au-delà de 16 To, cela génère des erreurs et finit par provoquer un incident sur le serveur d’index. 

   > [!IMPORTANT]
   > Pour empêcher SAP HANA d’augmenter la taille des fichiers de données au-delà de la limite des [16 To](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) du stockage sybsystem, définissez les paramètres suivants dans `global.ini`.  
   > -  datavolume_striping=true
   > - datavolume_striping_size_gb = 15000 Pour plus d’informations, voir la note SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > N’oubliez pas la note SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test de basculement SAP HANA 

1. Simuler un incident de nœud sur un nœud Worker SAP HANA  

   Exécutez les commandes suivantes en tant que **hn1**adm pour capturer l’état de l’environnement, avant de simuler l’incident du nœud.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   Exécutez la commande suivante en tant que racine (root) sur le nœud Worker, en l’occurrence **hanadb2** pour simuler un incident de nœud.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Surveillez le système pour la fin du basculement. Une fois le basculement terminé, capturez l’état : il doit ressembler à l’état présenté ci-dessous.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Pour éviter les retards avec le basculement SAP HANA, lorsqu’un nœud subit une panique du noyau, définissez `kernel.panic` à 20 secondes sur **toutes** les machines virtuelles HANA. La configuration est effectuée dans `/etc/sysctl`. Redémarrez les machines virtuelles pour activer la modification. Le basculement, lorsqu’un nœud subit une panique du noyau, peut prendre 10 minutes ou plus, si cette modification n’est pas effectuée.  

2. Arrêter le serveur de noms  
   Exécutez les commandes suivantes en tant que **hn1**adm pour vérifier l’état de l’environnement, avant le test :  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Exécutez les commandes suivantes en tant que **hn1**adm sur le nœud principal actif, qui est dans ce cas **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Le nœud de secours **hanadb3** prend la forme d’un nœud principal. État de la ressource après la fin du test de basculement :  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Redémarrez l’instance HANA sur **hanadb1**, c’est-à-dire sur la même machine virtuelle que sur laquelle le serveur de noms a été arrêté. Le nœud **hanadb1** rejoindra l’environnement et conservera son rôle de secours.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Attendez-vous à l’état suivant, une fois que SAP HANA a démarré sur **hanadb1** :  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Maintenant, supprimez à nouveau le serveur de noms sur le nœud principal actuellement actif, autrement dit, sur hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Le nœud **hanadb1** va reprendre le rôle du nœud principal. Une fois le test de basculement terminé, l’état se présente comme suit :
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   Démarrez SAP HANA sur **hanadb3** : il sera prêt à faire office de nœud de secours.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   L’état une fois que SAP HANA a démarré sur **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Étapes suivantes

* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour découvrir comment établir la haute disponibilité et planifier la récupération d’urgence de SAP 
* HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]
