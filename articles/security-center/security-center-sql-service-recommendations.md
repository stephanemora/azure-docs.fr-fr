---
title: Protection des données et du service SQL Azure dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos données et le service SQL Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332462"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Protection des données et du service SQL Azure dans Azure Security Center
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles, mise en réseau, SQL et données et applications.


### <a name="monitor-data-security"></a>Surveiller la sécurité des données

Lorsque vous cliquez sur **Sécurité des données** dans la section **Prévention**, le panneau **Ressources de données** s’ouvre et affiche des recommandations pour SQL et le stockage. Il contient également des [recommandations](security-center-sql-service-recommendations.md) pour l’état général de la base de données. Pour plus d’informations sur le chiffrement du stockage, consultez [Enable encryption for Azure storage account in Azure Security Center (Activer le chiffrement pour le compte de stockage Azure dans Azure Security Center)](security-center-enable-encryption-for-storage-account.md).

![Ressources de données](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Sous **Recommandations SQL**, vous pouvez cliquer sur une recommandation et obtenir des informations sur les actions permettant de résoudre un problème. L’exemple suivant montre le détail de la recommandation **Database Auditing & Threat detection on SQL databases (Audit de base de données et détection des menaces sur les bases de données SQL)**.

![Détails relatifs à une recommandation SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Le panneau **Activer l’audit et la détection de menaces sur les bases de données SQL** contient les informations suivantes :

* Une liste des bases de données SQL.
* Le serveur sur lequel elles se trouvent.
* Des informations indiquant si ce paramètre a été hérité du serveur ou s’il est unique dans cette base de données.
* L’état actuel du problème.
* Le niveau de gravité du problème.

Lorsque vous cliquez sur la base de données pour suivre cette recommandation, le panneau **Audit et détection des menaces** s’ouvre, comme illustré dans la capture d’écran suivante.

![Audit et détection des menaces](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pour activer l’audit, sélectionnez **ACTIVÉ** sous **Audit**.

## <a name="data-and-storage-recommendations"></a>Suggestions relatives aux données et au stockage

|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|Compte de stockage|20|Exiger un transfert sécurisé au compte de stockage|L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L'utilisation de HTTPS garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|Redis|20|Activer uniquement les connexions sécurisées à votre cache Azure pour Redis|Activer les connexions établies uniquement par le biais de SSL au cache Azure pour Redis. L'utilisation de connexions sécurisées garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|SQL|15|Activer Transparent Data Encryption sur des bases de données SQL|Activer le chiffrement transparent des données pour protéger les données au repos et respecter les exigences de conformité.|
|SQL|15|Activer l’audit sur les serveurs SQL|Activer l’audit sur les serveurs Azure SQL. (Service Azure SQL uniquement. N’inclut pas SQL en cours d’exécution sur vos machines virtuelles.)|
|SQL|15|Activer l’audit sur les bases de données SQL|Activer l’audit sur les bases de données Azure SQL. (Service Azure SQL uniquement. N’inclut pas SQL en cours d’exécution sur vos machines virtuelles.)|
|Data Lake Analytics|15|Activer le chiffrement au repos de Data Lake Analytics|Activer le chiffrement transparent des données pour sécuriser les données au repos dans votre service Data Lake Analytics. Le chiffrement au repos est transparent, ce qui signifie que Data Lake Analytics chiffre automatiquement les données avant leur stockage permanent et les déchiffre avant leur récupération. Aucune modification n’est donc requise dans les applications et services qui interagissent avec Data Lake Analytics en raison du chiffrement. Le chiffrement au repos réduit le risque de perte de données suite à un vol physique et permet également de répondre aux exigences de conformité réglementaire.|
|Data Lake Store|15|Activer le chiffrement au repos pour Data Lake Store|Activer le chiffrement transparent des données pour sécuriser les données au repos dans votre service Data Lake Store. Le chiffrement au repos est transparent, ce qui signifie que Data Lake Store chiffre automatiquement les données avant leur stockage permanent et les déchiffre avant leur récupération. Vous n’êtes pas obligé d’apporter des modifications aux applications et services qui interagissent avec Data Lake Store pour prendre en charge le chiffrement. Le chiffrement au repos réduit le risque de perte de données suite à un vol physique et permet également de répondre aux exigences de conformité réglementaire.|
|Data Lake Analytics|5.|Activer les journaux de diagnostic dans Data Lake Analytics|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Data Lake Store|5.|Activer les journaux de diagnostic dans Azure Data Lake Store|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|SQL|30|Corriger les vulnérabilités sur vos bases de données SQL|L'évaluation de la vulnérabilité SQL analyse les vulnérabilités de la sécurité dans votre base de données et expose tout manquement aux meilleures pratiques, comme les erreurs de configuration, les autorisations excessives et les données sensibles non protégées. La résolution des vulnérabilités détectées peut améliorer considérablement le niveau de sécurité de votre base de données.|
|SQL|20|Provisionner un administrateur Azure AD pour SQL Server|Approvisionner un administrateur Azure AD pour votre serveur SQL afin d’activer l’authentification Azure AD. L’authentification Azure AD permet une gestion simplifiée des autorisations et une gestion centralisée des utilisateurs de bases de données et d’autres services Microsoft.|
|Compte de stockage|15|Désactiver l'accès réseau non restreint au compte de stockage|Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Au lieu de cela, configurer les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions à partir d’Internet spécifique ou les clients locaux, vous pouvez accorder l’accès pour le trafic à partir de réseaux virtuels Azure spécifiques ou pour des plages d’adresses IP Internet publiques.|
|Compte de stockage|1|Migrer des comptes de stockage vers des ressources de modèle Azure Resource Manager|Utiliser le nouveau v2 d’Azure Resource Manager pour vos comptes de stockage pour fournir les améliorations de sécurité telles que : contrôle d’accès plus fort (RBAC), amélioration des audits, déploiement basé sur Resource Manager et de gouvernance, l’accès à des identités gérées, accès au coffre de clés pour secrets et l’authentification basée sur Active Directory Azure et prise en charge des balises et des groupes de ressources pour une gestion simplifiée de sécurité.|

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Protection de vos machines virtuelles dans Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protection de vos applications dans Azure Security Center](security-center-application-recommendations.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
