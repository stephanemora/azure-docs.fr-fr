---
title: Instance de cluster de basculement (FCI) SQL Server - Machines virtuelles Azure | Microsoft Docs
description: Cet article explique comment créer une instance de cluster de basculement SQL Server sur des machines virtuelles Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 1a69741ba3ced91b6b0d1fc4bcd4aea887452151
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792186"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurer une instance de cluster de basculement SQL Server sur des machines virtuelles Azure

Cet article explique comment créer une instance de cluster de basculement (FCI) SQL Server sur des machines virtuelles Azure dans le modèle Azure Resource Manager. Cette solution utilise [l’édition Espaces de stockage direct de Windows Server 2016 Datacenter](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) en tant que réseau SAN virtuel basé sur logiciel qui synchronise le stockage (disques de données) entre les nœuds (machines virtuelles Azure) dans un cluster Windows. La technologie Espaces de stockage direct est une nouveauté de Windows Server 2016.

Le schéma suivant illustre la solution complète sur les machines virtuelles Azure :

![La solution complète](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Ce schéma montre :

- Deux machines virtuelles Azure dans un cluster de basculement Windows Server. Lorsqu’une machine virtuelle se trouve dans un cluster de basculement, elle est également désignée comme *nœud* ou *nœud de cluster*.
- Chaque machine virtuelle possède au moins deux disques de données.
- La technologie Espaces de stockage direct synchronise les données sur le disque de données et présente le stockage synchronisé en tant que pool de stockage.
- Le pool de stockage présente un volume partagé de cluster (CSV) au cluster de basculement.
- Le rôle du cluster de l’instance de cluster de basculement SQL Server utilise le volume partagé de cluster pour les lecteurs de données.
- Un équilibrage de charge Azure pour contenir l’adresse IP de l’instance de cluster de basculement SQL Server.
- Un groupe à haute disponibilité Azure contient toutes les ressources.

>[!NOTE]
>Toutes les ressources Azure dans le schéma se trouvent dans le même groupe de ressources.

Pour plus d’informations sur les Espaces de stockage direct, consultez [l’édition Espaces de stockage direct S2D de Windows Server 2016 Datacenter](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

La technologie Espaces de stockage direct prend en charge deux types d’architectures : convergée et hyper-convergée. L’architecture dans ce document est hyper-convergée. Une infrastructure hyper-convergée place le stockage sur les mêmes serveurs que ceux qui hébergent l’application en cluster. Dans cette architecture, le stockage se trouve sur chaque nœud de l’instance de cluster de basculement SQL Server.

## <a name="licensing-and-pricing"></a>Licences et tarification

Sur les machines virtuelles Azure, vous pouvez acquérir une licence SQL Server à l’aide des images de machines virtuelles avec paiement à l’utilisation (PAYG) ou BYOL (apportez votre propre licence). Le type d’image que vous choisissez affecte la façon dont vous êtes facturé.

Avec la licence de paiement à l’utilisation, une instance de cluster de basculement (FCI) de SQL Server sur des machines virtuelles Azure entraîne des frais pour tous les nœuds de FCI, y compris les nœuds passifs. Pour plus d’informations, consultez [Tarification des machines virtuelles SQL Server Entreprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Si vous disposez d’un Contrat Entreprise et de la Software Assurance, vous pouvez utiliser un nœud FCI passif gratuit pour chaque nœud actif. Pour tirer parti de cet avantage dans Azure, utilisez des images de machines virtuelles BYOL, puis utilisez la même licence sur les nœuds actifs et passifs de l’instance de cluster de basculement. Pour plus d’informations, consultez [Accord Entreprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pour comparer les licences de paiement à l’utilisation et BYOL pour SQL Server sur des machines virtuelles Azure, consultez [Bien démarrer avec des machines virtuelles SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Pour plus d’informations sur les licences SQL Server, consultez [Tarification](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemple de modèle Azure

Vous pouvez créer la solution complète dans Azure à partir d’un modèle. Un exemple de modèle est disponible dans les [Modèles de démarrage rapide Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) sur GitHub. Cet exemple n’est pas conçu ni testé pour une charge de travail spécifique. Vous pouvez exécuter le modèle pour créer une instance de cluster de basculement SQL Server avec le stockage Espaces de stockage direct connecté à votre domaine. Vous pouvez évaluer le modèle et le modifier selon vos besoins.

## <a name="before-you-begin"></a>Avant de commencer

Il y a quelques éléments que vous devez connaître et avoir à disposition avant de commencer.

### <a name="what-to-know"></a>Éléments à connaître
Vous devez avoir une compréhension opérationnelle des technologies suivantes :

- [Technologies de cluster Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instances de cluster de basculement SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Un point important à connaître est que sur un cluster de basculement invité de machine virtuelle IaaS Azure, nous vous recommandons d’utiliser une seule carte réseau par serveur (nœud de cluster) et un seul sous-réseau. Les réseaux Azure intègrent une redondance physique, ce qui rend inutiles les cartes réseau et les sous-réseaux supplémentaires sur un cluster invité de machine virtuelle IaaS Azure. Le rapport de validation du cluster vous avertit que les nœuds sont accessibles uniquement sur un seul réseau. Vous pouvez ignorer cet avertissement sur les clusters de basculement invités de machines virtuelles Azure IaaS.

Vous devez également avoir une compréhension générale de ces technologies :

- [Solutions hyper-convergées utilisant les Espaces de stockage direct dans Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Groupes de ressources Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Pour le moment, les instances de cluster de basculement SQL Server sur des machines virtuelles Azure sont prises en charge uniquement avec le [mode de gestion léger](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) de l’[extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Pour passer du mode d’extension complet au mode d’extension léger, supprimez la ressource **Machine virtuelle SQL** pour les machines virtuelles correspondantes, puis inscrivez-les auprès du fournisseur de ressources de machine virtuelle SQL en mode léger. Quand vous supprimez la ressource **Machine virtuelle SQL** à partir du portail Azure, **décochez la case en regard de la machine virtuelle appropriée**. L’extension complète prend en charge des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées et la gestion avancée du portail. Ces fonctionnalités n’opèrent pas pour les machines virtuelles SQL une fois l’agent réinstallé en mode de gestion léger.

### <a name="what-to-have"></a>Éléments à mettre en place

Avant d’effectuer les étapes décrites dans cet article, vous devez déjà disposer des éléments suivants :

- Un abonnement Microsoft Azure.
- Un domaine Windows sur des machines virtuelles Azure.
- Un compte qui dispose des autorisations nécessaires pour créer des objets sur les machines virtuelles Azure et dans Active Directory.
- Un réseau virtuel Azure et un sous-réseau avec suffisamment d’espace d’adressage IP pour ces composants :
   - Les deux machines virtuelles.
   - L’adresse IP du cluster de basculement.
   - Une adresse IP pour chaque instance de cluster de basculement.
- Un DNS configuré sur le réseau Azure, pointant vers les contrôleurs de domaine.

Une fois ces conditions préalables en place, vous pouvez commencer la création de votre cluster de basculement. La première étape consiste à créer les machines virtuelles.

## <a name="step-1-create-the-virtual-machines"></a>Étape 1 : Créer les machines virtuelles

1. Connectez-vous au [Portail Azure](https://portal.azure.com) avec votre abonnement.

1. [Créez un groupe à haute disponibilité Azure](../tutorial-availability-sets.md).

   Le groupe à haute disponibilité regroupe les machines virtuelles entre les domaines d’erreur et les domaines de mise à jour. Cela garantit que votre application n’est pas affectée par les points de défaillance uniques, comme le commutateur réseau ou l’unité d’alimentation d’un rack de serveurs.

   Si vous n’avez pas créé le groupe de ressources pour vos machines virtuelles, faites-le lorsque vous créez un groupe à haute disponibilité Azure. Si vous utilisez le Portail Azure pour créer le groupe à haute disponibilité, procédez comme suit :

   1. Dans le Portail Azure, sélectionnez **Créer une ressource** pour ouvrir la Place de marché Azure. Recherchez **Groupe à haute disponibilité**.
   1. Sélectionnez **Groupe à haute disponibilité**.
   1. Sélectionnez **Create** (Créer).
   1. Sous **Créer un groupe à haute disponibilité**, indiquez les valeurs suivantes :
      - **Nom** : Nom du groupe à haute disponibilité.
      - **Abonnement**: Votre abonnement Azure.
      - **Groupe de ressources** : Si vous souhaitez utiliser un groupe existant, cliquez sur **Sélectionner un groupe existant** et sélectionnez le groupe dans la liste. Sinon, sélectionnez **Créer** et entrez un nom pour le groupe.
      - **Emplacement** : Définissez l’emplacement où vous souhaitez créer vos machines virtuelles.
      - **Domaines d’erreur** : Utilisez la valeur par défaut (**3**).
      - **Domaines de mise à jour** : Utilisez la valeur par défaut (**5**).
   1. Sélectionnez **Créer** pour créer le groupe à haute disponibilité.

1. Créez les machines virtuelles dans le groupe à haute disponibilité sélectionné.

   Configurez deux machines virtuelles SQL Server dans le groupe à haute disponibilité Azure. Pour obtenir des instructions, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Placez les deux machines virtuelles :

   - Dans le même groupe de ressources Azure que celui où se trouve le groupe à haute disponibilité.
   - Sur le même réseau que votre contrôleur de domaine.
   - Sur un sous-réseau avec un espace d’adressage IP suffisant pour les deux machines virtuelles et toutes les instances de cluster de basculement que vous pourriez utiliser sur le cluster.
   - Dans le groupe à haute disponibilité Azure.

      >[!IMPORTANT]
      >Vous ne pouvez pas définir ou modifier le groupe à haute disponibilité après avoir créé une machine virtuelle.

   Choisissez une image dans la Place de marché Azure. Vous pouvez utiliser une image Place de marché Azure qui inclut Windows Server et SQL Server, ou uniquement Windows Server. Pour plus d’informations, consultez [Présentation de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Les images SQL Server officielles dans la galerie Azure incluent une instance SQL Server installée, le logiciel d’installation SQL Server et la clé requise.

   Choisissez l’image appropriée en fonction de la façon dont vous souhaitez payer pour la licence SQL Server :

   - **Licence avec paiement à l’utilisation**. Le coût par seconde de ces images inclut l’attribution de licences SQL Server :
      - **SQL Server 2016 Enterprise sur Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard sur Windows Server 2016 Datacenter**
      - **SQL Server 2016 Developer sur Windows Server 2016 Datacenter**

   - **BYOL (apportez votre propre licence)**

      - **(BYOL) SQL Server 2016 Enterprise sur Windows Server 2016 Datacenter**
      - **(BYOL) SQL Server 2016 Standard sur Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Après avoir créé la machine virtuelle, supprimez l’instance SQL Server autonome préinstallée. Vous utiliserez le support SQL Server préinstallé pour créer l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et les Espaces de stockage direct.

   Vous pouvez également utiliser des images de la Place de marché Azure qui contiennent le système d’exploitation uniquement. Choisissez une image **Windows Server 2016 Datacenter** et installez l’instance de cluster de basculement SQL Server après avoir configuré le cluster de basculement et les Espaces de stockage direct. Cette image ne contient aucun support d’installation SQL Server. Placez le support d’installation de SQL Server dans un emplacement où vous pouvez exécuter l’installation pour chaque serveur.

1. Une fois que vos machines virtuelles ont été créées par Azure, connectez-vous à chacune en utilisant le protocole RDP.

   Lors de votre première connexion à une machine virtuelle avec le protocole RDP, une invite vous demande si vous souhaitez autoriser que cet ordinateur soit détecté sur le réseau. Sélectionnez **Oui**.

1. Si vous utilisez l’une des images de machine virtuelle basée sur SQL Server, supprimez l’instance SQL Server.

   1. Dans **Programmes et fonctionnalités**, cliquez avec le bouton droit sur **Microsoft SQL Server 2016 (64 bits)** et sélectionnez **Désinstaller/Modifier**.
   1. Sélectionnez **Supprimer**.
   1. Sélectionnez l’instance par défaut.
   1. Supprimer toutes les fonctionnalités sous **Services Moteur de base de données**. Ne supprimez pas les **Fonctionnalités partagées**. Vous devez voir quelque chose de similaire à la capture d’écran suivante :

      ![Sélectionner les fonctionnalités](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Sélectionnez **Suivant**, puis **Supprimer**.

1. <a name="ports"></a>Ouvrez les ports du pare-feu.

   Sur chaque machine virtuelle, ouvrez les ports suivants du pare-feu Windows :

   | Objectif | Port TCP | Notes
   | ------ | ------ | ------
   | SQL Server | 1433 | Port normal pour les instances par défaut de SQL Server. Si vous avez utilisé une image de la galerie, ce port s’ouvre automatiquement.
   | Sonde d’intégrité | 59999 | Tout port TCP ouvert. Dans une étape ultérieure, configurez la [sonde d’intégrité](#probe) de l’équilibrage de charge et le cluster pour qu’ils utilisent ce port.  

1. Ajoutez du stockage à la machine virtuelle. Pour plus d’informations, consultez [Ajouter du stockage](../disks-types.md).

   Les deux machines virtuelles ont besoin d’au moins deux disques de données.

   Attachez des disques bruts, et non des disques au format NTFS.
      >[!NOTE]
      >Si vous attachez des disques au format NTFS, vous pouvez uniquement activer la technologie Espaces de stockage direct sans vérification d’éligibilité de disque.  

   Attachez au moins deux disques SSD premium à chaque machine virtuelle. Nous vous recommandons d’utiliser au minimum des disques P30 (1 To).

   Définissez la mise en cache de l’hôte sur **Lecture seule**.

   La capacité de stockage que vous utilisez dans des environnements de production dépend de votre charge de travail. Les valeurs décrites dans cet article sont à des fins de démonstration et de test.

1. [Ajoutez les machines virtuelles à votre domaine préexistant](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Une fois les machines virtuelles créées et configurées, vous pouvez configurer le cluster de basculement.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Étape 2 : Configurer le cluster de basculement Windows Server avec la technologie Espaces de stockage direct

L’étape suivante consiste à configurer le cluster de basculement avec la technologie Espaces de stockage direct. Au cours de cette étape, vous allez effectuer les sous-étapes suivantes :

1. Ajouter la fonctionnalité de clustering de basculement Windows Server.
1. Valider le cluster.
1. Créer le cluster de basculement.
1. Créer le témoin cloud.
1. Ajouter du stockage.

### <a name="add-windows-server-failover-clustering"></a>Ajouter le clustering de basculement Windows Server

1. Connectez-vous à la première machine virtuelle avec RDP à l’aide d’un compte de domaine qui est membre du groupe Administrateurs locaux et qui dispose des autorisations pour créer des objets dans Active Directory. Utilisez ce compte pour le reste de la configuration.

1. [Ajoutez le clustering de basculement à chaque machine virtuelle](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Pour installer le clustering de basculement à partir de l’interface utilisateur, exécutez les étapes suivantes sur les deux machines virtuelles :
   1. Dans le **Gestionnaire de serveur**, sélectionnez **Gérer**, puis **Ajouter des rôles et fonctionnalités**.
   1. Dans **l’Assistant Ajout de rôles et de fonctionnalités**, sélectionnez **Suivant** jusqu’à ce que vous atteigniez la page **Sélectionner les fonctionnalités**.
   1. Dans **Sélectionner les fonctionnalités**, sélectionnez **Clustering de basculement**. Incluez toutes les fonctionnalités et les outils de gestion requis. Sélectionnez **Ajouter des fonctionnalités**.
   1. Sélectionnez **Suivant**, puis **Terminer** pour installer les fonctionnalités.

   Pour installer le clustering de basculement avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles :

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Pour plus d’informations concernant les étapes suivantes, consultez les instructions sous l’Étape 3 de [Solution hyper-convergée utilisant les Espaces de stockage direct dans Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Valider le cluster

Validez le cluster dans l’interface utilisateur ou avec PowerShell.

Pour valider le cluster à l’aide de l’interface utilisateur, procédez comme suit sur l’une des machines virtuelles :

1. Sous **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Gestionnaire du cluster de basculement**.
1. Sous **Gestionnaire du cluster de basculement**, sélectionnez **Action**, puis **Valider la configuration**.
1. Sélectionnez **Suivant**.
1. Sous **Sélectionner des serveurs ou un cluster**, entrez le nom des deux machines virtuelles.
1. Sous **Options de test**, sélectionnez **Exécuter uniquement les tests que je sélectionne**. Sélectionnez **Suivant**.
1. Sous **Sélection des tests**, sélectionnez tous les tests à l’exception de **Stockage**, comme illustré ici :

   ![Sélectionner les tests de validation du cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Sélectionnez **Suivant**.
1. Sous **Confirmation**, sélectionnez **Suivant**.

L’Assistant Validation d’une configuration exécute les tests de validation.

Pour valider le cluster avec PowerShell, exécutez le script suivant à partir d’une session PowerShell d’administrateur sur l’une des machines virtuelles :

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Après avoir validé le cluster, créez le cluster de basculement.

### <a name="create-the-failover-cluster"></a>Créer le cluster de basculement

Pour créer le cluster de basculement, vous avez besoin des éléments suivants :
- Les noms des machines virtuelles qui deviennent les nœuds du cluster.
- Un nom pour le cluster de basculement.
- Une adresse IP pour le cluster de basculement. Vous pouvez spécifier une adresse IP qui n’est pas utilisée sur le même réseau virtuel et sous-réseau Azure que les nœuds du cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 à Windows Server 2016

Le script PowerShell suivant crée un cluster de basculement pour Windows Server 2008 à Windows Server 2016. Mettez à jour le script avec les noms des nœuds (les noms des machines virtuelles) et une adresse IP disponible à partir du réseau virtuel Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Le script PowerShell suivant crée un cluster de basculement pour Windows Server 2019. Pour plus d’informations, consultez [Cluster de basculement : Objet réseau en cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Mettez à jour le script avec les noms des nœuds (les noms des machines virtuelles) et une adresse IP disponible à partir du réseau virtuel Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Créer un témoin cloud

Un témoin cloud est un nouveau type de témoin de quorum de cluster stocké dans un Storage Blob Azure. Il n’est donc pas nécessaire de disposer d’une machine virtuelle distincte qui héberge un partage de fichiers témoin.

1. [Créez un témoin cloud pour le cluster de basculement](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Créez un conteneur d’objets blob.

1. Enregistrez les clés d’accès et l’URL du conteneur.

1. Configurez le témoin de quorum du cluster de basculement. Consultez la section [Configurer le témoin de quorum dans l’interface utilisateur](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Ajouter du stockage

Les disques Espaces de stockage direct doivent être vides. Ils ne peuvent pas contenir de partitions ou d’autres données. Pour nettoyer les disques, suivez [les étapes décrites dans ce guide](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Activez les Espaces de stockage direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Le script PowerShell suivant active les Espaces de stockage direct :  

   ```powershell
   Enable-ClusterS2D
   ```

   Dans le **Gestionnaire du cluster de basculement**, vous pouvez maintenant voir le pool de stockage.

1. [Créez un volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   La technologie Espaces de stockage direct crée automatiquement un pool de stockage lorsque vous l’activez. Vous êtes maintenant prêt à créer un volume. La cmdlet PowerShell `New-Volume` automatise le processus de création du volume. Ce processus comprend la mise en forme, l’ajout du volume au cluster et la création d’un volume partagé de cluster (CSV). Cet exemple crée un volume partagé de cluster de 800 gigaoctets :

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Une fois cette commande terminée, un volume de 800 Go est monté en tant que ressource de cluster. Le volume se trouve sous `C:\ClusterStorage\Volume1\`.

   Cette capture d’écran montre un volume partagé de cluster avec Espaces de stockage direct :

   ![Volume partagé de cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Étape 3 : Tester le basculement du cluster de basculement

Dans le **Gestionnaire du cluster de basculement**, vérifiez que vous pouvez déplacer la ressource de stockage vers l’autre nœud du cluster. Si vous pouvez vous connecter au cluster de basculement en utilisant le **Gestionnaire du cluster de basculement** et déplacer le stockage d’un nœud à l’autre, vous êtes prêt à configurer l’instance de cluster de basculement.

## <a name="step-4-create-the-sql-server-fci"></a>Étape 4 : Créer l’instance de cluster de basculement SQL Server

Après avoir configuré le cluster de basculement et tous les composants du cluster, notamment le stockage, vous pouvez créer l’instance de cluster de basculement SQL Server.

1. Connectez-vous à la première machine virtuelle avec RDP.

1. Dans le **Gestionnaire du cluster de basculement**, vérifiez que toutes les ressources principales du cluster se trouvent sur la première machine virtuelle. Si nécessaire, déplacez toutes les ressources vers cette machine virtuelle.

1. Recherchez le support d’installation. Si la machine virtuelle utilise l’une des images Azure Marketplace, le support se situe sous `C:\SQLServer_<version number>_Full`. Sélectionnez **Configuration**.

1. Dans le **Centre d’installation SQL Server**, sélectionnez **Installation**.

1. Sélectionnez **Installation d’un nouveau cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer l’instance de cluster de basculement SQL Server.

   Les répertoires de données de l’instance de cluster de basculement doivent se trouver sur le stockage en cluster. Avec la technologie Espaces de stockage direct, il ne s’agit pas d’un disque partagé, mais d’un point de montage vers un volume sur chaque serveur. La technologie Espaces de stockage direct synchronise le volume entre les deux nœuds. Le volume est présenté au cluster en tant que volume partagé de cluster. Utilisez le point de montage du volume partagé de cluster pour les répertoires de données.

   ![Répertoires de données](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Une fois que vous avez terminé les instructions de l’assistant, le programme d’installation installe une instance de cluster de basculement SQL Server sur le premier nœud.

1. Une fois que le programme d’installation a installé l’instance de cluster de basculement sur le premier nœud, connectez-vous au second nœud avec RDP.

1. Ouvrez le **Centre d’installation SQL Server**. Sélectionnez **Installation**.

1. Sélectionnez **Ajouter un nœud à un cluster de basculement SQL Server**. Suivez les instructions de l’Assistant pour installer SQL Server et ajouter le serveur à l’instance de cluster de basculement.

   >[!NOTE]
   >Si vous avez utilisé une image de la galerie de la Place de marché Azure avec SQL Server, les outils SQL Server ont été inclus avec l’image. Si vous n’avez pas utilisé une de ces images, installez les outils de SQL Server séparément. Consultez [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Étape 5 : Créer l’équilibreur de charge Azure

Sur les machines virtuelles Azure, les clusters utilisent un équilibrage de charge pour conserver une adresse IP qui doit se trouver sur un nœud de cluster à la fois. Dans cette solution, l’équilibrage de charge contient l’adresse IP de l’instance de cluster de basculement SQL Server.

Pour plus d’informations, consultez [Créer et configurer un équilibrage de charge Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Créer l’équilibrage de charge dans le portail Azure

Pour créer l’équilibrage de charge :

1. Dans le Portail Azure, accédez au groupe de ressources contenant les machines virtuelles.

1. Sélectionnez **Ajouter**. Recherchez **Équilibreur de charge** dans la Place de marché Azure. Sélectionnez **Équilibreur de charge**.

1. Sélectionnez **Create** (Créer).

1. Configurez l’équilibrage de charge avec :

   - **Abonnement**: Votre abonnement Azure.
   - **Groupe de ressources** : Le groupe de ressources qui contient vos machines virtuelles.
   - **Nom** : Nom qui identifie l’équilibreur de charge.
   - **Région** : L’emplacement Azure qui contient vos machines virtuelles.
   - **Type** : Public ou privé. Un équilibrage de charge privé est accessible à partir du réseau virtuel. La plupart des applications Azure peut utiliser un équilibrage de charge privé. Si votre application doit accéder à SQL Server directement via Internet, utilisez un équilibrage de charge public.
   - **SKU** : Standard.
   - **Réseau virtuel** : Le même réseau que les machines virtuelles.
   - **Affectation d'adresses IP** : Statique. 
   - **Adresse IP privée** : L’adresse IP attribuée à la ressource réseau de cluster FCI SQL Server.

 La capture d’écran suivante montre l’interface utilisateur **Créer un équilibreur de charge** :

   ![Configurer l’équilibreur de charge](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurer le pool principal de l’équilibrage de charge

1. Revenez au groupe de ressources Azure contenant les machines virtuelles et recherchez le nouvel équilibrage de charge. Vous devrez peut-être actualiser l’affichage du groupe de ressources. Sélectionnez l’équilibreur de charge.

1. Sélectionnez **Pools principaux**, puis **Ajouter**.

1. Associez le pool principal au groupe à haute disponibilité contenant les machines virtuelles.

1. Sous **Configurations IP du réseau cible**, sélectionnez **MACHINE VIRTUELLE** et choisissez les machines virtuelles qui participent en tant que nœuds de cluster. Veillez à inclure toutes les machines virtuelles qui hébergeront l’infrastructure ICF.

1. Sélectionnez **OK** pour créer le pool principal.

### <a name="configure-a-load-balancer-health-probe"></a>Configurer une sonde d’intégrité d’équilibrage de charge

1. Dans le panneau de l’équilibrage de charge, sélectionnez **Sondes d’intégrité**.

1. Sélectionnez **Ajouter**.

1. Dans le panneau **Ajouter une sonde d’intégrité**, <a name="probe"></a>définissez les paramètres de la sonde d’intégrité :

   - **Nom** : Nom de la sonde d’intégrité.
   - **Protocole** : TCP.
   - **Port** : Définissez le port que vous avez créé dans le pare-feu pour la sonde d’intégrité dans [cette étape](#ports). Dans cet article, l’exemple utilise le port TCP `59999`.
   - **Intervalle** : 5 secondes.
   - **Seuil de défaillance sur le plan de l’intégrité** : 2 défaillances consécutives.

1. Sélectionnez **OK**.

### <a name="set-load-balancing-rules"></a>Définir les règles d’équilibrage de charge

1. Dans le panneau de l’équilibrage de charge, sélectionnez **Règles d’équilibrage de charge**.

1. Sélectionnez **Ajouter**.

1. Configurez les paramètres de règles d’équilibrage de charge :

   - **Nom** : Nom des règles d’équilibrage de charge.
   - **Adresse IP du serveur frontal** : L’adresse IP de la ressource réseau de cluster FCI SQL Server.
   - **Port** : Le port TCP FCI SQL Server. Le port d’instance par défaut est 1433.
   - **Port principal** : Utilise le même port que la valeur **Port** lorsque vous activez **Adresse IP flottante (retour direct du serveur)** .
   - **Pool principal** : Le nom du pool principal que vous avez configuré précédemment.
   - **Sonde d’intégrité** : La sonde d’intégrité que vous avez configurée précédemment.
   - **Persistance de session** : Aucune.
   - **Délai d’inactivité (minutes)**  : 4.
   - **Adresse IP flottante (retour direct du serveur)**  : activé.

1. Sélectionnez **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Étape 6 : Configurer le cluster pour la sonde

Définissez le paramètre de port de sonde de cluster dans PowerShell.

Pour définir le paramètre de port de sonde de cluster, mettez à jour les variables dans le script suivant avec des valeurs à partir de votre environnement. Supprimez les crochets pointus (`<` et `>`) du script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

La liste ci-dessous décrit les valeurs que vous devez mettre à jour :

   - `<Cluster Network Name>`: Nom du cluster de basculement Windows Server pour le réseau. Dans le **Gestionnaire du cluster de basculement** > **Réseaux**, cliquez avec le bouton droit sur le réseau et sélectionnez **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**.

   - `<SQL Server FCI IP Address Resource Name>`: Nom de la ressource d’adresse IP de l’instance de cluster de basculement SQL Server. Dans **Gestionnaire du cluster de basculement** > **Rôles**, sous le rôle de l’instance de cluster de basculement SQL Server, sous **Nom du serveur**, cliquez avec le bouton droit sur la ressource d’adresse IP, puis sélectionnez **Propriétés**. La valeur correcte est sous **Nom** dans l’onglet **Général**. 

   - `<ILBIP>`: Adresse IP de l’équilibreur de charge interne. Cette adresse est configurée dans le portail Azure en tant qu’adresse frontale d’équilibrage de charge interne. Il s’agit également de l’adresse IP de l’instance de cluster de basculement SQL Server. Vous pouvez la trouver dans le **Gestionnaire du cluster de basculement** sur la page de propriétés où se trouve également localisé le `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Port de sonde que vous avez configuré dans la sonde d’intégrité de l’équilibreur de charge. N’importe quel port TCP inutilisé est valide.

>[!IMPORTANT]
>Le masque de sous-réseau pour le paramètre de cluster doit être l’adresse de diffusion TCP IP : `255.255.255.255`.

Après avoir défini la sonde du cluster, vous pouvez voir tous les paramètres de cluster dans PowerShell. Exécutez ce script :

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Étape 7 : Tester le basculement de l’instance de cluster de basculement

Testez le basculement de l’instance de cluster de basculement pour valider le fonctionnement du cluster. Procédez comme suit :

1. Connectez-vous à l’un des nœuds de cluster FCI SQL Server avec RDP.

1. Ouvrez le **Gestionnaire du cluster de basculement**. Sélectionnez **Rôles**. Notez le nœud qui possède le rôle de l’instance de cluster de basculement SQL Server.

1. Cliquez avec le bouton droit sur le rôle de l’instance de cluster de basculement SQL Server.

1. Sélectionnez **Déplacer**, puis sélectionnez **Meilleur nœud possible**.

Le **Gestionnaire du cluster de basculement** présente le rôle et ses ressources hors connexion. Ensuite, les ressources sont déplacées et mises en ligne sur l’autre nœud.

### <a name="test-connectivity"></a>Tester la connectivité

Pour tester la connectivité, connectez-vous à une autre machine virtuelle sur le même réseau virtuel. Ouvrez **SQL Server Management Studio** et connectez-vous au nom FCI SQL Server.

>[!NOTE]
>Si nécessaire, vous pouvez [télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limites

La solution Machines virtuelles Azure prend en charge Microsoft Distributed Transaction Coordinator (MSDTC) sur Windows Server 2019, avec un stockage sur les volumes partagés en cluster (CSV) et un [équilibreur de charge standard](../../../load-balancer/load-balancer-standard-overview.md).

Concernant les machines virtuelles Azure, MSDTC n’est pas pris en charge sur Windows Server 2016 ou versions antérieures, car :

- La ressource MSDTC en cluster n’est pas configurable pour utiliser le stockage partagé. Sur Windows Server 2016, si vous créez une ressource MSDTC, celle-ci n’affiche pas le stockage partagé qui est disponible pour l’utilisation, et cela même si le stockage est disponible. Ce problème a été résolu dans Windows Server 2019.
- L’équilibreur de charge de base ne gère pas les ports RPC.

## <a name="see-also"></a>Voir aussi

[Configurer les Espaces de stockage direct à l’aide du bureau à distance (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solution hyper-convergée avec Espaces de stockage direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Vue d’ensemble des Espaces de stockage direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Prise en charge de SQL Server pour les Espaces de stockage direct](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
