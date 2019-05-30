---
title: FAQ sur l’utilisation d’Azure Database Migration Service | Microsoft Docs
description: Découvrez les questions fréquemment posées sur l’utilisation d’Azure Database Migration Service pour effectuer des migrations de base de données.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 856eee294eaa1426bc7c06661ac62ed0f9824dcb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225343"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>FAQ sur l’utilisation d’Azure Database Migration Service

Cet article répertorie les questions fréquemment posées sur l’utilisation d’Azure Database Migration Service ainsi que les réponses associées.

### <a name="q-what-is-azure-database-migration-service"></a>Q. Qu’est-ce qu’Azure Database Migration Service ?

Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime. Le service est actuellement disponible de manière générale, les efforts de développement continus étant axés sur :

* Fiabilité et performances.
* Ajout itératif de paires source/cible.
* Investissement continu dans les migrations sans problèmes.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Q. Quelles sont les paires source-cible actuellement prises en charge par Azure Database Migration Service ?

Le service prend actuellement en charge un certain nombre de scénarios de migration. Pour obtenir la liste complète des états de chaque scénario de migration disponible, consultez l’article [État des scénarios de migration pris en charge par Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status). Autres scénarios de migration sont en version préliminaire et nécessitent de soumettre une candidature via le site d’aperçu de DMS. Pour obtenir une liste complète des scénarios en version préliminaire et pour vous inscrire pour participer à une de ces offres, consultez le [site DMS Preview](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Comment Azure Database Migration Service se distingue-t-il des autres outils de migration de bases de données Microsoft tels que l’Assistant Migration de bases de données ou l’Assistant Migration SQL Server ?

Azure Database Migration Service est la méthode recommandée pour la migration de bases de données vers Microsoft Azure à l’échelle. Pour plus d’informations sur la façon dont Azure Database Migration Service se distingue des autres outils de migration de bases de données Microsoft et pour obtenir des recommandations sur l’utilisation du service pour différents scénarios, consultez le billet du blog [Differentiating Microsoft’s Database Migration Tools and Services](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/) (Faire la différence entre les services et outils de migration de bases de données Microsoft).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Comment Azure Database Migration Service se distingue-t-il de l’offre Azure Migrate ?

Le service Azure Migrate facilite la migration de machines virtuelles locales vers IaaS Azure. Le service évalue la pertinence de la migration et le dimensionnement en fonction des performances, et fournit des estimations de coût pour l’exécution de vos machines locales dans Azure. Azure Migrate est particulièrement utile pour les migrations de type lift-and-shift des charges de travail basées sur des machines virtuelles locales vers des machines virtuelles IaaS Azure. Toutefois, contrairement à Azure Database Migration Service, Azure Migrate n’est pas une offre de service de migration de bases de données spécialisée pour les plateformes de base de données relationnelle PaaS Azure comme Azure SQL Database, Azure SQL ou Azure SQL Database Managed Instance.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>Q. Quelles versions de SQL Server Azure Database Migration Service prend-il en source ?

En ce qui concerne la migration à partir de SQL Server, Azure Database Migration Service prend en charge toutes les versions entre SQL Server 2005 et SQL Server 2017 inclus.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Quel est le résumé des étapes nécessaires pour utiliser Azure Database Migration Service afin de migrer une base de données ?

Lors d’une migration de base de données classique et simple, vous effectuez les opérations suivantes :

1. Créer des bases de données cibles.
2. Migrer le schéma de bases de données à l’aide de [l’Assistant Migration de bases de données](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3. Créer une instance Azure Database Migration Service.
4. Créer un projet de migration spécifiant les bases de données sources, les bases de données cibles et les tables à migrer.
5. Lancer le chargement complet.
6. Choisir la validation ultérieure.
7. Effectuer un basculement manuel de votre environnement de production vers la nouvelle base de données cloud.

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Q. Quelles sont les conditions préalables à remplir pour utiliser Azure Database Migration Service ?

Il existe plusieurs conditions préalables requises pour vous garantir qu’Azure Database Migration Service s’exécute correctement lors de la migration de bases de données. Certaines des conditions préalables s’appliquent à tous les scénarios (paires source-cible) pris en charge par le service, tandis que d’autres sont propres à un scénario spécifique.

Les conditions préalables associées à Azure Database Migration Service communes à tous les scénarios de migration pris en charge incluent le besoin de :

* Créer un réseau virtuel pour Azure Database Migration Service en utilisant le modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs de sources locales à l’aide [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Vérifiez que votre réseau virtuel de Azure (VNet) les règles de groupe de sécurité réseau ne bloquent pas les éléments suivants ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.

Pour obtenir la liste de toutes les conditions préalables requises pour mener à bien des scénarios de migration spécifiques à l’aide d’Azure Database Migration Service, consultez les didacticiels connexes dans la [documentation](https://docs.microsoft.com/azure/dms/dms-overview) d’Azure Database Migration Service sur docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. Comment faire pour trouver l’adresse IP d’Azure Database Migration Service afin de créer une liste d’autorisations pour les règles de pare-feu utilisées pour accéder à ma base de données source pour la migration ?

Vous devrez peut-être ajouter des règles de pare-feu autorisant Azure Database Migration Service à accéder à votre base de données source pour la migration. L’adresse IP du service est dynamique, mais si vous utilisez Express Route, cette adresse est attribuée en privé par votre réseau d’entreprise. Le moyen le plus simple d’identifier l’adresse IP appropriée est de consulter le même groupe de ressources que votre ressource Azure Database Migration Service approvisionnée pour trouver l’interface réseau associée. Généralement, le nom de la ressource d’interface réseau commence par le préfixe de la carte réseau, et est suivi d’une séquence de caractères et de chiffres unique (par exemple, NIC-jj6tnztnmarpsskr82rbndyp). En sélectionnant cette ressource d’interface réseau, vous pouvez voir l’adresse IP devant être incluse dans la liste d’autorisations sur la page du portail Azure de présentation des ressources.

Par ailleurs, vous devez peut-être inclure la source du port que SQL Server écoute sur la liste d’autorisations. Par défaut, il s’agit du port 1433, mais le SQL Server source peut être configuré pour écouter d’autres ports également. Dans ce cas, vous devez aussi inclure ces ports sur la liste d’autorisations. Vous pouvez déterminer le port écouté par SQL Server à l’aide de la requête de vue de gestion dynamique :

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Vous pouvez également déterminer le port écouté par SQL Server en interrogeant le journal des erreurs SQL Server :

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Q. Existe-t-il des recommandations pour optimiser les performances d’Azure Database Migration Service ?

Vous pouvez effectuer quelques opérations pour accélérer la migration de votre base de données à l’aide du service :

* Utilisez le niveau tarifaire Usage général avec plusieurs processeurs lorsque vous créez votre instance de service pour permettre au service de tirer parti de multiples processeurs virtuels pour la parallélisation et le transfert plus rapide des données.
* Montez temporairement en puissance votre instance cible Azure SQL Database vers la référence SKU de niveau Premium pendant l’opération de migration des données pour réduire la limitation SQL Database pouvant avoir un impact sur les activités de transfert de données lors de l’utilisation de références SKU de niveau inférieur.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Comment faire pour configurer un réseau virtuel Microsoft Azure ?

Même si plusieurs didacticiels Microsoft peuvent vous présenter le processus de configuration d’un réseau virtuel Microsoft Azure, la documentation officielle est disponible dans l’article [Réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. Pourquoi Azure Database Migration Service est-il arrêté ou indisponible ?

Si l’utilisateur arrête de manière explicite Azure Database Migration Service (DMS) ou si le service est inactif pendant une période de 24 heures, le service est dans un état arrêté ou de pause automatique. Dans tous les cas, le service est indisponible et dans un état arrêté.  Pour reprendre les migrations actives, redémarrez le service.

### <a name="q-where-can-i-leave-feedback-about-azure-database-migration-service"></a>Q. Où puis-je laisser des commentaires sur Azure Database Migration Service ?

Nous attendons vos remarques ! Veuillez n’envoyer vos commentaires et / idées que vous disposez sur le Service de Migration de base de données Azure via User Voice, [ici](https://feedback.azure.com/forums/906100-azure-database-migration-service), ou en contactant l’équipe [poser de Migrations de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation d’Azure Database Migration Service et de la mise à disponibilité régionale, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](dms-overview.md).
