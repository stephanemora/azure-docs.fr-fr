---
title: Exemple - Blueprint Services partagés ISO 27001 - Mappage des contrôles
description: Mappage des contrôles de l’exemple de blueprint Services partagés ISO 27001 à Azure Policy et RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 93a26311216ca5682c02a319f989b3d342a33ce1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256479"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mappage des contrôles de l’exemple de blueprint Services partagés ISO 27001

L’article suivant décrit en détail le mappage de l’exemple de blueprint Services partagés ISO 27001 pour Azure Blueprints aux contrôles ISO 27001. Pour plus d’informations sur les contrôles, consultez [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Les mappages suivants ciblent les contrôles **ISO 27001:2013**. Utilisez le volet de navigation de droite pour accéder directement au mappage d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **[Préversion] Auditer les contrôles ISO 27001:2013 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Séparation des tâches

Le fait d’avoir un seul propriétaire d’abonnement Azure ne permet pas d’assurer la redondance administrative. À l’inverse, un nombre trop élevé de propriétaires d’abonnement Azure peut augmenter le risque d’une violation par le biais d’un compte de propriétaire compromis. Ce blueprint vous permet de maintenir un nombre approprié de propriétaires d’abonnement Azure en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui auditent le nombre de propriétaires pour les abonnements Azure. La gestion des autorisations des propriétaires d’abonnement peut vous aider à implémenter une séparation appropriée des tâches.

- [VERSION PRÉLIMINAIRE]: Audit minimum number of owners for subscription
- [VERSION PRÉLIMINAIRE]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Accès aux réseaux et aux services réseau

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour gérer qui a accès aux ressources Azure. Ce blueprint vous permet de contrôler l’accès aux ressources Azure en affectant sept définitions [Azure Policy](../../../policy/overview.md). Ces stratégies vérifient l’utilisation des types de ressources et des configurations susceptibles d’accorder un accès plus permissif aux ressources.
Le fait de savoir quelles ressources enfreignent ces stratégies peut vous aider à prendre des actions correctives visant à limiter l’accès aux ressources Azure aux utilisateurs autorisés.

- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Linux VM accounts with no passwords
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  mots de passes
- [VERSION PRÉLIMINAIRE]: Audit Linux VM accounts with no passwords
- [VERSION PRÉLIMINAIRE]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditer l'utilisation des comptes de stockage classiques
- Auditer l'utilisation des machines virtuelles classiques
- Auditer les machines virtuelles qui n’utilisent pas de disques managés

## <a name="a922-user-access-provisioning"></a>A.9.2.2 Distribution des accès aux utilisateurs

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour gérer qui a accès aux ressources Azure. Ce blueprint affecte trois définitions [Azure Policy](../../../policy/overview.md) afin de vérifier l’utilisation de l’authentification [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) pour les serveurs SQL et [Service Fabric](../../../../service-fabric/service-fabric-overview.md). L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft. Ce blueprint affecte également une définition Azure Policy pour vérifier l’utilisation des règles RBAC personnalisées. Ces dernières étant sujettes aux erreurs, le fait de savoir où elles sont implémentées peut vous aider à déterminer les besoins réels et l’implémentation appropriée.

- Auditer le provisionnement d'un administrateur Azure Active Directory pour un serveur SQL
- Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric
- Auditer l’utilisation de règles personnalisées RBAC

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestion des droits d’accès à privilèges

Ce blueprint vous permet de limiter et de contrôler les droits d’accès privilégié en affectant quatre définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes externes avec des autorisations de propriétaire et/ou en écriture et les comptes avec des autorisations de propriétaire et/ou en écriture pour lesquels l’authentification multifacteur n’est pas activée.

- [VERSION PRÉLIMINAIRE]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [VERSION PRÉLIMINAIRE]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [VERSION PRÉLIMINAIRE]: Audit external accounts with owner permissions on a subscription
- [VERSION PRÉLIMINAIRE]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestion des informations secrètes d’authentification des utilisateurs

Ce blueprint affecte trois définitions [Azure Policy](../../../policy/overview.md) afin d’auditer les comptes pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis. Ce blueprint affecte également deux définitions Azure Policy qui vérifient les autorisations associées aux fichiers de mot de passe des machines virtuelles Linux et qui émettent une alerte si elles sont définies incorrectement. Cette configuration vous permet de prendre une action corrective pour vérifier que les authentificateurs ne sont pas compromis.

- [VERSION PRÉLIMINAIRE]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [VERSION PRÉLIMINAIRE]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [VERSION PRÉLIMINAIRE]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Linux VM passwd file permissions
- [VERSION PRÉLIMINAIRE]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revue des droits d’accès utilisateurs

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte quatre définitions [Azure Policy](../../../policy/overview.md) qui permettent de déterminer les comptes devant en priorité faire l’objet d’une revue, notamment les comptes dépréciés et les comptes externes avec des autorisations élevées.

- [VERSION PRÉLIMINAIRE]: Audit deprecated accounts on a subscription
- [VERSION PRÉLIMINAIRE]: Audit deprecated accounts with owner permissions on a subscription
- [VERSION PRÉLIMINAIRE]: Audit external accounts with owner permissions on a subscription
- [VERSION PRÉLIMINAIRE]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Suppression ou adaptation des droits d’accès

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. Grâce à [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) et à RBAC, vous pouvez mettre à jour les rôles d’utilisateur pour refléter les changements organisationnels. Si nécessaire, vous pouvez empêcher des comptes de se connecter (ou les supprimer). Dans ce cas, les droits d’accès aux ressources Azure sont immédiatement supprimés. Ce blueprint affecte deux définitions [Azure Policy](../../../policy/overview.md) pour auditer le compte déprécié dont la suppression doit être envisagée.

- [VERSION PRÉLIMINAIRE]: Audit deprecated accounts on a subscription
- [VERSION PRÉLIMINAIRE]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 Système de gestion des mots de passe

Ce blueprint vous aide à appliquer des mots de passe forts en affectant 10 définitions [Azure Policy](../../../policy/overview.md) qui détectent les machines virtuelles Windows ne mettant pas en œuvre une force minimale ni d’autres exigences relatives aux mots de passe. Le fait d’avoir connaissance des machines virtuelles qui enfreignent la stratégie de force des mots de passe peut vous aider à prendre des actions correctives visant à rendre les mots de passe de tous les comptes d’utilisateurs de machine virtuelle conformes à la stratégie.

- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [VERSION PRÉLIMINAIRE]: Audit Windows VM enforces password complexity requirements
- [VERSION PRÉLIMINAIRE]: Audit Windows VM maximum password age 70 days
- [VERSION PRÉLIMINAIRE]: Audit Windows VM minimum password age 1 day
- [VERSION PRÉLIMINAIRE]: Audit Windows VM passwords must be at least 14 characters
- [VERSION PRÉLIMINAIRE]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Stratégie sur l’utilisation des contrôles de chiffrement

Ce blueprint vous aide à appliquer votre stratégie sur l’utilisation des contrôles de chiffrement en affectant 13 définitions [Azure Policy](../../../policy/overview.md) qui mettent en œuvre des contrôles de chiffrement spécifiques et détectent l’utilisation de paramètres de chiffrement faibles.
Le fait de savoir où vos ressources Azure peuvent avoir des configurations de chiffrement non optimales peut vous aider à prendre des mesures correctives visant à vérifier que les ressources sont configurées conformément à votre stratégie de sécurité des informations. Plus précisément, les stratégies affectées par ce blueprint exigent le chiffrement des comptes de stockage d’objets blob et des comptes Data Lake Storage ; exigent le chiffrement transparent des données dans les bases de données SQL ; vérifient le chiffrement sur les comptes de stockage, bases de données SQL, disques de machine virtuelle et variables de compte Automation ; vérifient les connexions non sécurisées aux comptes de stockage, aux applications de fonction, aux applications web, aux applications API et au Cache Redis ; vérifient si le chiffrement par mot de passe des machines virtuelles est faible ; et vérifient les communication Service Fabric non chiffrées.

- [VERSION PRÉLIMINAIRE]: Audit HTTPS only access for a Function App
- [VERSION PRÉLIMINAIRE]: Audit HTTPS only access for a Web Application
- [VERSION PRÉLIMINAIRE]: Audit HTTPS only access for an API App
- [VERSION PRÉLIMINAIRE]: Audit missing blob encryption for storage accounts
- [VERSION PRÉLIMINAIRE]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  le chiffrementn
- [VERSION PRÉLIMINAIRE]: Audit Windows VM should not store passwords using reversible encryption
- [VERSION PRÉLIMINAIRE]: Monitor unencrypted SQL database in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor unencrypted VM Disks in Azure Security Center
- Auditer l'activation du chiffrement des variables du compte Automation
- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage
- Auditer l'affectation à la propriété ClusterProtectionLevel de la valeur EncryptAndSign dans Service Fabric
- Auditer l’état du chiffrement transparent des données

## <a name="a1241-event-logging"></a>A.12.4.1 Journalisation des événements

Ce blueprint vous aide à vérifier que les événements système sont consignés en affectant sept définitions [Azure Policy](../../../policy/overview.md) qui vérifient les paramètres de journal d’audit sur les ressources Azure. Une stratégie affectée vérifie également si les machines virtuelles n’envoient pas de journaux à un espace de travail Log Analytics spécifié.

- [Préversion] : Vérifier le déploiement de Dependency Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Vérifier le déploiement de Dependency Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [VERSION PRÉLIMINAIRE]: Monitor unaudited SQL database in Azure Security Center
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.6.1 Gestion des vulnérabilités techniques

Ce blueprint vous permet de gérer les vulnérabilités du système d’informations en affectant cinq définitions [Azure Policy](../../../policy/overview.md) qui supervisent les mises à jour système manquantes ainsi que les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles. Ces insights fournissent des informations en temps réel sur l’état de la sécurité de vos ressources déployées et peuvent vous aider à classer par ordre de priorité les actions de correction.

- [VERSION PRÉLIMINAIRE]: Monitor missing Endpoint Protection in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor missing system updates in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor OS vulnerabilities in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor SQL vulnerability assessment results in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrictions liées à l’installation de logiciels

La solution Contrôles d’application adaptatifs proposée par Azure Security Center vous permet de contrôler les applications pouvant s’exécuter sur vos machines virtuelles situées dans Azure. Ce blueprint affecte une définition Azure Policy qui supervise les changements apportés à l’ensemble des applications autorisées. La mise en place de restrictions au niveau de l’installation des logiciels peut vous aider à réduire le risque d’introduction de vulnérabilités logicielles.

- [VERSION PRÉLIMINAIRE]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Contrôle des réseaux

Ce blueprint vous aide à gérer et à contrôler les réseaux en affectant une définition [Azure Policy](../../../policy/overview.md) qui supervise les groupes de sécurité réseau associés à des règles permissives. Les règles trop permissives sont susceptibles d’autoriser des accès réseau involontaires et doivent être passées en revue.

- [VERSION PRÉLIMINAIRE]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Politiques et procédures de transfert de l’information

Le blueprint vous aide à sécuriser le transfert des informations avec les services Azure en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui auditent les connexions non sécurisées aux comptes de stockage et au cache Redis.

- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 Protection des transactions liées aux services d’application

Ce blueprint vous aide à protéger les ressources du système d’information en affectant trois définitions [Azure Policy](../../../policy/overview.md) qui supervisent les points de terminaison, applications et comptes de stockage non protégés. Les points de terminaison et les applications qui ne sont pas protégés par un pare-feu, de même que les comptes de stockage avec un accès illimité, peuvent permettre un accès involontaire aux informations contenues dans le système d’information.

- [VERSION PRÉLIMINAIRE]: Monitor unprotected network endpoints in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor unprotected web application in Azure Security Center
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 Signalement des failles liées à la sécurité de l’information

Ce blueprint vous permet de vous tenir informé des vulnérabilités du système en affectant cinq définitions [Azure Policy](../../../policy/overview.md) qui supervisent les vulnérabilités, l’état des correctifs et les alertes déclenchées par des logiciels malveillants dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées.

- [VERSION PRÉLIMINAIRE]: Monitor missing Endpoint Protection in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor missing system updates in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor OS vulnerabilities in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor SQL vulnerability assessment results in Azure Security Center
- [VERSION PRÉLIMINAIRE]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Étapes suivantes

Vous venez de passer en revue le mappage des contrôles du blueprint Services partagés ISO 27001. Consultez à présent les articles suivants pour découvrir l’architecture et le déploiement de cet exemple :

> [!div class="nextstepaction"]
> [Blueprint Services partagés ISO 27001 - Vue d’ensemble](./index.md)
> [Blueprint Services partagés ISO 27001 - Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).