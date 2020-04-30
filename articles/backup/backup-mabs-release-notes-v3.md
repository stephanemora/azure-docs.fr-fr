---
title: Notes de publication du serveur de sauvegarde Microsoft Azure v3
description: Cet article décrit les informations relatives aux problèmes connus et les solutions de contournement pour le serveur de sauvegarde Microsoft Azure (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a5c99bcb95fde39bddc9e9db9ab000881c89081a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185623"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notes de publication du serveur de sauvegarde Microsoft Azure

Cet article décrit les problèmes connus et les solutions de contournement pour le serveur de sauvegarde Microsoft Azure (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Échecs de sauvegarde et de récupération pour les charges de travail en cluster

**Description :** échecs de la sauvegarde/restauration pour les sources de données en cluster, comme le cluster Hyper-V ou le cluster SQL (SQL Always On), ou Exchange dans le groupe de disponibilité de base de données après la mise à niveau de MABS V2 vers V3.

**Solution de contournement :** pour éviter ce problème, ouvrez SQL Server Management Studio (SSMS) et exécutez le script SQL suivant sur la base de données DPM :

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>La mise à niveau vers MABS V3 échoue dans les paramètres régionaux russes

**Description :** la mise à niveau de MABS V2 vers V3 dans les paramètres régionaux russes échoue avec le code d’erreur **4387**.

**Solution de contournement :** effectuez les étapes suivantes pour mettre à niveau vers MABS V3 avec le package d’installation russe :

1. [Sauvegardez](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) votre base de données SQL et désinstallez MABS V2 (choisissez de conserver les données protégées pendant la désinstallation).
2. Mettez à niveau vers SQL 2017 (Entreprise) et désinstallez Reporting dans le cadre de la mise à niveau.
3. [Installez](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installez](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Configurer Reporting à l’aide des paramètres indiqués dans [Configuration de SSRS avec SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Installez](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaurez](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL à l’aide de SSMS et exécutez l’outil de synchronisation DPM comme décrit [ici](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Mettez à jour la propriété « DataBaseVersion » dans la table dbo.tbl_DLS_GlobalSetting à l’aide de la commande suivante :

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Démarrez le service MSDPM.

## <a name="next-steps"></a>Étapes suivantes

[Nouveautés de MABS V3](backup-mabs-whats-new-mabs.md)
