---
title: Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Windows dans Azure | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur l'exécution de SQL Server sur les machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: c3b4fabb319a3ea76ee62c8c699d4613184a4e76
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791048"
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


1. **Est-il possible de déployer une image plus ancienne de SQL Server qui ne se trouve pas sur le portail Azure ?**

   Oui, en utilisant PowerShell. Pour plus d’informations sur le déploiement de machines virtuelles SQL Server à l’aide de PowerShell, consultez [Guide pratique pour provisionner des machines virtuelles SQL Server à l’aide d’Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Puis-je créer une image de Place de marché Azure SQL Server généralisée de ma machine virtuelle SQL Server et l’utiliser pour déployer des machines virtuelles ?**

   Oui, mais vous devez ensuite [inscrire chaque machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pour gérer votre machine virtuelle SQL Server dans le portail, et utiliser des fonctionnalités telles que la mise à jour corrective automatisée et les sauvegardes automatiques. Lorsque vous vous inscrivez auprès du fournisseur de ressources, vous devez également spécifier le type de licence pour chaque machine virtuelle SQL Server. 

1. **Puis-je utiliser mon propre disque dur virtuel pour déployer une machine virtuelle SQL Server ?**

   Oui, mais vous devez ensuite [inscrire chaque machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pour gérer votre machine virtuelle SQL Server dans le portail, et utiliser des fonctionnalités telles que la mise à jour corrective automatisée et les sauvegardes automatiques.

1. **Est-il possible de définir des configurations non affichées dans la galerie de machines virtuelles (par exemple, Windows 2008 R2 + SQL Server 2012) ?**

   Non. Pour les images de la galerie de machines virtuelles incluant SQL Server, vous devez sélectionner une des images fournies via le portail Azure ou [PowerShell](virtual-machines-windows-ps-sql-create.md). Toutefois, vous avez la possibilité de déployer une machine virtuelle Windows et d’y installer SQL Server. Vous devez ensuite [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pour gérer votre machine virtuelle SQL Server dans le portail et utiliser des fonctionnalités telles que la mise à jour corrective automatisée et les sauvegardes automatiques. 


## <a name="creation"></a>Création

1. **Comment créer une machine virtuelle Azure avec SQL Server ?**

   La méthode la plus simple consiste à créer une machine virtuelle qui inclut SQL Server. Pour obtenir un didacticiel sur l’inscription à Azure et la création d’une machine virtuelle SQL Server à partir du portail, consultez [Approvisionner une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous pouvez sélectionner une image de machine virtuelle qui utilise la gestion de licences SQL Server avec paiement à la seconde ou vous pouvez utiliser une image qui vous permet d’utiliser votre propre licence SQL Server. Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle avec une version sous licence gratuite (Developer ou Express) ou en réutilisant une licence locale. Veillez à [inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pour gérer votre machine virtuelle SQL Server dans le portail et utiliser des fonctionnalités telles que la mise à jour corrective automatisée et les sauvegardes automatiques. Si vous utilisez votre propre licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Pour en savoir plus, consultez l’article [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Tarification des machines virtuelles SQL Server Azure).

1. **Comment migrer ma base de données SQL Server locale vers le cloud ?**

   Créez tout d’abord une machine virtuelle Azure avec une instance SQL Server. Puis migrez vos bases de données locales vers cette instance. Pour les stratégies de migration de données, consultez la rubrique [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Gestion des licences

1. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**

   Il existe trois façons d'effectuer cette opération. Si vous êtes client d’un Accord Entreprise (EA), vous pouvez configurer l’une des [images de machine virtuelle prenant en charge les licences](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), également appelée BYOL (apportez votre propre licence). Si vous disposez de [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), vous pouvez activer [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) sur une image de paiement à l’utilisation (PAYG) existante. Vous pouvez également copier le support d’installation de SQL Server dans une machine virtuelle Windows Server, puis installer SQL Server sur la machine virtuelle. Veillez à inscrire votre machine virtuelle SQL Server auprès du [fournisseur de ressources](virtual-machines-windows-sql-register-with-resource-provider.md) pour pouvoir utiliser des fonctionnalités telles que la gestion du portail, la sauvegarde automatisée et la mise à jour corrective automatisée. 

1. **Dois-je payer une licence SQL Server sur une machine virtuelle Azure si elle est utilisée uniquement pour le mode veille ou le basculement ?**

   Pour disposer d’une licence passive gratuite pour un groupe de disponibilité secondaire de secours ou une instance mise en cluster de basculement, vous devez respecter tous les critères suivants, comme indiquer dans le [fichier PDF du guide de gestion des licences](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf) :

   1. Vous disposez de [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. L’instance SQL Server passive ne fournit pas de données SQL Server aux clients ni n’exécute de charges de travail SQL Server actives. Elle est utilisée uniquement pour la synchronisation avec le serveur principal et maintient la base de données passive dans un état de secours semi-automatique. Si elle fournit des données, telles que des rapports, à des clients exécutant des charges de travail SQL Server actives ou effectuant n’importe quel « travail », comme des sauvegardes supplémentaires à partir du serveur secondaire, il doit s’agir d’une instance SQL Server sous licence payante. 
   1. La licence SQL Server active est couverte par Software Assurance et n’autorise qu’**une seule** instance SQL Server secondaire passive avec la même quantité de calcul que le serveur actif sous licence. 
   1. La machine virtuelle SQL Server secondaire utilise le [modèle de licence](virtual-machines-windows-sql-ahb.md) BYOL (apportez votre propre licence) ou Azure Hybrid Benefit (AHB). 

1. **Puis-je modifier une machine virtuelle pour utiliser ma propre licence SQL Server si elle a été créée à partir de l’une des images de la galerie avec paiement à l’utilisation ?**

   Oui. Vous pouvez facilement basculer entre une image de la galerie de paiement à l’utilisation (PAYG) et BYOL (apportez votre propre licence) en activant [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Pour plus d'informations, consultez [Guide pratique pour changer le modèle de licence d'une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md). Actuellement, ce service n'est disponible que pour les clients du cloud public.

1. **Est-ce que SQL Server rencontre des temps d’arrêt si je change de modèle de licence ?**

   Non. Un [changement de modèle de licence](virtual-machines-windows-sql-ahb.md) n’entraîne aucun temps d’arrêt dans SQL Server, car il prend effet immédiatement et ne nécessite pas un redémarrage de la machine virtuelle. Toutefois, pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machines virtuelles SQL Server, vous devez disposer d’une [extension SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md). L’installation de l’extension SQL IaaS en mode _complet_ redémarre le service SQL Server. Par conséquent, si l’extension SQL IaaS doit être installée, installez-la en mode _léger_ pour des fonctionnalités limitées ou installez-la en mode _complet_ pendant une fenêtre de maintenance. L’extension SQL IaaS installée en mode _léger_ peut être mise à niveau en mode _complet_ à tout moment, mais nécessite un redémarrage du service SQL Server. 

1. **Puis-je utiliser le portail Azure pour gérer plusieurs instances sur la même machine virtuelle ?**

   Non. La gestion du portail est une fonctionnalité fournie par le fournisseur de ressources de la machine virtuelle SQL Server qui s’appuie sur l’extension de l’agent IaaS SQL Server. Par conséquent, les mêmes limitations s’appliquent au fournisseur de ressources que pour l’extension. Le portail peut uniquement gérer une instance par défaut, ou une instance nommée, à condition qu’elle soit correctement configurée. Pour plus d’informations sur ces limitations, consultez [Extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Les abonnements CSP peuvent-ils activer Azure Hybrid Benefit ?**

   Oui, Azure Hybrid Benefit est disponible pour les abonnements CSP. Les clients CSP doivent d'abord déployer une image Paiement à l'utilisation, puis [remplacer le modèle de licence](virtual-machines-windows-sql-ahb.md) par BYOL (apportez votre propre licence).

1. **Est-ce que l’inscription de ma machine virtuelle auprès du nouveau fournisseur de ressources de machine virtuelle SQL Server engendre des frais supplémentaires ?**

   Non. Le fournisseur de ressources de machine virtuelle SQL Server permet juste de faciliter encore plus la gestion de SQL Server sur les machines virtuelles Azure sans frais supplémentaires. 

1. **Le fournisseur de ressources de machine virtuelle SQL Server est-il disponible pour tous les clients ?**
 
   Oui, tant que la machine virtuelle SQL Server a été déployée sur le cloud public à l’aide du modèle Resource Manager, et non du modèle classique. Tous les autres clients peuvent s’inscrire auprès du nouveau fournisseur de ressources de machine virtuelle SQL Server. Toutefois, seuls les clients disposant de l’avantage [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) peuvent utiliser leur propre licence pour activer [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) sur une machine virtuelle SQL Server. 

1. **Que se passe-t-il pour la ressource (_Microsoft.SqlVirtualMachine_) de fournisseur de ressources si la ressource de machine virtuelle est déplacée ou supprimée ?** 

   Quand la ressource Microsoft.Compute/VirtualMachine est supprimée ou déplacée, la ressource Microsoft.SqlVirtualMachine associée est alors notifiée pour répliquer l’opération de façon asynchrone.

1. **Que se passe-t-il pour la machine virtuelle si la ressource (_Microsoft.SqlVirtualMachine_) de fournisseur de ressources est supprimée ?**

    La ressource Microsoft.Compute/VirtualMachine n’est pas impactée quand la ressource Microsoft.SqlVirtualMachine est supprimée. Toutefois, les changements de licence rétablissent par défaut la source d’image d’origine. 

1. **Est-il possible d’inscrire des machines virtuelles SQL Server auto-déployées auprès du fournisseur de ressources de machine virtuelle SQL Server ?**

    Oui. Si vous avez déployé SQL Server à partir de votre propre support et installé l’extension SQL IaaS, vous pouvez inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources pour profiter des avantages de facilité de gestion fournis par l’extension IaaS de SQL. Toutefois, vous ne pouvez pas convertir une machine virtuelle SQL Server auto-déployée en paiement à l’utilisation.

1. **Est-il possible de changer le modèle de licence sur une machine virtuelle SQL Server déployée avec le modèle Classique ?**

   Non. Le changement du modèle de licence n’est pas pris en charge sur une machine virtuelle classique. Vous pouvez migrer votre machine virtuelle vers le modèle Azure Resource Manager et l’inscrire auprès du fournisseur de ressources de machine virtuelle SQL Server. Une fois que la machine virtuelle est inscrite auprès du fournisseur de ressources de machine virtuelle SQL Server, les changements du modèle de licence sont disponibles sur la machine virtuelle. 
   


## <a name="administration"></a>Administration

1. **Puis-je installer une seconde instance SQL Server sur la même machine virtuelle ? Puis-je modifier les fonctionnalités installées de l’instance par défaut ?**

   Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter de nouvelles instances SQL Server ou pour modifier d’autres fonctionnalités SQL Server installées sur la machine. Notez que certaines fonctionnalités comme la sauvegarde automatisée, la mise à jour corrective automatisée et Azure Key Vault Integration s’exécutent uniquement sur l’instance par défaut ou sur une instance nommée qui a été correctement configurée (voir la question 3). 

1. **Puis-je désinstaller l’instance SQL Server par défaut ?**

   Oui, mais il existe quelques considérations à prendre en compte. Premièrement, la facturation associée à SQL Server peut continuer à s’appliquer en fonction du modèle de licence de la machine virtuelle. Deuxièmement, comme indiqué dans la réponse précédente, certaines fonctionnalités reposent sur l’[extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Si vous désinstallez l’instance par défaut sans supprimer l’extension IaaS, cette dernière continue de rechercher l’instance par défaut et peut générer des erreurs dans le journal des événements. Ces erreurs peuvent avoir deux sources : **Gestion des informations d’identification Microsoft SQL Server** et **Agent IaaS Microsoft SQL Server**. L’une des erreurs peut se présenter comme suit :

      Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible.

   Si vous décidez de désinstaller l’instance par défaut, vous devez également désinstaller [l’extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Puis-je utiliser une instance nommée de SQL Server avec l’extension IaaS** ?
   
   Oui, si l’instance nommée est la seule instance sur SQL Server et si l’instance par défaut d’origine a été [désinstallé correctement](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). En l’absence d’instance par défaut et si plusieurs instances nommées sont présentes sur une même machine virtuelle SQL Server, l’extension de l’agent IaaS SQL Server échoue à l’installation. 

1. **Puis-je supprimer complètement SQL Server d’une machine virtuelle SQL Server ?**

   Oui, mais vous serez toujours facturé pour votre machine virtuelle SQL Server de la manière décrite dans [Tarification des machines virtuelles SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Si vous n’avez plus besoin de SQL Server, vous pouvez déployer une nouvelle machine virtuelle et y migrer les données et les applications. Vous pouvez ensuite supprimer la machine virtuelle SQL Server.
   
## <a name="updating-and-patching"></a>Mise à jour et mise à jour corrective

1. **Comment passer à une autre version/édition de SQL Server dans une machine virtuelle Azure ?**

   Les clients peuvent modifier leur version/édition de SQL Server à l’aide du support d’installation qui contient leur version ou édition souhaitée de SQL Server. Une fois que l’édition a été modifiée, utilisez le portail Azure pour modifier la propriété d’édition de la machine virtuelle pour refléter avec précision la facturation pour la machine virtuelle. Pour plus d’informations, consultez [Modifier l’édition d’une machine virtuelle SQL Server](virtual-machines-windows-sql-change-edition.md). Il n’existe aucune différence de facturation entre les différentes versions de SQL Server. Par conséquent, une fois que la version de SQL Server a été modifiée, aucune action supplémentaire n’est nécessaire.

1. **Où puis-je obtenir le support d’installation pour modifier l’édition ou la version de SQL Server ?**

  Les clients disposant de la [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) peuvent obtenir leur support d’installation à partir du [Centre de gestion des licences en volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Les clients ne disposant pas de Software Assurance peuvent utiliser le support d’installation à partir d’une image de machine virtuelle SQL Server de Place de marché proposant l'édition souhaitée.

1. **Comment les mises à jour et les service packs sont-ils appliqués sur une machine virtuelle SQL Server ?**

   Les machines virtuelles vous permettent de contrôler la machine hôte, y compris quand et comment appliquer les mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement les mises à jour Windows, ou activer un service de planification appelé [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md). La mise à jour corrective automatisée installe toutes les mises à jour importantes, y compris les mises à jour de SQL Server de cette catégorie. Les autres mises à jour SQL Server facultatives doivent être installées manuellement.

1. **Puis-je mettre à niveau mon instance SQL Server 2008/2008 R2 après l’avoir inscrite auprès du fournisseur de ressources de machine virtuelle SQL Server ?**

   Oui. Vous pouvez utiliser n’importe quel support d’installation pour mettre à niveau la version et l’édition de SQL Server. Vous pouvez ensuite mettre à niveau votre [mode d’extension SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) de _aucun agent_ vers _complet_. Vous pouvez ainsi tirer profit de tous les avantages de l’extension SQL IaaS tels que la facilité de gestion du portail, les sauvegardes automatisées et la mise à jour corrective automatisée. 

## <a name="general"></a>Généralités

1. **Les instances de cluster de basculement (FCI) SQL Server sont-elles prises en charge sur les machines virtuelles Azure ?**

   Oui. Vous pouvez [créer un cluster de basculement Windows sur Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) et utiliser les espaces de stockage direct (S2D) pour le stockage du cluster. Vous pouvez également utiliser les solutions de clustering ou de stockage tierces comme décrit dans [Haute disponibilité et récupération d’urgence pour SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > À ce stade, l’[extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) _complète_ n’est pas prise en charge pour l’ICF SQL Server sur Azure. Nous vous recommandons de désinstaller l’extension _complète_ des machines virtuelles qui participent à l’ICF, puis d’installer l’extension en mode _léger_ à la place. Cette extension prend en charge des fonctionnalités telles que la sauvegarde et la mise à jour corrective automatisées, ainsi que certaines fonctionnalités du portail pour SQL Server. Ces fonctionnalités ne s’opèrent pas pour les machines virtuelles SQL Server une fois l’agent _complet_ désinstallé.

1. **Quelle est la différence entre les machines virtuelles SQL Server et le service SQL Database ?**

   Conceptuellement, l’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de l’exécution de SQL Server dans un centre de données distant. En revanche, la [base de données SQL](../../../sql-database/sql-database-technical-overview.md) fournit une base de données relationnelle complète en tant que service. Avec SQL Database, vous n’avez pas besoin d’accéder aux machines qui hébergent vos bases de données. Pour une comparaison complète, consultez [Choisir une option SQL Serveur cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Comment installer les outils SQL Data sur ma machine virtuelle Azure ?**

    Téléchargez et installez les outils SQL Data à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Les transactions distribuées avec MSDTC sont-elles prises en charge sur les machines virtuelles SQL Server ?**
   
    Oui. Le DTC local est pris en charge pour SQL Server 2016 SP2 et versions ultérieures. Cependant, les applications utilisant des groupes de disponibilité AlwaysOn doivent être testées, car les transactions en cours au moment d’un basculement échouent et doivent être relancées. Le DTC en cluster est disponible à partir de Windows Server 2019. 

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
