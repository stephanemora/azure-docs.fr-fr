---
title: Déployer la reprise d’activité avec JetStream DR (Préversion)
description: Apprenez à déployer JetStream Disaster Recovery (DR) pour le cloud privé de votre Solution Azure VMware et les charges de travail VMware locales.
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: f43d2a8db4ef29588ad5260c583faab31bd01228
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279067"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>Déployer la reprise d’activité avec JetStream DR (Préversion)

>[!IMPORTANT]
>JetStream DR sur la Solution Azure VMware (préversion) est actuellement en préversion publique.
>Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Jetstream DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) est une solution de récupération d’urgence Cloud Native conçue pour réduire le temps d’arrêt des machines virtuelles (VM) en cas de sinistre. Les instances de Jetstream DR sont déployées sur les sites protégés et de récupération. 

JetStream repose sur la base de la protection continue des données (CDP), à l’aide de l'[infrastructure VAIO VMware](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio), qui permet une perte de données minimum, voire nulle. La récupération d’urgence JetStream fournit le niveau de protection souhaité pour les applications métier et stratégiques. Il offre également une récupération d’urgence économique en utilisant des ressources minimales sur le site de récupération d’urgence et en utilisant un stockage cloud rentable, tel que le[Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/).

Dans cet article, vous allez déployer JetStream Disaster Recovery (DR) pour le cloud privé de votre Solution Azure VMware et les charges de travail VMware locales.

Pour en savoir plus sur la récupération d’urgence JetStream, consultez :

- [Présentation de la solution JetStream](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [JetStream DR sur la place de marché Azure](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [Articles de la base de connaissances JetStream](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>Composants principaux de la solution de récupération d’urgence JetStream

| Éléments | Description |
| --- | --- |
| **Appliance virtuelle du serveur d’administration JetStream (MSA)**  | MSA active la configuration du jour 0 et du jour 2, par exemple, les sites principaux, les domaines de protection et la récupération des machines virtuelles.  MSA est installé sur un nœud vSphere par l’administrateur du Cloud.  Le MSA implémente un plug-in vCenter qui vous permet de gérer la récupération d’urgence JetStream en mode natif à partir de vCenter. Le MSA ne gère pas les données de réplication des machines virtuelles protégées.  | 
| **Appliance virtuelle de récupération d’urgence JetStream (DRVA)**  | L’appliance de machine virtuelle Linux reçoit les données de réplication des machines virtuelles protégées à partir de l’hôte ESXi source. Elle est responsable du stockage des données de réplication sur le site de récupération d’urgence, généralement dans un magasin d’objets comme le Stockage Blob Azure. En fonction du nombre de machines virtuelles protégées et de la quantité de stockage à répliquer, la cloudadmin privée peut créer une ou plusieurs instances DRVA.  | 
| **Composants de l’hôte JetStream ESXi (packages de filtre d’e/s)**  | Logiciel JetStream installé sur chaque hôte ESXi configuré pour la récupération d’urgence JetStream. Le pilote hôte intercepte un e/s des machines virtuelles vSphere et envoie les données de réplication au DRVA.   | 
| **Domaine de protection JetStream**  | Groupe logique de machines virtuelles qui seront protégées ensemble à l’aide des mêmes stratégies et du même livre d’exécution. Les données de toutes les machines virtuelles d’un domaine de protection sont stockées dans la même instance de conteneur d’objets Blob Azure. La même instance DRVA gère la réplication vers le stockage de récupération d’urgence à distance pour toutes les machines virtuelles dans un domaine de protection.   | 
| **Conteneurs de stockage d’objets blob Azure**  | Les données répliquées des machines virtuelles protégées sont stockées dans des objets Blob Azure. Le logiciel JetStream crée une instance de conteneur d’objets Blob Azure pour chaque domaine de protection JetStream.    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Scénarios JetStream sur la solution VMware Azure
Vous pouvez utiliser la solution de récupération d’urgence JetStream avec la solution VMware Azure pour les deux scénarios suivants :  

- Récupération d’urgence VMware local vers la Solution VMware Azure

- Récupération d’urgence de la Solution VMware Azure vers la Solution VMware Azure

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Scénario 1 : Récupération d’urgence VMware local vers la Solution VMware Azure

Dans ce scénario, le site principal est votre environnement VMware local et le site de récupération d’urgence est un Cloud privé de la Solution VMware Azure.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagramme montrant le déploiement de JetStream depuis un emplacement local vers le cloud privé Azure VMware Solution." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Scénario 2 : Récupération d’urgence de la Solution VMware Azure vers la Solution VMware Azure

Dans ce scénario, le site principal est un Cloud privé de la Solution VMware Azure dans une région Azure. Le site de récupération d’urgence est un Cloud privé de la Solution VMware Azure dans une autre région Azure. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagramme montrant le déploiement de JetStream vers le cloud privé Azure VMware Solution." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>Prérequis


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Scénario 1 : Récupération d’urgence VMware local vers la Solution VMware Azure 

- Le cloud privé d’une solution VMware Azure a été déployé avec un minimum de trois nœuds dans la région de récupération d’urgence cible.  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Diagramme montrant le premier prérequis pour la solution de récupération d’urgence sur une solution VMware Azure." lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- Connectivité réseau configurée entre les appliances du site principal JetStream et l’instance de Stockage Blob Azure. 

- [Configurez et abonnez-vous à Jetstream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) à partir de la place de marché Azure pour télécharger le logiciel de récupération d’urgence Jetstream.

- [Compte de Stockage Blob Azure](../storage/common/storage-account-create.md) créé à l’aide d’un niveau de Performance Standard ou Premium. Pour [accéder au niveau, sélectionnez **Chaud**](../storage/blobs/access-tiers-overview.md). 

   >[!NOTE]
   >L’option **Activer l’espace de noms hiérarchique** sur l’objet Blob n’est pas prise en charge.   

- Un segment de réseau NSX-T configuré sur le cloud privé d’une Solution VMware Azure et éventuellement un DHCP activé sur le segment des appliances virtuelles JetStream.  

- Un serveur DNS configuré pour résoudre les adresses IP de la Solution VMware azure vCenter, des hôtes ESXi de la Solution Azure VMware, un compte de stockage Azure et le service de place de marché JetStream pour les appliances virtuelles JetStream. 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Scénario 2 : Récupération d’urgence de la Solution VMware Azure vers la Solution VMware Azure

- Le cloud privé d’une solution VMware Azure est déployé avec un minimum de trois nœuds dans les régions primaire et secondaire.  
 
- Connectivité réseau configurée entre les appliances du site principal JetStream et l’instance de Stockage Blob Azure. 

- [Configurez et abonnez-vous à Jetstream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) à partir de la place de marché Azure pour télécharger le logiciel de récupération d’urgence Jetstream.

- [Compte de Stockage Blob Azure](../storage/common/storage-account-create.md) créé à l’aide d’un niveau de Performance Standard ou Premium. Pour [accéder au niveau, sélectionnez **Chaud**](../storage/blobs/access-tiers-overview.md). 

   >[!NOTE]
   >L’option **Activer l’espace de noms hiérarchique** sur l’objet Blob n’est pas prise en charge.   

- Un segment de réseau NSX-T configuré sur le cloud privé d’une Solution VMware Azure et éventuellement un DHCP activé sur le segment des appliances virtuelles JetStream.  

- Un serveur DNS configuré sur les sites principaux et de récupération d’urgence pour résoudre les adresses IP de la Solution VMware Azure vCenter, des hôtes ESXi de la Solution VMware Azure, du compte de stockage Azure et du service de place de marché JetStream pour les appliances virtuelles JetStream. 


Pour plus d’informations sur les conditions préalables à la récupération d’urgence JetStream locale, consultez le [Guide de préinstallation de Jetstream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/).





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Installer JetStream DR sur la Solution VMware Azure  
 
Vous pouvez suivre ces étapes pour les deux scénarios pris en charge. 
 
1. Dans votre centre de données local, installez JetStream DR en suivant la [documentation de Jetstream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/).  

1. Dans le Cloud privé de votre Solution VMware Azure, installez JetStream DR à l’aide d’une commande d’Exécution. A partir du [portail Azure](https://portal.azure.com), sélectionnez **Exécuter la commande** > **Packages** > **Configuration JSDR**.  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Capture d’écran montrant comment accéder aux commandes d’exécution JetStream disponibles." lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >L’utilisateur CloudAdmin par défaut dans la Solution VMware Azure ne dispose pas des privilèges suffisants pour installer JetStream DR.  La solution VMware Azure permet une installation simplifiée et automatisée de la récupération d’urgence JetStream en invoquant la commande d’exécution de la solution VMware Azure pour la récupération d’urgence JetStream.  
 

1. Exécutez l’applet de commande **Invoke-PreflightJetDRInstall** , qui vérifie si les conditions préalables à l’installation de la récupération d’urgence Jetstream ont été respectées. Par exemple, il valide le nombre d’hôtes, les noms de cluster et les noms de machines virtuelles uniques requis. 

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Réseau**  | Nom du segment de réseau NSX-T dans lequel vous devez déployer le MSA JetStream.  |
   | **Magasins de données**  | Nom du magasin de données dans lequel vous allez déployer le MSA.  |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster. |
   | **Cluster** | Nom du cluster privé de la Solution VMware Azure sur lequel le MSA JetStream est déployé, par exemple **Cluster-1**. |
   | **VMName** | Nom de la machine virtuelle MSA JetStream, par exemple, **jetstreamServer**. |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Invoke-PreflightJetDRInstall-Exec1**. Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès. |
   | **Délai d'expiration**  | Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).
   

## <a name="install-the-jetstream-dr-msa"></a>Installer le MSA JetStream DR

La solution VMware Azure prend en charge l’installation de JetStream en utilisant des adresses IP statiques ou des adresses IP DHCP.  

### <a name="static-ip-address"></a>Adresse IP statique

1. Sélectionnez **Exécuter la commande** > **Packages** > **Install-JetDRWithStaticIP**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.


   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Magasins de données**  | Nom du magasin de données dans lequel vous allez déployer le MSA JetStream.  |
   | **VMName** | Nom de la machine virtuelle MSA JetStream, par exemple, **jetstreamServer**. |
   | **Cluster** | Nom du cluster privé de la Solution VMware Azure sur lequel le MSA JetStream est déployé, par exemple **Cluster-1**. |
   | **Masque réseau** | Masque réseau du MSA à déployer, par exemple, **22** ou **24**. |
   | **MSIp** | Adresse IP de la machine virtuelle MSA JetStream.   |
   | **Dns** | Adresse IP DNS que la machine virtuelle MSA JetStream doit utiliser.   |
   | **Passerelle** | Adresse IP de la passerelle de réseau pour la machine virtuelle MSA JetStream.  |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **HostName** | Nom d’hôte (FQDN) de la machine virtuelle MSA JetStream.  |
   | **Réseau**  | Nom du segment de réseau NSX-T dans lequel vous devez déployer le MSA JetStream. |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **install-JetDRWithStaticIP-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |


1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).


### <a name="dhcp-based-ip-address"></a>Adresse IP basée sur DHCP

Cette étape installe également l’offre groupée d’installation JetStream vSphere (VIB) sur les clusters qui ont besoin d’une protection de récupération d’urgence. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **Install-JetDRWithDHCP**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Magasins de données**  | Nom du magasin de données dans lequel vous allez déployer le MSA JetStream.  |
   | **VMName** | Nom de la machine virtuelle MSA JetStream, par exemple, **jetstreamServer**. |
   | **Cluster** | Nom du cluster privé de la Solution VMware Azure sur lequel le MSA JetStream est déployé, par exemple **Cluster-1**. |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **HostName** | Nom d’hôte (FQDN) de la machine virtuelle MSA JetStream.  |
   | **Réseau**  | Nom du segment de réseau NSX-T dans lequel vous devez déployer le MSA JetStream. |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Install-JetDRWithDHCP-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |
 
 
1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>Ajouter JetStream DR aux nouveaux clusters de la Solution VMware Azure  


1. Sélectionnez **Exécuter la commande** > **Packages** > **Enable-JetDRForCluster**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **MSIp** | Adresse IP de la machine virtuelle MSA JetStream.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Enable-JetDRForCluster-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |
  
1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).
 
 
 

## <a name="configure-jetstream-dr"></a>Configurer la récupération d’urgence JetStream 
 
Cette section présente uniquement une vue d’ensemble des étapes requises pour la configuration de la récupération d’urgence JetStream.  Pour obtenir des descriptions et des étapes détaillées, consultez la documentation sur la [Configuration de la récupération d’urgence Jetstream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/) . 
 
Une fois le MSA JetStream et le VIB JetStream DR installés sur les clusters de la Solution VMware Azure, utilisez le portail JetStream pour effectuer les étapes de configuration restantes. 



1. Accédez au portail JetStream à partir de l’appliance vCenter.

1. [Ajoutez un site de stockage externe](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/).  

1. [Déployez une appliance Jetstream DRVA](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/). 

1. [Créez un volume de magasin de journaux de réplication Jetstream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/) à l’aide de l’un des magasins de données disponibles pour le cluster de la Solution VMware Azure. 

   >[!TIP]
   >Le stockage local rapide, tel que le magasin de stockage vSAN, est préféré pour le journal de réplication. 
 
1. [Créez un domaine protégé par Jetstream](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/). vous fournirez le site de Stockage Blob Azure, l’instance DRVA JetStream et le journal de réplication créés lors des étapes précédentes. 

1. [Sélectionnez les machines virtuelles](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/) que vous souhaitez protéger, puis [démarrez la protection de la machine virtuelle](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/).

 
Pour connaître les étapes de configuration restantes pour la récupération d’urgence JetStream, telles que la création d’un runbook de basculement, l’appel du basculement sur le site de récupération d’urgence et l’appel de la restauration automatique sur le site principal, consultez la [documentation du Guide d’administration Jetstream](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/).  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Désactivation de la récupération d’urgence JetStream sur un cluster de la Solution VMware Azure  
 
Cet applet de commande désactive JetStream DR uniquement sur l’un des clusters et ne désinstalle pas complètement JetStream DR. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **Disable-JetDRForCluster**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **MSIp** | Adresse IP de la machine virtuelle MSA JetStream.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Disable-JetDRForCluster-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |

1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).


 
## <a name="uninstall-jetstream-dr"></a>Désinstaller JetStream DR  

1. Sélectionnez **Exécuter la commande** > **Packages** > **Invoke-PreflightJetDRUninstall**. Cet applet de commande vérifie si le cluster a au moins quatre hôtes (minimum requis). 

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **MSIp** | Adresse IP de la machine virtuelle MSA JetStream.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Invoke-PreflightJetDRUninstall-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |

1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).

1. Une fois l’applet de commande préliminaire terminé avec succès, sélectionnez **Uninstall-JetDR**, indiquez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **ProtectedCluster** | Nom du cluster de cloud privé de la Solution VMware Azure à protéger, par exemple, **Cluster-1**.  Vous ne pouvez fournir qu’un seul nom de cluster au cours de l’installation. |
   | **Informations d'identification**  |  Informations d'identification de l’utilisateur racine de la machine virtuelle MSA JetStream.   |
   | **MSIp** | Adresse IP de la machine virtuelle MSA JetStream.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique de l’exécution, par exemple, **Uninstall-JetDR-Exec1**.   Il est utilisé pour vérifier si l’applet de commande a été exécutée avec succès.  |

 1. [Affichez l’état de l’exécution](concepts-run-command.md#view-the-status-of-an-execution).


## <a name="region-availability"></a>Disponibilité des régions

La récupération d’urgence JetStream pour les solutions VMware Azure est disponible dans les régions suivantes : 

- USA Est 

- Europe Nord 

- USA Sud 

- Europe Ouest 

- USA Centre 

- USA Ouest 

- Asie Est 

- Japon Est 

- Brésil Sud 

- Est du Canada 

- Ouest du Royaume-Uni 


 
## <a name="support"></a>Assistance  
 
La récupération d’urgence JetStream est une solution prise en charge par le[Logiciel Jetstream](https://www.jetstreamsoft.com/) . Pour tout problème de produit ou de support avec JetStream, contactez support-avs@jetstreamsoft.com.  
 
La solution VMware Azure utilise la commande Exécuter (Préversion) pour automatiser l’installation et la désinstallation de la récupération d’urgence JetStream. Contactez le support Microsoft pour tout problème avec les commandes d’exécution. Pour les problèmes avec les applets de commande d’installation et de désinstallation de JetStream, contactez JetStream pour obtenir de l’aide. 



## <a name="next-steps"></a>Étapes suivantes

- [Configuration de l’infrastructure : récupération d’urgence JetStream pour la Solution VMware Azure](https://vimeo.com/480574312/b5386a871c) 

- [JetStream DR pour la Solution VMware Azure (Démonstration complète)](https://vimeo.com/475620858/2ce9413248)
   
   - [Prise en main de JetStream DR pour la Solution VMware Azure](https://vimeo.com/491880696/ec509ff8e3)

   - [Configurer et protéger les machines virtuelles](https://vimeo.com/491881616/d887590fb2)

   - [Basculement vers Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Restauration automatique sur un site local](https://vimeo.com/491884402/65ee817b60)

