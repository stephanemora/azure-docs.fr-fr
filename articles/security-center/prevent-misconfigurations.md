---
title: Comment empêcher les configurations incorrectes avec Azure Security Center
description: Découvrez comment utiliser les options « Appliquer » et « Refuser » de Security Center dans les pages des détails des recommandations
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558185"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser

Les erreurs de configuration de la sécurité sont à l’origine de la plupart des incidents. Security Center vous permet désormais de *prévenir* les erreurs de configurations des nouvelles ressources en ce qui concerne les recommandations spécifiques. 

Cette fonctionnalité peut vous aider à sécuriser vos charges de travail et à stabiliser votre degré de sécurisation.

La mise en œuvre d’une configuration sécurisée, basée sur une recommandation spécifique, est proposée en deux modes :

- En utilisant l’effet **Refuser** d’Azure Policy, vous pouvez empêcher la création de ressources non saines
- À l’aide de l’option **Appliquer** , vous pouvez tirer parti de l’effet **DeployIfNotExist** de la stratégie Azure et corriger automatiquement les ressources non saines lors de leur création

Cette option est disponible en haut de la page des détails des ressources pour les recommandations de sécurité sélectionnées (voir [Recommandations avec les options refuser/appliquer](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Empêcher la création de la ressource

1. Ouvrez la recommandation que vos nouvelles ressources doivent respecter, puis sélectionnez le bouton **Refuser** en haut de la page.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Page de recommandation avec le bouton Refuser mis en surbrillance":::

    Le volet de configuration s’ouvre et répertorie les options d’étendue. 

1. Définissez l’étendue en sélectionnant l’abonnement ou le groupe d’administration approprié.

    > [!TIP]
    > Vous pouvez utiliser les trois points à la fin de la ligne pour modifier un abonnement unique, ou utiliser les cases à cocher pour sélectionner plusieurs abonnements ou groupes, puis sélectionner **Remplacer par Refuser**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Définition de l’étendue pour Refuser Azure Policy":::


## <a name="enforce-a-secure-configuration"></a>Appliquer une configuration sécurisée

1. Ouvrez la recommandation pour laquelle vous allez déployer un déploiement de modèle si les nouvelles ressources ne correspondent pas, puis sélectionnez le bouton **Appliquer** en haut de la page.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Page de recommandation avec le bouton Appliquer mis en surbrillance":::

    Le volet de configuration s’ouvre avec toutes les options de configuration de la stratégie. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Appliquer des options de configuration":::

1. Définissez l’étendue, le nom de l’affectation et d’autres options pertinentes.

1. Sélectionnez **Revoir + créer**.

## <a name="recommendations-with-denyenforce-options"></a>Recommandations avec les options Refuser/Appliquer

Ces recommandations peuvent être utilisées avec l’option **Refuser** :

- L’accès aux comptes de stockage avec configurations de pare-feu et de réseau virtuel doit être limité
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
- Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager
- Les comptes de stockage doivent utiliser des règles de réseau virtuel pour restreindre l’accès au réseau
- L’utilisation du réseau hôte et des ports doit être limitée
- L’utilisation de montages de volume HostPath de pod doit être limitée à une liste connue pour restreindre l’accès aux nœuds à partir de conteneurs compromis
- La période de validité des certificats stockés dans Azure Key Vault ne doit pas dépasser 12 mois
- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager
- Le pare-feu d’applications web (WAF) doit être activé pour Application Gateway
- Web Application Firewall (WAF) doit être activé pour le service Azure Front Door Service

Ces recommandations peuvent être utilisées avec l’option **Appliquer** :

- L’audit sur SQL Server doit être activé
- Sauvegarde Azure doit être activé pour les machines virtuelles
- Azure Defender pour SQL doit être activé sur vos serveurs SQL
- Les journaux de diagnostic dans Azure Stream Analytics doivent être activés
- Les journaux de diagnostic doivent être activés dans les comptes Batch
- Les journaux de diagnostic dans Data Lake Analytics doivent être activés
- Les journaux de diagnostic dans Event Hub doivent être activés.
- Les journaux de diagnostic dans Key Vault doivent être activés.
- Les journaux de diagnostic dans Logic Apps doivent être activés.
- Les journaux de diagnostic dans les services Search doivent être activés.
- Les journaux de diagnostic dans Service Bus doivent être activés