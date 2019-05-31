---
title: Étendre la prise en charge de SQL Server 2008 et SQL Server 2008 R2 avec Azure
description: Découvrez comment étendre la prise en charge de SQL Server 2008 et SQL Server 2008 R2 en migration de votre instance de SQL Server vers Azure, ou en achetant une prise en charge étendue pour conserver les instances en local.
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
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243970"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Étendre la prise en charge de SQL Server 2008 et SQL Server 2008 R2 avec Azure

SQL Server 2008 et SQL Server 2008 R2 sont approchent la [fin de leur cycle de vie de prise en charge (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Étant donné que la plupart de nos clients utilisent toujours les deux versions, nous mettons à disposition plusieurs options pour continuer à recevoir la prise en charge. Vous pouvez migrer vos instances de SQL Server sur site vers les machines virtuelles (VM) Azure, migrer vers Azure SQL Database, ou restent sur site et acheter des mises à jour de sécurité.

Contrairement avec une instance gérée, migration vers une machine virtuelle Azure ne nécessite pas recertifying vos applications. Et contrairement à avec rester en local, vous recevrez des correctifs de sécurité étendus gratuit en migrant vers une machine virtuelle Azure. 

Le reste de cet article fournit des considérations pour la migration de votre instance de SQL Server vers une machine virtuelle Azure. 

## <a name="provisioning"></a>Approvisionnement 

Il existe un paiement à l’utilisation `SQL Server 2008 R2 on Windows Server 2008 R2` image disponible sur la place de marché Azure. 

Les clients qui se trouvent sur SQL Server 2008 soit devront installer ou mettre à niveau vers SQL Server 2008 R2. De même, les clients sur Windows Server 2008 soit doivent déployer leurs machines virtuelles à partir d’un disque dur virtuel personnalisé, ou mettre à niveau vers Windows Server 2008 R2. 

Images déployées via la place de marché sont fournis avec l’extension SQL IaaS préinstallée. L’extension IaaS de SQL est obligatoire pour les licences flexibles et de mise à jour corrective automatisée. Les clients qui déploient des machines virtuelles installées automatiquement devrez installer manuellement l’extension IaaS de SQL. L’extension IaaS de SQL n’est pas pris en charge sur Windows 2008. 

  > [!NOTE]
  > Pendant que le serveur SQL `Create` et `Manage` panneaux fonctionne avec l’image de 2008 R2 de SQL Server dans le portail Azure, les fonctionnalités suivantes sont _ne pas pris en charge_: Sauvegardes automatiques, l’intégration d’Azure Key Vault, R Services et configuration du stockage.

## <a name="licensing"></a>Gestion des licences
Déploiements de paiement à l’utilisation SQL Server 2008 R2 peuvent convertir [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Pour convertir une licence Software Assurance (SA) en fonction du paiement à l’utilisation, les clients doivent inscrire avec le VM SQL [fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Une fois inscrit avec le fournisseur de ressources SQL VM, le type de licence SQL sera interchangeable entre AHB et paiement à l’utilisation. 

Installation automatique des instances SQL Server 2008 ou SQL Server 2008 R2 sur la machine virtuelle Azure peuvent inscrire auprès du fournisseur de ressources SQL et convertir leur type de licence de paiement à l’utilisation.

## <a name="migration"></a>Migration
Vous pouvez migrer des instances de la fin du support SQL Server vers une machine virtuelle Azure avec des méthodes de sauvegarde/restauration manuelle ; Il s’agit de la méthode la plus courante de migration en local à une machine virtuelle Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Pour les migrations en bloc, nous vous recommandons de [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) service. Avec Azure Site Recovery, les clients peuvent répliquer la machine virtuelle entière, y compris SQL Server en local à une machine virtuelle Azure.

SQL Server nécessite des captures instantanées de cohérence des applications Azure Site Recovery pour garantir la récupération ; et Azure Site Recovery prend en charge des instantanés cohérents au niveau de l’application avec l’intervalle minimum de 1 heure. Le RPO minimale possible pour SQL Server avec les migrations d’Azure Site Recovery est 1 heure et le RTO est de 2 heures ainsi que les temps de récupération de SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

Le [Database Migration Service](/azure/dms/dms-overview) est une option pour les clients si la migration du système local pour machine virtuelle Azure en mettant à niveau de SQL Server vers SQL Server 2012 et supérieures.

## <a name="disaster-recovery"></a>Récupération d'urgence

Solutions de récupération d’urgence pour la fin du support SQL Server sur machine virtuelle Azure sont les suivantes :

- **Sauvegardes SQL Server**: Sauvegardes de SQL Server peuvent être utilisées pour récupérer des échecs de zone ou de SQL Server en cas de régionales. Étant donné que la fonctionnalité de sauvegarde managée n’est pas pris en charge pour la fin du support SQL Server, les clients doivent effectuer des sauvegardes manuellement.
- **Journaux de transaction**: Vous pouvez créer un réplica de livraison de journal dans une autre zone ou région Azure avec des restaurations continues afin de réduire le RTO. Les clients devront configurer manuellement l’envoi de journaux.
- **Azure Site Recovery** : Vous pouvez répliquer vos machines virtuelles entre les zones et les régions grâce à la réplication d’Azure Site Recovery. SQL Server nécessite des captures instantanées cohérentes d’application garantir la récupération en cas de sinistre. Azure Site Recovery offre un RPO de 1 heure minimale et 2 heures + durée de récupération de SQL Server RTO pour EOS SQL serveur de récupération d’urgence.

## <a name="security-patching"></a>Correctifs de sécurité
Mises à jour de sécurité pour les machines virtuelles SQL Server seront distribués via les canaux de Microsoft Update une fois que la machine virtuelle SQL Server a été inscrit avec le code SQL [fournisseur de ressources](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Correctifs peuvent être téléchargés manuellement ou automatiquement. 

**Automated patching** est activée par défaut. La mise à jour corrective automatisée permet à Azure de corriger automatiquement SQL Server et le système d’exploitation. Vous pouvez spécifier un jour de la semaine, heure et la durée d’une fenêtre de maintenance si l’IaaS Extension SQL est installée. Azure effectue la mise à jour corrective dans cette fenêtre de maintenance. La planification de la fenêtre de maintenance utilise les paramètres régionaux de la machine virtuelle pour l’heure.  Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Étapes suivantes

Migrer votre machine virtuelle SQL Server vers Azure

* [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)

Prise en main de SQL Server sur des machines virtuelles Azure :

* [Créer une machine virtuelle SQL Server dans le portail Azure](quickstart-sql-vm-create-portal.md)

Obtenir des réponses aux questions fréquemment posées sur les machines virtuelles SQL :

* [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md)
