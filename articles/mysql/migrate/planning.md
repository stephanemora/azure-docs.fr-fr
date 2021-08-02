---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Planification
description: Une zone d’atterrissage Azure est l’environnement cible défini comme l’emplacement final d’un projet de migration vers le cloud.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: 1cd9d78fab41305fc6cf4fc814a39a999502c795
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958598"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-planning"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Planification

### <a name="landing-zone"></a>Zone d’atterrissage

Une [zone d’atterrissage Azure](/azure/cloud-adoption-framework/ready/landing-zone/) est l’environnement cible défini comme l’emplacement final d’un projet de migration vers le cloud. Dans la plupart des projets, la zone d’atterrissage doit être scriptée par l’intermédiaire de modèles ARM pour sa configuration initiale. Enfin, elle doit être personnalisée avec PowerShell ou le portail Azure pour répondre aux besoins des charges de travail.

Comme la société WWI est basée à San Francisco, toutes les ressources de la zone d’atterrissage Azure ont été créées dans la région `US West 2`. Les ressources suivantes ont été créées pour prendre en charge la migration :

  - [Azure Database pour MySQL](../quickstart-create-mysql-server-database-using-azure-portal.md)

  - [Azure Database Migration Service (DMS)](../../dms/quickstart-create-data-migration-service-portal.md)

  - [ExpressRoute](../../expressroute/expressroute-introduction.md)

  - [Réseau virtuel Microsoft Azure](../../virtual-network/quick-create-portal.md) avec [conception hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) et établissement des [appairages de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md) correspondants

  - [App Service](../../app-service/overview.md)

  - [Application Gateway](../../load-balancer/quickstart-load-balancer-standard-internal-portal.md?tabs=option-1-create-internal-load-balancer-standard)

  - [Points de terminaison privés](../../private-link/private-endpoint-overview.md) pour les services d’application et l’instance MySQL

> [!NOTE]
> Dans le cadre de ce guide, deux modèles ARM (un avec des points de terminaison privés, l’autre sans) ont été fournis afin de déployer une zone d’atterrissage Azure potentielle pour un projet de migration MySQL. Le modèle ARM avec points de terminaison privés offre un scénario plus sécurisé et plus proche de la production. Une configuration manuelle supplémentaire de la zone d’atterrissage Azure peut être nécessaire, selon les besoins.

### <a name="networking"></a>Mise en réseau

Transférer les données du système source vers Azure Database pour MySQL de manière rapide et optimale est un élément essentiel à prendre en compte dans un projet de migration. De petites connexions peu fiables peuvent obliger les administrateurs à redémarrer la migration plusieurs fois jusqu’à l’obtention d’un résultat satisfaisant. Le redémarrage des migrations en raison de problèmes de réseau peut entraîner un gaspillage d’efforts.

Prenez le temps de comprendre et d’évaluer la connectivité réseau entre les environnements source, d’outil et de destination. Dans certains cas, il peut être utile de mettre à niveau la connectivité Internet ou de configurer une connexion ExpressRoute de l’environnement local vers Azure. Une fois que la connectivité entre l’environnement local et Azure a été créée, l’étape suivante consiste à valider que l’outil de migration sélectionné peut se connecter de la source à la destination.

L’emplacement de l’outil de migration détermine les exigences en matière de connectivité réseau. Comme indiqué dans le tableau ci-dessous, l’outil de migration sélectionné doit pouvoir se connecter à la fois à l’ordinateur local et à Azure. Azure doit être configuré pour accepter uniquement le trafic provenant de l’emplacement de l’outil de migration.

| Outil de migration                             | Type              | Emplacement        | Configuration requise du réseau entrant                                    | Configuration requise du réseau sortant                          |
|--------------------------------------------|-------------------|-----------------|-----------------------------------------------------------------|--------------------------------------------------------|
| **Database Migration Service (DMS)**           | Hors connexion           | Azure           | Autoriser 3306 à partir d’une adresse IP externe                                     | Chemin d’accès pour se connecter à l’instance de base de données Azure MySQL |
| **Import/Export (MySQL Workbench, mysqldump)** | Hors connexion           | Local     | Autoriser 3306 à partir d’une adresse IP interne                                     | Chemin d’accès pour se connecter à l’instance de base de données Azure MySQL |
| **Import/Export (MySQL Workbench, mysqldump)** | Hors connexion           | Azure VM        | Autoriser 3306 à partir d’une adresse IP externe                                     | Chemin d’accès pour se connecter à l’instance de base de données Azure MySQL |
| **mydumper/myloader**                          | Hors connexion           | Local     | Autoriser 3306 à partir d’une adresse IP interne                                     | Chemin d’accès pour se connecter à l’instance de base de données Azure MySQL |
| **mydumper/myloader**                          | Hors connexion           | Azure VM        | Autoriser 3306 à partir d’une adresse IP externe                                     | Chemin d’accès pour se connecter à l’instance de base de données Azure MySQL |
| **binlog**                                     | Hors connexion           | Local     | Autoriser 3306 à partir d’une adresse IP externe ou d’une adresse IP privée via des points de terminaison privés | Chemin d’accès pour chaque serveur de réplication vers le maître       |

D’autres considérations relatives à la mise en réseau sont à prendre en compte :

  - Le service DMS situé dans un réseau virtuel se voit attribuer une [IP publique dynamique](../../dms/faq.md#setup). Au moment de la création, vous pouvez placer le service à l’intérieur d’un réseau virtuel qui dispose d’une connectivité via une connexion [ExpressRoute](../../expressroute/expressroute-introduction.md) ou sur [un VPN site à site](../../vpn-gateway/tutorial-site-to-site-portal.md).

  - Si vous utilisez une machine virtuelle Azure pour exécuter les outils de migration, attribuez-lui une IP publique et autorisez-la uniquement à se connecter à l’instance MySQL locale.

  - Les pare-feu sortants doivent garantir la connectivité sortante à Azure Database pour MySQL. Les adresses IP de la passerelle MySQL sont disponibles sur la page[Architecture de connectivité dans Azure Database pour MySQL](../concepts-connectivity-architecture.md#azure-database-for-mysql-gateway-ip-addresses).

### <a name="ssltls-connectivity"></a>Connectivité SSL/TLS

Outre les implications pour les applications résultant de la migration vers une communication SSL, les types de connexion SSL/TLS sont également des éléments qui doivent être pris en compte. Après avoir créé la base de données Azure Database pour MySQL, vérifiez les paramètres SSL et lisez l’article [Connectivité SSL/TLS dans Azure Database pour MySQL](../concepts-ssl-connection-security.md) pour comprendre la manière dont les paramètres TLS peuvent influer sur la position de sécurité.

> [!Important]
> Prêtez attention à l’exclusion figurant sur la page. L’application de la version TLS n’est pas activée par défaut. Une fois le protocole TLS activé, le seul moyen de le désactiver consiste à réactiver le protocole SSL.

### <a name="wwi-scenario"></a>Scénario WWI

L’équipe responsable du cloud de WWI a créé les ressources nécessaires de la zone d’atterrissage Azure dans un groupe de ressources spécifique pour l’instance Azure Database pour MySQL. Pour créer la zone d’atterrissage, WWI a décidé de scripter la configuration et le déploiement à l’aide de modèles ARM. En utilisant des modèles ARM, la société peut rapidement détruire et reconfigurer l’environnement, le cas échéant.

Dans le cadre du modèle ARM, toutes les connexions entre les réseaux virtuels sont configurées à l’aide d’un Peering dans une architecture hub-and-spoke. La base de données et l’application sont placées dans des réseaux virtuels distincts. Une instance d’Azure Application Gateway est placée devant le service d’application pour permettre à ce dernier d’être isolé d’Internet. Azure App Service se connecte à Azure Database pour MySQL à l’aide d’un point de terminaison privé.

À l’origine, WWI souhaitait tester une migration en ligne, mais la configuration réseau requise pour que DMS se connecte à son environnement local rendait cette opération irréalisable. WWI a choisi d’effectuer une migration hors connexion à la place. L’outil MySQL Workbench a été utilisé pour exporter les données locales, puis pour importer les données dans l’instance Azure Database pour MySQL.

### <a name="planning-checklist"></a>Planification d'une liste de vérification

  - Préparez la zone d’atterrissage Azure. Envisagez d’utiliser le déploiement de modèles ARM au cas où l’environnement doive être détruit et reconstruit rapidement.

  - Vérifiez la configuration de la mise en réseau. La vérification doit inclure : la connectivité, la bande passante, la latence et les configurations de pare-feu.

  - Déterminez si vous allez utiliser la stratégie de migration des données en ligne ou hors connexion.

  - Décidez de la stratégie du certificat SSL.  


> [!div class="nextstepaction"]
> [Méthodes de migration](./migration-methods.md)