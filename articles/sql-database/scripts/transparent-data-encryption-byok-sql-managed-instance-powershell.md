---
title: 'PowerShell : Activer TDE avec BYOK - Azure SQL Database Managed Instance '
description: Découvrez comment configurer une entité Azure SQL Managed Instance pour qu’elle commence à utiliser Transparent Data Encryption (TDE) avec BYOK pour le chiffrement au repos à l’aide de PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691396"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gérer Transparent Data Encryption dans une instance gérée à l’aide de votre propre clé Azure Key Vault

Cet exemple de script PowerShell configure Transparent Data Encryption (TDE) avec une clé gérée par le client pour Azure SQL Managed Instance, en utilisant une clé provenant d’Azure Key Vault. Ceci est souvent désigné sous le nom de scénario BYOK (Bring Your Own Key) pour Transparent Data Encryption. Pour plus d’informations sur TDE avec une clé gérée par le client, consultez [TDE avec BYOK pour Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prérequis

- Une instance managée existante. Consultez [Utiliser PowerShell pour créer une instance managée Azure SQL Database](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

L’utilisation de PowerShell localement ou d’Azure Cloud Shell nécessite Azure PowerShell 2.3.2 ou une version ultérieure. Si une mise à niveau est nécessaire, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps) ou exécutez l’exemple de script ci-dessous afin d’installer le module pour l’utilisateur actuel :

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-scripts"></a>Exemples de scripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
