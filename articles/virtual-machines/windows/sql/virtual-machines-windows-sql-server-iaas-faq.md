---
title: Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Windows dans Azure | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur l'exécution de SQL Server sur les machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: edfd2e9e03aefa4833c8472a43d4857f08b95780
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495474"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Forum aux questions (FAQ) relatives à l’exécution de SQL Server sur les machines virtuelles Windows dans Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Cet article fournit des réponses à certaines des questions les plus courantes sur l’exécution de [SQL Server sur les machines virtuelles Windows dans Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Cet article se concentre sur les problèmes spécifiques à SQL Server sur les machines virtuelles Windows. Si vous exécutez SQL Server sur les machines virtuelles Linux, consultez le [Forum Aux Questions (FAQ) sur Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Images

1. **Quelles images de galerie de machines virtuelles SQL Server sont disponibles ?**

   Azure gère les images de machines virtuelles pour toutes les versions majeures prises en charge de SQL Server sur toutes les éditions pour Windows et Linux. Pour plus d’informations, consultez la liste complète des [images de machines virtuelles Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) et des [images de machines virtuelles Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Les images de galerie de machines virtuelles SQL Server sont-elles mises à jour?**

   Tous les deux mois, les images SQL Server dans la galerie de machines virtuelles sont mises à jour avec les dernières mises à jour Windows et Linux. Pour les images Windows, cela inclut toutes les mises à jour marquées comme importantes dans Windows Update, dont les Service Packs et mises à jour de sécurité SQL Server essentiels. Pour les images Linux, cela inclut les dernières mises à jour système. Les mises à jour cumulatives SQL Server sont gérées différemment pour Linux et Windows. Pour Linux, les mises à jour cumulatives SQL Server figurent également dans l’actualisation. Toutefois, à ce stade, les machines virtuelles Windows ne sont pas mises à jour avec les mises à jour cumulatives SQL Server ou Windows Server.

1. **Est-ce que des images de machines virtuelles SQL Server peuvent être supprimées de la galerie ?**

   Oui. Azure gère uniquement une image par version majeure et édition. Par exemple, quand un nouveau Service Pack SQL Server est publié, Azure ajoute une nouvelle image à la galerie pour ce Service Pack. L’image SQL Server pour le Service Pack précédent est immédiatement supprimée du portail Azure. Toutefois, elle est toujours disponible pour le provisionnement à partir de PowerShell pour les trois prochains mois. Après trois mois, l’image du Service Pack précédent n’est plus disponible. Cette stratégie de suppression s’applique également si une version SQL Server n’est plus prise en charge quand elle atteint la fin de son cycle de vie.

1. **Puis-je créer une image de disque dur virtuel à partir d’une machine virtuelle SQL Server ?**

   Oui, mais il existe quelques considérations à prendre en compte. Si vous déployez ce disque dur virtuel sur une nouvelle machine virtuelle dans Azure, la section Configuration de SQL Server ne s’affiche pas dans le portail. Vous devez alors gérer les options de configuration SQL Server via PowerShell. En outre, vous serez facturé en fonction du taux de la machine virtuelle SQL sur laquelle votre image était basée à l’origine. Ceci s’applique même si vous supprimez SQL Server à partir du disque dur virtuel avant le déploiement. 

1. **Est-il possible de définir des configurations non affichées dans la galerie de machines virtuelles (par exemple, Windows 2008 R2 + SQL Server 2012) ?**

    Non. Pour les images de la galerie de machines virtuelles incluant SQL Server, vous devez sélectionner une des images fournies.

## <a name="creation"></a>Création

1. **Comment créer une machine virtuelle Azure avec SQL Server ?**

   La solution la plus simple consiste à créer une machine virtuelle qui inclut SQL Server. Pour obtenir un didacticiel sur l’inscription à Azure et la création d’une machine virtuelle SQL à partir du portail, consultez [Provisionner une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous pouvez sélectionner une image de machine virtuelle qui utilise la gestion de licences SQL Server avec paiement à la seconde ou vous pouvez utiliser une image qui vous permet d’utiliser votre propre licence SQL Server. Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle avec une version sous licence gratuite (Developer ou Express) ou en réutilisant une licence locale. Si vous utilisez votre propre licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Pour en savoir plus, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).

1. **Comment migrer ma base de données SQL Server locale vers le cloud ?**

   Créez tout d’abord une machine virtuelle Azure avec une instance SQL Server. Puis migrez vos bases de données locales vers cette instance. Pour les stratégies de migration de données, consultez la rubrique [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Gestion des licences

1. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**

   Il existe deux façons d'effectuer cette opération. Vous pouvez configurer l’une des [images de machine virtuelle prenant en charge les licences](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), également appelée BYOL. Une autre option consiste à copier le support d’installation de SQL Server sur une machine virtuelle Windows Server, puis d’installer SQL Server sur la machine virtuelle. Toutefois, si vous installez SQL Server manuellement, il n’y a pas d’intégration du portail et l’extension Agent IaaS SQL Server n’est pas prise en charge. Par conséquent, des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées ne fonctionnent pas dans ce scénario. Pour cette raison, nous vous recommandons d’utiliser l’une des images de galerie BYOL. Pour utiliser BYOL ou votre propre support SQL Server sur une machine virtuelle Azure, vous devez avoir la [mobilité de licence à travers la Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Pour en savoir plus, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).


1. **Dois-je payer une licence SQL Server sur une machine virtuelle Azure si elle est utilisée uniquement pour le mode veille ou le basculement ?**

   Si vous disposez de la Software Assurance et utilisez la mobilité de licence comme décrit dans le FAQ concernant les licences des machines virtuelles (https://azure.microsoft.com/pricing/licensing-faq/), vous n’avez pas à payer une licence pour une instance SQL Server qui participe comme réplica secondaire passif à un déploiement de haute disponibilité. Dans le cas contraire, vous devez payer une licence.

1. **Puis-je modifier une machine virtuelle pour utiliser ma propre licence SQL Server si elle a été créée à partir de l’une des images de la galerie avec paiement à l’utilisation ?**

   Oui. Vous pouvez facilement passer d’un modèle de licence à l’autre, quelle que soit l’image qui a été déployée au départ. Pour plus d’informations, consultez [Guide pratique pour changer le modèle de licence d’une machine virtuelle SQL](virtual-machines-windows-sql-ahb.md).

1. **Dois-je utiliser des images BYOL ou un fournisseur de ressources de machine virtuelle SQL pour créer une nouvelle machine virtuelle SQL ?**

   Les images BYOL (Apportez votre propre licence) sont réservées aux clients EA. Les clients qui ont une Software Assurance doivent utiliser le fournisseur de ressources de machine virtuelle SQL pour créer une machine virtuelle SQL avec [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Est-ce que SQL Server rencontre des temps d’arrêt si je change de modèle de licence ?**

   Non. Un [changement de modèle de licence](virtual-machines-windows-sql-ahb.md) n’entraîne aucun temps d’arrêt dans SQL Server, car il prend effet immédiatement et ne nécessite pas un redémarrage de la machine virtuelle. 

1. **Les abonnements CSP peuvent-ils activer Azure Hybrid Benefit ?**

   Oui. Les [changements de modèle de licence](virtual-machines-windows-sql-ahb.md) sont disponibles pour les abonnements CSP. 

1. **Est-ce que l’inscription de ma machine virtuelle auprès du nouveau fournisseur de ressources de machine virtuelle SQL engendre des frais supplémentaires ?**

   Non. Le fournisseur de ressources de machine virtuelle SQL permet juste de faciliter encore plus la gestion de SQL Server sur les machines virtuelles Azure sans frais supplémentaires. 

1. **Le fournisseur de ressources de machine virtuelle SQL est-il disponible pour tous les clients ?**
 
   Oui. Tous les clients peuvent s’inscrire auprès du nouveau fournisseur de ressources de machine virtuelle SQL. Toutefois, seuls les clients avec l’avantage Software Assurance peuvent activer [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (ou BYOL) sur une machine virtuelle SQL Server. 

1. **Que se passe-t-il pour la ressource _* Microsoft.SqlVirtualMachine_* si la ressource de machine virtuelle est déplacée ou supprimée ?** 

   Quand la ressource Microsoft.Compute/VirtualMachine est supprimée ou déplacée, la ressource Microsoft.SqlVirtualMachine associée est alors notifiée pour répliquer l’opération de façon asynchrone.

1. **Que se passe-t-il pour la machine virtuelle si la ressource _* Microsoft.SqlVirtualMachine_* est supprimée ?**

   La ressource Microsoft.Compute/VirtualMachine n’est pas impactée quand la ressource Microsoft.SqlVirtualMachine est supprimée. Toutefois, les changements de licence rétablissent par défaut la source d’image d’origine. 

1. **Est-il possible d’inscrire des machines virtuelles SQL Server auto-déployées auprès du fournisseur de ressources de machine virtuelle SQL ?**

   Oui. Si vous avez déployé SQL Server à partir de votre propre support, vous pouvez inscrire votre machine virtuelle SQL auprès du fournisseur de ressources pour profiter des avantages de facilité de gestion fournis par l’extension IaaS de SQL. Toutefois, vous ne pouvez pas convertir une machine virtuelle SQL auto-déployée en PAYG. 

## <a name="administration"></a>Administration

1. **Puis-je installer une seconde instance SQL Server sur la même machine virtuelle ? Puis-je modifier les fonctionnalités installées de l’instance par défaut ?**

   Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter de nouvelles instances SQL Server ou pour modifier d’autres fonctionnalités SQL Server installées sur la machine. Notez que certaines fonctionnalités comme la sauvegarde automatisée, la mise à jour corrective automatisée et Azure Key Vault Integration fonctionnent uniquement sur l’instance par défaut.

1. **Puis-je désinstaller l’instance SQL Server par défaut ?**

   Oui, mais il existe quelques considérations à prendre en compte. Comme indiqué dans la réponse précédente, les fonctionnalités qui reposent sur [l’extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) fonctionnent uniquement sur l’instance par défaut. Si vous désinstallez l’instance par défaut, l’extension continue à la rechercher et peut générer des erreurs du journal des événements. Ces erreurs peuvent avoir deux sources : **gestion des informations d’identification Microsoft SQL Server** et **agent IaaS Microsoft SQL Server**. L’une des erreurs peut se présenter comme suit :

      Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible.

   Si vous décidez de désinstaller l’instance par défaut, vous devez également désinstaller [l’extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

1. **Puis-je supprimer complètement SQL Server d’une machine virtuelle SQL ?**

   Oui, mais vous serez toujours facturé pour votre machine virtuelle SQL de la manière décrite dans [Tarification des machines virtuelles SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Si vous n’avez plus besoin de SQL Server, vous pouvez déployer une nouvelle machine virtuelle et y migrer les données et les applications. Vous pouvez ensuite supprimer la machine virtuelle SQL Server.
   
## <a name="updating-and-patching"></a>Mise à jour et mise à jour corrective

1. **Comment mettre à niveau vers une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**

   Il n'existe actuellement aucune mise à niveau sur place pour une instance SQL Server exécutée sur une machine virtuelle Azure. Créez une machine virtuelle Azure avec la version/édition de SQL Server souhaitée, puis migrez vos bases de données vers le nouveau serveur à l’aide des [techniques de migration de données](virtual-machines-windows-migrate-sql.md)standard.

1. **Comment les mises à jour et les service packs sont-ils appliqués sur une machine virtuelle SQL Server ?**

   Les machines virtuelles vous permettent de contrôler la machine hôte, y compris quand et comment appliquer les mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement les mises à jour Windows, ou activer un service de planification appelé [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md). La mise à jour corrective automatisée installe toutes les mises à jour importantes, y compris les mises à jour de SQL Server de cette catégorie. Les autres mises à jour SQL Server facultatives doivent être installées manuellement.

## <a name="general"></a>Généralités

1. **Les instances de cluster de basculement (FCI) SQL Server sont-elles prises en charge sur les machines virtuelles Azure ?**

   Oui. Vous pouvez [créer un cluster de basculement Windows sur Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) et utiliser les espaces de stockage direct (S2D) pour le stockage du cluster. Vous pouvez également utiliser les solutions de clustering ou de stockage tierces comme décrit dans [Haute disponibilité et récupération d’urgence pour SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > À ce stade, l’[extension Agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) n’est pas prise en charge pour l’ICF SQL Server sur Azure. Nous vous recommandons de désinstaller l’extension des machines virtuelles qui participent à l’ICF. Cette extension prend en charge des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées, ainsi que certaines fonctionnalités du portail pour SQL. Ces fonctionnalités n’opèrent pas pour les machines virtuelles SQL une fois l’agent désinstallé.

1. **Quelle est la différence entre les machines virtuelles SQL et le service SQL Database ?**

   Conceptuellement, l’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de l’exécution de SQL Server dans un centre de données distant. En revanche, la [base de données SQL](../../../sql-database/sql-database-technical-overview.md) fournit une base de données relationnelle complète en tant que service. Avec SQL Database, vous n’avez pas besoin d’accéder aux machines qui hébergent vos bases de données. Pour obtenir une comparaison complète, consultez la rubrique [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Comment installer les outils SQL Data sur ma machine virtuelle Azure ?**

    Téléchargez et installez les outils SQL Data à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Ressources

**Machines virtuelles Windows** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Approvisionner une machine virtuelle Windows SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
* [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Machines virtuelles Linux** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Approvisionner une machine virtuelle Linux SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Forum Aux Questions (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
