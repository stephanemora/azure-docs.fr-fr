---
title: Configurer le quorum du cluster
description: 'Découvrez comment configurer un témoin de disque, un témoin de cloud ou un témoin de partage de fichiers en tant que quorum pour un cluster de basculement Windows Server sur SQL Server sur des machines virtuelles Azure. '
services: virtual-machines
documentationCenter: na
author: cawrites
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: chadam
ms.openlocfilehash: b1db3b5d809c8dd80b0d03d5bdd15636115fdef0
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581940"
---
# <a name="configure-cluster-quorum-for-sql-server-on-azure-vms"></a>Configurer le quorum de cluster pour SQL Server sur des machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article explique comment configurer l’une des trois options de quorum pour un cluster de basculement Windows Server s’exécutant sur SQL Server sur des machines virtuelles Azure : un témoin de disque, un témoin de cloud et un témoin de partage de fichiers.


## <a name="overview"></a>Vue d’ensemble

Le quorum d’un cluster est déterminé par le nombre d’éléments votants qui doivent faire partie de l’appartenance active au cluster pour que le cluster démarre correctement ou continue à s’exécuter. La configuration d’une ressource de quorum permet à un cluster à deux nœuds de continuer à fonctionner avec un seul nœud en ligne. Le cluster de basculement Windows Server est la technologie sous-jacente des options de haute disponibilité de SQL Server sur les machines virtuelles Azure : [instances de cluster de basculement](failover-cluster-instance-overview.md) et [groupes de disponibilité](availability-group-overview.md). 

Le témoin de disque est l’option de quorum la plus résiliente, mais pour utiliser un témoin de disque sur SQL Server sur une machine virtuelle Azure, vous devez utiliser un disque partagé Azure qui impose certaines limitations sur la solution de haute disponibilité. Par conséquent, utilisez un témoin de disque lorsque vous configurez votre instance de cluster de basculement avec des disques partagés Azure ; sinon, utilisez un témoin de cloud dans la mesure du possible. Si vous utilisez Windows Server 2012 R2 ou une version antérieure qui ne prend pas en charge le témoin de cloud, vous pouvez utiliser un témoin de partage de fichiers. 

Les options de quorum suivantes peuvent être utilisées pour SQL Server sur des machines virtuelles Azure : 

|  |[Témoin cloud](/windows-server/failover-clustering/deploy-cloud-witness) |[Témoin de disque](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[Témoin de partage de fichiers](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Systèmes d’exploitation pris en charge**| Windows Server 2016+ |Tous | Tous|

Pour en savoir plus sur le quorum, consultez [Vue d’ensemble du cluster de basculement Windows Server](hadr-windows-server-failover-cluster-overview.md). 

## <a name="cloud-witness"></a>Témoin de cloud

Un témoin de cloud est un type de témoin de quorum de cluster de basculement qui utilise le stockage Microsoft Azure pour fournir un vote sur le quorum du cluster. 


Le tableau suivant fournit des informations et des considérations supplémentaires sur le témoin de cloud : 

| Type de témoin  | Description  | Conditions requises et recommandations  |
| ---------    |---------        |---------                        |
| Témoin de cloud     |  <ul><li> Utilise le stockage Azure comme témoin de cloud, contient uniquement l’horodatage. </li><li> Idéal pour les déploiements sur plusieurs sites, dans plusieurs zones et plusieurs régions.</li> <li> Crée un conteneur connu `msft-cloud-witness` sous le compte de stockage Microsoft. </li> <li> Écrit un fichier blob unique avec l’ID unique du cluster correspondant utilisé comme nom de fichier du fichier blob sous le conteneur. </li>      |  <ul><li>La taille par défaut est de 1 Mo.</li><li> Utilisez **Usage général** comme genre de compte. Le stockage d’objets blob n’est pas pris en charge. </li><li> Utilisez le stockage Standard. Le stockage Azure Premium n’est pas pris en charge. </li><li> Le clustering de basculement utilise le fichier blob comme point d’arbitrage, ce qui nécessite des garanties de cohérence lors de la lecture des données. Par conséquent, vous devez sélectionner **Stockage localement redondant** comme type de **Réplication**.</li><li> Doit être exclu des sauvegardes et des analyses antivirus</li><li> Un témoin de disque n’est pas pris en charge avec la fonctionnalité Espaces de stockage direct</li> <li> Le témoin cloud utilise le protocole HTTPS (port 443 par défaut) pour établir la communication avec le service blob Azure. Assurez-vous que le port HTTPS est accessible via le proxy. </li>|

Lors de la configuration d’une ressource de quorum de témoin de cloud pour votre cluster de basculement, tenez compte des points suivants :
- Au lieu de stocker la clé d’accès, votre cluster de basculement génère et stocke de manière sécurisée un jeton de sécurité d’accès partagé (SAS).
- Le jeton SAS généré est valide tant que la clé d’accès demeure valide. Lors de la permutation de la clé d’accès primaire, il est important de commencer par mettre à jour le témoin de cloud (sur tous vos clusters qui utilisent ce compte de stockage) avec la clé d’accès secondaire avant de regénérer la clé d’accès primaire.
- Le témoin de cloud utilise l’interface REST HTTPS du service Compte de stockage Azure. Cela signifie que le port HTTPS doit être ouvert sur tous les nœuds du cluster.


Un témoin de cloud nécessite un compte Stockage Azure. Pour configurer un compte de stockage, effectuez ces étapes : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu hub, sélectionnez Nouveau -> Données + stockage -> Compte de stockage.
3. Dans la page Créer un compte de stockage, procédez comme suit :
    1. Entrez un nom pour votre compte de stockage. Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Le nom du compte de stockage doit être unique dans Azure.
    2. Pour **Type de compte**, sélectionnez **Usage général**.
    3. Pour **Performances**, sélectionnez **Standard**.
    2. Pour **Réplication**, sélectionnez **Stockage localement redondant (LRS)** .


Une fois votre compte de stockage créé, effectuez les étapes suivantes pour configurer votre ressource de quorum de témoin de cloud pour votre cluster de basculement : 


# <a name="powershell"></a>[PowerShell](#tab/powershell)

La commande PowerShell Set-ClusterQuorum existante a de nouveaux paramètres correspondant au témoin de cloud.

Vous pouvez configurer le témoin de cloud avec l’applet de commande [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) à l’aide de la commande PowerShell :

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey>
```

Dans le cas rare où vous devez utiliser un point de terminaison différent, utilisez cette commande PowerShell : 

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey> -Endpoint <servername>
```

Pour obtenir de l’aide pour trouver la clé d’accès du compte de stockage, consultez la [documentation du témoin de cloud](/windows-server/failover-clustering/deploy-cloud-witness). 


# <a name="failover-cluster-manager"></a>[Gestionnaire du cluster de basculement](#tab/fcm-gui)

Utilisez l’Assistant Configuration de quorum intégré au Gestionnaire du cluster de basculement pour configurer votre témoin de cloud. Pour ce faire, procédez comme suit : 

1. Ouvrez le Gestionnaire du cluster de basculement.

2. Cliquez avec le bouton droit sur le cluster -> **Plus d’actions** -> **Configurer les paramètres de quorum du cluster**. Cela lance l’Assistant Configuration de quorum du cluster.

    ![Capture d’écran du chemin du menu permettant de configurer les paramètres de quorum du cluster dans l’interface utilisateur du Gestionnaire du cluster de basculement](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-7.png)
    
3. Dans la page **Sélectionner les configurations de quorum**, sélectionnez **Sélectionner le témoin de quorum**.

    ![Capture d’écran de la case d’option « Sélectionner le témoin de quorum » dans l’Assistant Quorum du cluster](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-8.png)
   
4. Dans la page **Sélectionner le témoin de quorum**, sélectionnez **Configurer un témoin de cloud**.

    ![Capture d’écran de la case d’option pour sélectionner un témoin de cloud](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-9.png)
    
5. Dans la page **Configurer le témoin de cloud**, entrez les informations du compte Stockage Azure. Pour obtenir de l’aide sur la recherche de ces informations, consultez la [documentation du témoin de cloud](/windows-server/failover-clustering/deploy-cloud-witness). 
   1. (Paramètre obligatoire) Nom du compte Stockage Azure.
   2. (Paramètre obligatoire) Clé d’accès correspondant au compte de stockage.
       1. Lors de la création initiale, utilisez la clé d’accès primaire 
       2. Lors de la permutation de la clé d’accès primaire, utilisez la clé d’accès secondaire
   3. (Paramètre facultatif) Si vous envisagez d’utiliser un autre point de terminaison de service Azure (par exemple le service Microsoft Azure en Chine), mettez à jour le nom du serveur de point de terminaison.

    ![Capture d’écran du volet de configuration du témoin de cloud dans l’Assistant Quorum du cluster](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-10.png)
      

6. Une fois la configuration du témoin de cloud terminée, vous pouvez afficher la nouvelle ressource témoin dans le composant logiciel enfichable Gestionnaire du cluster de basculement.

    ![Configuration réussie du témoin de cloud](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-11.png)
    


---


## <a name="disk-witness"></a>Témoin de disque

Un témoin de disque est un petit disque en cluster qui se trouve dans le groupe de stockage disponible du cluster. Ce disque est hautement disponible et peut basculer d’un nœud vers un autre. 

Le témoin de disque est l’option de quorum recommandée en cas d’utilisation avec une solution de haute disponibilité de stockage partagé, telle que l’instance de cluster de basculement avec des disques partagés Azure. 

Le tableau suivant fournit des informations et des considérations supplémentaires sur le témoin de disque de quorum : 

| Type de témoin  | Description  | Conditions requises et recommandations  |
| ---------    |---------        |---------                        |
| Témoin de disque     |  <ul><li> Numéro d'unité logique dédié stockant une copie de la base de données de cluster</li><li> Particulièrement utile aux clusters dotés d'un stockage partagé (non répliqué)</li>       |  <ul><li>Le numéro d'unité logique doit avoir une taille minimale de 512 Mo</li><li> Son utilisation doit être dédiée au cluster et il ne doit pas être affecté à un rôle en cluster</li><li> Doit être inclus dans un stockage en cluster et réussir les tests de validation de stockage</li><li> Le disque ne doit pas être un volume partagé de cluster</li><li> Disque de base doté d'un seul volume</li><li> Ne nécessite pas de lettre de lecteur</li><li> Peut être formaté en NTFS ou ReFS</li><li> Peut éventuellement être configuré avec un RAID matériel à des fins de tolérance de panne</li><li> Doit être exclu des sauvegardes et des analyses antivirus</li><li> Un témoin de disque n’est pas pris en charge avec la fonctionnalité Espaces de stockage direct</li>|

Pour utiliser un disque partagé Azure pour le témoin de disque, vous devez d’abord créer le disque et le monter. Pour ce faire, suivez les étapes de la section [Monter le disque](failover-cluster-instance-azure-shared-disks-manually-configure.md#add-azure-shared-disk) du guide de l’instance de cluster de basculement de disque partagé Azure. Le disque n’a pas besoin d’être Premium. 

Une fois le disque monté, ajoutez-le au stockage du cluster en effectuant les étapes suivantes : 

1. Ouvrez le Gestionnaire du cluster de basculement. 
1. Sélectionnez **Disques** sous **Stockage** dans le volet de navigation gauche. 
1. Sélectionnez **Ajouter un disque** sous **Actions** dans le volet de navigation droit. 
1. Sélectionnez le lecteur partagé Azure que vous venez de monter et notez le nom, par exemple `Cluster Disk 3`. 

Une fois votre disque ajouté en tant que stockage en cluster, configurez-le comme témoin de disque à l’aide de PowerShell :  


La commande PowerShell Set-ClusterQuorum existante a de nouveaux paramètres correspondant au témoin de cloud.

Utilisez le chemin du partage de fichiers comme paramètre pour le témoin de disque lors de l’utilisation de l’applet de commande PowerShell [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) :

```PowerShell
Set-ClusterQuorum -NodeAndDiskMajority "Cluster Disk 3"
```

Vous pouvez également utiliser le Gestionnaire du cluster de basculement. Suivez les mêmes étapes que pour le témoin de cloud, mais choisissez le témoin de disque comme option de quorum à la place. 


## <a name="file-share-witness"></a>Témoin de partage de fichiers

Un témoin de partage de fichiers est un partage de fichiers SMB qui est généralement configuré sur un serveur de fichiers exécutant Windows Server. Il conserve les informations de clustering dans un fichier witness.log, mais ne stocke pas de copie de la base de données de clusters. Dans Azure, vous pouvez configurer un partage de fichiers sur une machine virtuelle distincte. 

Configurez un témoin de partage de fichiers si un témoin de disque ou un témoin de cloud n’est pas disponible ou n’est pas pris en charge dans votre environnement. 

Le tableau suivant fournit des informations et des considérations supplémentaires sur le témoin de partage de fichiers de quorum : 

| Type de témoin  | Description  | Conditions requises et recommandations  |
| ---------    |---------        |---------                        |
| Témoin de partage de fichiers     | <ul><li>Partage de fichiers SMB configuré sur un serveur de fichiers exécutant Windows Server</li><li> Ne stocke pas de copie de la base de données de cluster</li><li> Consigne les informations de cluster dans un fichier witness.log uniquement</li><li> Essentiellement utile pour les clusters multisites dotés d'un stockage répliqué </li>       |  <ul><li>Doit disposer au minimum de 5 Mo d'espace libre</li><li> Doit être dédié au seul cluster et ne pas être utilisé pour stocker des données d'utilisateurs ou d'applications</li><li> Doit avoir les autorisations en écriture activées pour l'objet ordinateur correspondant au nom du cluster</li></ul><br>Les autres considérations qui suivent s'appliquent à un serveur de fichiers hébergeant le témoin de partage de fichiers :<ul><li>Il est possible de configurer un seul serveur de fichiers avec des témoins de partage de fichiers pour plusieurs clusters.</li><li> Le serveur de fichiers doit se trouver sur un site distinct de la charge de travail de cluster. Chaque site de cluster dispose ainsi des mêmes chances de survie en cas de perte de communication réseau entre les sites. Si le serveur de fichiers sur trouve sur le même site, ce dernier devient le site principal et il est le seul à pouvoir accéder au partage de fichiers.</li><li> Le serveur de fichiers peut s'exécuter sur un ordinateur virtuel si celui-ci n'est pas hébergé sur le cluster qui utilise le témoin de partage de fichiers.</li><li> Pour bénéficier d'un haut niveau de disponibilité, le serveur de fichiers peut être configuré sur un cluster de basculement distinct. </li>      |

Une fois que vous avez créé votre partage de fichiers et configuré correctement les autorisations, montez le partage de fichiers sur vos nœuds en cluster. Vous pouvez suivre les mêmes étapes générales pour monter le partage de fichiers que celles décrites dans la section [Monter un partage de fichiers](failover-cluster-instance-premium-file-share-manually-configure.md) du guide pratique de l’instance de cluster de basculement à partage de fichiers Premium. 

Une fois votre partage de fichiers correctement configuré et monté, utilisez PowerShell pour ajouter le partage de fichiers en tant que ressource du témoin de quorum : 

```powershell
Set-ClusterQuorum -FileShareWitness <UNC path to file share> -Credential $(Get-Credential)
```

Vous serez invité à entrer un compte et un mot de passe pour un compte non-administrateur local (pour le partage de fichiers) disposant des droits d’administrateur complets sur le partage.  Le cluster fera en sorte que le nom et le mot de passe soient chiffrés et inaccessibles.

Vous pouvez également utiliser le Gestionnaire du cluster de basculement. Suivez les mêmes étapes que pour le témoin de cloud, mais choisissez le témoin de partage de fichiers comme option de quorum à la place. 

## <a name="change-quorum-voting"></a>Changer le vote du quorum


Il est possible de changer le vote de quorum d’un nœud participant à un cluster de basculement Windows Server. 

Lors de la modification des paramètres de vote de nœud, suivez ces instructions : 

| Instructions relatives au vote de quorum |
|-|
| Commencez avec des nœuds qui n’ont pas de vote par défaut. Chaque nœud ne doit avoir un vote qu’avec une justification explicite.|
| Activez les votes pour les nœuds de cluster qui hébergent le réplica principal d’un groupe de disponibilité, ou les propriétaires préférés d’une instance de cluster de basculement. |
| Activez les votes pour les propriétaires de basculement automatique. Chaque nœud susceptible d’héberger un réplica principal ou une instance de cluster de basculement suite à un basculement automatique doit disposer d’un vote. | 
| Si un groupe de disponibilité a plusieurs réplicas secondaires, activez uniquement les votes pour les réplicas qui ont un basculement automatique. | 
| Désactivez les votes pour les nœuds qui se trouvent dans des sites de reprise d’activité après sinistre secondaires. Les nœuds des sites secondaires ne doivent pas contribuer à la décision de mettre un cluster hors connexion s’il n’y a aucun problème au niveau du site principal. | 
| Faites en sorte d’avoir un nombre impair de votes, avec trois votes de quorum au minimum. Ajoutez un [témoin de quorum](hadr-cluster-quorum-configure-how-to.md) pour disposer d’un vote supplémentaire si nécessaire dans un cluster à deux nœuds. | 
| Réévaluez les affectations de vote après le basculement. Il n’est pas souhaitable de basculer dans une configuration de cluster qui ne prend pas en charge un quorum sain. |




## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez :

- [Paramètres HADR pour SQL Server sur des machines virtuelles Azure](hadr-cluster-best-practices.md)
- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Groupes de disponibilité Always On avec SQL Server sur les machines virtuelles Azure](availability-group-overview.md)
- [Cluster de basculement Windows Server avec SQL Server sur des machines virtuelles Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instances de cluster de basculement avec SQL Server sur des machines virtuelles Azure](failover-cluster-instance-overview.md)
- [Vue d’ensemble des instances de cluster de basculement](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
