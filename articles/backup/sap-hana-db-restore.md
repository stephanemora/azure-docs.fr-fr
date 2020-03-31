---
title: Restaurer les bases de données SAP HANA sur des machines virtuelles Azure
description: Dans cet article, découvrez comment restaurer des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287387"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurer les bases de données SAP HANA sur des machines virtuelles Azure

Cet article décrit comment restaurer des bases de données SAP HANA exécutées sur une machine virtuelle Azure, qui a été sauvegardée dans un coffre Azure Backup Recovery Services avec le service Sauvegarde Azure. Les restaurations peuvent être utilisées pour créer des copies des données dans le cadre de scénarios de développement ou de test ou pour revenir à un état antérieur.

Pour plus d’informations sur la sauvegarde de bases de données SAP HANA, consultez [Sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurer à un point dans le temps ou à un point de récupération

Sauvegarde Azure peut restaurer des bases de données SAP HANA s’exécutant sur des machines virtuelles Azure comme suit :

* Restaurer à une date ou une heure spécifique (à la seconde), en utilisant les sauvegardes de fichiers journaux. Sauvegarde Azure détermine automatiquement les sauvegardes différentielles complètes appropriées, et la chaîne des sauvegardes de fichiers journaux nécessaires pour restaurer en fonction de la date/heure sélectionnée.

* Restaurer en fonction d’une sauvegarde complète ou différentielle spécifique à un point de récupération spécifique.

## <a name="prerequisites"></a>Prérequis

Avant de restaurer une base de données, notez les points suivants :

* Vous pouvez restaurer la base de données uniquement sur une instance de SAP HANA qui se trouve dans la même région.

* L’instance cible doit être inscrite auprès du même coffre que la source.

* Sauvegarde Azure ne peut pas identifier deux instances de SAP HANA différentes sur la même machine virtuelle. Ainsi, il est impossible de restaurer des données depuis une instance vers une autre sur la même machine virtuelle.

* Pour que l’instance de SAP HANA cible soit prête pour la restauration, vérifiez son état **Préparation de la sauvegarde** :

  * Ouvrez le coffre dans lequel l’instance de SAP HANA cible est inscrite.

  * Dans le tableau de bord du coffre, sous **Démarrage**, choisissez **Sauvegarde**.

![Sauvegarde dans le tableau de bord du coffre](media/sap-hana-db-restore/getting-started-backup.png)

* Dans **Sauvegarde**, sous **Que souhaitez-vous sauvegarder ?** , choisissez **SAP HANA dans les machines virtuelles Azure**.

![Choisir SAP HANA dans les machines virtuelles Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* Sous **Découvrir les bases de données dans les machines virtuelles**, cliquez sur **Afficher les détails**.

![Afficher les détails](media/sap-hana-db-restore/view-details.png)

* Passez en revue la **Préparation de la sauvegarde** de la machine virtuelle cible.

![Serveurs protégés](media/sap-hana-db-restore/protected-servers.png)

* Pour en savoir plus sur les types de restauration pris en charge par SAP HANA, reportez-vous à la note SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148).

## <a name="restore-a-database"></a>Restaurer une base de données

* Ouvrez le coffre dans lequel la base de données SAP HANA à restaurer est inscrite.

* Dans le tableau de bord du coffre, sous **Éléments protégés**, choisissez **Éléments de sauvegarde**.

![Éléments de sauvegarde](media/sap-hana-db-restore/backup-items.png)

* Dans **Éléments de sauvegarde**, sous **Type de gestion de sauvegarde**, sélectionnez **SAP HANA dans les machines virtuelles Azure**.

![Type de gestion de sauvegarde](media/sap-hana-db-restore/backup-management-type.png)

* Sélectionnez la base de données à restaurer.

 ![Base de données à restaurer](media/sap-hana-db-restore/database-to-restore.png)

* Passez en revue le menu de la base de données. Il fournit des informations sur la sauvegarde de base de données, notamment :

  * Les points de restauration les plus anciens et les plus récents

  * L’état de la sauvegarde de fichier journal pour les dernières 24 et 72 heures pour la base de données

![Menu de la base de données](media/sap-hana-db-restore/database-menu.png)

* Sélectionnez **Restaurer la base de données**.

* Sous **Configuration de la restauration**, indiquez où (ou comment) restaurer les données :

  * **Autre emplacement** : restaure la base de données vers un autre emplacement et conserve la base de données source d’origine.

  * **Remplacer la base de données** : Restaurez les données sur la même instance de SAP HANA que la source d’origine. Cette option remplace la base de données d’origine.

![Configuration de la restauration](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restaurer à un autre emplacement

* Dans le menu **Configuration de la restauration**, sous **Où voulez-vous restaurer**, sélectionnez **Autre emplacement**.

![Restaurer à un autre emplacement](media/sap-hana-db-restore/restore-alternate-location.png)

* Sélectionnez le nom de l’hôte SAP HANA et le nom de l’instance dans lesquels vous souhaitez restaurer la base de données.
* Vérifiez si l’instance de SAP HANA cible est prête pour la restauration en passant en revue sa **Préparation de la sauvegarde**. Pour plus d’informations, reportez-vous à la [section des prérequis](#prerequisites).
* Dans la boîte de dialogue **Nom de la base de données restaurée**, indiquez le nom de la base de données cible.

> [!NOTE]
> Les restaurations SDC (Single Database Container) doivent suivre ces [contrôles](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Le cas échéant, sélectionnez **Remplacer si une base de données du même nom existe déjà sur l’instance HANA sélectionnée**.
* Sélectionnez **OK**.

![Configuration de la restauration : écran final](media/sap-hana-db-restore/restore-configuration-last.png)

* Dans **Sélectionner un point de restauration**, sélectionnez **Journaux (point dans le temps)** pour [effectuer une restauration vers un point spécifique dans le temps](#restore-to-a-specific-point-in-time). Ou sélectionnez **Complète et différentielle** pour [effectuer une restauration à un point de récupération spécifique](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurer et remplacer

* Dans le menu **Configuration de la restauration**, sous **Où voulez-vous restaurer**, sélectionnez **Remplacer la base de données** > **OK**.

![Remplacer la base de données](media/sap-hana-db-restore/overwrite-db.png)

* Dans **Sélectionner un point de restauration**, sélectionnez **Journaux (point dans le temps)** pour [effectuer une restauration vers un point spécifique dans le temps](#restore-to-a-specific-point-in-time). Ou sélectionnez **Complète et différentielle** pour [effectuer une restauration à un point de récupération spécifique](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Restaurer à un point spécifique dans le temps

Si vous avez sélectionné **Journaux d’activité (point dans le temps)** comme type de restauration, effectuez les actions suivantes :

* Sélectionnez un point de récupération dans le graphique du journal et sélectionnez **OK** pour choisir le point de restauration.

![Point de restauration](media/sap-hana-db-restore/restore-point.png)

* Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.

![Sélectionner Restaurer](media/sap-hana-db-restore/restore-restore.png)

* Suivez la progression de la restauration dans la zone **Notifications** ou suivez-la en sélectionnant **Restaurer les travaux** dans le menu de la base de données.

![Restauration correctement déclenchée](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurer à un point de récupération spécifique

Si vous avez sélectionné **Complète et différentielle** comme type de restauration, effectuez les actions suivantes :

* Sélectionnez un point de récupération dans la liste et sélectionnez **OK** pour choisir le point de restauration.

![Restaurer à un point de récupération spécifique](media/sap-hana-db-restore/specific-recovery-point.png)

* Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.

![Démarrer le travail de restauration](media/sap-hana-db-restore/restore-specific.png)

* Suivez la progression de la restauration dans la zone **Notifications** ou suivez-la en sélectionnant **Restaurer les travaux** dans le menu de la base de données.

![Progression de la restauration](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Dans les restaurations MDC, une fois que la base de référence système est restaurée vers une instance cible, vous devez réexécuter le script de pré-inscription. C’est uniquement à ce moment-là que les restaurations ultérieures de la base de données locataire réussiront. Pour en savoir plus, reportez-vous à [Résolution des problèmes : restauration MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment](sap-hana-db-manage.md) gérer des bases de données SAP HANA sauvegardées à l’aide de Sauvegarde Azure.
