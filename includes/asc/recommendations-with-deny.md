---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803560"
---
- L’accès aux comptes de stockage avec configurations de pare-feu et de réseau virtuel doit être limité
- Les variables de compte Automation doivent être chiffrées
- Azure Cache pour Redis doit se trouver dans un réseau virtuel
- Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)
- Azure Spring Cloud doit utiliser l’injection de réseau
- Les comptes Cognitive Services doivent activer le chiffrement des données avec une clé gérée par le client (CMK)
- Les limites de mémoire et du processeur du conteneur doivent être appliquées
- Les images conteneur doivent être déployées à partir de registres approuvés uniquement
- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- Tout conteneur avec une élévation des privilèges doit être évité
- Éviter les conteneurs partageant des espaces de noms d’hôte sensibles
- Les conteneurs doivent écouter uniquement sur les ports autorisés
- Le système de fichiers racine immuable (en lecture seule) doit être appliqué pour les conteneurs
- Les clés Key Vault doivent avoir une date d’expiration
- Les secrets Key Vault doivent avoir une date d’expiration
- La protection contre la suppression définitive doit être activée sur les coffres de clés
- La suppression réversible doit être activée sur les coffres de clés
- Les fonctionnalités Linux à privilèges minimum doivent être appliquées pour les conteneurs
- Seules les connexions sécurisées à votre cache Redis doivent être activées
- Le remplacement ou la désactivation du profil AppArmor des conteneurs doit être limité
- Les conteneurs privilégiés doivent être évités
- L’exécution des conteneurs en tant qu’utilisateur racine doit être évitée
- La sécurisation du transfert vers des comptes de stockage doit être activée
- La propriété ClusterProtectionLevel doit être définie sur EncryptAndSign pour les clusters Service Fabric
- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client
- Les services doivent écouter uniquement sur les ports autorisés
- L’accès public au compte de stockage doit être interdit
- Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager
- Les comptes de stockage doivent utiliser des règles de réseau virtuel pour restreindre l’accès au réseau
- L’utilisation du réseau hôte et des ports doit être limitée
- L’utilisation de montages de volume HostPath de pod doit être limitée à une liste connue pour restreindre l’accès aux nœuds à partir de conteneurs compromis
- La période de validité des certificats stockés dans Azure Key Vault ne doit pas dépasser 12 mois
- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager
- Le pare-feu d’applications web (WAF) doit être activé pour Application Gateway
- Web Application Firewall (WAF) doit être activé pour le service Azure Front Door Service

