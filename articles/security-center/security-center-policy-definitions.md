---
title: Définitions de stratégie Azure surveillées dans Azure Security Center | Microsoft Docs
description: Définitions de stratégie Azure surveillées dans Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: a4f9fc31f411d36e63775a3665b6dfe27eec7710
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52318938"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Stratégie de sécurité Azure surveillées par Azure Security Center
Cet article vous fournit une liste de définitions de stratégies Azure Policy qui peuvent être surveillées dans Security Center.

## <a name="available-security-policies"></a>Stratégies de sécurité disponibles

Pour comprendre les stratégies intégrées surveillées par Security Center, consultez le tableau suivant :

| Stratégie | Rôle de la stratégie |
| --- | --- |
|Auditer l'activation des journaux de diagnostic dans Service Fabric et Virtual Machine Scale Sets|Il est recommandé d'activer les journaux pour permettre le traçage de l'activité lors des enquêtes requises en cas d'incident ou de compromission.|
|Auditer les règles d'autorisation sur les espaces de noms Event Hub|Les clients Event Hub ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Auditer l’existence de règles d'autorisation sur les entités Event Hub|Auditer l’existence de règles d'autorisation sur les entités Event Hub pour accorder un accès à privilèges minimum.|
|Auditer l'accès réseau non restreint aux comptes de stockage|Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Au lieu de cela, configurer les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, l’accès au trafic peut être autorisé à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques.|
|Auditer l’utilisation de règles personnalisées RBAC|Auditer des rôles intégrés tels que « Propriétaire, contributeur, lecteur » au lieu des rôles RBAC personnalisés, qui sont susceptibles d’engendrer des erreurs. L’utilisation de rôles personnalisés est traitée comme une exception et nécessite un contrôle rigoureux et la modélisation des menaces.|
|Auditer l’activation des journaux de diagnostic dans Azure Stream Analytics|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.|
|Auditer le transfert sécurisé vers les comptes de stockage|Auditer l’exigence de transfert sécurisé dans votre compte de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L'utilisation de HTTPS garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|Auditer le provisionnement d'un administrateur Azure Active Directory pour un serveur SQL|Auditer l’approvisionnement d’un administrateur Azure Active Directory pour votre serveur SQL afin d’activer l’authentification Azure AD. L’authentification Azure AD permet une gestion simplifiée des autorisations et une gestion centralisée des utilisateurs de bases de données et d’autres services Microsoft.|
|Auditer les règles d'autorisation sur les espaces de noms Service Bus|Les clients Service Bus ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Auditer l'activation des journaux de diagnostic dans Service Bus|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.|
|Auditer l'affectation à la propriété ClusterProtectionLevel de la valeur EncryptAndSign dans Service Fabric|Service Fabric fournit trois niveaux de protection (None, Sign et EncryptAndSign) pour la communication nœud à nœud à l’aide d’un certificat de cluster principal. Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement.| 
|Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric|Auditer l'utilisation de l'authentification client uniquement par le biais d'Azure Active Directory dans Service Fabric| 
|Auditer l'activation des journaux de diagnostic dans le service Recherche|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'activation des connexions sécurisées uniquement à votre cache Redis|Auditer l'activation des connexions via SSL uniquement à votre cache Redis. L'utilisation de connexions sécurisées garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).| 
|Auditer l'activation des journaux de diagnostic dans Logic Apps|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'activation des journaux de diagnostic dans Key Vault|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.|
|Auditer l'activation des journaux de diagnostic dans Event Hub|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'activation des journaux de diagnostic dans Azure Data Lake Store|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'activation des journaux de diagnostic dans Data Lake Analytics|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'utilisation des comptes de stockage classiques|Profitez des améliorations apportées à Azure Resource Manager pour renforcer la sécurité de vos comptes de stockage : contrôle d'accès plus puissant, audit amélioré, déploiement et gouvernance basés sur Azure Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité, etc.| 
|Auditer l'utilisation des machines virtuelles classiques|Profitez des améliorations apportées à Azure Resource Manager pour renforcer la sécurité de vos machines virtuelles : contrôle d'accès plus puissant, audit amélioré, déploiement et gouvernance basés sur Azure Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité, etc.| 
|Auditer la configuration des règles d'alerte de métrique sur les comptes Batch|Auditer la configuration des règles d'alerte de métrique sur le compte Batch pour activer la métrique requise.| 
|Auditer la configuration des règles d'alerte de métrique sur les comptes Batch|Auditer la configuration des règles d'alerte de métrique sur le compte Batch pour activer la métrique requise.| 
|Auditer l'activation des journaux de diagnostic dans les comptes Batch|Auditer l’activation des journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l'activation du chiffrement des variables du compte Automation|Il est important d'activer le chiffrement des ressources variables du compte Automation lors du stockage de données sensibles.| 
|Auditer l'activation des journaux de diagnostic dans App Services|Auditer l'activation des journaux de diagnostic dans l’application. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.| 
|Auditer l’état du chiffrement transparent des données|Auditer l’état du chiffrement transparent des données pour les bases de données SQL.| 
|Auditer les paramètres d'audit au niveau du serveur SQL|Auditer l'existence d'un audit SQL au niveau du serveur.| 
|[Préversion] : surveiller la bases de données SQL non chiffrée dans Azure Security Center|Les serveurs ou bases de données SQL non chiffrés seront surveillés par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les bases de données SQL non auditées dans Azure Security Center|Les serveurs et bases de données SQL pour lesquels l'audit SQL n'est pas activé seront surveillés par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les mises à jour système manquantes dans Azure Security Center|Les mises à jour système de sécurité manquantes sur vos serveurs seront surveillées par Azure Security Center en tant que recommandation.| 
|[Préversion] : auditer le chiffrement d'objet blob manquant pour les comptes de stockage|Cette stratégie audite les comptes de stockage sans chiffrement des objets blob. Elle s’applique uniquement aux types de ressources Microsoft.Storage, pas aux autres fournisseurs de stockage. L'éventuel accès juste-à-temps au réseau sera surveillé par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller l'éventuel accès juste-à-temps (JIT) au réseau dans Azure Security Center|L'éventuel accès juste-à-temps (JIT) au réseau sera surveillé par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller la création éventuelle d'une liste verte d'applications dans Azure Security Center|La création éventuelle d'une liste verte d'applications sera surveillée par Azure Security Center.| 
|[Préversion] : surveiller l'accès réseau permissif dans Azure Security Center|Les groupes de sécurité réseau avec des règles trop permissives seront surveillés par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les vulnérabilités du système d'exploitation dans Azure Security Center|Les serveurs qui ne respectent pas la base de référence configurée seront surveillés par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les agents Endpoint Protection manquants dans Azure Security Center|Les serveurs sans agent Endpoint Protection installé seront surveillés par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les disques de machine virtuelle non chiffrés dans Azure Security Center|Les machines virtuelles sans chiffrement de disque activé seront surveillées par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les vulnérabilités de machine virtuelle dans Azure Security Center|Surveille les vulnérabilités détectées par la solution d’évaluation des vulnérabilités et les machines virtuelles sans solution d’évaluation des vulnérabilités dans Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les applications web non protégées dans Azure Security Center|Les applications web ne bénéficiant pas de la protection du pare-feu d'applications web seront surveillées par Azure Security Center en tant que recommandation.| 
|[Préversion] : surveiller les points de terminaison réseau non protégés dans Azure Security Center|Les points de terminaison réseau ne bénéficiant pas de la protection du pare-feu de nouvelle génération seront surveillés par Azure Security Center à titre de recommandation.| 
|[Préversion] : surveiller les résultats de l'évaluation des vulnérabilités SQL dans Azure Security Center|Supervisez les résultats de l'analyse des vulnérabilités et les recommandations sur la correction des vulnérabilités liées aux bases de données.| 
|[Préversion] : auditer le nombre maximal de propriétaires d'un abonnement|Il est recommandé de désigner jusqu'à 3 propriétaires d'abonnement pour réduire le risque de violation par un propriétaire compromis.| 
|[Préversion] : auditer le nombre minimal de propriétaires d'un abonnement|Il est recommandé de désigner plusieurs propriétaires d'abonnement pour disposer de la redondance de l'accès administrateur.| 
|[Préversion] : auditer les comptes d'un abonnement qui disposent d'autorisations de propriétaire sur lesquels MFA n'est pas activé|MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant d'autorisations de propriétaire afin d'éviter une violation des comptes ou des ressources.| 
|[Préversion] : auditer les comptes d'un abonnement qui disposent d'autorisations d'écriture sur lesquels MFA n'est pas activé|MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant de privilèges d'accès en écriture pour éviter une violation des comptes ou des ressources.| 
|[Préversion] : auditer les comptes d'un abonnement qui disposent d'autorisations de lecture sur lesquels MFA n'est pas activé|MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant de privilèges d'accès en lecture afin d'éviter une violation des comptes ou des ressources.| 
|[Préversion] : auditer les comptes dépréciés d'un abonnement qui disposent d'autorisations de propriétaire|Les comptes déconseillés disposant d’autorisations de type propriétaire doivent être supprimés de votre abonnement. Les comptes déconseillés sont des comptes qui ont été empêchés de se connecter.| 
|[Préversion] : auditer les comptes déconseillés d'un abonnement|Les comptes déconseillés doivent être supprimés de vos abonnements. Les comptes déconseillés sont des comptes qui ont été empêchés de se connecter.| 
|[Préversion] : auditer les comptes externes d'un abonnement qui disposent d'autorisations de type propriétaire|Les comptes externes avec des autorisations de type propriétaire doivent être supprimés de votre abonnement pour empêcher un accès non contrôlé.| 
|[Préversion] : auditer les comptes externes d'un abonnement qui disposent d'autorisations d'écriture|Les comptes externes avec des privilèges d'accès en écriture doivent être supprimés de votre abonnement afin d'empêcher un accès non contrôlé.| 
|[Préversion] : auditer les comptes externes d'un abonnement qui disposent d'autorisations de lecture|Les comptes externes avec des privilèges d'accès en lecture doivent être supprimés de votre abonnement afin d'empêcher un accès non contrôlé.| 




## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ Azure Security Center](security-center-faq.md) : obtenez des réponses aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

Pour en savoir plus sur Azure, consultez [Présentation d’Azure Policy](../azure-policy/azure-policy-introduction.md).
