---
title: AAfficher le principal de service d’une identité managée à l’aide de PowerShell - Azure AD
description: Instructions pas à pas pour afficher le principal du service d’une identité managée à l’aide de PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298646"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Afficher le principal du service d’une identité managée à l’aide de PowerShell

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à afficher le principal du service d’une identité managée à l’aide de PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous pour créer un compte gratuit](https://azure.microsoft.com/free/).
- Activez [l’identité affectée par le système sur une machine virtuelle](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou une [application](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Installer la dernière version [d’Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Afficher le principal du service

La commande suivante indique comment afficher le principal du service d’une machine virtuelle ou d’une application sur laquelle est activée une identité affectée par le système. Remplacez `<VM or application name>` par vos propres valeurs.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’affichage des principaux du service Azure AD avec PowerShell, consultez [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


