---
title: Contrôles de l’exemple de blueprint des tests d’évaluation de sécurité Azure
description: Mappage des contrôles de l’exemple de blueprint des tests d’évaluation de sécurité Azure à Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538661"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mappage des contrôles de l’exemple de blueprint des tests d’évaluation de sécurité Azure

L’article suivant explique en détail comment l’exemple de blueprint des tests d’évaluation de sécurité Azure Azure Blueprints est mappé aux contrôles des tests d’évaluation de sécurité Azure. Pour plus d’informations sur les contrôles, consultez [Tests d’évaluation de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/overview).

Les mappages suivants concernent les contrôles des **tests d’évaluation de sécurité Azure**. Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. Les contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] : Auditer les recommandations des tests d’évaluation de sécurité Azure et déployer des extensions de machine virtuelle de prise en charge spécifiques**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../../../policy/overview.md). Ces stratégies peuvent vous aider à [évaluer la conformité](../../../policy/how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance de 1:1 ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. De plus, la norme de conformité peut comprendre des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions Azure Policy pour cet exemple de blueprint de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Protéger les ressources à l’aide de groupes de sécurité réseau ou du pare-feu Azure sur votre réseau virtuel

- Les sous-réseaux doivent être associés à un groupe de sécurité réseau
- Les recommandations de renforcement de réseau adaptatif doivent être appliquées sur les machines virtuelles accessibles à partir d’Internet
- Les machines virtuelles doivent être connectées à un réseau virtuel approuvé
- Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau
- Service Bus doit utiliser un point de terminaison de service de réseau virtuel
- App Service doit utiliser un point de terminaison de service de réseau virtuel
- SQL Server doit utiliser un point de terminaison de service de réseau virtuel
- Event Hub doit utiliser un point de terminaison de service de réseau virtuel
- Cosmos DB doit utiliser un point de terminaison de service de réseau virtuel
- Key Vault doit utiliser un point de terminaison de service de réseau virtuel
- Auditer l'accès réseau non restreint aux comptes de stockage
- Les comptes de stockage doivent utiliser un point de terminaison de service de réseau virtuel
- Container Registry doit utiliser un point de terminaison de service de réseau virtuel
- Les réseaux virtuels doivent utiliser la passerelle de réseau virtuel spécifiée
- Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes
- \[Préversion\] : Le transfert IP doit être désactivé sur votre machine virtuelle.
- Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau
- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles
- Les ports de gestion doivent être fermés sur vos machines virtuelles

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

- Network Watcher doit être activé

## <a name="13-protect-critical-web-applications"></a>1.3 Protéger les applications web critiques

- Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application web
- CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web
- CORS ne doit pas autoriser toutes les ressources à accéder à vos applications de fonction
- CORS ne doit pas autoriser toutes les ressources à accéder à votre application API
- Le débogage à distance doit être désactivé pour les applications web
- Le débogage à distance doit être désactivé pour les applications de fonction
- Le débogage à distance doit être désactivé pour les applications API

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Refuser les communications avec des adresses IP connues comme étant malveillantes

- DDoS Protection Standard doit être activé
- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles
- Les recommandations de renforcement de réseau adaptatif doivent être appliquées sur les machines virtuelles accessibles à partir d’Internet

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Enregistrer les paquets réseau et les journaux de flux

- Network Watcher doit être activé

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les changements

- Prérequis de déploiement pour l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Accès réseau »
- Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Client réseau Microsoft »
- Déployer des prérequis pour auditer des configurations de machines virtuelles Windows dans « Options de sécurité - Sécurité réseau »
- Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Sécurité réseau »
- Déployer des prérequis pour auditer des configurations de machines virtuelles Windows dans « Options de sécurité - Serveur réseau Microsoft »
- Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Options de sécurité - Serveur réseau Microsoft »
- Déployer des prérequis pour auditer des configurations de machines virtuelles Windows dans « Modèles d’administration - Réseau »
- Afficher les résultats de l’audit des configurations de machines virtuelles Windows dans « Modèles d’administration - Réseau »

## <a name="22-configure-central-security-log-management"></a>2.2 Configurer la gestion centrale des journaux de sécurité

- L’agent Log Analytics doit être installé sur les machines virtuelles
- L’agent Log Analytics doit être installé sur les groupes identiques de machines virtuelles
- Déployer des prérequis pour auditer des machines virtuelles Windows sur lesquelles l’agent Log Analytics n’est pas connecté comme prévu
- Afficher les résultats de l’audit des machines virtuelles Windows sur lesquelles l’agent Log Analytics n’est pas connecté comme prévu
- Le profil de journal Azure Monitor doit collecter des journaux pour les catégories « écriture », « suppression » et « action »
- Azure Monitor doit collecter les journaux d’activité dans toutes les régions
- Le provisionnement automatique de l’agent de supervision Log Analytics doit être activé sur votre abonnement

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Activer la journalisation d’audit pour les ressources Azure

- Les journaux de diagnostic dans Azure Data Lake Store doivent être activés
- Les journaux de diagnostic dans Logic Apps doivent être activés.
- Les journaux de diagnostic dans IoT Hub doivent être activés
- Les journaux de diagnostic doivent être activés dans les comptes Batch
- Les journaux de diagnostic dans les groupes identiques de machines virtuelles doivent être activés
- Les journaux de diagnostic dans Event Hub doivent être activés.
- Les journaux de diagnostic dans les services Search doivent être activés.
- Les journaux de diagnostic dans App Services doivent être activés
- Les journaux de diagnostic dans Data Lake Analytics doivent être activés
- Les journaux de diagnostic dans Key Vault doivent être activés.
- Les journaux de diagnostic dans Service Bus doivent être activés
- Les journaux de diagnostic dans Azure Stream Analytics doivent être activés
- L’audit sur SQL Server doit être activé
- Auditer le paramètre de diagnostic

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Collecter les journaux de sécurité des systèmes d’exploitation

- Le provisionnement automatique de l’agent de supervision Log Analytics doit être activé sur votre abonnement
- L’agent Log Analytics doit être installé sur les machines virtuelles
- L’agent Log Analytics doit être installé sur les groupes identiques de machines virtuelles
- Déployer des prérequis pour auditer des machines virtuelles Windows sur lesquelles l’agent Log Analytics n’est pas connecté comme prévu
- Afficher les résultats de l’audit des machines virtuelles Windows sur lesquelles l’agent Log Analytics n’est pas connecté comme prévu

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Activer les alertes d’activité anormale

- Le niveau tarifaire standard Security Center doit être sélectionné
- Advanced Data Security doit être activé sur vos serveurs SQL
- Advanced Data Security doit être activée sur vos instances managées SQL.
- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security du serveur SQL.
- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security de l’instance gérée SQL.

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centraliser la journalisation anti-programme malveillant

- Microsoft Antimalware pour Azure doit être configuré pour mettre à jour automatiquement les signatures de protection
- Superviser les agents Endpoint Protection manquants dans Azure Security Center
- La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Tenir un inventaire des comptes d’administration

- Trois propriétaires au plus doivent être désignés pour votre abonnement
- Plusieurs propriétaires doivent être affectés à votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Utiliser des comptes d’administration dédiés

- Déployer des prérequis pour auditer des machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas seulement les membres spécifiés
- Afficher les résultats de l’audit des machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas seulement les membres spécifiés
- Déployer des prérequis pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Afficher les résultats d’audit des machines virtuelles Windows dans lesquelles le groupe Administrateurs ne contient pas tous les membres spécifiés
- Prérequis de déploiement pour auditer les machines virtuelles Windows dans lesquelles le groupe Administrateurs contient un membre spécifié
- Afficher les résultats de l’audit des machines virtuelles Windows dans lesquelles le groupe Administrateurs contient l’un des membres spécifiés
- Trois propriétaires au plus doivent être désignés pour votre abonnement
- Plusieurs propriétaires doivent être affectés à votre abonnement

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement
- L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Journaliser et générer des alertes en cas d’activités suspectes depuis les comptes d’administration

- Le niveau tarifaire standard Security Center doit être sélectionné

## <a name="39-use-azure-active-directory"></a>3.9 Utiliser Azure Active Directory

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL
- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client
- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application API
- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application web
- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application de fonction

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Revoir et harmoniser régulièrement les accès utilisateur

- Les comptes déconseillés doivent être supprimés de votre abonnement
- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Tenir un inventaire des informations sensibles

- Les données sensibles de vos bases de données SQL doivent être classifiées

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Chiffrer toutes les informations sensibles en transit

- La sécurisation du transfert vers des comptes de stockage doit être activée
- La dernière version de TLS doit être utilisée dans votre application API
- La dernière version de TLS doit être utilisée dans votre application web
- La dernière version de TLS doit être utilisée dans votre application de fonction
- Function App ne doit pas être accessible via HTTPS
- L'application web ne doit pas être accessible via HTTPS
- L'application API doit uniquement être accessible via HTTPS
- L’application de la connexion SSL doit être activée pour les serveurs de base de données MySQL
- L’application de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL
- Seules les connexions sécurisées à votre cache Redis doivent être activées

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Utiliser un outil de découverte actif pour identifier les données sensibles

- Les données sensibles de vos bases de données SQL doivent être classifiées
- Advanced Data Security doit être activé sur vos serveurs SQL
- Advanced Data Security doit être activée sur vos instances managées SQL.

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Utiliser Azure RBAC pour contrôler l’accès aux ressources

- Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sans Kubernetes Services.
- Auditer l’utilisation de règles personnalisées RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Chiffrer des informations sensibles au repos

- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL
- Le chiffrement de disque doit être appliqué sur les machines virtuelles
- Les disques non attachés doivent être chiffrés
- Le protecteur TDE du serveur SQL doit être chiffré avec votre propre clé.
- Le protecteur TDE de l’instance gérée SQL doit être chiffré avec votre propre clé.
- Les variables de compte Automation doivent être chiffrées
- La propriété ClusterProtectionLevel doit être définie sur EncryptAndSign pour les clusters Service Fabric

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Journaliser et générer des alertes en cas de modifications apportées aux ressources Azure critiques

- Azure Monitor doit collecter les journaux d’activité dans toutes les régions

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Exécuter les outils d’analyse des vulnérabilités automatisés

- L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL
- L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL
- \[Préversion\] L’évaluation des vulnérabilités doit être activée sur les machines virtuelles
- Les paramètres d’évaluation des vulnérabilités de SQL Server doivent contenir une adresse e-mail pour recevoir les rapports d’analyse

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Déployer une solution de gestion des correctifs de système d’exploitation automatisée

- Des mises à jour système doivent être installées sur vos machines
- Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques
- Vérifier que la version du .NET Framework est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version du .NET Framework est la plus récente, si elle est utilisée dans le cadre de l’application web
- Vérifier que la version du .NET Framework est la plus récente, si elle est utilisée dans le cadre de l’application API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Déployer une solution de gestion des correctifs logiciels tiers automatisée

- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application web
- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application web
- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application web
- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application API
- Kubernetes Services doit être mis à niveau vers une version non vulnérable de Kubernetes

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Utiliser un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes

- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités
- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées
- Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées
- Les vulnérabilités de vos bases de données SQL doivent être éliminées

## <a name="68-use-only-approved-applications"></a>6.8 Utiliser uniquement des applications approuvées

- Le niveau tarifaire standard Security Center doit être sélectionné
- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="69-use-only-approved-azure-services"></a>6.9 Utiliser uniquement des services Azure approuvés

- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager
- Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager

## <a name="610-implement-approved-application-list"></a>6.10 Implémenter une liste d’applications approuvées

- Le niveau tarifaire standard Security Center doit être sélectionné
- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Gérer les configurations de ressources Azure sécurisées

- \[Préversion\] : Des stratégies de sécurité des pods doivent être définies sur les services Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Préserver la sécurité des configurations du système d’exploitation

- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées
- Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implémenter la supervision de la configuration automatique pour les services Azure

- \[Préversion\] : Des stratégies de sécurité des pods doivent être définies sur les services Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implémenter la supervision de la configuration automatique pour les systèmes d’exploitation

- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées
- Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées
- Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées

## <a name="711-manage-azure-secrets-securely"></a>7.11 Gérer les secrets Azure en toute sécurité

- Les objets Key Vault doivent être récupérables

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Gérer les identités de façon sécurisée et automatique

- Une identité managée doit être utilisée dans votre application de fonction
- Une identité managée doit être utilisée dans votre application web
- Une identité managée doit être utilisée dans votre application API

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Utiliser un logiciel anti-programme malveillant géré de manière centralisée

- Superviser les agents Endpoint Protection manquants dans Azure Security Center
- La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

- Le niveau tarifaire standard Security Center doit être sélectionné

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Vérifier que les logiciels et signatures anti-programme malveillant sont à jour

- Microsoft Antimalware pour Azure doit être configuré pour mettre à jour automatiquement les signatures de protection

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Garantir des sauvegardes automatiques régulières

- La sauvegarde géoredondante à long terme doit être activée pour les bases de données Azure SQL
- La sauvegarde géoredondante doit être activée pour Azure Database pour MySQL
- La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL
- La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB
- La sauvegarde Azure doit être activée pour les machines virtuelles

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

- La sauvegarde géoredondante à long terme doit être activée pour les bases de données Azure SQL
- La sauvegarde géoredondante doit être activée pour Azure Database pour MySQL
- La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL
- La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB
- La sauvegarde Azure doit être activée pour les machines virtuelles

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Garantir la protection des sauvegardes et des clés gérées par le client

- Les objets Key Vault doivent être récupérables

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Créer une procédure de scoring et de hiérarchisation des incidents

- Le niveau tarifaire standard Security Center doit être sélectionné

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Fournir les informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

- Une adresse e-mail de contact de sécurité doit être fournie pour votre abonnement
- Un numéro de téléphone de contact de sécurité doit être fourni pour votre abonnement
- Les paramètres Advanced Data Security pour le serveur SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.
- Les paramètres Advanced Data Security pour l’instance gérée SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.
- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security SQL Server
- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security de l’instance managée SQL

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez examiné le mappage des contrôles du blueprint des tests d’évaluation de sécurité Azure, visitez Azure Policy dans le portail Azure pour affecter l’initiative :

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).