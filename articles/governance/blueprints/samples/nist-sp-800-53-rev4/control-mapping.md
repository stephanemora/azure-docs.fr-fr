---
title: Exemple - Blueprint NIST SP 800-53 R4 - Mappage des contrôles
description: Mappage des contrôles de l’exemple de blueprint NIST SP 800-53 R4 à Azure Policy et RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343080"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Mappage des contrôles de l’exemple de blueprint NIST SP 800-53 R4

L’article suivant décrit en détail le mappage de l’exemple de blueprint Azure Blueprints NIST SP 800-53 R4 aux contrôles NIST SP 800-53 R4. Pour plus d’informations sur les contrôles, consultez [NIST SP 800-53](https://nvd.nist.gov/800-53).

Les mappages suivants concernent les contrôles **NIST SP 800-53 (Rév. 4)** . Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **[Préversion] : Auditer les contrôles NIST SP 800-53 R4 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

## <a name="ac-2-account-management"></a>AC-2 Gestion des comptes

Ce blueprint vous aide à passer en revue les comptes qui peuvent ne pas être conformes aux exigences en matière de gestion des comptes de votre organisation. Ce blueprint affecte cinq définitions Azure Policy qui auditent les comptes externes avec des autorisations de lecture, d’écriture et de propriétaire sur un abonnement ainsi que les comptes dépréciés. En passant en revue les comptes audités par ces stratégies, vous pouvez prendre les mesures appropriées pour veiller au respect des exigences en matière de gestion des comptes.

- [Préversion]: Audit deprecated accounts on a subscription
- [Préversion]: Audit deprecated accounts with owner permissions on a subscription
- [Préversion]: Audit external accounts with owner permissions on a subscription
- [Préversion]: Audit external accounts with read permissions on a subscription
- [Préversion]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Gestion des comptes | Schémas basés sur des rôles

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte également deux définitions [Azure Policy](../../../policy/overview.md) afin d’auditer l’utilisation de l’authentification Azure Active Directory pour les serveurs SQL et Service Fabric. L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft.

- Auditer le provisionnement d'un administrateur Azure Active Directory pour un serveur SQL
- Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Gestion des comptes | Supervision des comptes/utilisation atypique

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. Toutes les demandes JIT pour accéder aux machines virtuelles sont journalisées dans le journal d’activité, ce qui vous permet de détecter toute utilisation atypique. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- [Préversion] : Superviser l’éventuel accès juste-à-temps (JIT) au réseau dans Azure Security Center

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Application du flux d’informations

CORS (Cross origin Resource Sharing) peut autoriser un domaine externe à demander des ressources App Services. Microsoft vous recommande d’autoriser uniquement les domaines requis à interagir avec vos API, fonctions et applications web. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) pour vous aider à superviser les restrictions d’accès aux ressources CORS dans Azure Security Center.
Le fait de comprendre les implémentations CORS peut vous aider à vérifier que des contrôles de flux d’informations sont implémentés.

- [Préversion]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>AC-5 Séparation des tâches

Le fait d’avoir un seul propriétaire d’abonnement Azure ne permet pas d’assurer la redondance administrative. À l’inverse, un nombre trop élevé de propriétaires d’abonnement Azure peut augmenter le risque d’une violation par le biais d’un compte de propriétaire compromis. Ce blueprint vous permet de maintenir un nombre approprié de propriétaires d’abonnement Azure en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui auditent le nombre de propriétaires pour les abonnements Azure. Ce blueprint affecte également quatre définitions Azure Policy qui vous aident à contrôler l’appartenance au groupe Administrateurs sur les machines virtuelles Windows. La gestion des autorisations des propriétaires d’abonnement et des administrateurs de machine virtuelle peut vous aider à implémenter une séparation appropriée des tâches.

- [Préversion]: Audit maximum number of owners for a subscription
- [Préversion]: Audit minimum number of owners for subscription
- Vérifier que le groupe Administrateurs dans les machines virtuelles Windows exclut les membres spécifiés
- Vérifier que le groupe Administrateurs dans les machines virtuelles Windows inclut les membres spécifiés
- Déployer une extension de machine virtuelle pour vérifier que le groupe Administrateurs dans les machines virtuelles Windows exclut les membres spécifiés
- Déployer une extension de machine virtuelle pour vérifier que le groupe Administrateurs dans les machines virtuelles Windows inclut les membres spécifiés

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Privilèges minimum | Révision des privilèges utilisateur

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte six définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes à examiner en priorité. L’examen de ces indicateurs de compte peut vous aider à vous assurer que les contrôles de privilège minimum sont implémentés.

- [Préversion]: Audit maximum number of owners for a subscription
- [Préversion]: Audit minimum number of owners for subscription
- Vérifier que le groupe Administrateurs dans les machines virtuelles Windows exclut les membres spécifiés
- Vérifier que le groupe Administrateurs dans les machines virtuelles Windows inclut les membres spécifiés
- Déployer une extension de machine virtuelle pour vérifier que le groupe Administrateurs dans les machines virtuelles Windows exclut les membres spécifiés
- Déployer une extension de machine virtuelle pour vérifier que le groupe Administrateurs dans les machines virtuelles Windows inclut les membres spécifiés

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Accès à distance | Supervision/contrôle automatisé

Ce blueprint vous aide à superviser et à contrôler les accès distants en affectant trois définitions [Azure Policy](../../../policy/overview.md) pour vérifier que le débogage distant pour l’application Azure App Service est désactivé, et deux définitions de stratégie qui auditent les machines virtuelles Linux autorisant les connexions distantes à partir de comptes sans mot de passe. Ce blueprint affecte également une définition Azure Policy qui vous aide à superviser les accès non restreints aux comptes de stockage. En supervisant ces indicateurs, vous pouvez vérifier que les méthodes d’accès à distance sont conformes à votre stratégie de sécurité.

- [Préversion]: Audit remote debugging state for a Function App
- [Préversion]: Audit remote debugging state for a Web Application
- [Préversion]: Audit remote debugging state for an API App
- [Préversion]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [Préversion]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Contenu des enregistrements d’audit | Gestion centralisée du contenu planifié des enregistrements d’audit

Les données de journal collectées par Azure Monitor sont stockées dans un espace de travail Log Analytics, permettant une configuration et une gestion centralisées. Ce blueprint vous permet de garantir que les événements sont journalisés. Il affecte pour cela sept définitions [Azure Policy](../../../policy/overview.md) qui auditent et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles Azure.

- [Préversion] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Préversion]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Linux VMs
- [Préversion]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Réponse aux échecs du processus d’audit

Ce blueprint affecte cinq définitions [Azure Policy](../../../policy/overview.md) qui supervisent les configurations d’audit et de journalisation des événements. En supervisant ces configurations, vous pouvez obtenir une indication de la défaillance ou d’une mauvaise configuration du système d’audit et prendre des actions correctives.

- [Préversion]: Monitor unaudited SQL servers in Azure Security Center
- Auditer le paramètre de diagnostic
- Auditer les instances gérées SQL sans Advanced Data Security
- Auditer les paramètres d'audit au niveau du serveur SQL
- Auditer les serveurs SQL sans Advanced Data Security

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Révision, analyse et rapports d’audit | Révision et analyse centralisées

Les données de journal collectées par Azure Monitor sont stockées dans un espace de travail Log Analytics, permettant la génération de rapports et l’analyse centralisées. Ce blueprint vous permet de garantir que les événements sont journalisés. Il affecte pour cela sept définitions [Azure Policy](../../../policy/overview.md) qui auditent et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles Azure.

- [Préversion] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Préversion]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Linux VMs
- [Préversion]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>AU-12 Génération de l’audit

Ce blueprint vous permet de garantir que les événements système sont journalisés. Il affecte pour cela 15 définitions [Azure Policy](../../../policy/overview.md) qui vérifient les paramètres de journalisation sur les ressources Azure. Ces définitions de stratégie vérifient et appliquent le déploiement de l’agent Log Analytics sur les machines virtuelles et la configuration des paramètres d’audit pour les autres types de ressources Azure. Ces définitions de stratégie vérifient également la configuration des journaux de diagnostic pour fournir des insights sur les opérations effectuées au sein des ressources Azure. L’audit et Advanced Data Security sont configurés sur les serveurs SQL.

- [Préversion] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Préversion]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Linux VMs
- [Préversion]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Windows VMs
- [Préversion]: Monitor unaudited SQL servers in Azure Security Center
- Appliquer les paramètres de diagnostic pour les groupes de sécurité réseau
- Auditer le paramètre de diagnostic
- Auditer les instances gérées SQL sans Advanced Data Security
- Auditer les paramètres d'audit au niveau du serveur SQL
- Auditer les serveurs SQL sans Advanced Data Security
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer l’audit sur des serveurs SQL

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Fonctionnalités essentielles | Empêcher l’exécution d’un programme

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle des applications peut s’exécuter dans un mode de mise en conformité qui empêche l’exécution d’applications non approuvées. Ce blueprint affecte une définition Azure Policy qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- [Préversion]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Fonctionnalités essentielles | Logiciels autorisés / Mise sur liste verte

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle d’applications vous permet de créer des listes d’applications approuvées pour vos machines virtuelles. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- [Préversion]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 Logiciels installés par l’utilisateur

Le contrôle des applications adaptatif dans Azure Security Center est une solution intelligente et automatisée de mise en liste verte d’applications de bout en bout qui peut bloquer ou empêcher l’exécution de logiciels spécifiques sur vos machines virtuelles. Le contrôle d’applications peut vous aider à appliquer et à superviser la conformité à des stratégies de restriction logicielle. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pour lesquelles une liste verte d’applications est recommandée mais n’a pas encore été configurée.

- [Préversion]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>CP-7 Site de traitement secondaire

Azure Site Recovery réplique les charges de travail exécutées sur des machines virtuelles à partir d’un emplacement principal vers un emplacement secondaire. Si une panne survient au niveau du site principal, la charge de travail échoue sur l’emplacement secondaire. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui audite les machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée. En supervisant cet indicateur, vous pouvez vérifier que les contrôles d’urgence nécessaires sont en place.

- Auditer des machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes privilégiés

Ce blueprint vous aide à limiter et à contrôler les accès privilégiés en affectant deux définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes dotés d’autorisations de propriétaire et/ou d’écriture pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis.

- [Préversion]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Préversion]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes non privilégiés

Ce blueprint vous aide à limiter et à contrôler les accès en affectant une définition [Azure Policy](../../../policy/overview.md) pour auditer les comptes dotés d’autorisations de lecture pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis.

- [Préversion]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>IA-5 Gestion des authentificateurs

Ce blueprint affecte cinq définitions [Azure Policy](../../../policy/overview.md) qui auditent les machines virtuelles Linux autorisant les connexions à distance à partir de comptes sans mot de passe et/ou associés à des autorisations incorrectes définies dans le fichier passwd. Ce blueprint affecte également une définition de stratégie qui audite la conjugaison du type de chiffrement de mot de passe pour les machines virtuelles Windows. En supervisant ces indicateurs, vous pouvez vérifier que les authentificateurs de système sont conformes à la stratégie d’identification et d’authentification de votre organisation.

- [Préversion]: Audit that Linux VMs do not have accounts without passwords
- [Préversion]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [Préversion]: Audit that Linux VMs have the passwd file permissions set to 0644
- [Préversion]: Audit that Windows VMs store passwords using reversible encryption
- [Préversion]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Gestion des authentificateurs | Authentification basée sur mot de passe

Ce blueprint vous aide à appliquer des mots de passe forts en affectant 12 définitions [Azure Policy](../../../policy/overview.md) qui auditent les machines virtuelles Windows ne mettant pas en œuvre une force minimale ni d’autres exigences relatives aux mots de passe. En identifiant les machines virtuelles qui enfreignent la stratégie de force des mots de passe , vous pouvez prendre des actions correctives visant à rendre les mots de passe de tous les comptes d’utilisateurs de machine virtuelle conformes à la stratégie de mot de passe de votre organisation.

- [Préversion]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [Préversion]: Audit that Windows VMs have a maximum password age of 70 days
- [Préversion]: Audit that Windows VMs have a minimum password age of 1 day
- [Préversion]: Audit that Windows VMs have the password complexity setting enabled
- [Préversion]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [Préversion]: Audit that Windows VMs store passwords using reversible encryption
- [Préversion]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [Préversion]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [Préversion]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [Préversion]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [Préversion]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [Préversion]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Analyse des vulnérabilités

Ce blueprint vous aide à gérer les vulnérabilités du système d’informations en affectant quatre définitions [Azure Policy](../../../policy/overview.md) qui supervisent les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées. Ce blueprint affecte également trois définitions de stratégie qui auditent et appliquent Advanced Data Security sur les serveurs SQL. Advanced Data Security comprend l’évaluation des vulnérabilités et des fonctionnalités de protection avancée contre les menaces pour vous aider à comprendre les vulnérabilités dans vos ressources déployées.

- Auditer les instances gérées SQL sans Advanced Data Security
- Auditer les serveurs SQL sans Advanced Data Security
- Déployer Advanced Data Security sur des serveurs SQL
- [Préversion]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Préversion]: Monitor OS vulnerabilities in Azure Security Center
- [Préversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Préversion]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Protection contre le déni de service

Le niveau Standard de déni de service distribué (DDoS) d’Azure offre des fonctionnalités et des capacités d’atténuation supplémentaires par rapport au niveau de service De base. Ces fonctionnalités supplémentaires incluent l’intégration d’Azure Monitor et la possibilité de passer en revue les rapports d’atténuation post-attaque. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vérifie si le niveau Standard DDoS est activé. En comprenant la différence de capacité entre les niveaux de service, vous pouvez choisir la meilleure solution pour traiter les protections contre le déni de service dans votre environnement Azure.

- [Préversion]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>SC-7 Protection de la limite

Ce blueprint vous aide à gérer et à contrôler la limite système en affectant une définition [Azure Policy](../../../policy/overview.md) qui supervise les groupes de sécurité réseau associés avec des règles permissives. Les règles trop permissives sont susceptibles d’autoriser des accès réseau involontaires et doivent être réexaminées. Ce blueprint affecte également une définition de stratégie qui supervise les recommandations en matière de sécurisation des groupes de sécurité réseau dans Azure Security Center. Azure Security Center analyse les modèles de trafic des machines virtuelles accessibles sur Internet et fournit des recommandations en matière de règle de groupe de sécurité réseau afin de réduire la surface d’attaque potentielle.
Ce blueprint affecte également trois définitions de stratégie qui supervisent les points de terminaison, applications et comptes de stockage non protégés. Les points de terminaison et les applications qui ne sont pas protégés par un pare-feu, de même que les comptes de stockage avec un accès illimité, peuvent permettre un accès involontaire aux informations contenues dans le système d’information.

- [Préversion]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [Préversion]: Monitor permissive network access in Azure Security Center
- [Préversion]: Monitor unprotected network endpoints in Azure Security Center
- [Préversion]: Monitor unprotected web application in Azure Security Center
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Protection de la limite | Points d’accès

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. L’accès JIT à la machine virtuelle vous aide à limiter le nombre de connexions externes à vos ressources dans Azure. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- [Préversion] : Superviser l’éventuel accès juste-à-temps (JIT) au réseau dans Azure Security Center

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Protection de la limite | Services de télécommunications externes

L’accès juste-à-temps (JIT) à la machine virtuelle verrouille le trafic entrant vers les machines virtuelles Azure, réduisant l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles en cas de besoin. L’accès JIT à la machine virtuelle vous aide à gérer les exceptions de votre stratégie de flux de trafic en facilitant les processus de demande d’accès et d’approbation. Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous aide à superviser les machines virtuelles pouvant prendre en charge l’accès juste-à-temps mais qui n’ont pas encore été configurées.

- [Préversion] : Superviser l’éventuel accès juste-à-temps (JIT) au réseau dans Azure Security Center

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Protection des informations au repos | Protection par chiffrement

Ce blueprint vous aide à appliquer votre stratégie sur l’utilisation des contrôles de chiffrement pour protéger les informations au repos en affectant 9 définitions [Azure Policy](../../../policy/overview.md) qui mettent en œuvre des contrôles de chiffrement spécifiques et détectent l’utilisation de paramètres de chiffrement faibles. Le fait de savoir où vos ressources Azure peuvent avoir des configurations de chiffrement non optimales peut vous aider à prendre des mesures correctives visant à vérifier que les ressources sont configurées conformément à votre stratégie de sécurité des informations. Plus précisément, les définitions de stratégie affectées par ce blueprint exigent le chiffrement des comptes Data Lake Storage, exigent le chiffrement transparent des données sur les bases de données SQL et vérifient l’absence de chiffrement sur les bases de données SQL, les disques de machine virtuelle et les variables de compte Automation.

- [Préversion]: Monitor unencrypted SQL databases in Azure Security Center
- [Préversion]: Monitor unencrypted VM Disks in Azure Security Center
- Auditer le transfert sécurisé vers les comptes de stockage
- Auditer les instances gérées SQL sans Advanced Data Security
- Auditer les serveurs SQL sans Advanced Data Security
- Auditer l’état du chiffrement transparent des données
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer le chiffrement transparent des données sur les bases de données SQL
- Appliquer le chiffrement sur les comptes Data Lake Store

## <a name="si-2-flaw-remediation"></a>SI-2 Correction des défauts

Ce blueprint vous aide à gérer les défauts du système d’informations en affectant six définitions [Azure Policy](../../../policy/overview.md) qui supervisent les mises à jour système manquantes ainsi que les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées. Ce blueprint affecte également une définition de stratégie qui garantit la mise à niveau automatique du système d’exploitation pour les groupes de machines virtuelles identiques.

- [Préversion]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [Préversion]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Préversion]: Monitor missing system updates in Azure Security Center
- [Préversion]: Monitor OS vulnerabilities in Azure Security Center
- [Préversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Préversion]: Monitor VM Vulnerabilities in Azure Security Center
- Appliquer la mise à niveau automatique du système d’exploitation avec des contrôles d’intégrité des applications sur les groupes de machines virtuelles identiques

## <a name="si-3-malicious-code-protection"></a>SI-3 Protection contre les codes malveillants

Ce blueprint vous aide à gérer la protection des point de terminaison, notamment la protection contre le code malveillant, en affectant trois définitions [Azure Policy](../../../policy/overview.md) qui vérifient l’absence de protection des point de terminaison sur les machines virtuelles dans Azure Security Center et appliquent la solution anti-programme malveillant de Microsoft sur les machines virtuelles Windows.

- [Préversion]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Préversion]: Monitor missing Endpoint Protection in Azure Security Center
- Déployer l’extension Microsoft IaaSAntimalware par défaut pour Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Protection contre les codes malveillants | Gestion centralisée

Ce blueprint vous aide à gérer la protection des point de terminaison, notamment la protection contre le code malveillant, en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui vérifient l’absence de protection des point de terminaison sur les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de gestion centralisée et de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées.

- [Préversion]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Préversion]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 Supervision du système d’information

Ce blueprint vous aide à superviser votre système en auditant et en appliquant la journalisation et la sécurité des données aux ressources Azure. Plus précisément, les stratégies affectées auditent et appliquent le déploiement de l’agent Log Analytics et de paramètres de sécurité renforcée pour les bases de données SQL, les comptes de stockage et les ressources réseau. Ces fonctionnalités peuvent vous aider à détecter des comportements anormaux et des indicateurs d’attaques afin de prendre des mesures appropriées.

- [Préversion] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- [Préversion]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Préversion]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Linux VMs
- [Préversion]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Préversion]: Deploy Log Analytics Agent for Windows VMs
- Auditer les instances gérées SQL sans Advanced Data Security
- Auditer les serveurs SQL sans Advanced Data Security
- Déployer Advanced Data Security sur des serveurs SQL
- Déployer Advanced Threat Protection sur les comptes de stockage
- Déployer l’audit sur des serveurs SQL
- Déployer Network Watcher lors de la création de réseaux virtuels
- Déployer la détection de menaces sur les serveurs SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Supervision du système d’information | Analyser le trafic/l’exfiltration secrète

Advanced Threat Protection pour Stockage Azure détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Les alertes de protection couvrent les modèles d’accès inhabituels, les extractions ou chargements anormaux ainsi que les activités de stockage suspectes. Ces indicateurs peuvent vous aider à détecter l’exfiltration secrète d’informations.

- Déployer Advanced Threat Protection sur les comptes de stockage

## <a name="next-steps"></a>Étapes suivantes

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).