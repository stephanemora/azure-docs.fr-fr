---
title: Identités gérées pour les ressources Azure
description: Une vue d’ensemble des identités managées pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/05/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c49ded056c642fa91331b7cc98d18da34d9c73a6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504348"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Que sont les identités managées pour les ressources Azure ?

La gestion des secrets et des informations d'identification utilisés pour sécuriser la communication entre les différents composants d'une solution constitue un défi courant pour les développeurs. Les identités managées permettent aux développeurs de ne plus avoir à gérer les informations d'identification. Les identités managées fournissent une identité utilisée par les applications lorsqu'elles se connectent à des ressources qui prennent en charge l'authentification Azure Active Directory (Azure AD). Les applications peuvent utiliser l'identité managée pour obtenir des jetons Azure AD. Par exemple, une application peut utiliser une identité managée pour accéder à des ressources comme [Azure Key Vault](../../key-vault/general/overview.md) où les développeurs peuvent stocker des informations d'identification de manière sécurisée, ou pour accéder à des comptes de stockage.

À quoi une identité managée peut-elle servir ?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Voici quelques-uns des avantages de l’utilisation des identités managées :

- Vous n’avez pas besoin de gérer les informations d’identification. Vous n’avez même pas accès à ces dernières.
- Vous pouvez utiliser des identités managées pour vous authentifier auprès de ressources qui prennent en charge l'[authentification Azure Active Directory](../authentication/overview-authentication.md), y compris vos propres applications.
- Les identités managées peuvent être utilisées sans coût supplémentaire.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="managed-identity-types"></a>Types d’identités managées

Il existe deux types d’identités administrées :

- **Affectation par le système** Certains services Azure vous permettent d’activer une identité managée directement sur une instance de service. Quand vous activez une identité managée affectée par le système, une identité est créée dans Azure AD qui est liée au cycle de vie de cette instance de service. Ainsi, quand la ressource est supprimée, Azure supprime automatiquement l’identité. Par défaut, seule cette ressource Azure peut utiliser cette identité pour demander des jetons à Azure AD.
- **Affectation par l’utilisateur** Vous pouvez également créer une identité managée en tant que ressource Azure autonome. Vous pouvez [créer une identité managée affectée par l’utilisateur](how-to-manage-ua-identity-portal.md) et l’attribuer à une ou plusieurs instances d’un service Azure. Une identité managée affectée par l’utilisateur est gérée séparément des ressources qui l’utilisent. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

Le tableau ci-dessous montre les différences entre les deux types d’identités managées.

|  Propriété    | Identité managée affectée par le système | Identité managée affectée par l’utilisateur |
|------|----------------------------------|--------------------------------|
| Création |  Créé dans le cadre d’une ressource Azure (par exemple, une machine virtuelle Azure ou Azure App Service) | Créé en tant que ressource Azure autonome |
| Cycle de vie | Cycle de vie partagé entre la ressource Azure et l’identité managée avec laquelle elle est créée. <br/> Lorsque la ressource parente est supprimée, l’identité managée l’est également. | Cycle de vie indépendant. <br/> Doit être explicitement supprimé. |
| Partage entre ressources Azure | Ne peut pas être partagé. <br/> Ne peut être associé qu’à une seule ressource Azure. | Peut être partagé <br/> Une même identité managée affectée par l’utilisateur peut être associée à plusieurs ressources Azure. |
| Cas d’utilisation courants | Charges de travail contenues dans une même ressource Azure <br/> Charges de travail pour lesquelles vous avez besoin d’identités indépendantes. <br/> Par exemple, une application qui s’exécute sur une seule machine virtuelle | Charges de travail qui s’exécutent sur plusieurs ressources et qui peuvent partager une même identité. <br/> Charges de travail qui ont besoin d’une autorisation préalable pour accéder à une ressource sécurisée dans le cadre d’un flux de provisionnement. <br/> Charges de travail dont les ressources sont recyclées fréquemment, mais pour lesquelles les autorisations doivent rester les mêmes. <br/> Par exemple, une charge de travail où plusieurs machines virtuelles doivent accéder à la même ressource |

>[!IMPORTANT]
>Quel que soit le type d’identité choisi, une identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Lorsqu’une identité managée est supprimée, le principal de service correspondant est automatiquement supprimé.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Comment puis-je utiliser des identités managées pour les ressources Azure ?

![Quelques exemples de la façon dont un développeur peut utiliser des identités managées pour accéder aux ressources à partir de son code sans gérer les informations d’authentification](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Quels sont les services Azure qui prennent en charge la fonctionnalité ?<a name="which-azure-services-support-managed-identity"></a>

Les identités managées pour les ressources Azure peuvent servir à l’authentification auprès des services prenant en charge l’authentification Azure AD. Pour obtenir la liste des services Azure qui prennent en charge la fonctionnalité d’identités managées pour les ressources Azure, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](./services-support-managed-identities.md).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utiliser une identité managée de machine virtuelle Linux attribuée par le système pour accéder à Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../../app-service/overview-managed-identity.md)
* [Comment utiliser des identités managées avec Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implémentation d’identités managées pour des ressources Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).