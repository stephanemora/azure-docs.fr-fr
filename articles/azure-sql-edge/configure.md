---
title: Configurer Azure SQL Edge (préversion)
description: En savoir plus sur la configuration d'Azure SQL Edge (préversion)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594008"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurer Azure SQL Edge (préversion)

Azure SQL Edge prend en charge la configuration par le biais d'une deux options suivantes :

- Utilisation des variables d'environnement :
- Utilisation du fichier mssql.conf placé dans le dossier /var/opt/mssql.

> [!NOTE]
> La définition de variables d’environnement remplace les paramètres spécifiés dans le fichier mssql.conf.

## <a name="configure-using-environment-variables"></a>Configurer à l'aide des variables d’environnement

Azure SQL Edge expose plusieurs variables d’environnement qui peuvent être utilisées pour configurer le conteneur SQL Edge. Ces variables d’environnement correspondent à un sous-ensemble des variables d’environnement disponibles pour SQL Server sur Linux. Pour plus d’informations sur les variables d’environnement SQL Server sur Linux, consultez [Variables d’environnement](/sql/linux/sql-server-linux-configure-environment-variables/).

Les variables d’environnement SQL Server sur Linux suivantes ne sont pas prises en charge par Azure SQL Edge. Si elles sont définies, ces variables d’environnement seront ignorées lors de l’initialisation du conteneur.

| Variable d’environnement | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Activez le groupe de disponibilité. Par exemple, « 1 » est activé et « 0 » est désactivé. |

> [!IMPORTANT]
> La variable d’environnement *MSSQL_PID* pour SQL Edge accepte uniquement **Premium** et **Developer** en tant que valeurs valides. Azure SQL Edge ne prend pas en charge l’initialisation à l’aide d’une clé de produit.

> [!NOTE]
> Pour télécharger le contrat de licence utilisateur final Azure SQL Edge, consultez [Contrat de licence utilisateur final](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Spécification des variables d’environnement

Les variables d’environnement pour SQL Edge peuvent être spécifiées lors du déploiement d’Azure SQL Edge via le [portail Azure](deploy-portal.md). Elles peuvent être ajoutées dans la section « Variables d’environnement » du déploiement de module ou dans le cadre de l’option de création de conteneur, comme décrit ci-dessous.

*Définir l'utilisation des options de variables d'environnement*

![définir l'utilisation d'une liste de variables d'environnement](media/configure/set-environment-variables.png)

*Définir l'utilisation des options de création de conteneur*

![définir l'utilisation des options de création de conteneur](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configurer à l’aide du fichier mssql.conf

Contrairement à SQL Server sur Linux, Azure SQL Edge n’inclut pas l’[utilitaire de configuration mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/). Dès lors, le fichier mssql.conf doit être configuré manuellement et placé dans le lecteur de stockage persistant mappé au dossier /var/opt/mssql/ dans le module SQL Edge. Lors du déploiement de SQL Edge à partir de la Place de marché Azure, ce mappage est spécifié en tant qu’option **Mounts" dans l’option de création de conteneur.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Les options mssql.conf suivantes ne s’appliquent pas à SQL Edge :
</br></br>
|Option|Description|
|:---|:---|
|**Feedback des clients** | Choisir si SQL Server envoie ou non un feedback à Microsoft. |
|**Profil de messagerie de base de données** | Définir le profil de messagerie de base de données par défaut pour SQL Server sur Linux. |
|**Haute disponibilité** | Activer les groupes de disponibilité. |
|**Microsoft Distributed Transaction Coordinator** | Configurer et dépanner MSDTC sur Linux. Les autres options de configuration liées aux transactions distribuées ne sont pas prises en charge par SQL Edge. Pour plus d’informations sur ces options de configuration supplémentaires, consultez [Configurer MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**CLUF MLServices** | Accepter les CLUF R et Python pour les packages Machine Learning Services. S'applique à SQL Server 2019 uniquement.|
|**outboundnetworkaccess** |Activez l'accès réseau sortant pour les extensions [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python et Java.|

Vous trouverez ci-dessous un exemple de fichier mssql.conf fonctionnant pour SQL Edge. Pour plus d’informations sur le format du fichier mssql.conf, consultez [Format mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-step"></a>Étape suivante

- [Se connecter à Azure SQL Edge](connect.md)
- [Générer une solution IoT de bout en bout avec SQL Edge](tutorial-deploy-azure-resources.md)
