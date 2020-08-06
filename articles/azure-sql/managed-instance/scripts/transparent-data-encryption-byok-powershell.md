---
title: 'PowerShell : Activer TDE avec BYOK (Bring Your Own Key) TDE'
titleSuffix: Azure SQL Managed Instance
description: Découvrez comment configurer Azure SQL Managed Instance pour commencer à utiliser Transparent Data Encryption (TDE) avec Bring Your Own Key (BYOK) pour le chiffrement au repos à l’aide de PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 34d91b23b0383f4128b7f03619ad0e2b07496f33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014148"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption dans SQL Managed Instance à l’aide de votre propre clé Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Cet exemple de script PowerShell configure Transparent Data Encryption (TDE) avec une clé gérée par le client pour Azure SQL Managed Instance, en utilisant une clé provenant d’Azure Key Vault. Ceci est souvent désigné sous le nom de scénario Bring Your Own Key (BYOK) pour Transparent Data Encryption. Pour plus d'informations, consultez [Transparent Data Encryption Azure SQL avec une clé gérée par le client](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Prérequis

- Une instance managée existante. Consultez [Utiliser PowerShell pour créer une instance gérée](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

L’utilisation de PowerShell localement ou d’Azure Cloud Shell nécessite Azure PowerShell 2.3.2 ou une version ultérieure. Si une mise à niveau est nécessaire, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps) ou exécutez l’exemple de script ci-dessous afin d’installer le module pour l’utilisateur actuel :

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-scripts"></a>Exemples de scripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/).

Vous trouverez d’autres exemples de scripts PowerShell pour SQL Managed Instance dans [Scripts PowerShell Azure SQL Managed Instance](../../database/powershell-script-content-guide.md).
