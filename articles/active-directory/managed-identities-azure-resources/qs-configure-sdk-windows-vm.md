---
title: Utiliser un SDK Azure pour configurer une machine virtuelle avec des identités managées pour ressources Azure
description: Instructions détaillées pour configurer et utiliser des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un SDK Azure.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29446f6f0a86a00cc92fe46211caddd22aaa4859
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112562"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurer une machine virtuelle avec des identités managées pour ressources Azure en utilisant un SDK Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory (AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment activer et supprimer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant un SDK Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDK Azure prenant en charge les identités managées pour ressources Azure 

Azure prend en charge plusieurs plates-formes de programmation via une série de [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads). Plusieurs d’entre eux ont été mis à jour pour prendre en charge les identités managées pour ressources Azure, et fournissent des exemples appropriés pour en illustrer l’utilisation. Cette liste a été mise à jour, suite à l’ajout d’une prise en charge supplémentaire :

| Kit SDK | Exemple |
| --- | ------ | 
| .NET   | [Gérer une ressource à partir d’une machine virtuelle activée avec des identités managées pour ressources Azure](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gérer un stockage à partir d’une machine virtuelle activée avec des identités managées pour ressources Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Créer une machine virtuelle avec une identité managée affectée par le système activée](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Créer une machine virtuelle avec une identité managée affectée par le système activée](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Créer une machine virtuelle Azure avec une identité affectée par le système activée](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment utiliser également le portail Azure, PowerShell, CLI et des modèles de ressources, voir les articles à ce propos répertoriés dans la section **Configurer une identité pour une machine virtuelle Azure**.
