---
title: Définitions de stratégie Azure surveillées dans Azure Security Center | Microsoft Docs
description: Définitions de stratégie Azure surveillées dans Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877494"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Stratégies de sécurité Azure surveillés par Security Center
Cet article fournit une liste de définitions de stratégie de Azure que vous pouvez surveiller dans Azure Security Center. Pour plus d’informations sur les stratégies de sécurité, consultez [Utilisation des stratégies de sécurité](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Stratégies de sécurité disponibles

Pour en savoir plus sur les stratégies intégrées qui sont surveillés par Security Center, consultez le tableau suivant :

| Stratégie | Rôle de la stratégie |
| --- | --- |
|Audit l’activation des diagnostics consigne dans Azure Service Fabric et les machines virtuelles identiques|Nous vous recommandons d’activer les journaux afin que le journal d’activité est disponible pour un examen après un incident ou une compromission.|
|Règles d’autorisation d’audit sur les espaces de noms Event Hubs|Azure Event Hubs les clients ne doivent pas utiliser une stratégie d’accès au niveau de l’espace de noms qui fournit l’accès à toutes les files d’attente et rubriques dans un espace de noms. Pour s’aligner avec le modèle de sécurité de privilège minimum, vous devez créer des stratégies d’accès au niveau de l’entité pour les files d’attente et rubriques pour fournir l’accès à l’entité spécifique.|
|Existence d’audit des règles d’autorisation sur les entités Event Hubs|Auditer l’existence des règles d’autorisation sur les entités Event Hubs pour accorder l’accès de moindre privilège.|
|Auditer l'accès réseau non restreint aux comptes de stockage|Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Configurer des règles de réseau afin que seules les applications des réseaux autorisés peuvent accéder au compte de stockage. Pour autoriser les connexions à partir d’internet spécifiques ou les clients locaux, accorder l’accès à du trafic à partir de réseaux virtuels Azure spécifiques ou à internet public adresse IP plages.|
|Auditer l’utilisation de règles personnalisées RBAC|L’audit des rôles intégrés, tels que « Propriétaire, collaborateur ou lecteur » au lieu des rôles de contrôle (RBAC) de l’accès en fonction du rôle personnalisé, qui sont sujettes à des. Utilisation des rôles personnalisés est traitée comme une exception et nécessite une révision rigoureuse et la modélisation des menaces.|
|Auditer l’activation des journaux de diagnostic dans Azure Stream Analytics|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer le transfert sécurisé vers les comptes de stockage|Configuration requise d’un transfert sécurisé dans votre compte de stockage de l’audit. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). Utilisation du protocole HTTPS garantit l’authentification entre le serveur et le service. Il protège également les données en transit contre les attaques de couche réseau, tels que man-in-the-middle, écoutes clandestines et de piratage de session.|
|Audit d’approvisionnement d’un administrateur Azure Active Directory pour SQL Server|L’audit d’approvisionnement d’un administrateur Azure Active Directory (Azure AD) pour SQL Server pour activer l’authentification Azure AD. L’authentification Azure AD prend en charge la gestion des autorisations simplifiée et gestion centralisée des identités des utilisateurs de base de données et des autres services Microsoft.|
|Auditer les règles d'autorisation sur les espaces de noms Service Bus|Azure Service Bus les clients ne doivent pas utiliser une stratégie d’accès au niveau de l’espace de noms qui fournit l’accès à toutes les files d’attente et rubriques dans un espace de noms. Pour s’aligner avec le modèle de sécurité de privilège minimum, créez des stratégies d’accès au niveau de l’entité pour les files d’attente et rubriques pour fournir l’accès à l’entité spécifique.|
|Auditer l'activation des journaux de diagnostic dans Service Bus|L’activation des journaux de l’audit et les inscrire pour conserver en une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l'affectation à la propriété ClusterProtectionLevel de la valeur EncryptAndSign dans Service Fabric|Service Fabric fournit trois niveaux de protection pour la communication nœud à nœud qui utilise un certificat de cluster principal : None, l’authentification et EncryptAndSign. Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement.|
|Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric|Auditer l’utilisation de l’authentification du client que par le biais d’Azure AD dans Service Fabric.|
|Auditer l'activation des journaux de diagnostic dans le service Recherche|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Audit de l’activation d’uniquement des connexions sécurisées dans le Cache Azure Redis|Auditer l’activation uniquement des connexions via SSL vers le Cache Azure pour Redis. Utilisation de connexions sécurisées garantit l’authentification entre le serveur et le service. Il protège également les données en transit contre les attaques de couche réseau, tels que man-in-the-middle, écoutes clandestines et de piratage de session.|
|Auditer l'activation des journaux de diagnostic dans Logic Apps|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l'activation des journaux de diagnostic dans Key Vault|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Audit de l’activation des journaux de diagnostic dans Event Hubs|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l'activation des journaux de diagnostic dans Azure Data Lake Store|L’activation des journaux de l’audit et de les conserver pendant un an. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l'activation des journaux de diagnostic dans Data Lake Analytics|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l'utilisation des comptes de stockage classiques|Utilisez Azure Resource Manager pour vos comptes de stockage pour fournir les améliorations de sécurité. Il s’agit des actions suivantes : <br>-Contrôle d’accès plus fort (RBAC)<br>-Amélioration des audits<br>-Gouvernance et déploiement Resource Manager azure<br>-Accès aux identités gérées<br>-Accès à Azure Key Vault pour les clés secrètes<br>-L’authentification basée sur Active Directory azure<br>-Prise en charge pour les balises et les groupes de ressources pour faciliter la gestion sécurité|
|Auditer l'utilisation des machines virtuelles classiques|Utilisez Azure Resource Manager pour vos machines virtuelles pour fournir des améliorations de sécurité.  Il s’agit des actions suivantes : <br>-Contrôle d’accès plus fort (RBAC)<br>-Amélioration des audits<br>-Gouvernance et déploiement Resource Manager azure<br>-Accès aux identités gérées<br>-Accès à Azure Key Vault pour les clés secrètes<br>-L’authentification basée sur Active Directory azure<br>-Prise en charge pour les balises et les groupes de ressources pour faciliter la gestion sécurité|
|Auditer la configuration des règles d'alerte de métrique sur les comptes Batch|Audit de la configuration de règles d’alerte métrique sur les comptes Azure Batch pour activer la mesure nécessaire.|
|Auditer l'activation des journaux de diagnostic dans les comptes Batch|L’activation des journaux de l’audit et les pour conserver jusqu'à une année. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Audit de l’activation du chiffrement des variables de compte Automation|Il est important d’activer le chiffrement des ressources de variables de compte Azure Automation lorsque vous stockez des données sensibles.|
|Audit de l’activation des journaux de diagnostic dans App Service|Auditer l'activation des journaux de diagnostic dans l’application. Cela crée des pistes d’activité pour l’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.|
|Auditer l’état du chiffrement transparent des données|Auditer l’état du chiffrement transparent des données pour les bases de données SQL.|
|Audit des paramètres d’audit de niveau de SQL Server|Auditer l’existence de l’audit SQL au niveau du serveur.|
|\[[Préversion] : Superviser les bases de données SQL non chiffrées dans Azure Security Center|Azure Security Center analyse non chiffré de serveurs SQL ou de bases de données comme cela est recommandé.|
|\[[Préversion] : Superviser les bases de données SQL non auditées dans Azure Security Center|Azure Security Center surveille les serveurs SQL et les bases de données qui n’ont pas activé comme cela est recommandé de l’audit de SQL.|
|\[[Préversion] : Superviser les mises à jour système manquantes dans Azure Security Center|Azure Security Center analyse manquantes des mises à jour système de sécurité sur vos serveurs comme recommandé.|
|\[[Préversion] : Auditer le chiffrement d’objet blob manquant pour les comptes de stockage|Chiffrement d’objet blob des comptes de stockage d’audit qui n’utilisent pas. Cela s’applique uniquement aux types de ressources de stockage de Microsoft, pas de stockage à partir d’autres fournisseurs. Azure Security Center surveille l’accès juste-à-temps réseau possible comme recommandé.|
|\[[Préversion] : Surveiller l’accès juste-à-temps de réseau possibles dans Azure Security Center|Azure Security Center surveille l’accès juste-à-temps réseau possible comme recommandé.|
|\[[Préversion] : Superviser la configuration éventuelle d’une liste verte d’applications dans Azure Security Center|Azure Security Center analyse la configuration de liste verte d’application possibles.|
|\[[Préversion] : Superviser l’accès réseau permissif dans Azure Security Center|Azure Security Center surveille réseau des groupes de sécurité qui ont des règles trop permissives, comme recommandé.|
|\[[Préversion] : Superviser les vulnérabilités du système d’exploitation dans Azure Security Center|Azure Security Center surveille les serveurs qui ne répondent à la base de référence configurée comme recommandé.| 
|\[[Préversion] : Superviser les agents Endpoint Protection manquants dans Azure Security Center|Azure Security Center surveille les serveurs qui n’ont pas un agent de Microsoft System Center Endpoint Protection installé comme recommandé.|
|\[[Préversion] : Surveiller les disques de machine virtuelle non chiffrés dans Azure Security Center|Azure Security Center surveille les machines virtuelles qui n’ont pas disk encryption est activé comme recommandé.|
|\[[Préversion] : Surveiller les vulnérabilités de machine virtuelle dans Azure Security Center|Surveiller les vulnérabilités détectées par la solution d’évaluation des vulnérabilités et les machines virtuelles qui n’ont pas une solution d’évaluation des vulnérabilités dans Azure Security Center comme recommandé.|
|\[[Préversion] : Superviser les applications web non protégées dans Azure Security Center|Azure Security Center surveille les applications qui ne disposent pas de protection de pare-feu d’applications web comme recommandé web.|
|\[[Préversion] : Superviser les points de terminaison réseau non protégés dans Azure Security Center|Azure Security Center surveille les points de terminaison dépourvus de protection de pare-feu de génération suivante comme cela est recommandé de réseau.|
|\[[Préversion] : Superviser les résultats de l’évaluation des vulnérabilités SQL dans Azure Security Center|Évaluation des vulnérabilités moniteur résultats d’analyse et des recommandations afin de corriger les vulnérabilités de base de données.|
|\[[Préversion] : Auditer le nombre maximal de propriétaires d’un abonnement|Nous vous recommandons de désigner jusqu'à trois propriétaires d’abonnements pour réduire le risque potentiel de violation par un propriétaire compromis.|
|\[[Préversion] : Auditer le nombre minimal de propriétaires d’un abonnement|Nous vous recommandons de désigner plusieurs propriétaire de l’abonnement pour garantir un accès administrateur redondance.|
|\[[Préversion] : Auditer les comptes d’un abonnement qui disposent d’autorisations de propriétaire sur lesquels MFA n’est pas activé|L’authentification multifacteur (MFA) doit être activée pour tous les comptes d’abonnement disposant des autorisations de propriétaire pour éviter une violation des comptes ou des ressources.|
|\[[Préversion] : Auditer les comptes d’un abonnement qui disposent d’autorisations d’écriture sur lesquels MFA n’est pas activé|L’authentification multifacteur doit être activée pour tous les comptes d’abonnement qui ont des autorisations en écriture pour éviter la violation des comptes ou des ressources.|
|\[[Préversion] : Auditer les comptes d’un abonnement qui disposent d’autorisations de lecture sur lesquels MFA n’est pas activé|L’authentification multifacteur doit être activée pour tous les comptes d’abonnement qui ont des autorisations pour éviter la violation des comptes ou des ressources en lecture.|
|\[[Préversion] : Auditer les comptes dépréciés d’un abonnement qui disposent d’autorisations de propriétaire|Les comptes obsolètes disposant des autorisations de propriétaire doivent être supprimés de votre abonnement. Les comptes obsolètes ont été bloqués de la connexion.|
|\[[Préversion] : Auditer les comptes dépréciés d’un abonnement|Les comptes déconseillés doivent être supprimés de vos abonnements. Les comptes obsolètes ont été bloqués de la connexion.|
|\[[Préversion] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations de type propriétaire|Les comptes externes disposant des autorisations de propriétaire doivent être supprimés de votre abonnement pour empêcher l’accès d’autorisations.|
|\[[Préversion] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations d’écriture|Les comptes externes qui ont écrire les autorisations doivent être supprimées de votre abonnement pour empêcher tout accès non contrôlé.|
|\[[Préversion] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations de lecture|Les comptes externes qui ont des autorisations de lecture doivent être supprimés à partir de votre abonnement pour empêcher tout accès non contrôlé.|




## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour en savoir plus sur Security Center, consultez les articles suivants.

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : Découvrez comment planifier et comprendre les considérations de conception dans Azure Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gérer et résoudre les alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Superviser les solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ sur Azure Security Center](security-center-faq.md) : Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

Pour en savoir plus sur la stratégie Azure, consultez [What ' s Azure Policy ?](../governance/policy/overview.md).
