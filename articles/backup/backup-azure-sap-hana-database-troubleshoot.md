---
title: Résoudre les erreurs lors de la sauvegarde des bases de données SAP HANA en utilisant la sauvegarde Azure | Microsoft Docs
description: Décrit comment résoudre les erreurs courantes qui peuvent survenir lorsque vous utilisez le service Sauvegarde Azure pour sauvegarder des bases de données SAP HANA.
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: pullabhk
ms.openlocfilehash: 221b669c141681749709d6a5a406c78499f21032
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465470"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Résoudre les problèmes de sauvegarde des bases de données SAP HANA sur Azure

Cet article fournit des informations de dépannage pour la sauvegarde des bases de données SAP HANA sur les machines virtuelles Azure.

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

## <a name="common-user-errors"></a>Erreurs utilisateur courantes

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

données| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Échec de la connexion au système HANA. Vérifiez que votre système est en cours d’exécution.| Le service Sauvegarde Azure ne peut pas se connecter à HANA, car la base de données HANA est indisponible. Ou HANA est en cours d’exécution, mais n’autorise pas le service Sauvegarde Azure à se connecter. | Vérifiez si la base de données ou le service HANA est inactif. Si la base de données ou le service Hana est en cours d’exécution, vérifiez si [toutes les autorisations sont définies](#setting-up-permissions). Si la clé est manquante, réexécutez le script de préinscription pour créer une nouvelle clé. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Configuration Backint non valide détectée. Arrêtez la protection et reconfigurez la base de données.| Les paramètres backInt ne sont pas spécifiés correctement pour la sauvegarde Azure. | Vérifiez si [les paramètres sont définis](#setting-up-backint-parameters). Si les paramètres basés sur backInt sont présents au niveau de l’HÔTE, supprimez-les. Si les paramètres ne sont pas présents au niveau de l’HÔTE, mais ont été modifiés manuellement au niveau de la base de données, rétablissez les valeurs appropriées comme décrit précédemment. Ou, sur le Portail Microsoft Azure, exécutez **Arrêter la protection et conserver les données de sauvegarde**, puis sélectionnez **Reprendre la sauvegarde**.|
