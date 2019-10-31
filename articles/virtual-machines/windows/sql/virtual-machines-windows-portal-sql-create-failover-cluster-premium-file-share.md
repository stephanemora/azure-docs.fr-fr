---
title: Instance de cluster de basculement SQL Server avec partage de fichiers Premium – Machines virtuelles Azure
description: Cet article explique comment créer une instance de cluster de basculement SQL Server en utilisant un partage de fichiers Premium sur des machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: cebfb5315a7a0b7cf92dbb9e3e77ff4c35851aa8
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757816"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurer une instance de cluster de basculement SQL Server avec un partage de fichiers Premium sur des machines virtuelles Azure

Cet article explique comment créer une instance de cluster de basculement SQL Server sur des machines virtuelles Azure à l’aide d’un [partage de fichiers Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Les partages de fichiers Premium sont des partages de fichiers à faible latence s’appuyant sur des disques SSD, qui sont entièrement pris en charge pour une utilisation avec une instance de cluster de basculement pour SQL Server 2012 et ultérieur sur Windows Server 2012 et ultérieur. Les partages de fichiers Premium vous offrent une plus grande flexibilité, ce qui vous permet de redimensionner et de mettre à l’échelle le partage de fichiers sans temps d’arrêt. 


## <a name="before-you-begin"></a>Avant de commencer

Il existe quelques éléments que vous devez connaître et deux choses que vous devez mettre en place avant de continuer.

Vous devez avoir une compréhension opérationnelle des technologies suivantes :

- [Technologies de cluster Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instances de cluster de basculement SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Une différence très importante est que sur un cluster de basculement de machine virtuelle IaaS Azure, nous vous recommandons d’utiliser une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. Les réseaux Azure intègrent une redondance physique qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Même si le rapport de validation de cluster émet un avertissement stipulant que les nœuds sont uniquement accessibles sur un seul réseau, vous pouvez ignorer ce dernier sans problème sur les clusters de basculement de machine virtuelle IaaS Azure. 

De plus, vous devez également avoir une compréhension générale des technologies suivantes :

- [Partage de fichiers Premium Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Groupes de ressources Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Pour le moment, les instances de cluster de basculement SQL Server sur des machines virtuelles Azure sont prises en charge uniquement avec le mode de gestion [léger](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) de l’[extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Pour passer du mode d’extension complet au mode d’extension léger, supprimez la ressource **Machine virtuelle SQL** pour les machines virtuelles correspondantes, puis inscrivez-les auprès du fournisseur de ressources de machine virtuelle SQL en mode [léger](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Quand vous supprimez la ressource **Machine virtuelle SQL** à partir du portail Azure, décochez la case en regard de la machine virtuelle appropriée. L’extension complète prend en charge des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées et la gestion avancée du portail. Ces fonctionnalités ne fonctionnent pas pour les machines virtuelles SQL une fois l’agent réinstallé en mode de gestion [léger](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider).

### <a name="workload-consideration"></a>Considération relative aux charges de travail

Les partages de fichiers Premium fournissent une capacité d’IOPS et de débit qui répond aux besoins de nombreuses charges de travail. Cependant, pour les charges de travail gourmandes en E/S, il peut être préférable d’utiliser une [instance de cluster de basculement SQL Server avec des espaces de stockage direct](virtual-machines-windows-portal-sql-create-failover-cluster.md) basée sur des disques Premium managés ou des disques Ultra.  

Contrôlez l’activité d’IOPS de votre environnement actuel et vérifiez que les fichiers Premium fournissent les IOPS nécessaires avant de lancer un déploiement ou une migration. Utilisez les compteurs de disque de l’Analyseur de performances Windows et analysez le nombre total d’IOPS (Transferts disque/s) et le débit (Octets disque/s) requis pour les fichiers de données, de journal et TempDB SQL Server. De nombreuses charges de travail ont des E/S en rafales. Il est donc judicieux de vérifier et de noter le nombre maximal d’IOPS et le nombre moyen d’IOPS pendant les périodes d’utilisation intensive. Les partages de fichiers Premium fournissent des IOPS en fonction de la taille du partage. Les fichiers Premium fournissent également une fonctionnalité de rafale qui vous permet de tripler la taille de la base de référence pendant une heure. 

Pour plus d’informations sur les performances des partages de fichiers Premium, consultez [Niveaux de performances de partage de fichiers](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers). 

### <a name="licensing-and-pricing"></a>Licences et tarification

Sur les machines virtuelles Azure, vous pouvez acquérir une licence SQL Server à l’aide des images de machines virtuelles avec paiement à l’utilisation (PAYG) ou BYOL (apportez votre propre licence). Le type d’image que vous choisissez affecte la façon dont vous êtes facturé.

Avec la licence PAYG, une instance de cluster de basculement (FCI) de SQL Server sur des machines virtuelles Azure entraîne des frais pour tous les nœuds de FCI, y compris les nœuds passifs. Pour plus d’informations, consultez [Tarification des machines virtuelles SQL Server Entreprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Les clients avec un Contrat Entreprise et la Software Assurance ont le droit d’utiliser un nœud FCI passif gratuit pour chaque nœud actif. Pour tirer parti de cet avantage dans Azure, utilisez des images de machines virtuelles BYOL, puis utilisez la même licence sur les nœuds actifs et passifs de l’instance de cluster de basculement. Pour plus d’informations, consultez [Accord Entreprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pour comparer les licences PAYG et BYOL pour SQL Server sur des machines virtuelles Azure, consultez [Bien démarrer avec des machines virtuelles SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Pour plus d’informations sur les licences SQL Server, consultez [Tarification](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="limitations"></a>Limites

- Filestream n’est pas pris en charge pour un cluster de basculement avec un partage de fichiers Premium. Pour utiliser Filestream, déployez votre cluster en utilisant des [espaces de stockage direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 

## <a name="prerequisites"></a>Prérequis 

Avant de suivre les instructions de cet article, vérifiez que vous disposez déjà des éléments suivants :

- Un abonnement Microsoft Azure.
- Un domaine Windows sur des machines virtuelles Azure.
- Un compte autorisé à créer des objets sur la machine virtuelle Azure.
- Un réseau virtuel et un sous-réseau Azure avec suffisamment d’espace d’adressage IP pour les composants suivants :
   - Les deux machines virtuelles.
   - L’adresse IP du cluster de basculement.
   - Une adresse IP pour chaque instance de cluster de basculement.
- Un DNS configuré sur le réseau Azure, pointant vers les contrôleurs de domaine.
- Un [partage de fichiers Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) basé sur le quota de stockage de votre base de données pour vos fichiers de données. 
- Un partage de fichiers pour les sauvegardes qui est différent du partage de fichiers Premium utilisé pour vos fichiers de données. Ce partage de fichiers peut être Standard ou Premium. 

Une fois ces conditions préalables en place, vous pouvez passer à la création de votre cluster de basculement. La première étape consiste à créer les machines virtuelles.

## <a name="step-1-create-virtual-machines"></a>Étape 1 : Créer des machines virtuelles

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre abonnement.

1. [Créez un groupe à haute disponibilité Azure](../tutorial-availability-sets.md).

   Le groupe à haute disponibilité regroupe les machines virtuelles entre les domaines d’erreur et les domaines de mise à jour. Le groupe à haute disponibilité garantit que votre application n’est pas affectée par les points de défaillance uniques, comme le commutateur réseau ou l’unité d’alimentation d’un rack de serveurs.

   Si vous n’avez pas créé le groupe de ressources pour vos machines virtuelles, faites-le lorsque vous créez un groupe à haute disponibilité Azure. Si vous utilisez le portail Azure pour créer le groupe à haute disponibilité, procédez comme suit :

   - Dans le portail Azure, cliquez sur **+** pour ouvrir Azure Marketplace. Recherchez **Groupe à haute disponibilité**.
   - Cliquez sur **Groupe à haute disponibilité**.
   - Cliquez sur **Créer**.
   - Dans le panneau **Créer un groupe à haute disponibilité**, définissez les valeurs suivantes :
      - **Nom** : Nom du groupe à haute disponibilité.
      - **Abonnement**: Votre abonnement Azure.
      - **Groupe de ressources** : Si vous souhaitez utiliser un groupe existant, cliquez sur **Utiliser l’existant** et sélectionnez le groupe dans la liste déroulante. Sinon, choisissez **Créer** et entrez un nom pour le groupe.
      - **Emplacement** : Définissez l’emplacement où vous souhaitez créer vos machines virtuelles.
      - **Domaines d’erreur** : Utilisez la valeur par défaut (3).
      - **Domaines de mise à jour** : Utilisez la valeur par défaut (5).
   - Cliquez sur **Créer** pour créer le groupe à haute disponibilité.

1. Créez les machines virtuelles dans le groupe à haute disponibilité sélectionné.

   Configurez deux machines virtuelles SQL Server dans le groupe à haute disponibilité Azure. Pour obtenir des instructions, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Placez les deux machines virtuelles :

   - Dans le même groupe de ressources Azure que celui où se trouve le groupe à haute disponibilité.
   - Sur le même réseau que votre contrôleur de domaine.
   - Sur un sous-réseau avec un espace d’adressage IP suffisant pour les deux machines virtuelles et toutes les instances de cluster de basculement que vous pourriez utiliser sur ce cluster.
   - Dans le groupe à haute disponibilité Azure.   

      >[!IMPORTANT]
      >Vous ne pouvez pas définir ni modifier un groupe à haute disponibilité après la création d’une machine virtuelle.

   Choisissez une image dans Azure Marketplace. Vous pouvez utiliser une image Marketplace qui inclut Windows Server et SQL Server, ou uniquement Windows Server. Pour plus d’informations, consultez [Présentation de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Les images SQL Server officielles dans la galerie Azure incluent une instance SQL Server installée, ainsi que le logiciel d’installation SQL Server et la clé requise.

   >[!IMPORTANT]
   > Après avoir créé la machine virtuelle, supprimez l’instance SQL Server autonome préinstallée. Vous utiliserez le support SQL Server préinstallé pour créer l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et le partage de fichiers Premium en tant que stockage. 

   Vous pouvez également utiliser des images Azure Marketplace avec le système d’exploitation seulement. Choisissez une image **Windows Server 2016 Datacenter** et installez l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et le partage de fichiers Premium en tant que stockage. Cette image ne contient aucun support d’installation SQL Server. Placez le support d’installation dans un emplacement où vous pouvez exécuter l’installation de SQL Server pour chaque serveur. 

1. Une fois que vos machines virtuelles ont été créées par Azure, connectez-vous à chaque machine virtuelle en utilisant le protocole RDP.

   Lors de votre première connexion à une machine virtuelle avec le protocole RDP, l’ordinateur vous demande si vous souhaitez autoriser que cet ordinateur soit détecté sur le réseau. Cliquez sur **Oui**.

1. Si vous utilisez l’une des images de machine virtuelle basée sur SQL Server, supprimez l’instance SQL Server.

   - Dans **Programmes et fonctionnalités**, cliquez avec le bouton droit sur **Microsoft SQL Server 201_ (64 bits)** et sur **Désinstaller/Modifier**.
   - Cliquez sur **Supprimer**.
   - Sélectionnez l’instance par défaut.
   - Supprimer toutes les fonctionnalités sous **Services Moteur de base de données**. Ne supprimez pas les **Fonctionnalités partagées**. Consultez l’illustration suivante :

      ![Supprimer des fonctionnalités](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Cliquez sur **Suivant**, puis sur **Supprimer**.

1. <a name="ports"></a>Ouvrez les ports du pare-feu.

   Sur chaque machine virtuelle, ouvrez les ports suivants du pare-feu Windows.

   | Objectif | Port TCP | Notes
   | ------ | ------ | ------
   | SQL Server | 1433 | Port normal pour les instances par défaut de SQL Server. Si vous avez utilisé une image de la galerie, ce port s’ouvre automatiquement.
   | Sonde d’intégrité | 59999 | Tout port TCP ouvert. Dans une étape ultérieure, configurez la [sonde d’intégrité](#probe) de l’équilibrage de charge et le cluster pour qu’ils utilisent ce port.   
   | Partage de fichiers | 445 | Port utilisé par le service de partage de fichiers. 

1. [Ajoutez les machines virtuelles à votre domaine préexistant](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Une fois les machines virtuelles créées et configurées, vous pouvez configurer le partage de fichiers Premium.

## <a name="step-2-mount-premium-file-share"></a>Étape 2 : Monter le partage de fichiers Premium

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte de stockage.
1. Accédez à **Partages de fichiers** sous **Service de fichiers** et sélectionnez le partage de fichiers Premium que vous souhaitez utiliser pour votre stockage SQL. 
1. Sélectionnez **Connecter** pour afficher la chaîne de connexion de votre partage de fichiers. 
1. Sélectionnez la lettre de lecteur que vous souhaitez utiliser dans la liste déroulante, puis copiez les deux blocs de code dans un bloc-notes.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Copiez les deux commandes PowerShell à partir du portail de connexion au partage de fichiers":::

1. Établissez une connexion RDP à la machine virtuelle SQL Server à l’aide du compte que votre instance de cluster de basculement SQL Server utilisera pour le compte de service. 
1. Lancez une console de commande PowerShell d’administration. 
1. Exécutez les commandes que vous avez enregistrées précédemment à partir du portail. 
1. Accédez au partage avec l’Explorateur de fichiers ou la boîte de dialogue **Exécuter** (touche Windows + r) en utilisant le chemin réseau `\\storageaccountname.file.core.windows.net\filesharename`. Exemple : `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Créez au moins un dossier sur le partage de fichiers auquel vous venez de vous connecter afin d’y placer vos fichiers de données SQL. 
1. Répétez ces étapes sur chaque machine virtuelle SQL Server qui fera partie du cluster. 

  > [!IMPORTANT]
  > Envisagez d’utiliser un partage de fichiers distinct pour les fichiers de sauvegarde pour préserver la capacité d’IOPS et de taille de ce partage pour les fichiers de données et les fichiers journaux. Vous pouvez utiliser un partage de fichiers Premium ou Standard pour les fichiers de sauvegarde.

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Étape 3 : Configurer un cluster de basculement avec un partage de fichiers 

L’étape suivante consiste à configurer le cluster de basculement. Dans cette étape, vous allez exécuter les sous-étapes suivantes :

1. Ajouter la fonctionnalité Windows de Clustering de basculement
1. Valider le cluster
1. Créer le cluster de basculement
1. Créer le témoin cloud


### <a name="add-windows-failover-clustering-feature"></a>Ajouter la fonctionnalité Windows de Clustering de basculement

1. Pour commencer, connectez-vous à la première machine virtuelle avec RDP à l’aide d’un compte de domaine qui est membre du groupe Administrateurs locaux et qui dispose des autorisations pour créer des objets dans Active Directory. Utilisez ce compte pour le reste de la configuration.

1. [Ajoutez la fonctionnalité de Clustering de basculement à chaque machine virtuelle](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Pour installer la fonctionnalité de Clustering de basculement à partir de l’interface utilisateur, exécutez les étapes suivantes sur les deux machines virtuelles.
   - Dans le **Gestionnaire de serveur**, cliquez sur **Gérer**, puis sur **Ajouter des rôles et fonctionnalités**.
   - Dans **l’Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant** jusqu’à ce que vous atteigniez la page **Sélectionner les fonctionnalités**.
   - Dans **Sélectionner les fonctionnalités**, cliquez sur **Clustering de basculement**. Incluez toutes les fonctionnalités et les outils de gestion requis. Cliquez sur **Ajouter des fonctionnalités**.
   - Cliquez sur **Suivant**, puis sur **Terminer** pour installer les fonctionnalités.

   Pour installer la fonctionnalité Clustering de basculement avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Valider le cluster

Ce guide fait référence aux instructions sous [Valider le cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Validez le cluster dans l’interface utilisateur ou avec PowerShell.

Pour valider le cluster avec l’interface utilisateur, effectuez les étapes suivantes à partir d’une des machines virtuelles.

1. Dans le **Gestionnaire de serveur**, cliquez sur **Outils**, puis cliquez sur **Gestionnaire du cluster de basculement**.
1. Dans le **Gestionnaire du cluster de basculement**, cliquez sur **Action**, puis cliquez sur **Valider la configuration...** .
1. Cliquez sur **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, choisissez **Exécuter uniquement les tests que je sélectionne**. Cliquez sur **Suivant**.
1. Sous **Sélection du test**, incluez tous les tests sauf **Stockage** et **Espaces de stockage direct**. Consultez l’illustration suivante :

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Tests de validation du cluster":::

1. Cliquez sur **Suivant**.
1. Sous **Confirmation**, cliquez sur **Suivant**.

**L’Assistant Validation d’une configuration** exécute les tests de validation.

Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Après avoir validé le cluster, créez le cluster de basculement.

### <a name="create-the-failover-cluster"></a>Créer le cluster de basculement


Pour créer le cluster de basculement, vous avez besoin des éléments suivants :
- Les noms des machines virtuelles qui deviennent les nœuds du cluster.
- Un nom pour le cluster de basculement.
- Une adresse IP pour le cluster de basculement. Vous pouvez spécifier une adresse IP qui n’est pas utilisée sur le même réseau virtuel et sous-réseau Azure que les nœuds du cluster.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

Le script PowerShell suivant crée un cluster de basculement pour **Windows Server 2012-2016**. Mettez à jour le script avec les noms des nœuds (les noms des machines virtuelles) et une adresse IP disponible à partir du réseau virtuel Azure :

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Le script PowerShell suivant crée un cluster de basculement pour Windows Server 2019.  Pour plus d’informations, passez en revue le blog [Failover Cluster: Cluster network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Mettez à jour le script avec les noms des nœuds (les noms des machines virtuelles) et une adresse IP disponible à partir du réseau virtuel Azure :

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Créer un témoin cloud

Un témoin cloud est un nouveau type de témoin de quorum de cluster stocké dans un Azure Storage Blob. Il n’est donc pas nécessaire de disposer d’une machine virtuelle distincte qui héberge un partage de fichiers témoin.

1. [Créez un témoin cloud pour le cluster de basculement](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Créez un conteneur d’objets blob.

1. Enregistrez les clés d’accès et l’URL du conteneur.

1. Configurez le témoin de quorum du cluster de basculement. Consultez la section [Configurer le témoin de quorum dans l’interface utilisateur](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) dans l’interface utilisateur.


## <a name="step-4-test-cluster-failover"></a>Étape 4 : Tester le basculement de cluster

Testez le basculement de votre cluster. Dans le Gestionnaire du cluster de basculement, cliquez avec le bouton droit sur votre cluster > **Autres actions** > **Déplacer une ressource de cluster principale** > **Sélectionner le nœud** et sélectionnez l’autre nœud du cluster. Déplacez la ressource de cluster principale vers chaque nœud du cluster, puis replacez-la sur le nœud principal. Si vous parvenez à déplacer le cluster vers chaque nœud, vous êtes prêt à installer SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testez le basculement du cluster en déplaçant la ressource principale sur les autres nœuds":::

## <a name="step-5-create-sql-server-fci"></a>Étape 5 : Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Connectez-vous à la première machine virtuelle avec RDP.

1. Dans le **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressources principales du cluster se trouvent sur la première machine virtuelle. Si nécessaire, déplacez toutes les ressources vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. Cliquez sur **Setup**.

1. Dans le **Centre d’installation SQL Server**, cliquez sur **Installation**.

1. Cliquez sur **Installation d’un nouveau cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer l’instance de cluster de basculement SQL Server.

   Les répertoires de données de l’instance de cluster de basculement doivent se trouver sur le partage de fichiers Premium. Tapez le chemin complet du partage, au format `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Un avertissement s’affiche, vous informant que vous avez spécifié un serveur de fichiers comme répertoire de données. Ceci est normal. Vérifiez que le compte avec lequel vous avez conservé le partage de fichiers est le même que celui utilisé par le service SQL Server afin d’éviter toute défaillance. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Utilisez le partage de fichiers en tant que répertoires de données SQL":::

1. Une fois l’Assistant terminé, le programme d’installation installe une instance de cluster de basculement SQL Server sur le premier nœud.

1. Une fois que le programme d’installation a correctement installé l’instance de cluster de basculement sur le premier nœud, connectez-vous au second nœud avec RDP.

1. Ouvrez le **Centre d’installation SQL Server**. Cliquez sur **Installation**.

1. Cliquez sur **Ajouter un nœud à un cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer SQL Server et ajouter ce serveur à l’instance de cluster de basculement.

   >[!NOTE]
   >Si vous avez utilisé une image de la galerie Azure Marketplace avec SQL Server, les outils SQL Server ont été inclus avec l’image. Si vous n’avez pas utilisé cette image, installez les outils SQL Server séparément. Consultez [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Étape 6 : Créer un équilibreur de charge Azure

Sur les machines virtuelles Azure, les clusters utilisent un équilibrage de charge pour conserver une adresse IP qui doit se trouver sur un nœud de cluster à la fois. Dans cette solution, l’équilibrage de charge contient l’adresse IP de l’instance de cluster de basculement SQL Server.

[Créez et configurez un équilibrage de charge Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Créer l’équilibrage de charge dans le portail Azure

Pour créer l’équilibrage de charge :

1. Dans le portail Azure, accédez au groupe de ressources contenant les machines virtuelles.

1. Cliquez sur **+ Ajouter**. Recherchez **Équilibrage de charge** dans Azure Marketplace. Cliquez sur **Équilibrage de charge**.

1. Cliquez sur **Créer**.

1. Configurez l’équilibrage de charge avec :

   - **Abonnement**: Votre abonnement Azure.
   - **Groupe de ressources** : Utilisez le même groupe de ressources que celui de vos machines virtuelles.
   - **Nom** : Nom qui identifie l’équilibreur de charge.
   - **Région** : Utilisez le même emplacement Azure que celui de vos machines virtuelles.
   - **Type** : L’équilibreur de charge peut être public ou privé. Un équilibrage de charge privé est accessible à partir du même réseau virtuel. La plupart des applications Azure peut utiliser un équilibrage de charge privé. Si votre application doit accéder à SQL Server directement via Internet, utilisez un équilibrage de charge public.
   - **SKU** : La référence SKU de votre équilibreur de charge doit être standard. 
   - **Réseau virtuel** : Le même réseau que les machines virtuelles.
   - **Affectation d'adresses IP** : L’attribution d’adresses IP doit être statique. 
   - **Adresse IP privée** : La même adresse IP que celle attribuée à la ressource réseau de cluster FCI SQL Server.
   Consultez l’illustration suivante :

   ![CréerÉquilibrageCharge](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurer le pool principal de l’équilibrage de charge

1. Revenez au groupe de ressources Azure contenant les machines virtuelles et recherchez le nouvel équilibrage de charge. Vous devrez peut-être actualiser l’affichage du groupe de ressources. Cliquez sur l’équilibreur de charge.

1. Cliquez sur **Pools principaux**, puis sur **+ Ajouter** pour ajouter un pool principal.

1. Associez le pool principal au groupe à haute disponibilité contenant les machines virtuelles.

1. Sous **Configurations IP du réseau cible**, cochez **MACHINE VIRTUELLE** et choisissez les machines virtuelles qui participent en tant que nœuds de cluster. Veillez à inclure toutes les machines virtuelles qui hébergeront l’infrastructure ICF. 

1. Cliquez sur **OK** pour créer le pool principal.

### <a name="configure-a-load-balancer-health-probe"></a>Configurer une sonde d’intégrité d’équilibrage de charge

1. Dans le panneau de l’équilibrage de charge, cliquez sur **Sondes d’intégrité**.

1. Cliquez sur **+ Ajouter**.

1. Dans le panneau **Ajouter une sonde d’intégrité**, <a name="probe"></a>définissez les paramètres de la sonde d’intégrité :

   - **Nom** : Nom de la sonde d’intégrité.
   - **Protocole** : TCP.
   - **Port** : Définissez le port que vous avez créé dans le pare-feu pour la sonde d’intégrité dans [cette étape](#ports). Dans cet article, l’exemple utilise le port TCP `59999`.
   - **Intervalle** : 5 secondes.
   - **Seuil de défaillance sur le plan de l’intégrité** : 2 défaillances consécutives.

1. Cliquez sur OK.

### <a name="set-load-balancing-rules"></a>Définir les règles d’équilibrage de charge

1. Dans le panneau de l’équilibreur de charge, cliquez sur **Règles d’équilibrage de charge**.

1. Cliquez sur **+ Ajouter**.

1. Configurez les paramètres des règles d’équilibrage de charge :

   - **Nom** : Nom des règles d’équilibrage de charge.
   - **Adresse IP du serveur frontal** : Utilisez l’adresse IP de la ressource réseau de cluster FCI SQL Server.
   - **Port** : Défini pour le port TCP FCI SQL Server. Le port d’instance par défaut est 1433.
   - **Port principal** : Cette valeur utilise le même port que la valeur **Port** lorsque vous activez **Adresse IP flottante (retour direct du serveur)** .
   - **Pool principal** : Utilisez le nom du pool back-end que vous avez configuré précédemment.
   - **Sonde d’intégrité** : Utilisez la sonde d’intégrité que vous avez configurée précédemment.
   - **Persistance de session** : Aucune.
   - **Délai d’inactivité (minutes)**  : 4.
   - **Adresse IP flottante (retour direct du serveur)**  : activé

1. Cliquez sur **OK**.

## <a name="step-7-configure-cluster-for-probe"></a>Étape 7 : Configurer le cluster pour la sonde

Définissez le paramètre de port de sonde de cluster dans PowerShell.

Pour définir le paramètre de port de sonde de cluster, mettez à jour les variables dans le script suivant avec des valeurs à partir de votre environnement. Supprimez les crochets pointus `<>` du script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Dans le script précédent, définissez les valeurs pour votre environnement. La liste ci-dessous décrit les valeurs :

   - `<Cluster Network Name>`: Nom du cluster de basculement Windows Server pour le réseau. Dans le **Gestionnaire du cluster de basculement** > **Réseaux**, cliquez avec le bouton droit sur le réseau et cliquez sur **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**. 

   - `<SQL Server FCI IP Address Resource Name>`: Nom de la ressource d’adresse IP de l’instance de cluster de basculement SQL Server. Dans **Gestionnaire du cluster de basculement** > **Rôles**, sous le rôle de l’instance de cluster de basculement SQL Server, sous **Nom du serveur**, cliquez avec le bouton droit sur la ressource d’adresse IP, puis cliquez sur **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**. 

   - `<ILBIP>`: Adresse IP de l’équilibreur de charge interne. Cette adresse est configurée dans le portail Azure en tant qu’adresse frontale d’équilibrage de charge interne. Il s’agit également de l’adresse IP de l’instance de cluster de basculement SQL Server. Vous pouvez la trouver dans le **Gestionnaire du cluster de basculement** sur la page de propriétés où se trouve également localisé le `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Port de sonde que vous avez configuré dans la sonde d’intégrité de l’équilibreur de charge. N’importe quel port TCP inutilisé est valide. 

>[!IMPORTANT]
>Le masque de sous-réseau pour le paramètre de cluster doit être l’adresse de diffusion TCP IP : `255.255.255.255`.

Après avoir défini la sonde du cluster, vous pouvez voir tous les paramètres de cluster dans PowerShell. Exécutez le script qui suit :

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Étape 8 : Tester le basculement de l’instance de cluster de basculement

Testez le basculement de l’instance de cluster de basculement pour valider le fonctionnement du cluster. Effectuez également les étapes suivantes :

1. Connectez-vous à l’un des nœuds de cluster FCI SQL Server avec RDP.

1. Ouvrez le **Gestionnaire du cluster de basculement**. Cliquez sur **Rôles**. Notez le nœud qui possède le rôle de l’instance de cluster de basculement SQL Server.

1. Cliquez avec le bouton droit sur le rôle de l’instance de cluster de basculement SQL Server.

1. Cliquez sur **Déplacer** et sur **Meilleur nœud possible**.

Le **Gestionnaire du cluster de basculement** présente le rôle et ses ressources hors connexion. Ensuite, les ressources sont déplacées et mises en ligne sur l’autre nœud.

### <a name="test-connectivity"></a>Tester la connectivité

Pour tester la connectivité, connectez-vous à une autre machine virtuelle sur le même réseau virtuel. Ouvrez **SQL Server Management Studio** et connectez-vous au nom FCI SQL Server.

>[!NOTE]
>Si nécessaire, vous pouvez [télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limites

La solution Machines virtuelles Azure prend en charge Microsoft Distributed Transaction Coordinator (MSDTC) sur Windows Server 2019, avec un stockage sur les volumes partagés en cluster (CSV) et un [équilibreur de charge standard](../../../load-balancer/load-balancer-standard-overview.md).

Concernant les machines virtuelles Azure, MSDTC n’est pas pris en charge sur Windows Server 2016 ou versions antérieures, car :

- La ressource MSDTC en cluster n’est pas configurable pour utiliser le stockage partagé. Avec Windows Server 2016, si vous créez une ressource MSDTC, celle-ci n’affiche pas le stockage partagé qui est disponible pour l’utilisation, et cela même si le stockage est présent. Ce problème a été résolu dans Windows Server 2019.
- L’équilibreur de charge de base ne gère pas les ports RPC.

## <a name="see-also"></a>Voir aussi

- [Technologies de cluster Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instances de cluster de basculement SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).
