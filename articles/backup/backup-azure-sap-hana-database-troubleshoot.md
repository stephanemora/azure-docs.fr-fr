---
title: Résoudre les erreurs lors de la sauvegarde des bases de données SAP HANA en utilisant la sauvegarde Azure | Microsoft Docs
description: Décrit comment résoudre les erreurs courantes qui peuvent survenir lorsque vous utilisez le service Sauvegarde Azure pour sauvegarder des bases de données SAP HANA.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 00e37030417da97d2c57b0fb5872422e7048a2bc
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954448"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Résoudre les problèmes de sauvegarde des bases de données SAP HANA sur Azure

Cet article fournit des informations de dépannage pour la sauvegarde des bases de données SAP HANA sur les machines virtuelles Azure. La section suivante décrit les données conceptuelles importantes requises pour diagnostiquer les erreurs courantes dans une sauvegarde SAP HANA.

## <a name="prerequisites"></a>Prérequis

Dans le cadre de la [configuration requise](backup-azure-sap-hana-database.md#prerequisites), assurez-vous que le script de préinscription a été exécuté sur la machine virtuelle sur laquelle HANA est installé.

### <a name="setting-up-permissions"></a>Configurer les autorisations

Voici ce que fait le script de préinscription :

1. Il crée AZUREWLBACKUPHANAUSER dans un système HANA et ajoute les rôles et autorisations requis :
    - DATABASE ADMIN : permet de créer de nouvelles bases de données pendant la restauration.
    - CATALOG READ : permet de lire le catalogue de sauvegarde.
    - SAP_INTERNAL_HANA_SUPPORT : permet d’accéder à certaines tables privées.
2. Ajoute une clé à Hdbuserstore pour le plug-in HANA afin de gérer toutes les opérations (requêtes de base de données, opérations de restauration, configuration et exécution de la sauvegarde).
   
   Pour confirmer la création de la clé, exécutez la commande HDBSQL sur la machine HANA avec les informations d’identification SIDADM :

    ``` hdbsql
    hdbuserstore list
    ```
    
    La sortie de commande doit afficher la clé {SID} {DBNAME} avec l’utilisateur en tant que AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Vérifiez que vous disposez d’un ensemble unique de fichiers SSFS sous le chemin d’accès **/usr/sap/{SID}/home/.hdb/** . Vous ne devez trouver qu’un seul dossier dans ce chemin d’accès.

### <a name="setting-up-backint-parameters"></a>Configurer les paramètres BackInt

Après avoir choisi une base de données pour la sauvegarde, le service Sauvegarde Azure configure les paramètres backInt au niveau de la BASE DE DONNÉES :

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Assurez-vous que ces paramètres *ne sont pas* situés au niveau de l’HÔTE. Les paramètres au niveau de l’hôte remplacent ces paramètres et peuvent entraîner un comportement inattendu.

## <a name="restore-checks"></a>Restaurer les vérifications

### <a name="single-container-database-sdc-restore"></a>Restauration d’une base de données à conteneur unique (SDC)

Prenez en charge les entrées lors de la restauration d’une base de données à conteneur unique (SDC) pour HANA sur un autre ordinateur SDC. Le nom de la base de données doit être indiqué en minuscules et « sdc » ajouté entre crochets. L’instance HANA s’affiche en majuscules.

Supposez qu’une instance SDC HANA « H21 » est sauvegardée. La page Éléments de sauvegarde affiche le nom de l’élément de sauvegarde sous la forme **« H21 (SDC) »** . Si vous tentez de restaurer cette base de données sur un autre SDC cible, par exemple H11, les entrées suivantes doivent être fournies.

![Entrées de restauration SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Notez les points suivants :
- Par défaut, le nom de la base de données restaurée est renseigné avec le nom de l’élément de sauvegarde, c.-à-d., H21 (SDC)
- La sélection de la cible en tant que H11 ne modifie pas automatiquement le nom de la base de données restaurée. **Elle doit être modifiée en H11 (SDC)** . Dans le cas de SDC, le nom de la base de code restaurée sera l’ID d’instance cible avec des lettres minuscules et « sdc » est ajouté entre crochets.
- Étant donné que SDC ne peut avoir qu’une seule base de données, vous devez également activer la case à cocher pour autoriser le remplacement des données de la base de données existantes par les données du point de récupération.
- Linux est sensible à la casse alors assurez-vous de respecter la casse.

### <a name="multiple-container-database-mdc-restore"></a>Restauration de la base de données à conteneurs multiples (MDC)

Dans plusieurs bases de données de conteneur pour HANA, la configuration standard est SYSTEMDB + 1 ou plusieurs bases de données de locataire. La restauration d’une instance de SAP HANA entière signifie qu’il faut restaurer les bases de données SYSTEMDB et de locataire. L’une restaure d’abord SYSTEMDB, puis procède à la restauration de la base de données du locataire. La base de données système signifie essentiellement remplacer les informations système sur la cible sélectionnée. Cela remplace également les informations relatives à BackInt dans l’instance cible. Par conséquent, une fois que la base de référence système est restaurée vers une instance cible, vous devez réexécuter le script de pré-inscription. C’est uniquement à ce moment-là que les restaurations ultérieures de la base de données locataire réussiront.

## <a name="common-user-errors"></a>Erreurs utilisateur courantes

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

données| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Échec de la connexion au système HANA. Vérifiez que votre système est en cours d’exécution.| Le service Sauvegarde Azure ne peut pas se connecter à HANA, car la base de données HANA est indisponible. Ou HANA est en cours d’exécution, mais n’autorise pas le service Sauvegarde Azure à se connecter. | Vérifiez si la base de données ou le service HANA est inactif. Si la base de données ou le service Hana est en cours d’exécution, vérifiez si [toutes les autorisations sont définies](#setting-up-permissions). Si la clé est manquante, réexécutez le script de préinscription pour créer une nouvelle clé. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Configuration Backint non valide détectée. Arrêtez la protection et reconfigurez la base de données.| Les paramètres backInt ne sont pas spécifiés correctement pour la sauvegarde Azure. | Vérifiez si [les paramètres sont définis](#setting-up-backint-parameters). Si les paramètres basés sur backInt sont présents au niveau de l’HÔTE, supprimez-les. Si les paramètres ne sont pas présents au niveau de l’HÔTE, mais ont été modifiés manuellement au niveau de la base de données, rétablissez les valeurs appropriées comme décrit précédemment. Ou, sur le Portail Microsoft Azure, exécutez **Arrêter la protection et conserver les données de sauvegarde**, puis sélectionnez **Reprendre la sauvegarde**.|