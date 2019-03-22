---
title: Exemple - ISO 27001 plan - mappage de contrôle
description: Mappage de contrôle de l’exemple de solution blueprint ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f655db8643a16e6818f5becf25c8761fb4c9093b
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58008014"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Mappage de contrôle de l’exemple de plan Azure plans ISO 27001

L’article suivant décrit en détail comment Azure plans ISO 27001 Shared Services blueprint exemples de mappages pour les contrôles ISO 27001. Pour plus d’informations sur les contrôles, consultez [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Les mappages suivants sont à la **ISO 27001 : 2013** contrôles. Utilisez le volet de navigation de droite pour accéder directement à un mappage de contrôle spécifique. De nombreux contrôles mappés sont implémentées avec un [Azure Policy](../../../policy/overview.md) initiative. Pour vérifier l’initiative terminée, ouvrez **stratégie** dans le portail Azure et sélectionnez le **définitions** page. Ensuite, recherchez et sélectionnez le  **[Préversion] d’Audit de ISO 27001 : 2013 contrôle et déployer des Extensions de machine virtuelle spécifique pour prendre en charge les exigences d’audit** initiative de stratégie intégrée.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 une répartition des tâches

Avoir qu’un seul propriétaire d’abonnement Azure n’autorise pas pour la redondance d’administration. À l’inverse, avoir trop de propriétaires d’abonnement Azure peut augmenter le risque d’une violation via un compte de propriétaire compromis. Cette solution blueprint vous permet de maintenir un nombre approprié de propriétaires d’abonnement Azure, affectez deux [Azure Policy](../../../policy/overview.md) définitions qu’auditer le nombre de propriétaires d’abonnements Azure. La gestion des autorisations de propriétaire d’abonnement peuvent vous aider à implémenter une séparation appropriée des tâches.

- [Préversion]: Audit minimum number of owners for subscription
- [Préversion]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 l’accès aux réseaux et services réseau

Azure implémente [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour gérer les utilisateurs ayant accès aux ressources Azure. Cette solution blueprint vous permet de contrôler l’accès aux ressources Azure, affectez sept [Azure Policy](../../../policy/overview.md) définitions. Ces stratégies auditer l’utilisation des types de ressources et les configurations qui peuvent autoriser plus permissives l’accès aux ressources.
Ressources de présentation qui vont à l’encontre de ces scénarios permettent de vous aider à profiter des actions correctives pour garantir l’accès des ressources Azure est limité à des utilisateurs autorisés.

- [Préversion]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Préversion]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Mots de passes
- [Préversion]: Audit Linux VM accounts with no passwords
- [Préversion]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditer l'utilisation des comptes de stockage classiques
- Auditer l'utilisation des machines virtuelles classiques
- Auditer les machines virtuelles qui n'utilisent pas de disques managés

## <a name="a922-user-access-provisioning"></a>Attribution de l’accès utilisateur A.9.2.2

Azure implémente [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour gérer les utilisateurs ayant accès aux ressources Azure. Cette solution blueprint affecte trois [Azure Policy](../../../policy/overview.md) définitions pour auditer les utilisent de [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) l’authentification pour les serveurs SQL et [Service Fabric](../../../../service-fabric/service-fabric-overview.md). L’authentification à l’aide d’Azure Active Directory permet de gestion des autorisations simplifiée et la gestion centralisée des identités des utilisateurs de base de données et des autres services Microsoft. Cette solution blueprint affecte également une définition de stratégie de Azure pour auditer l’utilisation de règles RBAC personnalisées. Comprendre où les règles RBAC personnalisées sont implémentent peut vous aider à vérifier la nécessité et l’implémentation appropriée, comme les règles RBAC personnalisées sont sujettes.

- Auditer le provisionnement d'un administrateur Azure Active Directory pour un serveur SQL
- Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric
- Auditer l’utilisation de règles personnalisées RBAC

## <a name="a923-management-of-privileged-access-rights"></a>Gestion de A.9.2.3 de privilège des droits d’accès

Cette solution blueprint vous permet de limiter et contrôler les droits d’accès privilégié en assignant quatre [Azure Policy](../../../policy/overview.md) définitions pour auditer les comptes externes avec le propriétaire et/ou écrire les autorisations et les comptes dont le propriétaire et/ou des autorisations en écriture que n’avez pas activé l’authentification multifacteur.

- [Préversion]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Préversion]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Préversion]: Audit external accounts with owner permissions on a subscription
- [Préversion]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Gestion de A.9.2.4 secret d’informations d’authentification des utilisateurs

Cette solution blueprint affecte trois [Azure Policy](../../../policy/overview.md) définitions au contrôle des comptes qui n’ont pas l’authentification multifacteur est activée. L’authentification multifacteur permet de sécuriser les comptes même si une partie des informations d’authentification est compromise. En surveillant les comptes sans l’authentification multifacteur est activée, vous pouvez identifier les comptes qui peuvent être plus susceptibles d’être compromis. Cette solution blueprint affecte également les deux définitions de stratégie Azure qui auditent Linux VM autorisations de fichier de mot de passe pour être averti si elles sont définies incorrectement. Cette configuration vous permet d’entreprendre une action corrective pour garantir des authentificateurs ne sont pas compromises.

- [Préversion]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Préversion]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Préversion]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Préversion]: Deploy VM extension to audit Linux VM passwd file permissions
- [Préversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Révision A.9.2.5 de droits d’accès utilisateur

Azure implémente [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) vous aide à vous gérez qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez vérifier qui a accès aux ressources Azure et leurs autorisations. Cette solution blueprint attribue quatre [Azure Policy](../../../policy/overview.md) définitions au contrôle des comptes qui doivent être prioritaires pour la révision, y compris les comptes d’amortissement et externe avec des autorisations élevées.

- [Préversion]: Audit deprecated accounts on a subscription
- [Préversion]: Audit deprecated accounts with owner permissions on a subscription
- [Préversion]: Audit external accounts with owner permissions on a subscription
- [Préversion]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Suppression de A.9.2.6 ou ajustement de droits d’accès

Azure implémente [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) vous aide à vous gérez qui a accès aux ressources dans Azure. À l’aide de [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) et RBAC, vous pouvez mettre à jour des rôles d’utilisateur afin de refléter les modifications organisationnelles. Si nécessaire, comptes peuvent être bloqués de la connexion (ou supprimés), qui supprime immédiatement les droits d’accès aux ressources Azure. Cette solution blueprint assigne deux [Azure Policy](../../../policy/overview.md) définitions de compte d’amortissement doit être prises en compte pour la suppression de l’audit.

- [Préversion]: Audit deprecated accounts on a subscription
- [Préversion]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>Système de gestion de mot de passe A.9.4.3

Cette solution blueprint vous permet d’appliquer des mots de passe forts, affectez 10 [Azure Policy](../../../policy/overview.md) définitions qui auditent les machines virtuelles Windows qui n’appliquent pas minimal et autres exigences de mot de passe. La reconnaissance des machines virtuelles en violation de la stratégie de force de mot de passe vous permet d’utiliser des actions correctives pour vérifier que les mots de passe pour tous les comptes d’utilisateur de machine virtuelle sont conformes avec la stratégie.

- [Préversion]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Préversion]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Préversion]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Préversion]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Préversion]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Préversion]: Audit Windows VM enforces password complexity requirements
- [Préversion]: Audit Windows VM maximum password age 70 days
- [Préversion]: Audit Windows VM minimum password age 1 day
- [Préversion]: Audit Windows VM passwords must be at least 14 characters
- [Préversion]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Stratégie A.10.1.1 sur l’utilisation de contrôles de chiffrement

Cette solution blueprint vous permet d’appliquer votre stratégie sur l’utilisation de contrôles de la manière dont le chiffrement en affectant 13 [Azure Policy](../../../policy/overview.md) définitions qui appliquent des contrôles de la manière dont le chiffrement spécifiques et d’audit d’utilisent des paramètres de chiffrement faible.
Comprendre où vos ressources Azure peuvent avoir des configurations non optimales de chiffrement peut vous aider à prendre des mesures correctives afin de ressources sont configurés conformément à votre stratégie de sécurité des informations. Plus précisément, les stratégies affectées par cette solution blueprint exigent le chiffrement pour les comptes de stockage d’objets blob et les comptes de stockage data lake ; exiger le chiffrement transparent des données sur les bases de données SQL ; audit du manque de chiffrement sur les comptes de stockage, les bases de données SQL, les disques de machine virtuelle et les variables de compte automation ; audit des connexions sécurisées pour les comptes de stockage, applications de fonction, application Web, applications API et le Cache Redis ; auditer le chiffrement de mot de passe faible machine virtuelle ; et d’audit de communication de Service Fabric non chiffrée.

- [Préversion]: Audit HTTPS only access for a Function App
- [Préversion]: Audit HTTPS only access for a Web Application
- [Préversion]: Audit HTTPS only access for an API App
- [Préversion]: Audit missing blob encryption for storage accounts
- [Préversion]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  le chiffrementn
- [Préversion]: Audit Windows VM should not store passwords using reversible encryption
- [Préversion]: Monitor unencrypted SQL database in Azure Security Center
- [Préversion]: Monitor unencrypted VM Disks in Azure Security Center
- Auditer l'activation du chiffrement des variables du compte Automation
- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage
- Auditer l'affectation à la propriété ClusterProtectionLevel de la valeur EncryptAndSign dans Service Fabric
- Auditer l’état du chiffrement transparent des données

## <a name="a1241-event-logging"></a>Journalisation des événements A.12.4.1

Cette solution blueprint vous permet de vérifier les événements de système sont enregistrés en affectant des sept [Azure Policy](../../../policy/overview.md) définitions qui d’audit consigne les paramètres de ressources Azure. Stratégie attribuée vérifie également si les machines virtuelles ne sont pas envoyer des journaux à un espace de travail analytique de journal spécifié.

- [Préversion] : Déploiement de l’Agent de dépendance d’audit - VM Image (système d’exploitation) non listées
- [Préversion] : Déploiement de l’Agent de dépendance dans le VMSS - Image de machine virtuelle (OS) retirée de la liste de l’audit
- [Préversion] : Déploiement de l’Agent d’Analytique du journal d’audit - VM Image (système d’exploitation) non listées
- [Préversion] : Déploiement de l’Agent d’Analytique de journal d’audit dans le VMSS - Image de machine virtuelle (OS) non répertorié
- [Préversion]: Monitor unaudited SQL database in Azure Security Center
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>Gestion de A.12.1 de vulnérabilités techniques

Cette solution blueprint vous permet de gérer les vulnérabilités du système d’information en affectant des cinq [Azure Policy](../../../policy/overview.md) définitions qui surveillent les mises à jour système, des vulnérabilités du système d’exploitation, des vulnérabilités SQL et machine virtuelle manquants vulnérabilités. Ces informations fournissent des informations en temps réel sur l’état de sécurité de vos ressources déployées et peuvent vous aider à hiérarchiser les actions de mise à jour.

- [Préversion]: Monitor missing Endpoint Protection in Azure Security Center
- [Préversion]: Monitor missing system updates in Azure Security Center
- [Préversion]: Monitor OS vulnerabilities in Azure Security Center
- [Préversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Préversion]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>Restrictions de A.12.6.2 sur l’installation du logiciel

Contrôles d’application adaptative est la solution à partir d’Azure Security Center vous permet de contrôler les applications pouvant s’exécuter sur vos machines virtuelles situées dans Azure. Cette solution blueprint affecte une définition de stratégie de Azure qui surveille les modifications apportées à l’ensemble des applications autorisées. Restrictions sur l’installation du logiciel peuvent vous aider à réduire le risque d’introduction de vulnérabilités logicielles.

- [Préversion]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Contrôles de réseau A.13.1.1

Cette solution blueprint permet de gérer et contrôler les réseaux en assignant un [Azure Policy](../../../policy/overview.md) définition qui surveille les groupes de sécurité réseau avec des règles strictes. Des règles trop permissives peuvent autoriser l’accès réseau involontaires et doivent être examinées.

- [Préversion]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procédures et stratégies de transfert des informations A.13.2.1

Le plan vous permet de garantir le transfert des informations avec les services Azure est sécurisé, affectez deux [Azure Policy](../../../policy/overview.md) définitions pour auditer les connexions sécurisées pour les comptes de stockage et le Cache Redis.

- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage

## <a name="a1413-protecting-application-services-transactions"></a>Transactions des services d’application de protection A.14.1.3

Cette solution blueprint vous permet de protéger les ressources du système d’informations, affectez trois [Azure Policy](../../../policy/overview.md) définitions qui surveille les points de terminaison non protégés, les applications et les comptes de stockage. Points de terminaison et les applications qui ne sont pas protégées par un pare-feu et les comptes de stockage avec un accès illimité peuvent autoriser l’accès involontaire aux informations contenues dans le système d’information.

- [Préversion]: Monitor unprotected network endpoints in Azure Security Center
- [Préversion]: Monitor unprotected web application in Azure Security Center
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="a1613-reporting-information-security-weaknesses"></a>Failles de sécurité informations A.16.1.3 Reporting

Cette solution blueprint vous permet d’assurer la prise de conscience des vulnérabilités du système, affectez cinq [Azure Policy](../../../policy/overview.md) définitions qui surveille les vulnérabilités, l’état de correctif et les alertes de logiciels malveillants dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent de disposer des informations en temps réel de l’état de la sécurité des ressources Azure déployées.

- [Préversion]: Monitor missing Endpoint Protection in Azure Security Center
- [Préversion]: Monitor missing system updates in Azure Security Center
- [Préversion]: Monitor OS vulnerabilities in Azure Security Center
- [Préversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Préversion]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez passé en revue le mappage de contrôle de la solution blueprint ISO 27001 Shared Services, consultez les articles suivants pour en savoir plus sur l’architecture et le déploiement de cet exemple :

> [!div class="nextstepaction"]
> [Plan de ISO 27001 Shared Services - vue d’ensemble](./index.md)
> [ISO 27001 Shared Services blueprint - déployer des étapes](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).