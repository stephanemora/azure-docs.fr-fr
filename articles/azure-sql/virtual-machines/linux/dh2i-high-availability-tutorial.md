---
title: Configurer le groupe de disponibilité Always On avec DH2i DxEnterprise s’exécutant sur des machines virtuelles Azure basées sur Linux
description: Utilisez DH2i DxEnterprise en tant que gestionnaire de cluster afin d’atteindre une haute disponibilité pour un groupe de disponibilité lié à des machines virtuelles Azure SQL Server sur Linux
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 07752eb5c7f18a8952c43e77afed78b06432aca6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568531"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Tutoriel - Configurer un groupe de disponibilité Always On à trois nœuds avec DH2i DxEnterprise s’exécutant sur des machines virtuelles Azure basées sur Linux

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ce tutoriel explique comment configurer un groupe de disponibilité Always On SQL Server avec DH2i DxEnterprise s’exécutant sur des machines virtuelles Azure basées sur Linux. 

Pour plus d’informations sur DxEnterprise, consultez [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft assure le support lié au déplacement des données, au groupe de disponibilité et aux composants SQL Server. Contactez DH2i pour tout support lié à la documentation du cluster DH2i DxEnterprise, dans le cadre de la gestion du cluster et du quorum.
 

Il se compose des étapes suivantes :

> [!div class="checklist"]
> * Installer SQL Server sur toutes les machines virtuelles Azure qui feront partie du groupe de disponibilité.
> * Installer DxEnterprise sur toutes les machines virtuelles, puis configurer le cluster DxEnterprise.
> * Créer les hôtes virtuels pour assurer la prise en charge du basculement et de la haute disponibilité, puis ajouter un groupe de disponibilité et une base de données à ce dernier.
> * Créer l’équilibreur de charge Azure interne pour l’écouteur de groupe de disponibilité (facultatif).
> * Effectuer un basculement manuel ou automatique.

Dans ce tutoriel, nous allons configurer un cluster DxEnterprise à l’aide de l’[IU du client DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Éventuellement, vous pouvez également configurer le cluster à l’aide de l’interface de ligne de commande [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/). Pour cet exemple, nous avons utilisé quatre machines virtuelles. Trois de ces machines virtuelles exécutent Ubuntu 18.04 et font partie du cluster à trois nœuds. La quatrième machine virtuelle exécute Windows 10 avec l’outil DxAdmin pour gérer et configurer le cluster.

## <a name="prerequisites"></a>Prérequis

- Créez quatre machines virtuelles dans Azure. Suivez l’article [Démarrage rapide : Créer une machine virtuelle Linux dans le portail Azure](../../../virtual-machines/linux/quick-create-portal.md) pour créer des machines virtuelles basées sur Linux. De même, pour créer la machine virtuelle basée sur Windows, suivez l’article [Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure](../../../virtual-machines/windows/quick-create-portal.md).
- Installez .NET 3.1 sur toutes les machines virtuelles Linux qui vont faire partie du cluster. Suivez les instructions documentées [ici](/dotnet/core/install/linux) en fonction du système d’exploitation Linux que vous choisissez.
- Vous devez disposer obligatoirement d’une licence DxEnterprise valide avec activation des fonctionnalités de gestion des groupes de disponibilité. Pour plus d’informations sur l’obtention d’un essai gratuit, consultez la page relative à l’[essai gratuit de DxEnterprise](https://dh2i.com/trial/).

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Installer SQL Server sur toutes les machines virtuelles Azure qui feront partie du groupe de disponibilité

Dans ce tutoriel, nous configurons un cluster Linux à trois nœuds qui exécute le groupe de disponibilité. Suivez la documentation relative à l’[installation de SQL Server sur Linux](/sql/linux/sql-server-linux-overview#install) en fonction du choix de votre plateforme Linux. Nous vous recommandons également d’installer les [outils SQL Server](/sql/linux/sql-server-linux-setup-tools) pour ce tutoriel.
 
> [!NOTE]
> Vérifiez que l’OS Linux que vous choisissez est une distribution usuelle prise en charge à la fois par [DH2i DxEnterprise (consultez la section Configuration requise minimale)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) et [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> Dans cet exemple, nous utilisons Ubuntu 18.04, qui est pris en charge à la fois par DH2i DxEnterprise et Microsoft SQL Server.

Pour ce tutoriel, nous n’allons pas installer SQL Server sur la machine virtuelle Windows, car ce nœud ne fera pas partie du cluster. Il est utilisé uniquement pour gérer le cluster à l’aide de DxAdmin.

Une fois cette étape effectuée, SQL Server et les [outils SQL Server](/sql/linux/sql-server-linux-setup-tools) (éventuellement) sont installés sur les trois machines virtuelles Linux qui participent au groupe de disponibilité.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Installer DxEnterprise sur toutes les machines virtuelles et configurer le cluster

Dans cette étape, nous allons installer DH2i DxEnterprise pour Linux sur les trois machines virtuelles Linux. Le tableau suivant décrit le rôle de chaque serveur dans le cluster :

| Nombre d'ordinateurs virtuels | Rôle DH2i DxEnterprise | Rôle de réplica du groupe de disponibilité Microsoft SQL Server |
|--|--|--|
| 1 | Nœud de cluster - Linux | Principal |
| 1 | Nœud de cluster - Linux | Secondaire - Validation synchrone |
| 1 | Nœud de cluster - Linux | Secondaire - Validation synchrone |
| 1 | Client DxAdmin | N/D |


Pour installer DxEnterprise sur les trois nœuds Linux, suivez la documentation de DH2i DxEnterprise en fonction du système d’exploitation Linux que vous choisissez. Installez DxEnterprise en utilisant l’une des méthodes listées ci-dessous.

- **Ubuntu**
    - [Guide de démarrage rapide pour l’installation à partir d’un dépôt](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Guide de démarrage rapide de l’extension](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guide de démarrage rapide des images de la Place de marché](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Guide de démarrage rapide pour l’installation à partir d’un dépôt](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Guide de démarrage rapide de l’extension](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guide de démarrage rapide des images de la Place de marché](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Pour installer uniquement l’outil client DxAdmin sur la machine virtuelle Windows, suivez le [Guide de démarrage rapide de l’IU du client DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Après cette étape, le cluster DxEnterprise est créé sur les machines virtuelles Linux, et le client DxAdmin installé sur la machine cliente Windows. 

> [!NOTE]
> Vous pouvez également créer un cluster à trois nœuds dans lequel l’un des nœuds est ajouté en *mode de configuration uniquement*, comme décrit [ici](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes), pour activer le basculement automatique. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Créer les hôtes virtuels pour assurer la prise en charge du basculement et la haute disponibilité

Dans cette étape, nous allons créer un hôte virtuel et un groupe de disponibilité, puis nous allons ajouter une base de données, le tout à l’aide de l’IU de DxAdmin.   

> [!NOTE]
> Au cours de cette étape, les instances de SQL Server vont redémarrer pour permettre l’activation de la fonctionnalité Always On. 

Connectez-vous à la machine cliente Windows exécutant DxAdmin pour permettre la connexion au cluster créé à l’étape ci-dessus. Suivez les étapes décrites dans [Groupes de disponibilité MSSQL avec DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) pour activer Always On et créer l’hôte virtuel ainsi que le groupe de disponibilité. 

> [!TIP]
> Avant d’ajouter les bases de données, vérifiez que la base de données est créée et sauvegardée sur l’instance principale de SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Créer l’équilibreur de charge Azure interne pour l’écouteur (facultatif)

Dans cette étape facultative, vous pouvez créer et configurer l’équilibreur de charge Azure qui contient les adresses IP des écouteurs de groupe de disponibilité. Pour plus d’informations sur Azure Load Balancer, consultez [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Pour configurer l’équilibreur de charge Azure et l’écouteur de groupe de disponibilité avec DxAdmin, suivez le [Guide de démarrage rapide pour Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/) de DxEnterprise.

Après cette étape, un écouteur de groupe de disponibilité est créé et mappé à l’équilibreur de charge Azure interne.

## <a name="test-manual-or-automatic-failover"></a>Tester le basculement manuel ou automatique

Pour le test de basculement automatique, vous pouvez arrêter le réplica principal (mettre la machine virtuelle hors tension à partir du portail Azure). Cela va entraîner la réplication de l’indisponibilité soudaine du nœud principal. Le comportement attendu est le suivant :
- Le gestionnaire de cluster promeut l’un des réplicas secondaires du groupe de disponibilité en réplica principal.
- Le réplica principal à l’arrêt rejoint automatiquement le cluster après sa sauvegarde. Le gestionnaire de cluster le promeut en réplica secondaire.

 
Vous pouvez également effectuer un basculement manuel en suivant les étapes mentionnées ci-dessous :

1. Connectez-vous au cluster via DxAdmin   
1. Développez l’hôte virtuel pour le groupe de disponibilité
1. Cliquez avec le bouton droit sur le nœud cible/réplica secondaire, puis sélectionnez **Start Hosting on Member** (Démarrer l’hébergement sur le membre) pour lancer le basculement 

Pour plus d’informations sur d’autres opérations dans DxEnterprise, accédez au [Guide de l’administrateur DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) et au [Guide des commandes DxCLI pour DxEnterprise](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [groupes de disponibilité sur Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Démarrage rapide : Créer une machine virtuelle Linux dans le portail Azure](../../../virtual-machines/linux/quick-create-portal.md)
- [Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure](../../../virtual-machines/windows/quick-create-portal.md)
- [Plateformes prises en charge pour SQL Server 2019 sur Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)