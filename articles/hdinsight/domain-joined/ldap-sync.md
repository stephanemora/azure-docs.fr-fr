---
title: Synchronisation LDAP dans Ranger et Apache Ambari dans Azure HDInsight
description: Cet article aborde la synchronisation LDAP dans Ranger et Ambari, et fournit des indications générales.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77465666"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronisation LDAP dans Ranger et Apache Ambari dans Azure HDInsight

Les clusters du Pack Sécurité Entreprise (ESP) HDInsight utilisent Ranger pour obtenir une autorisation. Apache Ambari et Ranger synchronisent tous les deux les utilisateurs et les groupes de manière indépendante et fonctionnent un peu différemment. Cet article est destiné à traiter la synchronisation LDAP dans Ranger et Ambari.

## <a name="general-guidelines"></a>Recommandations générales

* Déployez toujours les clusters avec des groupes.
* Au lieu de changer les filtres de groupe dans Ambari et Ranger, essayez de les gérer tous dans Azure AD et utilisez des groupes imbriqués pour importer les utilisateurs nécessaires.
* Une fois qu’un utilisateur est synchronisé, il n’est pas supprimé même s’il ne fait pas partie des groupes.
* Si vous devez changer directement les filtres LDAP, utilisez d’abord l’interface utilisateur car elle contient certaines validations.

## <a name="users-are-synced-separately"></a>Les utilisateurs sont synchronisés séparément

Ambari et Ranger ne partagent pas la base de données utilisateur car ils ont deux utilités différentes. Si un utilisateur doit utiliser l’interface utilisateur Ambari, il doit être synchronisé avec Ambari. Si l’utilisateur n’est pas synchronisé avec Ambari, l’interface utilisateur/l’API Ambari le rejettera, mais d’autres parties du système fonctionneront (celles-ci sont protégées par Ranger ou Resource Manager, et non pas par Ambari). Si vous voulez inclure l’utilisateur dans une stratégie Ranger, synchronisez l’utilisateur avec Ranger.

Quand un cluster sécurisé est déployé, les membres du groupe sont synchronisés de manière transitive (tous les sous-groupes et leurs membres) à la fois avec Ambari et Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Configuration et synchronisation d’utilisateurs Ambari

À partir des nœuds principaux, un travail Cron, `/opt/startup_scripts/start_ambari_ldap_sync.py`, est exécuté toutes les heures pour planifier la synchronisation des utilisateurs. Le travail Cron appelle les API REST Ambari pour effectuer la synchronisation. Le script soumet une liste d’utilisateurs et de groupes à synchroniser. (Étant donné que les utilisateurs peuvent ne pas appartenir aux groupes spécifiés, les deux sont spécifiés individuellement.) Ambari synchronise sAMAccountName en tant que nom d’utilisateur et tous les membres du groupe, de manière transitive.

Les journaux doivent se trouver dans `/var/log/ambari-server/ambari-server.log`. Pour plus d’informations, consultez [Configurer le niveau de journalisation d’Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Dans les clusters Data Lake, le hook de publication de créations d’utilisateur est utilisé pour créer les dossiers de base des utilisateurs synchronisés et ils sont définis comme propriétaires de ces dossiers. Si l’utilisateur n’est pas synchronisé correctement avec Ambari, il peut rencontrer des échecs lors de l’accès à des dossiers de préproduction et à d’autres dossiers temporaires.

### <a name="update-groups-to-be-synced-to-ambari"></a>Mettre à jour les groupes à synchroniser avec Ambari

Si vous ne pouvez pas gérer les appartenances aux groupes dans Azure AD, vous avez deux possibilités :

* Effectuez une synchronisation unique, comme décrit plus en détail dans [Synchroniser des utilisateurs et des groupes LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Chaque fois que l’appartenance aux groupes change, vous devez réeffectuer cette synchronisation.

* Écrivez un travail Cron, puis appelez l’[API Ambari régulièrement](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) avec les nouveaux groupes.

## <a name="ranger-user-sync-and-configuration"></a>Configuration et synchronisation d’utilisateurs Ranger

Ranger dispose d’un moteur de synchronisation intégré qui s’exécute toutes les heures pour synchroniser les utilisateurs. Il ne partage pas la base de données utilisateur avec Ambari. HDInsight configure le filtre de recherche pour synchroniser l’utilisateur administrateur, l’utilisateur de surveillance et les membres du groupe spécifié lors de la création du cluster. Les membres du groupe seront synchronisés de manière transitive :

* Désactivez la synchronisation incrémentielle.
* Activez le mappage de synchronisation de groupes d’utilisateurs.
* Spécifiez le filtre de recherche permettant d’inclure les membres des groupes transitifs.
* Synchronisez sAMAccountName pour les utilisateurs et l’attribut de nom des groupes.

### <a name="group-or-incremental-sync"></a>Synchronisation incrémentielle ou de groupes

Ranger prend en charge une option de synchronisation de groupes, mais il fonctionne comme une intersection avec le filtre d’utilisateurs. Ce n’est pas une union entre les appartenances aux groupes et le filtre d’utilisateur. Un cas d’utilisation typique pour le filtre de synchronisation de groupes dans Ranger est : - filtre de groupe : (dn=clusteradmingroup), filtre d’utilisateur : (city=seattle).

La synchronisation incrémentielle fonctionne uniquement pour les utilisateurs qui sont déjà synchronisés (la première fois). Elle ne synchronisera pas les nouveaux utilisateurs ajoutés aux groupes après la synchronisation initiale.

### <a name="update-ranger-sync-filter"></a>Mettre à jour le filtre de synchronisation de Ranger

Le filtre LDAP se trouve dans l’interface utilisateur Ambari, sous la section de configuration de la synchronisation des utilisateurs Ranger. Le filtre existant se présente sous la forme `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`. Veillez à ajouter un prédicat à la fin et à tester le filtre à l’aide de la commande de recherche `net ads`, de ldp.exe ou de quelque chose de semblable.

## <a name="ranger-user-sync-logs"></a>Journaux de synchronisation des utilisateurs Ranger

La synchronisation des utilisateurs Ranger peut se produire en dehors de l’un ou l’autre des nœuds principaux. Les journaux se trouvent dans `/var/log/ranger/usersync/usersync.log`. Pour augmenter le niveau de détail des journaux, effectuez les étapes suivantes :

1. Connectez-vous à Ambari.
1. Accédez à la section de configuration de Ranger.
1. Accédez à la section **usersync-log4j** avancée.
1. Définissez le niveau de `log4j.rootLogger` sur `DEBUG`. (Après ce changement, il doit ressembler à `log4j.rootLogger = DEBUG,logFile,FilterLog`).
1. Enregistrez la configuration, puis redémarrez Ranger.

## <a name="next-steps"></a>Étapes suivantes

* [Problèmes d’authentification dans Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchroniser les utilisateurs Azure AD avec un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
