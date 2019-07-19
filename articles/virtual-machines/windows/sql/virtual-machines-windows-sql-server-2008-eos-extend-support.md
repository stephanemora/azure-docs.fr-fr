---
title: Étendre la prise en charge pour SQL Server 2008 et SQL Server 2008 R2 avec Azure
description: Découvrez comment étendre la prise en charge de SQL Server 2008 et SQL Server 2008 R2 en procédant à la migration de votre instance SQL Server vers Azure, ou en achetant une prise en charge étendue pour conserver les instances en local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ecb7030fa3652525a36ce15d66ea6e5daf9c3296
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304219"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Étendre la prise en charge pour SQL Server 2008 et SQL Server 2008 R2 avec Azure

SQL Server 2008 et SQL Server 2008 R2 se rapprochent de la [fin de leur cycle de vie de prise en charge](https://www.microsoft.com/sql-server/sql-server-2008). Comme beaucoup de nos clients utilisent toujours les deux versions, nous fournissons plusieurs options qui permettent de continuer à recevoir la prise en charge. Vous pouvez migrer vos instances SQL Server locales vers les machines virtuelles (VM) Azure, migrer vers Azure SQL Database ou demeurer en local et acheter des mises à jour de sécurité étendues.

À la différence d’une instance managée, la migration vers une machine virtuelle Azure ne nécessite pas de faire certifier de nouveau vos applications. Et contrairement à la solution de rester en local, vous recevez des correctifs de sécurité étendus gratuits en migrant vers une machine virtuelle Azure.

Le reste de cet article présente des points à prendre en considération pour la migration de votre instance SQL Server vers une machine virtuelle Azure.

## <a name="provisioning"></a>Approvisionnement

Il existe une image `SQL Server 2008 R2 on Windows Server 2008 R2` avec paiement à l’utilisation, disponible sur la Place de marché Azure.

Les clients qui sont sur SQL Server 2008 devront procéder à une installation automatique, ou à une mise à niveau vers SQL Server 2008 R2. De même, les clients sur Windows Server 2008 devront déployer leurs machines virtuelles à partir d’un disque dur virtuel personnalisé, ou mettre à niveau vers Windows Server 2008 R2.

Les images déployées par le biais de la Place de marché sont fournies avec l’extension IaaS SQL préinstallée. L’extension IaaS SQL est obligatoire pour la gestion des licences flexibles et la mise à jour corrective automatisée. Les clients qui déploient des machines virtuelles installées automatiquement doivent installer manuellement l’extension IaaS SQL. L’extension IaaS SQL n’est pas prise en charge sur Windows 2008.

  > [!NOTE]
  > Bien que les panneaux `Create` et `Manage` de SQL Server fonctionne avec l’image de SQL Server 2008 R2 dans le portail Azure, les fonctionnalités suivantes _ne sont pas prises en charge_ : les sauvegardes automatiques, l’intégration d’Azure Key Vault, R Services et la configuration du stockage.

## <a name="licensing"></a>Gestion des licences
Les déploiements de SQL Server 2008 R2 avec paiement à l’utilisation peuvent être convertis en [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Pour convertir une licence Software Assurance (SA) en paiement à l’utilisation, les clients doivent s’inscrire auprès du [fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider) de machines virtuelles SQL. Une fois inscrit auprès de ce fournisseur de ressources, les types de licences SQL (AHB et paiement à l’utilisation) sont interchangeables.

Les instances SQL Server 2008 ou SQL Server 2008 R2 installées automatiquement sur une machine virtuelle Azure peuvent être inscrites auprès du fournisseur de ressources SQL et convertir leur type de licence pour un paiement à l’utilisation.

## <a name="migration"></a>Migration
Vous pouvez migrer des instances SQL Server en fin de support vers une machine virtuelle Azure à l’aide de méthodes de sauvegarde et de restauration manuelles ; il s’agit là du procédé de migration le plus courant pour passer du système local à une machine virtuelle Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pour les migrations en bloc, nous recommandons le service [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Avec Azure Site Recovery, les clients peuvent répliquer la machine virtuelle entière, y compris SQL Server, d’un système local vers une machine virtuelle Azure.

SQL Server nécessite des instantanés de cohérence d’application Azure Site Recovery pour garantir la récupération ; Azure Site Recovery prend en charge ces instantanés avec un intervalle minimum d’une heure. Le RPO minimum possible pour SQL Server avec les migrations Azure Site Recovery est d’une heure, et le RTO de deux heures, auxquelles il faut ajouter le temps de récupération de SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) représente une alternative pour les clients s’ils effectuent la migration, d’un système local vers une machine virtuelle Azure, en mettant à niveau SQL Server vers SQL Server 2012 ou une version supérieure.

## <a name="disaster-recovery"></a>Récupération d'urgence

Les solutions de récupération d’urgence pour SQL Server en fin de support sur une machine virtuelle Azure sont les suivantes :

- **Sauvegardes SQL Server** : utilisez la sauvegarde Azure pour protéger votre instance SQL Server en fin de support contre les ransomwares, la suppression accidentelle et les données endommagées. La solution est actuellement en préversion pour SQL Server en fin de support ; elle prend en charge SQL Server 2008 et 2008 R2 s’exécutant sur Windows 2008 R2 SP1. Pour plus d’informations, consultez cet [article](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2).
- **Copie des journaux de transaction** : Vous pouvez créer un réplica de la copie des journaux de transaction dans une autre zone ou région Azure, avec des restaurations continues pour réduire le RTO. Les clients doivent configurer manuellement la copie des journaux de transaction.
- **Azure Site Recovery** : Vous pouvez répliquer votre machine virtuelle entre les zones et les régions par l’intermédiaire de la réplication d’Azure Site Recovery. Les captures instantanées de cohérence d’application sont indispensables à SQL Server pour garantir la récupération en cas de sinistre. Azure Site Recovery offre un RPO d’une heure minimum, et un RTO de deux heures + temps de récupération SQL Server, pour SQL Server DR en fin de support.

## <a name="security-patching"></a>Correctifs de sécurité
Les mises à jour de sécurité étendues pour les machines virtuelles SQL Server seront distribuées via les canaux de Microsoft Update une fois la machine virtuelle SQL Server inscrite auprès du [fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider) SQL. Les correctifs peuvent être téléchargés manuellement ou automatiquement.

**Automated patching** est activée par défaut. La mise à jour corrective automatisée permet à Azure de corriger automatiquement SQL Server et le système d’exploitation. Vous pouvez spécifier un jour de la semaine, une heure et une durée pour la fenêtre de maintenance si l’extension IaaS SQL est installée. Azure effectue la mise à jour corrective dans cette fenêtre de maintenance. La planification de la fenêtre de maintenance utilise les paramètres régionaux de la machine virtuelle pour l’heure.  Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Étapes suivantes

Migrer votre machine virtuelle SQL Server vers Azure

* [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)

Prise en main de SQL Server sur des machines virtuelles Azure :

* [Créer une machine virtuelle SQL Server dans le portail Azure](quickstart-sql-vm-create-portal.md)

Obtenir des réponses aux questions fréquemment posées sur les machines virtuelles SQL :

* [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md)
