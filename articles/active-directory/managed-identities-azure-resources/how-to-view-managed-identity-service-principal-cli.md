---
title: Afficher le principal de service d’une identité managée - Interface de ligne de commande Azure - Azure AD
description: Instructions pas à pas pour afficher le principal du service d’une identité managée à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8eec72666eadf90a401dc8f0adb77df77dbf782
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969310"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Afficher le principal du service d’une identité managée à l’aide d’Azure CLI

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à afficher le principal du service d’une identité managée à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Conditions préalables requises

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous pour créer un compte gratuit](https://azure.microsoft.com/free/).
- Activez [l’identité affectée par le système sur une machine virtuelle](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) ou une [application](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Pour exécuter les exemples de scripts, vous avez deux options :
    - Utiliser [Azure Cloud Shell](../../cloud-shell/overview.md), que vous pouvez ouvrir en utilisant le bouton **Essayer** dans le coin supérieur droit des blocs de code.
    - Exécuter les scripts localement en installant la dernière version d’[Azure CLI](/cli/azure/install-azure-cli), puis en vous connectant à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure dans lequel vous voulez créer des ressources.   

## <a name="view-the-service-principal"></a>Afficher le principal du service

La commande suivante indique comment afficher le principal du service d’une machine virtuelle ou d’une application avec identité managée activée. Remplacez `<VM or application name>` par vos propres valeurs. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des principaux de service Azure AD à l’aide d’Azure CLI, voir [az ad sp](/cli/azure/ad/sp).
