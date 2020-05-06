---
title: Exemples de contrôles de blueprint Charge de travail ASE/SQL ISO 27001
description: Correspondance des contrôles de l’exemple de blueprint Charge de travail App Service Environment/SQL Database ISO 27001 à Azure Policy et RBAC.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: bc370170374654bf8bc9ae9ed3f1b545f4db8cba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75920701"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Correspondance des contrôles de l’exemple de blueprint Charge de travail ASE/SQL ISO 27001

L’article suivant décrit en détail comment l’exemple de blueprint Charge de travail ASE/SQL ISO 27001 Azure Blueprints mappe aux contrôles ISO 27001. Pour plus d’informations sur les contrôles, consultez [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Les correspondances suivantes concernent les contrôles **ISO 27001:2013**. Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] Auditer les contrôles ISO 27001:2013 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../../../policy/overview.md). Ces stratégies peuvent vous aider à [évaluer la conformité](../../../policy/how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance de 1:1 ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions Azure Policy pour cet exemple de blueprint de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Répartition des tâches

Le fait d’avoir un seul propriétaire d’abonnement Azure ne permet pas d’assurer la redondance administrative. À l’inverse, un nombre trop élevé de propriétaires d’abonnement Azure peut augmenter le risque d’une violation par le biais d’un compte de propriétaire compromis. Ce blueprint vous permet de maintenir un nombre approprié de propriétaires d’abonnement Azure en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui auditent le nombre de propriétaires pour les abonnements Azure. La gestion des autorisations des propriétaires d’abonnement peut vous aider à implémenter une séparation appropriée des tâches.

- \[Préversion\] : Auditer le nombre minimal de propriétaires d’un abonnement
- \[Préversion\] : Auditer le nombre maximal de propriétaires d’un abonnement

## <a name="a821-classification-of-information"></a>A.8.2.1 Classification des informations

Le [service d’évaluation des vulnérabilités SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) Azure peut vous aider à découvrir des données sensibles stockées dans vos bases de données et inclut des recommandations permettant de classer ces données. Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) pour vérifier que les vulnérabilités identifiées au cours de l’analyse Évaluation des vulnérabilités SQL ont été corrigées.

- \[Préversion\] : Superviser les résultats de l’évaluation des vulnérabilités SQL dans Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Accès aux réseaux et aux services réseau

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour gérer qui a accès aux ressources Azure. Ce blueprint vous permet de contrôler l’accès aux ressources Azure en affectant sept définitions [Azure Policy](../../../policy/overview.md). Ces stratégies vérifient l’utilisation des types de ressources et des configurations susceptibles d’accorder un accès plus permissif aux ressources.
Le fait de savoir quelles ressources enfreignent ces stratégies peut vous aider à prendre des actions correctives visant à limiter l’accès aux ressources Azure aux utilisateurs autorisés.

- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer les comptes de machines virtuelles Linux sans mot de passe
- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer une machine virtuelle Linux autorisant les connexions à distance à partir de comptes sans mot de passe
- \[Préversion\] : Auditer les comptes de machines virtuelles Linux sans mot de passe
- \[Préversion\] : Auditer une machine virtuelle Linux autorisant les connexions à distance à partir de comptes sans mot de passe
- Auditer l'utilisation des comptes de stockage classiques
- Auditer l'utilisation des machines virtuelles classiques
- Faire l’audit des machines virtuelles n’utilisant aucun disque managé

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestion des droits d’accès à privilèges

Ce blueprint vous permet de limiter et de contrôler les droits d’accès privilégié en affectant quatre définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes externes avec des autorisations de propriétaire et/ou en écriture et les comptes avec des autorisations de propriétaire et/ou en écriture pour lesquels l’authentification multifacteur n’est pas activée. Azure implémente le contrôle d’accès en fonction du rôle (RBAC) pour gérer qui a accès aux ressources Azure. Ce blueprint attribue également trois définitions Azure Policy afin de vérifier l’utilisation de l’authentification Azure Active Directory pour les serveurs SQL et Service Fabric. L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft. Ce blueprint affecte également une définition Azure Policy pour vérifier l’utilisation des règles RBAC personnalisées. Ces dernières étant non exemptes d’erreurs, le fait de savoir où elles sont implémentées peut vous aider à déterminer les besoins réels et l’implémentation appropriée.

- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations de propriétaire sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations d’écriture sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations de type propriétaire
- \[Préversion\] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations d’écriture
- Auditer le provisionnement d'un administrateur Azure Active Directory pour un serveur SQL
- Auditer l'utilisation d'Azure Active Directory pour l'authentification client dans Service Fabric
- Auditer l’utilisation de règles personnalisées RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestion des informations secrètes d’authentification des utilisateurs

Ce blueprint affecte trois définitions [Azure Policy](../../../policy/overview.md) afin d’auditer les comptes pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur permet de sécuriser les comptes même si un élément des informations d’authentification est compromis. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis. Ce blueprint affecte également deux définitions Azure Policy qui vérifient les autorisations associées aux fichiers de mot de passe des machines virtuelles Linux et qui émettent une alerte si elles sont définies incorrectement. Cette configuration vous permet de prendre une action corrective pour vérifier que les authentificateurs ne sont pas compromis.

- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations de propriétaire sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations de lecture sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations d’écriture sur lesquels MFA n’est pas activé
- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer les autorisations de fichiers passwd de machine virtuelle Linux
- \[Préversion\] : Vérifier que les autorisations de fichiers /etc/passwd de machine virtuelle Linux sont définies sur 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revue des droits d’accès utilisateurs

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint affecte quatre définitions [Azure Policy](../../../policy/overview.md) qui permettent de déterminer les comptes devant en priorité faire l’objet d’une revue, notamment les comptes dépréciés et les comptes externes avec des autorisations élevées.

- \[Préversion\] : Auditer les comptes dépréciés d’un abonnement
- \[Préversion\] : Auditer les comptes dépréciés d’un abonnement qui disposent d’autorisations de propriétaire
- \[Préversion\] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations de type propriétaire
- \[Préversion\] : Auditer les comptes externes d’un abonnement qui disposent d’autorisations d’écriture

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Suppression ou adaptation des droits d’accès

Azure implémente le [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. Grâce à [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) et à RBAC, vous pouvez mettre à jour les rôles d’utilisateur pour refléter les changements organisationnels. Si nécessaire, vous pouvez empêcher des comptes de se connecter (ou les supprimer). Dans ce cas, les droits d’accès aux ressources Azure sont immédiatement supprimés. Ce blueprint affecte deux définitions [Azure Policy](../../../policy/overview.md) pour auditer le compte déprécié dont la suppression doit être envisagée.

- \[Préversion\] : Auditer les comptes dépréciés d’un abonnement
- \[Préversion\] : Auditer les comptes dépréciés d’un abonnement qui disposent d’autorisations de propriétaire

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Sécuriser les procédures de connexion

Ce blueprint attribue trois définitions Azure Policy afin d’auditer les comptes pour lesquels l’authentification multifacteur n’est pas activée. L’authentification multifacteur Azure fournit une sécurité supplémentaire en exigeant une deuxième forme d’authentification, et procure une authentification renforcée. En supervisant les comptes pour lesquels l’authentification multifacteur n’est pas activée, vous pouvez identifier ceux qui sont plus susceptibles d’être compromis.

- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations de propriétaire sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations de lecture sur lesquels MFA n’est pas activé
- \[Préversion\] : Auditer les comptes d’un abonnement qui disposent d’autorisations d’écriture sur lesquels MFA n’est pas activé

## <a name="a943-password-management-system"></a>A.9.4.3 Système de gestion des mots de passe

Ce blueprint vous aide à appliquer des mots de passe forts en affectant 10 définitions [Azure Policy](../../../policy/overview.md) qui détectent les machines virtuelles Windows ne mettant pas en œuvre une force minimale ni d’autres exigences relatives aux mots de passe. Le fait d’avoir connaissance des machines virtuelles qui enfreignent la stratégie de force des mots de passe peut vous aider à prendre des actions correctives visant à rendre les mots de passe de tous les comptes d’utilisateurs de machine virtuelle conformes à la stratégie.

- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer les exigences de complexité de mot de passe
- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer l’âge maximal du mot de passe des machines virtuelles Windows de 70 jours
- \[Préversion\] : Déployer une extension de machine virtuelle pour auditer l’âge minimal du mot de passe des machines virtuelles Windows de 1 jour
- \[Préversion\] : Déployer une extension de machine virtuelle pour vérifier que les mots de passe des machines virtuelles Windows comportent au moins 14 caractères
- \[Préversion\] : Déployer une extension de machine virtuelle pour vérifier que les machines virtuelles Windows n’autorisent pas les 24 mots de passe précédents
- \[Préversion\] : Vérifier que les machines virtuelles Windows appliquent les exigences de complexité de mot de passe
- \[Préversion\] : Auditer la durée de vie maximale du mot de passe de machine virtuelle Windows de 70 jours
- \[Préversion\] : Auditer la durée de vie minimale du mot de passe de machine virtuelle Windows de 1 jour
- \[Préversion\] : Vérifier que les mots de passe des machines virtuelles Windows comportent au moins 14 caractères
- \[Préversion\] : Vérifier que les machines virtuelles Windows n’autorisent pas les 24 mots de passe précédents

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Stratégie sur l’utilisation des contrôles de chiffrement

Ce blueprint vous aide à appliquer votre stratégie sur l’utilisation des contrôles de chiffrement en affectant 13 définitions [Azure Policy](../../../policy/overview.md) qui mettent en œuvre des contrôles de chiffrement spécifiques et détectent l’utilisation de paramètres de chiffrement faibles.
Le fait de savoir où vos ressources Azure peuvent avoir des configurations de chiffrement non optimales peut vous aider à prendre des mesures correctives visant à vérifier que les ressources sont configurées conformément à votre stratégie de sécurité des informations. Plus précisément, les stratégies affectées par ce blueprint exigent le chiffrement des comptes de stockage d’objets blob et des comptes Data Lake Storage ; exigent le chiffrement transparent des données dans les bases de données SQL ; vérifient le chiffrement sur les comptes de stockage, bases de données SQL, disques de machine virtuelle et variables de compte Automation ; vérifient les connexions non sécurisées aux comptes de stockage, aux applications de fonction, aux applications web, aux applications API et au Cache Redis ; vérifient si le chiffrement par mot de passe des machines virtuelles est faible ; et vérifient les communication Service Fabric non chiffrées.

- \[Préversion\] : Auditer l’accès HTTPS uniquement pour une application de fonction
- \[Préversion\] : Auditer l’accès HTTPS uniquement pour une application web
- \[Préversion\] : Auditer l’accès HTTPS uniquement pour une application API
- \[Préversion\] : Auditer le chiffrement d’objet blob manquant pour les comptes de stockage
- \[Préversion\] : Déployer une extension de machine virtuelle pour vérifier que les machines virtuelles Windows ne stockent pas les mots de passe à l’aide du chiffrement réversible
- \[Préversion\] : Vérifier que les machines virtuelles Windows ne stockent pas les mots de passe à l’aide du chiffrement réversible
- \[Préversion\] : Superviser les disques de machine virtuelle non chiffrés dans Azure Security Center
- Auditer l'activation du chiffrement des variables du compte Automation
- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage
- Auditer l'affectation à la propriété ClusterProtectionLevel de la valeur EncryptAndSign dans Service Fabric
- Auditer l’état du chiffrement transparent des données
- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="a1241-event-logging"></a>A.12.4.1 Journalisation des événements

Ce blueprint vous aide à vérifier que les événements système sont consignés en affectant sept définitions [Azure Policy](../../../policy/overview.md) qui vérifient les paramètres de journal d’audit sur les ressources Azure.
Les journaux de diagnostic fournissent des insights sur les opérations effectuées dans vos ressources Azure.

- \[Préversion\] : Vérifier le déploiement de Dependency Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier le déploiement de Dependency Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Journaux d’administrateur et d’opérateur

Ce blueprint vous permet de garantir que les événements système sont journalisés en attribuant sept définitions Azure Policy qui vérifient les paramètres du journal sur les ressources Azure. Les journaux de diagnostic fournissent des insights sur les opérations effectuées dans vos ressources Azure.

- \[Préversion\] : Vérifier le déploiement de Dependency Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier le déploiement de Dependency Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Synchronisation de l’horloge

Ce blueprint vous permet de garantir que les événements système sont journalisés en attribuant sept définitions Azure Policy qui vérifient les paramètres du journal sur les ressources Azure. Les journaux Azure s’appuient sur les horloges internes synchronisées pour créer un enregistrement corrélé dans le temps des événements entre les ressources.

- \[Préversion\] : Vérifier le déploiement de Dependency Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Vérifier le déploiement de Dependency Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent - Image de machine virtuelle (système d’exploitation) non listée
- \[Préversion\] : Auditer le déploiement de Log Analytics Agent dans VMSS - Image de machine virtuelle (système d’exploitation) non listée
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installation de logiciels sur les systèmes d’exploitation

La solution Contrôles d’application adaptatifs proposée par Azure Security Center vous permet de contrôler les applications pouvant s’exécuter sur vos machines virtuelles situées dans Azure. Ce blueprint affecte une définition Azure Policy qui supervise les changements apportés à l’ensemble des applications autorisées. Grâce à cette fonctionnalité, vous pouvez contrôler l’installation de logiciels et d’applications sur des machines virtuelles Azure.

- \[Préversion\] : Superviser la configuration éventuelle d’une liste verte d’applications dans Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Gestion des vulnérabilités techniques

Ce blueprint vous permet de gérer les vulnérabilités du système d’informations en attribuant cinq définitions [Azure Policy](../../../policy/overview.md) qui supervisent les mises à jour système manquantes ainsi que les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles dans Azure Security Center. Azure Security Center fournit des fonctionnalités de création de rapports qui vous permettent d’obtenir des insights en temps réel sur l’état de la sécurité des ressources Azure déployées.

- \[Préversion\] : Superviser les agents Endpoint Protection manquants dans Azure Security Center
- \[Préversion\] : Superviser les mises à jour système manquantes dans Azure Security Center
- \[Préversion\] : Superviser les vulnérabilités du système d’exploitation dans Azure Security Center
- \[Préversion\] : Superviser les résultats de l’évaluation des vulnérabilités SQL dans Azure Security Center
- \[Préversion\] : Superviser les vulnérabilités de machine virtuelle dans Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrictions liées à l’installation de logiciels

La solution Contrôles d’application adaptatifs proposée par Azure Security Center vous permet de contrôler les applications pouvant s’exécuter sur vos machines virtuelles situées dans Azure. Ce blueprint affecte une définition Azure Policy qui supervise les changements apportés à l’ensemble des applications autorisées. La mise en place de restrictions au niveau de l’installation des logiciels peut vous aider à réduire le risque d’introduction de vulnérabilités logicielles.

- \[Préversion\] : Superviser la configuration éventuelle d’une liste verte d’applications dans Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Contrôle des réseaux

Ce blueprint vous aide à gérer et à contrôler les réseaux en affectant une définition [Azure Policy](../../../policy/overview.md) qui supervise les groupes de sécurité réseau associés à des règles permissives. Les règles trop permissives sont susceptibles d’autoriser des accès réseau involontaires et doivent être réexaminées. Ce blueprint attribue également trois définitions Azure Policy qui supervisent les points de terminaison, applications et comptes de stockage non protégés. Les points de terminaison et les applications qui ne sont pas protégés par un pare-feu, de même que les comptes de stockage avec un accès illimité, peuvent permettre un accès involontaire aux informations contenues dans le système d’information.

- \[Préversion\] : Superviser l’accès réseau permissif dans Azure Security Center
- \[Préversion\] : Superviser les points de terminaison réseau non protégés dans Azure Security Center
- \[Préversion\] : Superviser les applications web non protégées dans Azure Security Center
- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Politiques et procédures de transfert de l’information

Le blueprint vous aide à sécuriser le transfert des informations avec les services Azure en affectant deux définitions [Azure Policy](../../../policy/overview.md) qui auditent les connexions non sécurisées aux comptes de stockage et au cache Redis.

- Auditer l'activation des connexions sécurisées uniquement à votre cache Redis
- Auditer le transfert sécurisé vers les comptes de stockage

## <a name="next-steps"></a>Étapes suivantes

Vous venez de passer en revue le mappage des contrôles de l’exemple de blueprint Charge de travail App Service Environment/SQL Database ISO 27001. Consultez à présent les articles suivants pour découvrir l’architecture et le déploiement de cet exemple :

> [!div class="nextstepaction"]
> [Blueprint Charge de travail App Service Environment/SQL Database ISO 27001 - Vue d’ensemble](./index.md)
> [Blueprint Charge de travail App Service Environment/SQL Database ISO 27001 - Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).