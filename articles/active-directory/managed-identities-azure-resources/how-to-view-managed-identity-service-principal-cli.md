---
title: Afficher le principal de service d’une identité managée - Interface de ligne de commande Azure - Azure AD
description: Instructions pas à pas pour afficher le principal du service d’une identité managée à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 82529ab954c5474345e2702840daa1c7bca2ff1d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501182"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Afficher le principal du service d’une identité managée à l’aide d’Azure CLI

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à afficher le principal du service d’une identité managée à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Conditions préalables requises

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous pour créer un compte gratuit](https://azure.microsoft.com/free/).
- Activez [l’identité affectée par le système sur une machine virtuelle](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou une [application](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) si vous préférez utiliser une console CLI locale et connectez-vous à Azure à l’aide de la commande `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Afficher le principal du service

La commande suivante indique comment afficher le principal du service d’une machine virtuelle ou d’une application avec identité managée activée. Remplacez `<VM or application name>` par vos propres valeurs. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des principaux de service Azure AD à l’aide d’Azure CLI, voir [az ad sp](/cli/azure/ad/sp).


