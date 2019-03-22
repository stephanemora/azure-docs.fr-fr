---
title: Mainframe réhébergement sur des machines virtuelles Azure | Microsoft Docs
description: Ré-héberger vos charges de travail mainframe tels que des systèmes IBM Z à l’aide de machines virtuelles (VM) sur Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192715"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe réhébergement sur des machines virtuelles

Migration des charges de travail à partir de macroordinateur environnements vers le cloud vous permet de moderniser votre infrastructure et souvent réduire les coûts. De nombreuses charges de travail peuvent être transférées vers Azure suite à quelques modifications mineures du code (mettre à jour le nom des bases de données, par exemple).

Le terme *mainframe* se réfère généralement à un système informatique de grande taille, mais la grande majorité actuellement déployée sont des serveurs de système IBM Z ou systèmes compatibles IBM exécutant MVS, DOS, VSE, OS/390 ou z/OS.

Une machine virtuelle (VM) est utilisée pour isoler et gérer les ressources pour une application spécifique sur une seule instance. Macroordinateurs tels que IBM z/OS utilisent des Partitions logiques (partitions logiques) à cet effet. Un grand système peut utiliser une partition logique pour une région CICS avec des programmes COBOL associés et une partition logique distincte pour la base de données IBM Db2. Classique [application multi-niveau sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) déploie les machines virtuelles Azure dans un réseau virtuel qui peut être subdivisé en sous-réseaux pour chaque niveau.

Les machines virtuelles Azure peuvent exécuter des environnements de macroordinateur émulation et les compilateurs qui prennent en charge les scénarios de lift-and-shift. Développement et les tests sont souvent parmi les premières charges de travail à migrer à partir d’un grand système vers un environnement de développement/test Azure. Les composants de serveur communs que vous pouvez émuler incluent le processus de transaction en ligne (OLTP), batch et systèmes d’ingestion de données comme le montre l’illustration suivante.

![Les environnements d’émulation sur Azure vous permettent de vous permettent d’exécuter des systèmes basé sur z/OS.](media/01-overview.png)

Certaines charges de travail mainframe peuvent être migrées vers Azure en toute simplicité relative, tandis que d’autres peuvent être réhébergés sur Azure à l’aide d’une solution de partenaire. Pour obtenir des instructions détaillées sur le choix d’une solution de partenaire, le [centre de Migration de macroordinateurs Azure](https://azure.microsoft.com/migration/mainframe/) peut aider.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Configuration d’environnement de développement/test à l’aide d’une plateforme de réhébergement Micro Focus

Micro Focus Enterprise Server est un des plus grand macroordinateur réhébergement plateformes disponibles. Vous pouvez l’utiliser exécuter vos charges de travail z/OS sur un x86 moins coûteux platform sur Azure.

Pour commencer, consultez les articles suivants :

- [Installation de Micro Focus Enterprise Server 4.0 et code Enterprise Developer 4.0 sur Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Configurer Micro Focus CICS BankDemo pour Micro Focus Enterprise Developer 4.0 dans Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame sur Azure

TmaxSoft OpenFrame est un macroordinateur populaires réhébergement de solution qui permet de facilement développer vos ressources de macroordinateur existantes et les transférer vers Azure. Un environnement OpenFrame sur Azure est approprié pour le développement, de démonstrations, de test ou de charges de travail de production.

Pour commencer, téléchargez le [OpenFrame de TmaxSoft installer sur Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) livre électronique.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Configurer un environnement de développement/test à l’aide d’IBM Z Dev/Test 12.0

IBM Z Development et environnement de Test (IBM zD & T) configure un environnement hors production sur Azure que vous pouvez utiliser pour le développement, tests et démonstrations d’applications basées sur le système d’exploitation z.

L’environnement d’émulation sur Azure peut héberger plusieurs instances de Z via Application développeurs contrôlé Distributions (ADCDs). Vous pouvez exécuter zD & T Personal Edition, zD & T Parallel Sysplex et zD & T Enterprise Edition sur Azure et Azure Stack.

Pour commencer, consultez les articles suivants :

-   [Configurer IBM zD & 12.0 T sur Azure](./ibm/install-ibm-z-environment.md)
-   [Configurer ADCD sur zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Migrer IBM DB2 pureScale vers Azure

L’environnement IBM DB2 pureScale fournit un cluster de bases de données pour Azure avec haute disponibilité et scalabilité sur les systèmes d’exploitation Linux. Quoique différent de l’environnement d’origine, IBM DB2 pureScale sur Linux offre des fonctionnalité de haute disponibilité et d’évolutivité similaires à IBM DB2 pour z/OS exécuté dans une configuration Parallel Sysplex sur un ordinateur central.

Pour commencer, consultez [pureScale IBM DB2 sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considérations

Lorsque vous migrez des charges de travail mainframe vers l’infrastructure Azure en tant que service (IaaS), vous pouvez choisir parmi plusieurs types de ressources informatiques à la demande et évolutives, y compris les machines virtuelles Azure. Azure propose une gamme de [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) et [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) machines virtuelles.

### <a name="high-availability-and-failover"></a>Haute disponibilité et basculement

Azure offre basée sur l’engagement de contrats de niveau de service (SLA), où plusieurs 9 s disponibilité est la valeur par défaut, optimisée avec la réplication locale ou basée sur une zone géographique des services. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

Pour Azure IaaS comme les machines virtuelles, basculement s’appuie sur les fonctionnalités spécifiques du système, telles que des instances de clustering de basculement et [à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Lorsque vous utilisez la plateforme Azure comme une ressource de service (PaaS), tel que [base de données SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) et [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), la plateforme gère automatiquement les basculements.

### <a name="scalability"></a>Extensibilité

Macroordinateurs généralement montent en puissance, lors de la mise à l’échelle de cloud environnement out. Azure propose une gamme de [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) et [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) tailles à répondre à vos besoins. Le cloud s’adapte également augmenter ou réduire les spécifications d’utilisateur exact de correspondance. Puissance de calcul, stockage et services [mise à l’échelle](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) sur la demande dans un modèle de facturation basée sur l’utilisation.

### <a name="storage"></a>Stockage

Dans le cloud, vous disposez d’un éventail d’options de stockage flexible, évolutive, et vous payez uniquement ce dont vous avez besoin. [Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) offre un magasin d’objets hautement scalable pour les objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie fiable et un magasin NoSQL. Les machines virtuelles bénéficient d’un stockage sur disque sécurisé et persistant grâce à des disques managés et non managés.

### <a name="backup-and-recovery"></a>Sauvegarde et récupération

Maintenance de votre propre site de récupération d’urgence peut être coûteux. Azure propose des options faciles à implémenter et rentables pour [sauvegarde](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [récupération](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et [redondance](https://docs.microsoft.com/azure/storage/common/storage-redundancy) aux niveaux locales et régionales, ou par le biais de géo-redondance.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pour les migrations mainframe

Plusieurs entités du secteur public aimerais déplacer leurs applications de macroordinateur à une plateforme flexible et plus moderne. Microsoft Azure Government est une instance séparée physiquement des services en nuage basé sur la plateforme Microsoft Azure globale mais empaqueté pour les systèmes d’administrations fédérales, nationales et locales. Il fournit des services de conformité spécifiquement pour les agences gouvernementales américaines et leurs partenaires, la protection et la sécurité de niveau international.

Azure Government d’obtenir une autorisation provisoire de fonctionnement (P-ATO) pour FedRAMP fort Impact pour les systèmes nécessitant ce type d’environnement. 

Pour commencer, téléchargez [cloud de Microsoft Azure Government pour les applications de macroordinateur](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>En savoir plus

Si vous envisagez une migration mainframe, notre très [partenaire](partner-workloads.md) écosystème est disponible pour vous aider. Pour obtenir des conseils détaillés sur le choix d’une solution de partenaire, consultez [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

Voir aussi :

-   [Migration mainframe](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Dépannage](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Démystification de macroordinateur pour la migration vers Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
