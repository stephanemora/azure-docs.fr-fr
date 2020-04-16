---
title: Comment créer une instance gérée
description: Découvrez comment configurer et gérer une instance managée Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: b56375388f6293d27bcd2f2548d8b20205a92b15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638030"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Utiliser une instance managée dans Azure SQL Database

Dans cet article, vous trouverez des guides, des scripts et des explications pour vous aider à gérer et à configurer votre instance gérée.

## <a name="migration"></a>Migration

- [Migrer vers une instance gérée](sql-database-managed-instance-migrate.md) : découvrez les processus et outils de migration recommandés pour la migration vers une instance gérée.

- [Migrer le certificat TDE vers une instance gérée](sql-database-managed-instance-migrate-tde-certificate.md) : si votre base de données SQL Server est protégée par le chiffrement transparent des données (TDE), vous devez migrer le certificat qu’une instance gérée peut utiliser pour déchiffrer la sauvegarde que vous souhaitez restaurer dans Azure.

## <a name="network-configuration"></a>Configuration réseau

- [Déterminer la taille d’un sous-réseau d’instance géré](sql-database-managed-instance-determine-size-vnet-subnet.md) : Managed Instance est placé dans un sous-réseau dédié qui ne peut pas être redimensionné une fois que vous y avez ajouté les ressources. Par conséquent, vous devez calculer la plage d’adresses IP nécessaire pour le sous-réseau en fonction du nombre et des types d’instances que vous souhaitez déployer dans le sous-réseau.
- [Créer un réseau virtuel et un sous-réseau pour une instance gérée](sql-database-managed-instance-create-vnet-subnet.md) : le réseau virtuel et le sous-réseau Azure sur lesquels vous voulez déployer vos instances gérées doivent être configurés conformément à la [configuration réseau décrite ici](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Dans ce guide, vous trouverez le moyen le plus simple de créer et configurer correctement votre réseau virtuel et votre sous-réseau pour les instances gérées.
- [Configurer un réseau virtuel et un sous-réseau existants pour une instance gérée](sql-database-managed-instance-configure-vnet-subnet.md) : si vous souhaitez configurer votre réseau virtuel et votre sous-réseau pour y déployer des instances gérées, vous trouverez ici le script qui vérifie la [configuration réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements) et configure votre sous-réseau en fonction des exigences.
- [Configurer les DNS personnalisés](sql-database-managed-instance-custom-dns.md) : vous devez configurer des DNS personnalisés si vous souhaitez accéder à des ressources externes sur les domaines personnalisés depuis votre instance gérée via le serveur lié des profils de courrier de la base de données.
- [Synchroniser la configuration réseau](sql-database-managed-instance-sync-network-configuration.md) : il peut arriver que bien que votre [application soit intégrée à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md), vous ne puissiez pas établir de connexion à une instance gérée. Vous pouvez par exemple essayer d’actualiser la configuration de mise en réseau pour votre plan de service.
- [Rechercher l'adresse IP du point de terminaison de gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md) : une instance gérée utilise le point de terminaison public à des fins de gestion. Vous pouvez déterminer l’adresse IP du point de terminaison de gestion avec le script décrit ici.
- [Vérifier la protection de pare-feu intégré](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) : une instance gérée est protégée par un pare-feu intégré qui autorise uniquement le trafic sur les ports nécessaires. Vous pouvez vérifier les règles du pare-feu intégré avec script décrit dans ce guide.
- [Connecter des applications](sql-database-managed-instance-connect-app.md) : une instance gérée est placée dans votre propre réseau virtuel Azure avec une adresse IP privée. Découvrez les différents modèles de connexion des applications à votre instance gérée.

## <a name="feature-configuration"></a>Configuration des fonctionnalités

- La [réplication transactionnelle](replication-with-sql-database-managed-instance.md) vous permet de répliquer vos données entre des instances gérées ou entre un SQL Server local et une instance gérée et vice-versa. Découvrez comment utiliser et configurer la réplication de transaction dans ce guide.
- [Configurer la détection des menaces](sql-database-managed-instance-threat-detection.md) : la [détection des menaces](sql-database-threat-detection-overview.md) est une fonctionnalité intégrée d’Azure SQL Database qui détecte les attaques potentielles comme l’injection SQL ou l’accès à partir d’emplacements suspects. Dans ce guide, vous pouvez apprendre à activer et à configurer la [détection des menaces](sql-database-threat-detection-overview.md) pour une instance gérée.
- La [création d’alertes](sql-database-managed-instance-alerts.md) vous permet de configurer des alertes sur des métriques supervisées, telles que l’utilisation du processeur, la consommation de l’espace de stockage, les IOPS, etc. pour l’instance managée. Dans ce guide, vous allez apprendre à activer et à configurer des alertes pour l’instance managée.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [guides de procédures pour les bases de données uniques](sql-database-howto-single-database.md)
