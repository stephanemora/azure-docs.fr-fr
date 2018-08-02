---
title: Guide pratique pour configurer Managed Service Identity activé sur une machine virtuelle Azure au moyen d’un kit SDK Azure
description: Instructions détaillées pour configurer et utiliser Managed Service Identity sur une machine virtuelle Azure à l’aide d’un kit SDK Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257658"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>Configurer Managed Service Identity pour une machine virtuelle au moyen d’un kit SDK Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity fournit des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous découvrez comment activer et supprimer Managed Service Identity sur une machine virtuelle Azure à l’aide d’un Kit SDK Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>Kits SDK Azure avec prise en charge de Managed Service Identity 

Azure prend en charge plusieurs plates-formes de programmation via une série de [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads). Plusieurs d’entre eux ont été mis à jour pour prendre en charge Managed Service Identity, et donner des exemples correspondants qui illustrent son utilisation. Cette liste a été mise à jour, suite à l’ajout d’une prise en charge supplémentaire :

| SDK | Exemple |
| --- | ------ | 
| .NET   | [Gérer une ressource à partir d’une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gérer le stockage à partir d’une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Créer une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Créer une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Créer une machine virtuelle Azure avec une MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Étapes suivantes

- Consultez les articles connexes répertoriés sous la section « Configuration de Managed Service Identity pour une machine virtuelle Azure », afin de savoir également comment utiliser le portail Azure, PowerShell, CLI et les modèles de ressources.

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
