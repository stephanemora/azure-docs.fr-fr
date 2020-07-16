---
title: Configurer Azure SQL Edge (préversion)
description: En savoir plus sur la configuration d'Azure SQL Edge (préversion).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636238"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurer Azure SQL Edge (préversion)

Azure SQL Edge prend en charge la configuration par le biais d'une deux options suivantes :

- Variables d'environnement
- Un fichier mssql.conf placé dans le dossier /var/opt/mssql

> [!NOTE]
> La définition de variables d’environnement remplace les paramètres spécifiés dans le fichier mssql.conf.

## <a name="configure-by-using-environment-variables"></a>Configurer à l'aide des variables d’environnement

Azure SQL Edge expose plusieurs variables d’environnement qui peuvent être utilisées pour configurer le conteneur SQL Edge. Ces variables d’environnement correspondent à un sous-ensemble de celles disponibles pour SQL Server sur Linux. Pour plus d’informations sur les variables d’environnement SQL Server sur Linux, consultez [Variables d’environnement](/sql/linux/sql-server-linux-configure-environment-variables/).

La variable d’environnement SQL Server sur Linux suivante n’est pas prise en charge par Azure SQL Edge. Si elle est définie, cette variable d’environnement sera ignorée lors de l’initialisation du conteneur.

| Variable d’environnement | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Activez le groupe de disponibilité. Par exemple, **1** est activé et **0** est désactivé. |

> [!IMPORTANT]
> La variable d’environnement **MSSQL_PID** pour SQL Edge accepte uniquement **Premium** et **Developer** en tant que valeurs valides. Azure SQL Edge ne prend pas en charge l’initialisation à l’aide d’une clé de produit.

> [!NOTE]
> Téléchargez les [Termes du contrat de licence logiciel Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) pour Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Spécifier les variables d’environnement

Spécifiez les variables d’environnement pour SQL Edge lorsque vous déployez le service via le [portail Azure](deploy-portal.md). Vous pouvez les ajouter dans la section **Variables d’environnement** du déploiement de module ou dans le cadre des **Options de création de conteneur**.

Ajoutez les valeurs dans des **Variables d’environnement**.

![Définir en utilisant une liste de variables d'environnement](media/configure/set-environment-variables.png)

Ajoutez des valeurs dans **Options de création de conteneur**.

![Définir en utilisant des options de création de conteneur](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Configurer à l’aide d’un fichier mssql.conf

Azure SQL Edge n’inclut pas [l’utilitaire de configuration mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) comme le fait SQL Server sur Linux. Vous devez configurer manuellement le fichier mssql.conf et le placer dans le lecteur de stockage persistant qui est mappé au dossier /var/opt/mssql/ dans le module SQL Edge. Lors du déploiement de SQL Edge à partir de la Place de marché Azure, ce mappage est spécifié en tant qu’option **Mounts** dans les **Options de création de conteneur**.

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

|Option|Description|
|:---|:---|
|**Feedback des clients** | Choisissez si SQL Server envoie des commentaires à Microsoft. |
|**Profil Database Mail** | Définir le profil de messagerie de base de données par défaut pour SQL Server sur Linux. |
|**Haute disponibilité** | Activer les groupes de disponibilité. |
|**Microsoft Distributed Transaction Coordinator** | Configurer et dépanner MSDTC sur Linux. Les autres options de configuration liées aux transactions distribuées ne sont pas prises en charge par SQL Edge. Pour plus d’informations sur ces options de configuration supplémentaires, consultez [Configurer MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**CLUF MLServices** | Accepter les CLUF R et Python pour les packages Azure Machine Learning. S'applique à SQL Server 2019 uniquement.|
|**outboundnetworkaccess** |Activez l'accès réseau sortant pour les extensions [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python et Java.|

L’exemple suivant de fichier mssql.conf fonctionne pour SQL Edge. Pour plus d’informations sur le format du fichier mssql.conf, consultez [Format mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-steps"></a>Étapes suivantes

- [Se connecter à Azure SQL Edge](connect.md)
- [Générer une solution IoT de bout en bout avec SQL Edge](tutorial-deploy-azure-resources.md)
