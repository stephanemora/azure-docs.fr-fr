---
title: Résoudre les erreurs de sauvegarde de bases de données SAP HANA
description: Décrit comment résoudre les erreurs courantes qui peuvent survenir lorsque vous utilisez le service Sauvegarde Azure pour sauvegarder des bases de données SAP HANA.
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: d3dce152b428fc29c203236d8d61a88c96d5134d
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796686"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Résoudre les problèmes de sauvegarde des bases de données SAP HANA sur Azure

Cet article fournit des informations de dépannage pour la sauvegarde des bases de données SAP HANA sur les machines virtuelles Azure. Pour en savoir plus sur les scénarios de sauvegarde de bases de données SAP HANA actuellement pris en charge, consultez [Prise en charge des scénarios](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Prérequis et autorisations

Reportez-vous aux sections [Conditions préalables](tutorial-backup-sap-hana-db.md#prerequisites) et [Ce que fait le script de préinscription](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) avant de configurer les sauvegardes.

## <a name="common-user-errors"></a>Erreurs utilisateur courantes

###### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Message d’erreur**      | <span style="font-weight:normal">Sauvegarde Azure ne dispose pas des privilèges de rôle nécessaires pour effectuer des opérations de sauvegarde et de restauration</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Causes possibles**    | Toutes les opérations échouent avec cette erreur quand l’utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER) n’a pas le rôle **SAP_INTERNAL_HANA_SUPPORT** affecté ou si le rôle a peut-être été remplacé.                          |
| **Action recommandée** | Téléchargez et exécutez le [script de préinscription](https://aka.ms/scriptforpermsonhana) sur l’instance SAP HANA, ou affectez manuellement le rôle **SAP_INTERNAL_HANA_SUPPORT** à l’utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER).<br><br>**Remarque**<br><br>Si vous utilisez HANA 2.0 SPS04 Rev 46 et versions ultérieures, cette erreur ne se produit pas, car l’utilisation du rôle **SAP_INTERNAL_HANA_SUPPORT** est déconseillée dans ces versions de HANA. |

###### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **Message d’erreur**      | <span style="font-weight:normal">Échec de la connexion au système HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Causes possibles**    | <ul><li>Échec de la connexion à l’instance HANA</li><li>La base de données système est hors connexion</li><li>La base de données du locataire est hors connexion</li><li>L’utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER) n’a pas d’autorisations ou de privilèges suffisants.</li></ul> |
| **Action recommandée** | Vérifiez que le système est opérationnel. Si la ou les bases de données sont opérationnelles, vérifiez que les autorisations nécessaires sont définies en téléchargeant et en exécutant le [script de préinscription](https://aka.ms/scriptforpermsonhana) sur l’instance SAP HANA. |

###### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **Message d’erreur**      | <span style="font-weight:normal">L’instance SAP HANA spécifiée n’est pas valide ou est introuvable</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Causes possibles**    | <ul><li>L’instance SAP HANA spécifiée n’est pas valide ou est introuvable.</li><li>Il est impossible de sauvegarder plusieurs instances SAP HANA sur une seule machine virtuelle Azure.</li></ul> |
| **Action recommandée** | <ul><li>Assurez-vous qu’une seule instance HANA est en cours d’exécution sur la machine virtuelle Azure.</li><li>Exécutez le script à partir du volet Discover DB (que vous pouvez également trouver [ici](https://aka.ms/scriptforpermsonhana)) avec l’instance SAP HANA correcte pour résoudre le problème.</li></ul> |

###### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **Message d’erreur**      | <span style="font-weight:normal">La séquence de journaux de transactions consécutifs de sauvegarde est rompue</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Causes possibles**    | Une interruption de la séquence de journaux de transactions consécutifs LSN HANA peut être déclenchée pour différentes raisons, notamment :<ul><li>Échec de l’appel à Stockage Azure pour valider la sauvegarde.</li><li>La base de données du locataire est hors connexion.</li><li>La mise à niveau de l’extension a mis fin à un travail de sauvegarde en cours.</li><li>Impossible de se connecter au Stockage Azure lors de la sauvegarde.</li><li>SAP HANA a annulé une transaction dans le processus de sauvegarde.</li><li>Une sauvegarde est terminée, mais le catalogue n’a pas encore été mis à jour avec succès dans le système HANA.</li><li>Échec de la sauvegarde du point de vue du service Sauvegarde Azure, mais réussite du point de vue de HANA : la sauvegarde de fichier journal/destination du catalogue a peut-être été mise à jour de backint vers le système de fichiers, ou l’exécutable backint a peut-être été modifié.</li></ul> |
| **Action recommandée** | Pour résoudre ce problème, le service Sauvegarde Azure déclenche une sauvegarde complète de réparation automatique. Pendant que cette sauvegarde de réparation automatique est en cours, toutes les sauvegardes de journaux déclenchées par HANA échouent avec l’erreur **OperationCancelledBecauseConflictingAutohealOperationRunningUserError**. Une fois la sauvegarde complète de réparation automatique terminée, les journaux et toutes les autres sauvegardes commencent à fonctionner comme prévu.<br>Si vous ne voyez pas de sauvegarde complète de réparation automatique déclenchée ni aucune sauvegarde (complète/différentielle/incrémentielle) réussie dans les 24 heures, contactez le support technique Microsoft.</br> |

###### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **Message d’erreur**      | <span style="font-weight:normal">Mise à niveau de SDC vers MDC détectée.</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Causes possibles**    | Lorsqu’un système SDC est mis à niveau vers MDC, les sauvegardes échouent avec cette erreur. |
| **Action recommandée** | Pour résoudre le problème, consultez [Mise à niveau de SDC vers MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid). |

###### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **Message d’erreur**      | <span style="font-weight:normal">Les sauvegardes échouent avec cette erreur lorsque la configuration backint n’est pas correctement mise à jour.</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Causes possibles**    | La configuration backint mise à jour pendant le flux de configuration de la protection par Sauvegarde Azure est modifiée/mise à jour par le client. |
| **Action recommandée** | Vérifiez que les paramètres suivants (backint) sont définis :<br><ul><li>[catalog_backup_using_backint:true]</li><li>[enable_accumulated_catalog_backup:false]</li><li>[parallel_data_backup_backint_channels:1]</li><li>[log_backup_timeout_s:900)]</li><li>[backint_response_timeout:7200]</li></ul>Si les paramètres backint sont présents au niveau HOST, supprimez-les. Toutefois, si les paramètres ne sont pas présents au niveau de l’hôte mais ont été modifiés manuellement au niveau de la base de données, assurez-vous que les valeurs de niveau base de données sont définies ci-dessus. Sinon, sur le portail Microsoft Azure, exécutez [Arrêter la protection et conserver les données de sauvegarde](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database), puis sélectionnez Reprendre la sauvegarde. |

###### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**Message d’erreur**  | <span style="font-weight:normal">Les systèmes source et cible pour la restauration ne sont pas compatibles</span>  |
|---------|---------|
|**Causes possibles**   | Le flux de restauration échoue avec cette erreur lorsque les bases de données HANA source et cible et les systèmes ne sont pas compatibles. |
|Action recommandée   |   Assurez-vous que votre scénario de restauration ne figure pas dans la liste suivante des restaurations potentiellement incompatibles :<br> **Cas 1 :** SYSTEMDB ne peut pas être renommé lors de la restauration.<br>**Cas 2 :** Source – SDC et Cible – MDC : La base de données source ne peut pas être restaurée en tant que SYSTEMDB ou base de données de locataire sur la cible. <br> **Cas 3 :** Source – MDC et Cible – SDC : Impossible de restaurer la base de données source (SYSTEMDB ou base de données de locataire) sur la cible.<br>Pour plus d’informations, consultez la note **1642148** dans [SAP Support Launchpad](https://launchpad.support.sap.com). |

###### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**Message d’erreur** | <span style="font-weight:normal">La base de données configurée pour la sauvegarde n’existe pas.</span>
--------- | --------------------------------
**Causes possibles** | Si une base de données qui a été configurée pour la sauvegarde est supprimée, toutes les sauvegardes planifiées et ad hoc sur cette base de données échoueront.
**Action recommandée** | Vérifiez si la base de données est supprimée. Recréez la base de données ou [arrêtez la protection](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (avec ou sans conservation des données) pour la base de données.

###### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**Message d’erreur** | <span style="font-weight:normal">Sauvegarde Azure ne dispose pas de privilèges suffisants pour effectuer des opérations de sauvegarde et de restauration.</span>
---------- | ---------
**Causes possibles** | L’utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER) créé par le script de préinscription n’a pas un ou plusieurs des rôles suivants :<ul><li>Pour MDC, DATABASE ADMIN et BACKUP ADMIN (pour HANA 2.0 SPS05 et versions ultérieures) pour créer des bases de données lors de la restauration.</li><li>Pour SDC, BACKUP ADMIN pour créer des bases de données lors de la restauration.</li><li>CATALOG READ pour lire le catalogue de sauvegarde.</li><li>SAP_INTERNAL_HANA_SUPPORT pour accéder à certaines tables privées. Obligatoire uniquement pour les versions SDC et MDC antérieures à HANA 2.0 SPS04 rév. 46. Cela n’est pas obligatoire pour HANA 2.0 SPS04 rév. 46 et versions ultérieures, car nous obtenons maintenant les informations requises des tables publiques avec le correctif de l’équipe HANA.</li></ul>
**Action recommandée** | Pour résoudre ce problème, ajoutez manuellement les rôles et les autorisations nécessaires pour l’utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER), ou téléchargez et réexécutez le [script de préinscription](https://aka.ms/scriptforpermsonhana) sur l’instance SAP HANA.

###### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**Message d’erreur** | <span style="font-weight:normal">La base de données/le mot de passe de l’utilisateur de sauvegarde a expiré.</span>
----------- | -----------
**Causes possibles** | La base de données/l’utilisateur de sauvegarde créé par le script de préinscription ne définit pas l’expiration du mot de passe. Toutefois, en cas de modification, vous pouvez voir cette erreur.
**Action recommandée** | Téléchargez et exécutez le [script de préinscription](https://aka.ms/scriptforpermsonhana) sur l’instance SAP HANA pour résoudre le problème.

###### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**Message d’erreur** | <span style="font-weight:normal">Erreur SAP HANA</span>
------------ | ----------
**Causes possibles** | Stockage sécurisé dans le système de fichiers (SSFS) incohérent reçu du moteur SAP HANA.
**Action recommandée** | Collaborez avec l'équipe SAP HANA pour résoudre ce problème. Pour plus d’informations, consultez la note SAP **0002097613**.

###### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**Message d’erreur** | <span style="font-weight:normal">Impossible de se connecter au service AAD à partir du système HANA.</span>
--------- | --------
**Causes possibles** | Les paramètres de pare-feu ou de proxy en tant que compte de service de plug-in d’extension de sauvegarde n’autorisent pas la connexion sortante vers AAD.
**Action recommandée** | Corrigez les paramètres de pare-feu ou de proxy de la connexion sortante vers AAD, puis réessayez l’opération.

###### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**Message d’erreur** | <span style="font-weight:normal">Magasin d’autorités de certification mal configuré</span>
-------- | -------
**Causes possibles** | Le processus hôte du plug-in de l’extension de sauvegarde ne peut pas accéder au magasin d’autorité de certification racine (sous _/var/lib/ca-certificates/ca-bundle.pem_ dans le cas de SLES).
**Action recommandée** | Corrigez le problème du magasin de l’autorité de certification en utilisant `chmod o+r` pour restaurer l’autorisation d’origine.  Ensuite, redémarrez le service d’hôte de plug-in pour que les sauvegardes et les restaurations s’effectuent correctement.

###### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**Message d’erreur** | <span style="font-weight:normal">Sauvegarde de réparation en cours.</span>
---------- | -------
**Causes possibles** | La Sauvegarde Azure déclenche une sauvegarde complète de réparation pour gérer l’interruption de la séquence de journaux de transactions consécutifs LSN. Bien que cette opération de réparation complète soit en cours, les sauvegardes (complètes/différentielles/incrémentielles) déclenchées via le portail/l’interface CLI échouent avec cette erreur.
**Action recommandée** | Attendez la fin de la sauvegarde complète corrective avant de déclencher une autre sauvegarde.

###### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**Message d’erreur** | <span style="font-weight:normal">Une opération en conflit est en cours</span>.
----------- | -------------
**Causes possibles** | Une sauvegarde complète/différentielle/incrémentielle est déclenchée avec le portail/l’interface CLI/des clients natifs HANA alors qu’une autre sauvegarde complète/différentielle/incrémentielle est déjà en cours.
**Action recommandée** | Attendez la fin du travail de sauvegarde actif avant de déclencher une nouvelle sauvegarde complète ou différentielle.

###### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunning UserError

**Message d’erreur** | <span style="font-weight:normal">Sauvegarde complète de réparation automatique en cours.</span>
------- | -------
**Causes possibles** | Sauvegarde Azure déclenche une sauvegarde complète de réparation automatique pour résoudre **UserErrorHANALSNValidationFailure**. Pendant que cette sauvegarde de réparation automatique est en cours, toutes les sauvegardes de journaux déclenchées par HANA échouent avec l’erreur **OperationCancelledBecauseConflictingAutohealOperationRunningUserError**.<br>Une fois la sauvegarde complète de réparation automatique terminée, les journaux et toutes les autres sauvegardes commencent à fonctionner comme prévu.</br>
**Action recommandée** | Attendez la fin de la sauvegarde complète de réparation automatique avant de déclencher une nouvelle sauvegarde complète ou différentielle.

###### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**Message d’erreur** | <span style="font-weight:normal">Script de préinscription non exécuté.</span>
--------- | --------
**Causes possibles** | Le script de préinscription SAP HANA pour configurer l’environnement n’a pas été exécuté.
**Action recommandée** | Téléchargez et réexécutez le [script de préinscription](https://aka.ms/scriptforpermsonhana) sur l’instance SAP HANA.


###### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**Message d’erreur** | <span style="font-weight:normal">La base de données cible ne peut pas être remplacée pour la restauration.</span>
------- | -------
**Causes possibles** | La base de données cible existe, mais elle ne peut pas être remplacée. Le remplacement forcé n’est pas défini dans le flux de restauration sur le portail/l’interface CLI.
**Action recommandée** | Restaurez la base de données en sélectionnant l'option Forcer le remplacement ou restaurez vers une autre base de données cible.

###### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**Message d’erreur** | <span style="font-weight:normal">RecoverySys.py n’a pas pu être exécuté correctement pour restaurer la base de système.</span>
-------- | ---------
**Causes possibles** | Les causes possibles de l’échec de la restauration de la base de système sont les suivantes :<ul><li>Sauvegarde Azure ne peut pas trouver **Recoverysys.py** sur l’ordinateur HANA. Cela se produit lorsque l’environnement HANA n’est pas configuré correctement.</li><li>**Recoverysys.py** est présent, mais le déclenchement de ce script n’a pas réussi à appeler HANA pour effectuer la restauration.</li><li>Recoverysys.py a pu appeler HANA pour effectuer la restauration, mais HANA n’a pas pu la faire.</li></ul>
**Action recommandée** | <ul><li>Pour le problème 1, collaborez avec l’équipe SAP HANA pour le résoudre.</li><li>Pour les problèmes 2 et 3, consultez le suivi des journaux en exécutant la commande HDSetting.sh dans l’invite sid-adm. Par exemple, _/usr/sap/SID/HDB00/HDBSetting.sh_.</li></ul>Partagez ces résultats avec l’équipe SAP HANA pour résoudre le problème.

###### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**Message d’erreur** | <span style="font-weight:normal">Le format du nom de la base de données restauré est incorrect.</span>
--------- | --------
**Causes possibles** | Le nom de base de données restaurée que vous avez fourni n’est pas au format acceptable/attendu.
**Action recommandée** | Vérifiez que le nom de la base de données restaurée commence par une lettre et ne contient aucun symbole autre que des chiffres ou un trait de soulignement.<br>Il peut contenir jusqu’à 127 caractères uniquement et ne doit pas commencer par « \_SYS_\".

###### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**Message d’erreur** | <span style="font-weight:normal">Le répertoire sid-adm par défaut a changé.</span>
------- | -------
**Causes possibles** | Le répertoire **sid-adm** par défaut a été modifié et **HDBSetting.sh** n’est pas disponible dans ce répertoire par défaut.
**Action recommandée** | Si HXE est le SID, vérifiez que la variable d’environnement HOME est définie sur _/usr/sap/HXE/home_ sous l’utilisateur **sid-adm**.

###### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**Message d’erreur** | <span style="font-weight:normal">Fichier HDBSetting.sh introuvable.</span>
--------- | -------
**Causes possibles** | La restauration de la base de données système a échoué parce que l’environnement utilisateur **&lt;sid&gt;adm** n’a pas trouvé le fichier **HDBsettings.sh** pour déclencher la restauration.
**Action recommandée** | Collaborez avec l'équipe SAP HANA pour résoudre ce problème.<br><br>Si HXE est le SID, vérifiez que la variable d’environnement HOME est définie sur _/usr/sap/HXE/home_ sous l’utilisateur **sid-adm**.

## <a name="restore-checks"></a>Restaurer les vérifications

### <a name="single-container-database-sdc-restore"></a>Restauration d’une base de données à conteneur unique (SDC)

Prenez en charge les entrées lors de la restauration d’une base de données à conteneur unique (SDC) pour HANA sur un autre ordinateur SDC. Le nom de la base de données doit être indiqué en minuscules et « sdc » ajouté entre crochets. L’instance HANA s’affiche en majuscules.

Supposez qu’une instance SDC HANA « H21 » est sauvegardée. La page Éléments de sauvegarde affiche le nom de l’élément de sauvegarde sous la forme **« H21 (SDC) »** . Si vous tentez de restaurer cette base de données sur un autre SDC cible, par exemple H11, les entrées suivantes doivent être fournies.

![Nom de la base de données SDC restaurée](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Notez les points suivants :

- Par défaut, le nom de la base de données restaurée est renseigné avec le nom de l’élément de sauvegarde. Dans ce cas, il s’agit de h21(sdc).
- La sélection de la cible en tant que H11 ne modifie pas automatiquement le nom de la base de données restaurée. **Elle doit être modifiée en H11 (SDC)** . Concernant SDC, le nom de la base de données restaurée sera l’ID d’instance cible avec des lettres minuscules suivi de « sdc » entre crochets.
- Étant donné que SDC ne peut avoir qu’une seule base de données, vous devez aussi cocher la case pour autoriser le remplacement des données de la base de données existantes par les données du point de récupération.
- Linux respecte la casse. Par conséquent, veillez à la conserver.

### <a name="multiple-container-database-mdc-restore"></a>Restauration de la base de données à conteneurs multiples (MDC)

Dans plusieurs bases de données de conteneur pour HANA, la configuration standard est SYSTEMDB + 1 ou plusieurs bases de données de locataire. La restauration d’une instance de SAP HANA entière signifie qu’il faut restaurer les bases de données SYSTEMDB et de locataire. L’une restaure d’abord SYSTEMDB, puis procède à la restauration de la base de données du locataire. La base de données système signifie essentiellement remplacer les informations système sur la cible sélectionnée. Cette restauration remplace également les informations relatives à BackInt dans l’instance cible. Par conséquent, une fois la base système restaurée vers une instance cible, vous devez réexécuter le script de préinscription. C’est uniquement à ce moment-là que les restaurations ultérieures de la base de données locataire réussiront.

## <a name="back-up-a-replicated-vm"></a>Sauvegarder une machine virtuelle répliquée

### <a name="scenario-1"></a>Scénario 1

La machine virtuelle d’origine a été répliquée à l’aide d’Azure Site Recovery ou d’une Sauvegarde des machines virtuelles Azure. La nouvelle machine virtuelle a été créée pour simuler l’ancienne . Autrement dit, les paramètres sont exactement les mêmes (cela est dû au fait que la machine virtuelle d’origine a été supprimée et que la restauration a été effectuée à partir de la sauvegarde de machine virtuelle ou d’Azure Site Recovery).

Ce scénario peut inclure deux cas. Découvrez comment sauvegarder la machine virtuelle répliquée dans les deux cas ci-dessous :

1. La nouvelle machine virtuelle créée porte le même nom et se trouve dans les mêmes groupe de ressources et abonnement que la machine virtuelle supprimée.

    - L’extension est déjà présente sur la machine virtuelle, mais n’est visible pour aucun des services.
    - Réexécutez le script de préinscription.
    - Réinscrivez l’extension pour la même machine dans le portail Azure (**Sauvegarde** -> **Afficher les détails** > sélectionner la machine virtuelle Azure appropriée -> Réinscrire).
    - La sauvegarde des bases de données sauvegardées (à partir de la machine virtuelle supprimée) doit ensuite démarrer correctement.

2. La nouvelle machine virtuelle créée présente l’une des caractéristiques suivantes :

    - Elle porte un nom différent de celui de la machine virtuelle supprimée.
    - Elle porte le même nom que la machine virtuelle supprimée, mais se trouve dans un groupe de ressources ou un abonnement différents de ceux de la machine virtuelle supprimée.

    Si c’est le cas, procédez comme suit :

    - L’extension est déjà présente sur la machine virtuelle, mais n’est visible pour aucun des services.
    - Exécutez le script de préinscription.
    - Si vous découvrez et protégez les nouvelles bases de données, vous commencez à voir des bases de données actives en double dans le portail. Pour éviter cela, vous devez [Arrêter la protection avec données conservées](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pour les anciennes bases de données. Ensuite, passez aux étapes restantes.
    - Détecter les bases de données pour activer la sauvegarde.
    - Activez les sauvegardes sur ces bases de données.
    - Les bases de données déjà sauvegardées (à partir de la machine virtuelle supprimée) continueront d’être stockées dans le coffre (avec leurs sauvegardes conservées conformément à la stratégie).

### <a name="scenario-2"></a>Scénario 2

La machine virtuelle d’origine a été répliquée à l’aide d’Azure Site Recovery ou d’une sauvegarde de machine virtuelle Azure. La nouvelle machine virtuelle a été créée à partir du contenu, pour être utilisée comme modèle. Il s’agit d’une nouvelle machine virtuelle avec un nouveau SID.

Procédez comme suit pour activer les sauvegardes sur la nouvelle machine virtuelle :

- L’extension est déjà présente sur la machine virtuelle, mais n’est visible pour aucun des services.
- Exécutez le script de préinscription. En fonction du SID de la nouvelle machine virtuelle, deux scénarios peuvent se produire :
  - La machine virtuelle d’origine et la nouvelle machine virtuelle ont le même SID. Le script de pré-inscription s’exécutera correctement.
  - La machine virtuelle d’origine et la nouvelle machine virtuelle ont des SID différents. Le script de pré-inscription échouera. Contactez le support technique pour obtenir de l’aide dans ce scénario.
- Détectez les bases de données que vous souhaitez sauvegarder.
- Activez les sauvegardes sur ces bases de données.

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Mise à niveau de la version de SDC ou de MDC sur la même machine virtuelle

Les mises à niveau du système d’exploitation et les changements de version de SDC ou de MDC qui n’entraînent pas de modification du SID peuvent être gérées comme suit :

- Assurez-vous que les nouvelles versions du système d’exploitation, de SDC ou de MDC sont actuellement [prises en charge par Sauvegarde Azure](sap-hana-backup-support-matrix.md#scenario-support).
- [Arrêtez la protection avec données conservées](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pour la base de données.
- Effectuez la mise à niveau ou la mise à jour.
- Réexécutez le script de préinscription. Souvent, le processus de mise à niveau peut supprimer [les rôles nécessaires](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). L’exécution du script de préinscription permet de vérifier tous les rôles requis.
- Réactivez la protection de la base de données.

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Mise à niveau de SDC vers MDC sans modification du SID

Vous pouvez gérer les mises à niveau de SDC vers MDC qui n’entraînent pas de modification du SID comme suit :

- Assurez-vous que la nouvelle version de MDC est actuellement [prise en charge par Sauvegarde Azure](sap-hana-backup-support-matrix.md#scenario-support).
- [Arrêtez la protection avec données conservées](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) pour l’ancienne base de données SDC.
- Effectuez la mise à niveau. À l’issue de l’opération, le système HANA est maintenant MDC avec une base de données système et une ou plusieurs bases de données de locataire.
- Réexécuter le [script de préinscription](https://aka.ms/scriptforpermsonhana)
- Réinscrivez l’extension pour la même machine dans le portail Azure (**Sauvegarde** -> **Afficher les détails** > sélectionner la machine virtuelle Azure appropriée -> Réinscrire).
- Sélectionnez **Redécouvrir les bases de données** pour la même machine virtuelle. Cette action a pour effet de faire apparaître les nouvelles bases de données de l’étape 3 en tant que SYSTEMDB et Tenant DB, et non SDC.
- L’ancienne base de données SDC continue d’exister dans le coffre, et les anciennes données sauvegardées sont conservées conformément à la stratégie.
- Configurez la sauvegarde de ces bases de données.

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Mise à niveau de SDC vers MDC avec modification du SID

Vous pouvez gérer les mises à niveau de SDC vers MDC qui entraînent une modification du SID comme suit :

- Assurez-vous que la nouvelle version de MDC est actuellement [prise en charge par Sauvegarde Azure](sap-hana-backup-support-matrix.md#scenario-support).
- **Arrêtez la protection avec données conservées** pour l’ancienne base de données SDC.
- Effectuez la mise à niveau. À l’issue de l’opération, le système HANA est maintenant MDC avec une base de données système et une ou plusieurs bases de données de locataire.
- Réexécutez le [script de préinscription](https://aka.ms/scriptforpermsonhana) avec les détails (nouveaux SID et MDC) corrects. En raison d’une modification du SID, vous risquez de rencontrer des problèmes avec l’exécution du script. Si vous rencontrez des problèmes, contactez le support de Sauvegarde Azure.
- Réinscrivez l’extension pour la même machine dans le portail Azure (**Sauvegarde** -> **Afficher les détails** > sélectionner la machine virtuelle Azure appropriée -> Réinscrire).
- Sélectionnez **Redécouvrir les bases de données** pour la même machine virtuelle. Cette action a pour effet de faire apparaître les nouvelles bases de données de l’étape 3 en tant que SYSTEMDB et Tenant DB, et non SDC.
- L’ancienne base de données SDC continue d’exister dans le coffre, et les anciennes données sauvegardées sont conservées conformément à la stratégie.
- Configurez la sauvegarde de ces bases de données.

## <a name="re-registration-failures"></a>Échecs de réinscription

Vérifiez la présence d’un ou plusieurs des symptômes suivants avant de déclencher l’opération de réinscription :

- Toutes les opérations (comme la sauvegarde, la restauration et la configuration de la sauvegarde) échouent sur la machine virtuelle avec un des codes d’erreur suivants : **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Si la zone de l’**État de la sauvegarde** de l’élément de sauvegarde affiche **Inaccessible**, excluez toutes les autres causes susceptibles d’entraîner le même état :

  - Absence d’autorisation pour effectuer les opérations liées à la sauvegarde sur la machine virtuelle
  - La machine virtuelle étant arrêtée, les sauvegardes ne peuvent pas se produire
  - Problèmes de réseau

Ces symptômes peuvent survenir pour une ou plusieurs des raisons suivantes :

- Une extension a été supprimée ou désinstallée sur le portail.
- La machine virtuelle a été restaurée à un point dans le temps via une restauration de disques sur place.
- La machine virtuelle a été arrêtée pendant une période prolongée, entraînant l’expiration de la configuration d’extension qui s’y trouvait.
- La machine virtuelle a été supprimée et une autre machine virtuelle a été créée avec le même nom et le même groupe de ressources que la machine virtuelle supprimée.

Dans les scénarios précédents, nous vous recommandons de déclencher une opération de réinscription sur la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [questions fréquentes (FAQ)](./sap-hana-faq-backup-azure-vm.yml) sur la sauvegarde des bases de données SAP HANA sur des machines virtuelles Azure.
