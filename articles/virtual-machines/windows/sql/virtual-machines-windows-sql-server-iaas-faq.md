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
ms.openlocfilehash: 5299437dea18510fa5f85ee27240c8afc434d125
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680802"
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


1. **Il est possible de déployer une image plus anciennes de SQL Server qui n’est pas visible dans le portail Azure ?**

   Oui, en utilisant PowerShell. Pour plus d’informations sur le déploiement de machines virtuelles SQL Server à l’aide de PowerShell, consultez [Guide pratique pour provisionner des machines virtuelles SQL Server à l’aide d’Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Puis-je créer une image de disque dur virtuel à partir d’une machine virtuelle SQL Server ?**

   Oui, mais il existe quelques considérations à prendre en compte. Si vous déployez ce disque dur virtuel sur une machine virtuelle dans Azure, vous n’obtenez pas la section de Configuration SQL Server dans le portail. Vous devez alors gérer les options de configuration SQL Server via PowerShell. En outre, vous serez facturé en fonction du taux de la machine virtuelle SQL sur laquelle votre image était basée à l’origine. Ceci s’applique même si vous supprimez SQL Server à partir du disque dur virtuel avant le déploiement. 

1. **Est-il possible de définir des configurations non affichées dans la galerie de machines virtuelles (par exemple, Windows 2008 R2 + SQL Server 2012) ?**

   Non. Pour les images de la galerie de machines virtuelles incluant SQL Server, vous devez sélectionner une des images fournies via le portail Azure ou [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Création

1. **Comment créer une machine virtuelle Azure avec SQL Server ?**

   La solution la plus simple consiste à créer une machine virtuelle qui inclut SQL Server. Pour obtenir un didacticiel sur l’inscription à Azure et la création d’une machine virtuelle SQL à partir du portail, consultez [Provisionner une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous pouvez sélectionner une image de machine virtuelle qui utilise la gestion de licences SQL Server avec paiement à la seconde ou vous pouvez utiliser une image qui vous permet d’utiliser votre propre licence SQL Server. Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle avec une version sous licence gratuite (Developer ou Express) ou en réutilisant une licence locale. Si vous utilisez votre propre licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Pour en savoir plus, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).

1. **Comment migrer ma base de données SQL Server locale vers le cloud ?**

   Créez tout d’abord une machine virtuelle Azure avec une instance SQL Server. Puis migrez vos bases de données locales vers cette instance. Pour les stratégies de migration de données, consultez la rubrique [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Gestion des licences

1. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**

   Il existe deux façons d'effectuer cette opération. Vous pouvez configurer l’une des [images de machine virtuelle prenant en charge les licences](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), également appelée BYOL. Une autre option consiste à copier le support d’installation de SQL Server sur une machine virtuelle Windows Server, puis d’installer SQL Server sur la machine virtuelle. Toutefois, si vous installez SQL Server manuellement, il n’y a pas d’intégration du portail et l’extension Agent IaaS SQL Server n’est pas prise en charge. Par conséquent, des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées ne fonctionnent pas dans ce scénario. Pour cette raison, nous vous recommandons d’utiliser l’une des images de galerie BYOL. Pour utiliser BYOL ou votre propre support SQL Server sur une machine virtuelle Azure, vous devez avoir la [mobilité de licence à travers la Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Pour en savoir plus, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).


1. **Dois-je payer une licence SQL Server sur une machine virtuelle Azure si elle est utilisée uniquement pour le mode veille ou le basculement ?**

   Si vous disposez de Software Assurance et utilisez License Mobility comme décrit dans [FAQ sur les licences Virtual Machine](https://azure.microsoft.com/pricing/licensing-faq/), vous n’avez pas à payer pour une instance SQL Server participant en tant que réplica secondaire passif dans un déploiement haute disponibilité de la licence. Dans le cas contraire, vous devez payer une licence.

1. **Puis-je modifier une machine virtuelle pour utiliser ma propre licence SQL Server si elle a été créée à partir de l’une des images de la galerie avec paiement à l’utilisation ?**

   Oui. Vous pouvez facilement passer d'un modèle de licence à l'autre si vous avez initialement créé une image de la galerie avec paiement à l'utilisation. En revanche, vous ne pourrez pas basculer votre licence en mode Paiement à l'utilisation si vous avez commencé avec une image BYOL (apportez votre propre licence). Pour plus d'informations, consultez [Guide pratique pour changer le modèle de licence d'une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Actuellement, ce service n'est disponible que pour les clients du cloud public.

1. **Dois-je utiliser des images BYOL ou un fournisseur de ressources de machine virtuelle SQL pour créer une nouvelle machine virtuelle SQL ?**

   Les images BYOL (Apportez votre propre licence) sont réservées aux clients EA. Les clients qui ont une Software Assurance doivent utiliser le fournisseur de ressources de machine virtuelle SQL pour créer une machine virtuelle SQL avec [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Est-ce que SQL Server rencontre des temps d’arrêt si je change de modèle de licence ?**

   Non. Un [changement de modèle de licence](virtual-machines-windows-sql-ahb.md) n’entraîne aucun temps d’arrêt dans SQL Server, car il prend effet immédiatement et ne nécessite pas un redémarrage de la machine virtuelle. Toutefois, pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles SQL, vous devez disposer d'une [extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md). L'installation de l'extension SQL IaaS redémarre le service SQL Server. Par conséquent, si l'extension SQL IaaS doit être installée, cette installation doit être effectuée pendant une fenêtre de maintenance. 

1. **Les abonnements CSP peuvent-ils activer Azure Hybrid Benefit ?**

   Oui, Azure Hybrid Benefit est disponible pour les abonnements CSP. Les clients CSP doivent d'abord déployer une image Paiement à l'utilisation, puis [remplacer le modèle de licence](virtual-machines-windows-sql-ahb.md) par BYOL (apportez votre propre licence).  

1. **Est-ce que l’inscription de ma machine virtuelle auprès du nouveau fournisseur de ressources de machine virtuelle SQL engendre des frais supplémentaires ?**

   Non. Le fournisseur de ressources de machine virtuelle SQL permet juste de faciliter encore plus la gestion de SQL Server sur les machines virtuelles Azure sans frais supplémentaires. 

1. **Le fournisseur de ressources de machine virtuelle SQL est-il disponible pour tous les clients ?**
 
   Oui. Tous les clients peuvent s’inscrire auprès du nouveau fournisseur de ressources de machine virtuelle SQL. Toutefois, seuls les clients avec l’avantage Software Assurance peuvent activer [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (ou BYOL) sur une machine virtuelle SQL Server. 

1. **Que se passe-t-il pour les _Microsoft.SqlVirtualMachine_ ressource si la ressource de machine virtuelle est déplacée ou supprimée ?** 

   Quand la ressource Microsoft.Compute/VirtualMachine est supprimée ou déplacée, la ressource Microsoft.SqlVirtualMachine associée est alors notifiée pour répliquer l’opération de façon asynchrone.

1. **Que se passe-t-il à la machine virtuelle si le _Microsoft.SqlVirtualMachine_ ressource est supprimée ?**

    La ressource Microsoft.Compute/VirtualMachine n’est pas impactée quand la ressource Microsoft.SqlVirtualMachine est supprimée. Toutefois, les changements de licence rétablissent par défaut la source d’image d’origine. 

1. **Est-il possible d’inscrire des machines virtuelles SQL Server auto-déployées auprès du fournisseur de ressources de machine virtuelle SQL ?**

    Oui. Si vous avez déployé SQL Server à partir de votre propre support et installé l’extension SQL IaaS, vous pouvez inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources pour profiter des avantages de facilité de gestion fournis par l’extension IaaS de SQL. Toutefois, vous ne pouvez pas convertir une machine virtuelle SQL auto-déployée en paiement à l’utilisation.

## <a name="administration"></a>Administration

1. **Puis-je installer une seconde instance SQL Server sur la même machine virtuelle ? Puis-je modifier les fonctionnalités installées de l’instance par défaut ?**

   Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter de nouvelles instances SQL Server ou pour modifier d’autres fonctionnalités SQL Server installées sur la machine. Notez que certaines fonctionnalités, telles que la sauvegarde automatisée, les correctifs automatiques et Azure Key Vault Integration fonctionnent uniquement sur l’instance par défaut ou instance une commande nommée qui a été configuré correctement (voir la Question 3). 

1. **Puis-je désinstaller l’instance SQL Server par défaut ?**

   Oui, mais il existe quelques considérations à prendre en compte. Comme indiqué dans la réponse précédente, il existe des fonctionnalités qui reposent sur le [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).  Si vous désinstallez l’instance par défaut sans supprimer l’extension IaaS également, l’extension continue à chercher et peut générer des erreurs du journal des événements. Ces erreurs peuvent avoir deux sources : **Gestion des informations d’identification Microsoft SQL Server** et **Agent IaaS Microsoft SQL Server**. L’une des erreurs peut se présenter comme suit :

      Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible.

   Si vous décidez de désinstaller l’instance par défaut, vous devez également désinstaller [l’extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

1. **Puis-je utiliser une instance nommée de SQL Server avec l’extension IaaS**?
   
   Oui, si l’instance nommée est la seule instance sur le serveur SQL Server, et si l’instance par défaut d’origine a été [désinstallé correctement](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). S’il n’existe aucune instance par défaut et il existe plusieurs instances nommées sur une seule machine virtuelle de SQL Server, l’extension IaaS échoue à installer. 

1. **Puis-je supprimer complètement SQL Server d’une machine virtuelle SQL ?**

   Oui, mais vous serez toujours facturé pour votre machine virtuelle SQL de la manière décrite dans [Tarification des machines virtuelles SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Si vous n’avez plus besoin de SQL Server, vous pouvez déployer une nouvelle machine virtuelle et y migrer les données et les applications. Vous pouvez ensuite supprimer la machine virtuelle SQL Server.
   
## <a name="updating-and-patching"></a>Mise à jour et mise à jour corrective

1. **Comment modifier pour une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**

   Les clients avec Software Assurance peuvent sur place les mises à niveau de leur SQL Server s’exécutant sur une machine virtuelle Azure à l’aide du support d’installation dans le portail de gestion des licences de Volume. Cependant, actuellement, il n’existe aucun moyen de modifier l’édition d’une instance de SQL Server. Créer une nouvelle machine virtuelle Azure avec l’édition de SQL Server souhaitée, puis migrez vos bases de données vers le nouveau serveur à l’aide de la norme [techniques de migration de données](virtual-machines-windows-migrate-sql.md).

1. **Comment les mises à jour et les service packs sont-ils appliqués sur une machine virtuelle SQL Server ?**

   Les machines virtuelles vous permettent de contrôler la machine hôte, y compris quand et comment appliquer les mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement les mises à jour Windows, ou activer un service de planification appelé [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md). La mise à jour corrective automatisée installe toutes les mises à jour importantes, y compris les mises à jour de SQL Server de cette catégorie. Les autres mises à jour SQL Server facultatives doivent être installées manuellement.

## <a name="general"></a>Généralités

1. **Les instances de cluster de basculement (FCI) SQL Server sont-elles prises en charge sur les machines virtuelles Azure ?**

   Oui. Vous pouvez [créer un cluster de basculement Windows sur Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) et utiliser les espaces de stockage direct (S2D) pour le stockage du cluster. Vous pouvez également utiliser les solutions de clustering ou de stockage tierces comme décrit dans [Haute disponibilité et récupération d’urgence pour SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > À ce stade, l’[extension Agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) n’est pas prise en charge pour l’ICF SQL Server sur Azure. Nous vous recommandons de désinstaller l’extension des machines virtuelles qui participent à l’ICF. Cette extension prend en charge des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées, ainsi que certaines fonctionnalités du portail pour SQL. Ces fonctionnalités n’opèrent pas pour les machines virtuelles SQL une fois l’agent désinstallé.

1. **Quelle est la différence entre les machines virtuelles SQL et le service SQL Database ?**

   Conceptuellement, l’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de l’exécution de SQL Server dans un centre de données distant. En revanche, la [base de données SQL](../../../sql-database/sql-database-technical-overview.md) fournit une base de données relationnelle complète en tant que service. Avec SQL Database, vous n’avez pas besoin d’accéder aux machines qui hébergent vos bases de données. Pour une comparaison complète, consultez [Choisir une option SQL Serveur cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Comment installer les outils SQL Data sur ma machine virtuelle Azure ?**

    Téléchargez et installez les outils SQL Data à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

1. **Sont des transactions distribuées avec MSDTC pris en charge sur les machines virtuelles SQL Server ?**
   
    Oui. DTC local est pris en charge pour SQL Server 2016 SP2 et supérieure. Toutefois, les applications doivent être testées lors de l’utilisation des groupes de disponibilité Always On, en tant que transactions en cours lors d’un basculement échoue et doit être retentée. DTC en cluster est disponible à partir de Windows Server 2019. 

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
