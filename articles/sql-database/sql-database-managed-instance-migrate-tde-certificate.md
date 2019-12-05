---
title: 'Migrer un certificat TDE : instance gérée'
description: Découvrez comment migrer un certificat protégeant la clé de chiffrement d’une base de données vers Azure SQL Database Managed Instance à l’aide de Transparent Data Encryption.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 0f6e379287323d9353acd887cf30d5c9c0065959
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555384"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrer le certificat d’une base de données protégée par TDE vers Azure SQL Database Managed Instance

Lorsque vous migrez une base de données protégée par [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) vers Azure SQL Database Managed Instance à l’aide d’une option de restauration native, le certificat correspondant du serveur SQL Server local ou IaaS doit être migré avant la restauration de la base de données. Cet article vous guide dans le processus de migration manuelle du certificat vers Azure SQL Database Managed Instance :

> [!div class="checklist"]
> * Exporter un certificat dans un fichier Personal Information Exchange (.pfx)
> * Extraire le certificat du fichier vers une chaîne en base 64
> * Le charger à l’aide d’une cmdlet PowerShell

Pour prendre connaissance d’une autre possibilité de migration fluide d’une base de données protégée par TDE et du certificat correspondant à l’aide d’un service géré, consultez [Migrer SQL Server vers Azure SQL Database Managed Instance à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Un certificat migré permet de restaurer uniquement la base de données protégée par TDE. Peu de temps après la restauration, le certificat migré est remplacé par un autre protecteur, soit un certificat géré par le service, soit une clé asymétrique du coffre de clés, en fonction du type de chiffrement transparent des données défini sur l’instance.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes indiquées dans cet article, vous avez besoin des éléments requis suivants :

- L’outil en ligne de commande [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) installé sur le serveur local ou un autre ordinateur avec accès au certificat exporté dans un fichier. Cet outil fait partie du kit [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), un environnement de ligne de commande autonome.
- [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) version 5.0 ou supérieure installé.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Vérifiez que vous disposez des éléments suivants :

- Module Azure PowerShell [installé et mis à jour](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Module Az.Sql](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Exécutez les commandes suivantes dans PowerShell pour installer/mettre à jour le module :

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exporter un certificat TDE dans un fichier Personal Information Exchange (.pfx)

Le certificat peut être exporté directement à partir du serveur SQL Server source ou du magasin de certificats s’il y est conservé.

### <a name="export-certificate-from-the-source-sql-server"></a>Exporter un certificat à partir du serveur SQL Server source

Pour exporter un certificat avec SQL Server Management Studio et le convertir au format .pfx, procédez comme suit. Dans les étapes indiquées ci-dessous, les noms génériques *TDE_Cert* et *full_path* sont utilisés comme noms de certificat et de fichier et comme chemins d’accès. Il convient de les remplacer par les noms réels.

1. Dans SSMS, ouvrez une nouvelle fenêtre de requête et connectez-vous au serveur SQL Server source.

1. Utilisez le script suivant pour répertorier les bases de données protégées par TDE et obtenir le nom du certificat protégeant le chiffrement de la base de données à migrer :

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![liste des certificats TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Exécutez le script suivant pour exporter le certificat vers une paire de fichiers (.cer et .pvk), en conservant les informations de clés publiques et privées :

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![sauvegarder un certificat TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Utilisez la console PowerShell pour copier les informations de certificat d’une paire de fichiers qui viennent d’être créés vers un fichier .pfx à l’aide de l’outil Pvk2Pfx :

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exporter un certificat à partir d’un magasin de certificats

Si le certificat est conservé dans le magasin de certificats de l’ordinateur local du serveur SQL Server, vous pouvez l’exporter en procédant comme suit :

1. Ouvrez la console PowerShell et exécutez la commande suivante pour ouvrir le composant logiciel enfichable Certificats de Microsoft Management Console :

   ```cmd
   certlm
   ```

2. Dans le composant logiciel enfichable MMC Certificats, développez le chemin d’accès Personnel -> Certificats pour afficher la liste des certificats.

3. Cliquez avec le bouton droit sur le certificat, puis cliquez sur Exporter...

4. Suivez l’Assistant pour exporter le certificat et la clé privée au format Personal Information Exchange.

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Charger le certificat vers Azure SQL Database Managed Instance à l’aide d’une applet de commande Azure PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Commencez par les étapes de préparation dans PowerShell :

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Une fois que toutes les étapes de préparation sont effectuées, exécutez les commandes suivantes pour charger le certificat encodé en base 64 vers l’instance Managed Instance cible :

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Vous devez d’abord [configurer un coffre de clés Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) avec votre fichier *.pfx*.

1. Commencez par les étapes de préparation dans PowerShell :

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Une fois que toutes les étapes de préparation sont effectuées, exécutez les commandes suivantes pour charger le certificat encodé en base 64 vers l’instance Managed Instance cible :

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Le certificat est désormais disponible dans l’instance Managed Instance spécifiée, et la sauvegarde de la base de données correspondante protégée par TDE peut être restaurée avec succès.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à migrer un certificat qui protège la clé de chiffrement d’une base de données avec TDE d’un serveur SQL Server local et IaaS vers Azure SQL Database Managed Instance.

Consultez [Restaurer une sauvegarde de base de données dans Azure SQL Database Managed Instance](sql-database-managed-instance-get-started-restore.md) pour apprendre à restaurer une sauvegarde de base de données dans Azure SQL Database Managed Instance.
