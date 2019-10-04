---
title: Protéger des services de données et de stockage Azure dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos données et le service SQL Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: memildin
ms.openlocfilehash: 80611fe0c37af7dfd27b561186f3e967ad3159b4
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201044"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Protéger des services de données et de stockage Azure dans Azure Security Center
Cette rubrique montre comment afficher et mettre en œuvre des recommandations en matière de sécurité pour les ressources de stockage et de données. Azure Security Center a trouvé ces recommandations lors de l’analyse de l’état de sécurité de vos ressources Azure.

## <a name="view-your-data-security-information"></a>Afficher les informations sur la sécurité de vos données

1. Dans la section **Hygiène de sécurité de la ressource**, cliquez sur **Ressources de données et de stockage**.

   ![Ressources de données et de stockage](./media/security-center-monitoring/click-data.png)

    La page **Sécurité des données** s’ouvre avec des recommandations pour les ressources de données.

     ![Ressources de données](./media/security-center-monitoring/sql-overview.png)

À partir de cette page, vous pouvez :

* Cliquer sur l’onglet **Vue d’ensemble** pour lister toutes les recommandations relatives aux ressources de données. 
* Cliquer sur chaque onglet et afficher les recommandations par type de ressource.

    > [!NOTE]
    > Pour plus d’informations sur le chiffrement du stockage, consultez la page [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Appliquer une recommandation sur une ressource de données

1. À partir d’un onglet de ressource, cliquez sur une ressource. La page d’informations s’ouvre et affiche les recommandations à appliquer.

    ![Informations sur la ressource](./media/security-center-monitoring/sql-recommendations.png)

2. Cliquez sur une recommandation. La page Recommandation s’ouvre et affiche les **Étapes de correction** à effectuer pour implémenter la recommandation.

   ![Étapes de correction](./media/security-center-monitoring/remediate1.png)

3. Cliquez sur **Entreprendre une action**. La page de paramètres de ressource s’affiche.

    ![Activer une recommandation](./media/security-center-monitoring/remediate2.png)

4. Effectuez les **Étapes de correction** et cliquez sur **Enregistrer**.

## <a name="data-and-storage-recommendations"></a>Suggestions relatives aux données et au stockage

|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|Compte de stockage|20|La sécurisation du transfert vers des comptes de stockage doit être activée|L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). Le protocole HTTPS garantit l’authentification entre le serveur et le service, et protège les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|Redis|20|Seules les connexions sécurisées à votre cache Redis doivent être activées|Activer les connexions établies uniquement par le biais de SSL au cache Azure pour Redis. L'utilisation de connexions sécurisées garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|SQL|15|Transparent Data Encryption sur les bases de données SQL doit être activé|Activer le chiffrement transparent des données pour protéger les données au repos et respecter les exigences de conformité.|
|SQL|15|L’audit des serveurs SQL doit être activé|Activer l’audit sur les serveurs Azure SQL. (Service Azure SQL uniquement. N’inclut pas SQL en cours d’exécution sur vos machines virtuelles.)|
|Data Lake Analytics|5\.|Les journaux de diagnostic dans Data Lake Analytics doivent être activés|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Data Lake Store|5\.|Les journaux de diagnostic dans Azure Data Lake Store doivent être activés|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|SQL|30|Les vulnérabilités sur vos bases de données SQL doivent être éliminées|L’évaluation de la vulnérabilité SQL analyse les vulnérabilités de la sécurité dans votre base de données et expose tout manquement aux bonnes pratiques, comme les erreurs de configuration, les autorisations excessives et les données sensibles non protégées. La résolution des vulnérabilités détectées peut améliorer considérablement le niveau de sécurité de votre base de données.|
|SQL|20|Provisionner un administrateur Azure AD pour SQL Server|Approvisionner un administrateur Azure AD pour votre serveur SQL afin d’activer l’authentification Azure AD. L’authentification Azure AD permet une gestion simplifiée des autorisations et une gestion centralisée des utilisateurs de bases de données et d’autres services Microsoft.|
|Compte de stockage|15|L’accès aux comptes de stockage avec pare-feu et configurations de réseau virtuel doit être limité|Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Au lieu de cela, configurer les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, vous pouvez accorder l’accès au trafic à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques.|
|Compte de stockage|1|Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager|Profitez des améliorations apportées à Azure Resource Manager v2 pour renforcer la sécurité de vos comptes de stockage : contrôle d’accès plus puissant, audit amélioré, déploiement et gouvernance basés sur Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité, et ainsi de suite.|

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)

Pour plus d’informations sur Security Center, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
