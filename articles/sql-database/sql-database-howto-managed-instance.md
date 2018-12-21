---
title: Configurer Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment configurer et gérer Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439516"
---
# <a name="how-to-use-managed-instance"></a>Utiliser Managed Instance

Dans cette section, vous trouverez des guides, des scripts et des explications pour vous aider à gérer et à configurer votre base de données Azure SQL Database Managed Instance.

## <a name="migration"></a>Migration

- [Migrer vers Managed instance](sql-database-managed-instance-migrate.md) : découvrez les processus de migration et les outils recommandés pour la migration vers Managed Instance.

- [Migrer le certificat TDE vers Managed Instance](sql-database-managed-instance-migrate-tde-certificate.md) : si votre base de données SQL Server est protégée par le chiffrement transparent des données (TDE), vous devez migrer le certificat que Managed Instance peut utiliser pour déchiffrer la sauvegarde que vous souhaitez restaurer dans Azure.

## <a name="network-configuration"></a>Configuration réseau

- [Déterminer la taille du sous-réseau Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md) : Managed Instance est placé dans un sous-réseau dédié qui ne peut pas être redimensionné une fois que vous y avez ajouté les ressources. Par conséquent, vous devez calculer la plage d’adresses IP nécessaire pour le sous-réseau en fonction du nombre et des types d’instances que vous souhaitez déployer dans le sous-réseau.
- [Créer un réseau virtuel et un sous-réseau pour Managed Instance](sql-database-managed-instance-create-vnet-subnet.md) : le réseau virtuel et le sous-réseau Azure sur lesquels vous voulez déployer vos instances Managed Instance doivent être configurés conformément à la [configuration réseau décrite ici](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Dans ce guide, vous trouverez le moyen le plus simple de créer et configurer correctement votre réseau virtuel et votre sous-réseau pour Managed Instance.
- [Configurer un réseau virtuel et un sous-réseau existants pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md) : si vous souhaitez configurer votre réseau virtuel et votre sous-réseau pour y déployer des instances Managed Instance, vous trouverez ici le script qui vérifie la [configuration réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements) et configure votre sous-réseau en fonction des exigences.
- [Configurer les DNS personnalisés](sql-database-managed-instance-custom-dns.md) : vous devez configurer des DNS personnalisés si vous souhaitez accéder à des ressources externes sur les domaines personnalisés depuis votre Managed Instance via le serveur lié des profils de courrier de la base de données.
- [Synchroniser la configuration réseau](sql-database-managed-instance-sync-network-configuration.md) : il peut arriver que bien que votre [application soit intégrée à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md) vous ne puissiez pas établir de connexion à une instance Managed Instance. Vous pouvez par exemple essayer d’actualiser la configuration de mise en réseau pour votre plan de service.
- [Rechercher l’adresse IP de point de terminaison de gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md) : Managed Instance utilise le point de terminaison public uniquement pour la gestion. Vous pouvez déterminer l’adresse IP du point de terminaison de gestion avec le script décrit ici.
- [Vérifier la protection de pare-feu intégré](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) : Managed Instance est protégé par un pare-feu intégré qui autorise uniquement le trafic sur les ports nécessaires. Vous pouvez vérifier les règles du pare-feu intégré avec script décrit dans ce guide.
- [Connecter des applications](sql-database-managed-instance-connect-app.md) : Managed Instance est placé dans votre propre réseau virtuel Azure avec une adresse IP privée. Découvrez les différents modèles de connexion des applications à votre instance Managed Instance.

## <a name="feature-configuration"></a>Configuration des fonctionnalités

- La [réplication transactionnelle](replication-with-sql-database-managed-instance.md) vous permet de répliquer vos données entre instances Managed Instance ou entre une SQL Server local et Managed Instance et vice-versa. Découvrez comment utiliser et configurer la réplication de transaction dans ce guide.
- [Configurer la détection des menaces](sql-database-managed-instance-threat-detection.md) : la [détection des menaces](sql-database-threat-detection-overview.md) est une fonctionnalité intégrée d’Azure SQL Database qui détecte les attaques potentielles comme l’injection SQL ou l’accès à partir d’emplacements suspects. Dans ce guide, vous pouvez apprendre à activer et à configurer la [détection des menaces](sql-database-threat-detection-overview.md) pour Managed Instance.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [guides de procédures dans la base de données unique](sql-database-howto-single-database.md)