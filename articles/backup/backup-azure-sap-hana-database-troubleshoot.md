---
title: Résolution des erreurs lors de la sauvegarde des bases de données SAP HANA à l’aide de la sauvegarde Azure | Microsoft Docs
description: Ce guide explique comment résoudre les erreurs courantes qui surviennent lorsque vous sauvegardez des bases de données SAP HANA à l’aide de la sauvegarde Azure.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514171"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Résoudre les problèmes de sauvegarde de serveurs SAP HANA sur Azure

Cet article fournit des informations de dépannage pour la protection des bases de données SAP HANA sur les machines virtuelles Azure. Avant de passer à la résolution des problèmes, essayons de comprendre quelques points clés sur les autorisations et les paramètres.

## <a name="understanding-pre-requisites"></a>Comprendre les conditions préalables

Dans le cadre des [conditions préalables](backup-azure-sap-hana-database.md#prerequisites), le script de préinscription doit être exécuté sur la machine virtuelle où HANA est installé afin de configurer les autorisations appropriées.

### <a name="setting-up-permissions"></a>Configurer les autorisations

Voici ce que fait le script de préinscription :

1. Il crée AZUREWLBACKUPHANAUSER dans un système HANA et ajoute les rôles et autorisations requis comme indiqué ci-dessous :
    - DATABASE ADMIN : permet de créer de nouvelles bases de données pendant la restauration
    - CATALOG READ : permet de lire le catalogue de sauvegarde
    - SAP_INTERNAL_HANA_SUPPORT : permet d’accéder à certaines tables privées
2. Il ajoute la clé à Hdbuserstore pour le plug-in HANA afin d’effectuer toutes les opérations (demande de base de données, configuration des sauvegardes, sauvegarde, restauration)
   
   - Pour confirmer la création de la clé, exécutez la commande HDBSQL au sein de la machine HANA avec les informations d’identification SIDADM :

    ``` hdbsql
    hdbuserstore list
    ```
    
    La sortie de commande doit afficher la clé {SID} {DBNAME} avec l’utilisateur en tant que « AZUREWLBACKUPHANAUSER ».

> [!NOTE]
> Vérifiez que vous disposez d’un ensemble unique de fichiers SSFS sous le chemin d’accès « /usr/sap/{SID}/home/.hdb/ ». Vous ne devez trouver qu’un seul dossier sous ce chemin d’accès.

### <a name="setting-up-backint-parameters"></a>Configurer les paramètres BackInt

Après avoir choisi une base de données pour la sauvegarde, le service Sauvegarde Azure configurera les paramètres backInt au niveau de la BASE DE DONNÉES.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Assurez-vous que ces paramètres ne sont PAS situés au niveau de l’HÔTE. Les paramètres au niveau de l’hôte remplaceront ces paramètres, ce qui pourra provoquer un comportement différent de celui attendu.

## <a name="understanding-common-user-errors"></a>Comprendre les erreurs utilisateur courantes

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Impossible de se connecter à system.check HANA, car votre système est opérationnel.| Le service Sauvegarde Azure n’est pas en mesure de se connecter à HANA, car la base de données HANA est hors service. Sinon, HANA est en cours d’exécution mais n’autorise pas le service Sauvegarde Azure à se connecter | Vérifiez si le service/la base de données HANA est hors service. Si le service/la base de données HANA est opérationnel, vérifiez si toutes les autorisations sont définies comme indiqué [ici](#setting-up-permissions). Si la clé est manquante, réexécutez le script de préinscription pour créer une nouvelle clé. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Configuration Backint non valide détectée. Arrêtez la protection et reconfigurez la base de données.| Les paramètres backInt ne sont pas spécifiés correctement pour la sauvegarde Azure. | Vérifiez si tous les paramètres sont définis comme indiqué [ici](#setting-up-backint-parameters). Si les paramètres basés sur backInt sont présents au niveau de l’HÔTE, supprimez-les. Si les paramètres ne sont pas présents sur l’HÔTE, mais ont été modifiés manuellement au niveau de la base de données, rétablissez les valeurs appropriées comme indiqué précédemment. Sinon, effectuez à nouveau les opérations « arrêter la protection en conservant les données » dans le portail Azure et « reprendre la sauvegarde ».|
