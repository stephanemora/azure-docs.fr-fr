---
title: Définitions de stratégie Azure surveillées dans Azure Security Center | Microsoft Docs
description: Cet article fournit une liste de définitions de stratégies Azure Policy que vous pouvez superviser dans Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559261"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Stratégies de sécurité Azure supervisées par Azure Security Center
Cet article fournit une liste de définitions de stratégies Azure Policy que vous pouvez superviser dans Security Center. Pour plus d’informations sur les stratégies de sécurité, consultez [Utilisation des stratégies de sécurité](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Stratégies de sécurité disponibles

Pour plus d’informations sur les stratégies intégrées supervisées par Security Center, consultez le tableau suivant :

| Stratégie | Rôle de la stratégie |
| --- | --- |
|Les journaux de diagnostic dans Virtual Machine Scale Sets doivent être activés|Nous vous recommandons d’activer les journaux afin qu’une piste des activités soit disponible pour un examen après un incident ou une compromission.|
|Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Event Hub|Les clients Azure Event Hubs ne doivent pas utiliser une stratégie d’accès au niveau de l’espace de noms qui donne accès à l’ensemble des files d’attente et des rubriques d’un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d’accès au niveau de l’entité pour les files d’attente et les rubriques afin de limiter l’accès à l’entité spécifique.|
|Des règles d’autorisation sur l’entité Event Hub doivent être définies|Auditez l’existence de règles d’autorisation sur les entités Event Hubs pour accorder un accès avec privilèges minimum.|
|L’accès aux comptes de stockage avec configurations de pare-feu et de réseau virtuel doit être limité|Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Configurez des règles réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, accordez l’accès au trafic à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques.|
|Auditer l’utilisation de règles personnalisées RBAC|Auditez des rôles intégrés, comme « Propriétaire, Contributeur, Lecteur » au lieu des rôles RBAC personnalisés, qui sont susceptibles d’engendrer des erreurs. L’utilisation de rôles personnalisés est traitée comme une exception et nécessite un contrôle rigoureux et une modélisation des menaces.|
|Les journaux de diagnostic dans Azure Stream Analytics doivent être activés|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|La sécurisation du transfert vers des comptes de stockage doit être activée|Auditez les exigences de transfert sécurisé dans votre compte de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L’utilisation du protocole HTTPS garantit l’authentification entre le serveur et le service. Elle protège aussi les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|Un administrateur Azure AD pour SQL Server doit être provisionné|Auditez le provisionnement d’un administrateur Azure Active Directory (Azure AD) pour SQL Server afin d’activer l’authentification Azure AD. L’authentification Azure AD prend en charge une gestion simplifiée des autorisations et une gestion centralisée des utilisateurs de bases de données et d’autres services Microsoft.|
|Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Service Bus|Les clients Azure Service Bus ne doivent pas utiliser une stratégie d’accès au niveau de l’espace de noms qui donne accès à l’ensemble des files d’attente et des rubriques d’un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, créez des stratégies d’accès au niveau de l’entité pour les files d’attente et les rubriques afin de limiter l’accès à l’entité spécifique.|
|Les journaux de diagnostic doivent être activés dans Service Bus|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|La propriété ClusterProtectionLevel sur EncryptAndSign dans Service Fabric doit être définie|Service Fabric fournit trois niveaux de protection pour la communication nœud à nœud qui utilise un certificat de cluster principal : None, Sign et EncryptAndSign. Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement.|
|L’authentification client doit utiliser Azure Active Directory|Auditez l’utilisation de l’authentification client seulement via Azure AD dans Service Fabric.|
|Les journaux de diagnostic doivent être activés dans les services Recherche|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Seules les connexions sécurisées à votre cache Redis doivent être activées|Auditez l’activation seulement pour les connexions via SSL au cache Azure pour Redis. L’utilisation de connexions sécurisées garantit l’authentification entre le serveur et le service. Elle protège aussi les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|
|Les journaux de diagnostic doivent être activés dans Logic Apps|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Les journaux de diagnostic doivent être activés dans Key Vault|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Les journaux de diagnostic doivent être activés dans Event Hub|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Les journaux de diagnostic doivent être activés dans Azure Data Lake Store|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Les journaux de diagnostic doivent être activés dans Data Lake Analytics|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Les comptes de stockage doivent être migrés vers de nouvelles ressources AzureRM|Utilisez Azure Resource Manager pour vos comptes de stockage pour fournir des améliorations de la sécurité. Il s’agit des actions suivantes : <br>- Contrôle d’accès plus fort (RBAC)<br>- Amélioration des audits<br>- Déploiement et gouvernance basés sur Azure Resource Manager<br>- Accès aux identités managées<br>- Accès à Azure Key Vault pour les secrets<br>- Authentification basée sur Azure AD<br>- Prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité|
|Les machines virtuelles doivent être migrées vers de nouvelles ressources AzureRM|Utilisez Azure Resource Manager pour vos machines virtuelles afin de fournir des améliorations de la sécurité. Il s’agit des actions suivantes : <br>- Contrôle d’accès plus fort (RBAC)<br>- Amélioration des audits<br>- Déploiement et gouvernance basés sur Azure Resource Manager<br>- Accès aux identités managées<br>- Accès à Azure Key Vault pour les secrets<br>- Authentification basée sur Azure AD<br>- Prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité|
|Des règles d’alerte de métrique doivent être configurées sur les comptes Batch|Auditez la configuration des règles d’alerte de métrique sur les comptes Azure Batch pour activer la métrique nécessaire.|
|Les journaux de diagnostic doivent être activés dans les comptes Batch|Auditez l’activation des journaux et conservez-les jusqu’à un an. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|Le chiffrement doit être activé sur les variables des comptes Automation|Il est important d’activer le chiffrement des ressources de variables des comptes Automation quand vous stockez des données sensibles.|
|Les journaux de diagnostic doivent être activés dans App Services|Auditer l’activation des journaux de diagnostic dans l’application. Ceci crée des pistes des activités pour les investigations en cas d’incident de sécurité ou de compromission du réseau.|
|La technologie Transparent Data Encryption doit être activée sur les bases de données SQL|Auditer l’état du chiffrement transparent des données pour les bases de données SQL.|
|L’audit des serveurs SQL doit être activé|Auditez l’existence de l’audit SQL au niveau du serveur.|
|\[Préversion] : Superviser les bases de données SQL non chiffrées dans Azure Security Center|Azure Security Center supervise les serveurs ou les bases de données SQL non chiffrés comme c’est recommandé.|
|\[Préversion] : Superviser les bases de données SQL non auditées dans Azure Security Center|Azure Security Center supervise les serveurs et les bases de données SQL où l’audit SQL n’est pas activé comme c’est recommandé.|
|\[Préversion] : Des mises à jour système doivent être installées sur vos machines|Azure Security Center supervise les mises à jour système de sécurité manquantes sur vos serveurs comme c’est recommandé.|
|\[Préversion] : Auditer le chiffrement d’objet blob manquant pour les comptes de stockage|Auditez les comptes de stockage qui n’utilisent pas le chiffrement des objets blob. Ceci s’applique seulement aux types de ressources de stockage Microsoft, mais pas au stockage d’autres fournisseurs. Azure Security Center supervise l’accès juste-à-temps possible au réseau comme c’est recommandé.|
|\[Préversion] : Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles|Azure Security Center supervise l’accès juste-à-temps possible au réseau comme c’est recommandé.|
|\[Préversion] : Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles|Azure Security Center supervise la configuration de la liste verte des applications possibles.|
|\[Préversion] : Les groupes de sécurité réseau manquants pour les machines virtuelles doivent être configurés|Azure Security Center supervise les groupes de sécurité réseau qui ont des règles trop permissives, comme c’est recommandé.|
|\[Préversion] : Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées|Azure Security Center supervise les serveurs qui ne satisfont pas à la base de référence configurée comme c’est recommandé.| 
|\[Préversion] : Endpoint Protection doit être installé sur les machines virtuelles|Azure Security Center supervise les serveurs où un agent Microsoft System Center Endpoint Protection n’est pas installé, comme c’est recommandé.|
|\[Préversion] : Le chiffrement de disque doit être appliqué sur les machines virtuelles|Azure Security Center supervise les machines virtuelles où le chiffrement de disque n’est pas activé comme c’est recommandé.|
|\[Préversion] : Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités|Supervisez les vulnérabilités détectées par la solution d’évaluation des vulnérabilités et les machines virtuelles sans solution d’évaluation des vulnérabilités dans Azure Security Center comme c’est recommandé.|
|\[Préversion] : Superviser les applications web non protégées dans Azure Security Center|Azure Security Center supervise les applications web qui ne disposent pas de la protection des pare-feux d’applications web, comme c’est recommandé.|
|\[Préversion] : La solution de protection des points de terminaison doit être installée sur les machines virtuelles|Azure Security Center supervise les points de terminaison réseau dépourvus de la protection de pare-feu de nouvelle génération, comme c’est recommandé.|
|\[Préversion] : Les vulnérabilités de vos bases de données SQL doivent être éliminées|Supervisez les résultats de l’analyse d’évaluation des vulnérabilités et faites des recommandations sur la façon de remédier aux vulnérabilités des bases de données.|
|\[Préversion] : Trois propriétaires au plus doivent être désignés pour votre abonnement|Nous vous recommandons de désigner au plus 3 propriétaires d’abonnement afin de réduire le risque potentiel de violation par un propriétaire compromis.|
|\[Préversion] : Plusieurs propriétaires doivent être affectés à votre abonnement|Nous vous recommandons de désigner plusieurs propriétaires d’abonnement pour garantir la redondance de l’accès administrateur.|
|\[Préversion] : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement |MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l’abonnement disposant d’autorisations de propriétaire afin d’empêcher une violation des comptes ou des ressources.|
|\[Préversion] : L’authentification multifacteur doit être activée sur vos comptes de l’abonnement disposant d’autorisations d’écriture|L’authentification multifacteur doit être activée pour tous les comptes de l’abonnement disposant d’autorisations d’écriture afin d’empêcher une violation des comptes ou des ressources.|
|\[Préversion] : L’authentification multifacteur doit être activée sur les comptes de l’abonnement disposant d’autorisations de lecture|L’authentification multifacteur doit être activée pour tous les comptes de l’abonnement disposant d’autorisations de lecture afin d’empêcher une violation des comptes ou des ressources.|
|\[Préversion] : Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement. La connexion avec des comptes dépréciés a été bloquée.|
|\[Préversion] : Les comptes déconseillés doivent être supprimés de votre abonnement|Les comptes déconseillés doivent être supprimés de vos abonnements. La connexion avec des comptes dépréciés a été bloquée.|
|\[Préversion] : Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Les comptes externes avec des autorisations de propriétaire doivent être supprimés de votre abonnement pour empêcher l’accès aux autorisations.|
|\[Préversion] : Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement|Les comptes externes avec des autorisations d’écriture doivent être supprimés de votre abonnement pour empêcher les accès non supervisés.|
|\[Préversion] : Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement|Les comptes externes avec des autorisations de lecture doivent être supprimés de votre abonnement pour empêcher les accès non supervisés.|




## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants.

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : Découvrez les considérations sur la conception dans Azure Security Center et comment les planifier.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gérer et résoudre les alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Superviser les solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ sur Azure Security Center](security-center-faq.md) : Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

Pour plus d’informations sur Azure Policy, consultez [Présentation d’Azure Policy](../governance/policy/overview.md).
