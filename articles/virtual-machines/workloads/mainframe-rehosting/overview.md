---
title: Mainframe réhébergement sur des machines virtuelles
description: Ré-héberger vos charges de travail mainframe tels que des systèmes IBM Z à l’aide de machines virtuelles (VM) sur Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487449"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe réhébergement sur des machines virtuelles

Migration des charges de travail à partir de macroordinateur environnements vers le cloud vous permet de moderniser votre infrastructure et souvent réduire les coûts. De nombreuses charges de travail peuvent être transférées vers Azure suite à quelques modifications mineures du code (mettre à jour le nom des bases de données, par exemple).

En règle générale, le terme *mainframe* signifie un système informatique de grande taille. Plus précisément, la grande majorité actuellement en cours d’utilisation sont des serveurs de système IBM Z ou systèmes compatibles IBM qui s’exécutent MVS, DOS, VSE, OS/390 ou z/OS.

Une machine virtuelle (VM) est utilisée pour isoler et gérer les ressources pour une application spécifique sur une seule instance. Macroordinateurs tels que IBM z/OS utilisent des Partitions logiques (partitions logiques) à cet effet. Un grand système peut utiliser une partition logique pour une région CICS avec des programmes COBOL associés et une partition logique distincte pour la base de données IBM Db2. Classique [application multi-niveau sur Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) déploie les machines virtuelles Azure dans un réseau virtuel qui peut être subdivisé en sous-réseaux pour chaque niveau.

Les machines virtuelles Azure peuvent exécuter des environnements de macroordinateur émulation et les compilateurs qui prennent en charge les scénarios de lift-and-shift. Développement et les tests sont souvent parmi les premières charges de travail à migrer à partir d’un grand système vers un environnement de développement/test Azure. Les composants de serveur communs que vous pouvez émuler incluent le processus de transaction en ligne (OLTP), batch et systèmes d’ingestion de données comme le montre l’illustration suivante.

![Les environnements d’émulation sur Azure vous permettent de vous permettent d’exécuter des systèmes basé sur z/OS.](media/01-overview.png)

Certaines charges de travail mainframe peuvent être migrées vers Azure en toute simplicité relative, tandis que d’autres peuvent être réhébergés sur Azure à l’aide d’une solution de partenaire. Pour obtenir des instructions détaillées sur le choix d’une solution de partenaire, le [centre de Migration de macroordinateurs Azure](https://azure.microsoft.com/migration/mainframe/) peut aider.

## <a name="mainframe-migration"></a>Migration de mainframe

Réhébergement, régénérer, remplacer ou mettre hors service ? IaaS ou PaaS ? Pour déterminer la bonne stratégie de migration pour votre application de macroordinateur, consultez le [migration Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) guide dans le centre d’Architecture Azure.

## <a name="micro-focus-rehosting-platform"></a>Plateforme de réhébergement Micro Focus

Micro Focus Enterprise Server est un des plus grand macroordinateur réhébergement plateformes disponibles. Vous pouvez l’utiliser exécuter vos charges de travail z/OS sur un x86 moins coûteux platform sur Azure.

Pour commencer :

- [Installation de serveur d’entreprise et développeur d’entreprise sur Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configuration de CICS BankDemo pour développeur d’entreprise sur Azure](./microfocus/demo.md)
- [Exécuter le serveur d’entreprise dans un conteneur Docker sur Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame sur Azure

TmaxSoft OpenFrame est une solution de réhébergement de macroordinateur populaire utilisée dans les scénarios de lift-and-shift. Un environnement OpenFrame sur Azure est approprié pour le développement, de démonstrations, de test ou de charges de travail de production.

Pour commencer :

- [Prise en main TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Téléchargez le livre électronique](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12.0

IBM Z Development et environnement de Test (IBM zD & T) configure un environnement hors production sur Azure que vous pouvez utiliser pour le développement, tests et démonstrations d’applications basées sur le système d’exploitation z.

L’environnement d’émulation sur Azure peut héberger différents types d’instances de Z via Application développeurs contrôlé Distributions (ADCDs). Vous pouvez exécuter zD & T Personal Edition, zD & T Parallel Sysplex et zD & T Enterprise Edition sur Azure et Azure Stack.

Pour commencer :

- [Configurer IBM zD & 12.0 T sur Azure](./ibm/install-ibm-z-environment.md)
- [Configurer ADCD sur zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale sur Azure

L’environnement de pureScale IBM DB2 fournit un cluster de base de données pour Azure. Il n’est pas identique à l’environnement d’origine, mais elle offre similaire de disponibilité et de mise à l’échelle comme IBM DB2 pour z/OS est en cours d’exécution dans une configuration de Parallel Sysplex.

Pour commencer, consultez [pureScale IBM DB2 sur Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considérations

Lorsque vous migrez des charges de travail mainframe vers l’infrastructure Azure en tant que service (IaaS), vous pouvez choisir parmi plusieurs types de ressources informatiques à la demande et évolutives, y compris les machines virtuelles Azure. Azure propose une gamme de [Linux](/azure/virtual-machines/linux/overview) et [Windows](/azure/virtual-machines/windows/overview) machines virtuelles.

### <a name="compute"></a>Calcul

La puissance de calcul Azure les comparaisons à la capacité d’un macroordinateur. Si vous envisagez de migrer d’une charge de travail mainframe sur Azure, comparez la métrique de macroordinateur d’un million d’instructions par seconde (MIPS) pour les processeurs virtuels. 

Découvrez comment [déplacer le calcul de macroordinateur vers Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Haute disponibilité et basculement

Azure propose des contrats de niveau de service basée sur l’engagement (SLA). Multiple de disponibilité est la valeur par défaut, et les contrats SLA peuvent être optimisées avec la réplication locale ou basée sur une zone géographique des services. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

Avec Azure IaaS comme une machine virtuelle, fonctions spécifiques du système fournissent la prise en charge du basculement — par exemple, le clustering de basculement instances et [à haute disponibilité](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Lorsque vous utilisez la plateforme Azure comme une ressource de service (PaaS), la plateforme gère automatiquement basculement. Exemples [base de données SQL Azure](/azure/sql-database/sql-database-technical-overview) et [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Extensibilité

En règle générale, les ordinateurs mainframe procèdent à un scale-up, tandis que les environnements cloud procèdent à un scale-out. Azure propose une gamme de [Linux](/azure/virtual-machines/linux/sizes) et [Windows](/azure/virtual-machines/windows/sizes) tailles à répondre à vos besoins. Le cloud s’adapte également augmenter ou réduire les spécifications d’utilisateur exact de correspondance. Puissance de calcul, stockage et services [mise à l’échelle](/azure/architecture/best-practices/auto-scaling) sur la demande dans un modèle de facturation basée sur l’utilisation.

### <a name="storage"></a>Stockage

Dans le cloud, vous disposez d’un éventail d’options de stockage flexible, évolutive, et vous payez uniquement ce dont vous avez besoin. [Stockage Azure](/azure/storage/common/storage-introduction) offre un magasin d’objets hautement scalable pour les objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie fiable et un magasin NoSQL. Les machines virtuelles bénéficient d’un stockage sur disque sécurisé et persistant grâce à des disques managés et non managés.

Découvrez comment [déplacer le stockage de macroordinateur à Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Sauvegarde et récupération

Maintenance de votre propre site de récupération d’urgence peut être coûteux. Azure propose des options faciles à implémenter et rentables pour [sauvegarde](/azure/backup/backup-introduction-to-azure-backup), [récupération](/azure/site-recovery/site-recovery-overview), et [redondance](/azure/storage/common/storage-redundancy) aux niveaux locales et régionales, ou par le biais de géo-redondance.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pour les migrations mainframe

Plusieurs entités du secteur public aimerais déplacer leurs applications de macroordinateur à une plateforme flexible et plus moderne. Microsoft Azure Government est une instance séparée physiquement de la plateforme Microsoft Azure globale, empaquetées pour systèmes gouvernementaux fédérales, nationales et locales. Il fournit des services de conformité spécifiquement pour les agences gouvernementales américaines et leurs partenaires, la protection et la sécurité de niveau international.

Azure Government d’obtenir une autorisation provisoire de fonctionnement (P-ATO) pour FedRAMP fort Impact pour les systèmes nécessitant ce type d’environnement.

Pour commencer, téléchargez [cloud de Microsoft Azure Government pour les applications de macroordinateur](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Étapes suivantes

Demandez à notre [partenaires](partner-workloads.md) pour vous aider à migrer ou ré-héberger vos applications mainframe. Pour obtenir des instructions détaillées sur le choix d’une solution de partenaire, consultez le [Alliance de modernisation de plateforme](https://www.platformmodernization.org/pages/mainframe.aspx) site Web.

Voir aussi :

- [Livres blancs sur des sujets de macroordinateur](mainframe-white-papers.md)
- [Migration mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Dépannage](/azure/virtual-machines/troubleshooting/)
- [Démystification de macroordinateur pour la migration vers Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
