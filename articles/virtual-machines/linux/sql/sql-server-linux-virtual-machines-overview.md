---
title: Présentation de SQL Server sur les machines virtuelles Linux Azure | Microsoft Docs
description: Découvrez comment exécuter les éditions complètes de SQL Server sur les machines virtuelles Linux Azure. Obtenez des liens directs vers toutes les images de machine virtuelle SQL Server Linux et le contenu associé.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d376672336845958fb6434a78177f42aca938229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70081969"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Présentation de SQL Server sur les machines virtuelles Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

SQL Server sur les machines virtuelles Azure vous permet d’utiliser des versions complètes de SQL Server dans le cloud sans devoir gérer du matériel local. Les machines virtuelles SQL Server simplifient également les coûts de licence lorsque vous payez à l’utilisation.

Les machines virtuelles Azure sont exécutées dans différentes [régions géographiques](https://azure.microsoft.com/regions/) du monde entier. Elles offrent également un éventail de [tailles de machine](../sizes.md). La galerie d’images de machine virtuelle vous permet de créer une machine virtuelle SQL Server avec la version, l’édition et le système d’exploitation appropriés. Les machines virtuelles constituent ainsi une bonne solution pour différentes charges de travail SQL Server.

## <a name="get-started-with-sql-vms"></a><a id="create"></a> Bien démarrer avec les machines virtuelles SQL

Pour commencer, choisissez une image de machine virtuelle SQL Server avec la version, l’édition et le système d’exploitation requis. Les sections suivantes fournissent des liens directs vers le portail Azure pour les images de la galerie de machines virtuelles SQL Server.

> [!TIP]
> Pour mieux comprendre la tarification des images SQL, consultez la [page de tarification des machines virtuelles SQL Server Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Système d’exploitation | Édition |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Pour afficher les images de machines virtuelles Windows SQL Server disponibles, consultez [Présentation de SQL Server sur les machines virtuelles Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Packages installés

Lorsque vous configurez SQL Server sur Linux, vous installez le package du moteur de base de données et plusieurs packages facultatifs selon vos besoins. Les images de machines virtuelles Linux pour SQL Server installent automatiquement la plupart des packages pour vous. Le tableau suivant montre les packages installés pour chaque distribution.

| Distribution | [Moteur de base de données](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [outils](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Recherche en texte intégral](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Module complémentaire HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![non](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![non](./media/sql-server-linux-virtual-machines-overview/no.png) | ![non](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Oui](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Produits et services associés

### <a name="linux-virtual-machines"></a>Machines virtuelles Linux

* [Présentation des machines virtuelles](../overview.md)

### <a name="storage"></a>Stockage

* [Introduction à Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Mise en réseau

* [Présentation du réseau virtuel](../../../virtual-network/virtual-networks-overview.md)
* [Adresses IP dans Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Créer un nom de domaine complet dans le portail Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux)
* [Comparaison Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Étapes suivantes

Prise en main de SQL Server sur des machines virtuelles Azure Linux :

* [Créer une machine virtuelle SQL Server dans le portail Azure](provision-sql-server-linux-virtual-machine.md)

Obtenir des réponses aux questions fréquemment posées sur les machines virtuelles SQL sur Linux :

* [Forum aux questions (FAQ) concernant SQL Server sur les machines virtuelles Azure Linux](sql-server-linux-faq.md)
