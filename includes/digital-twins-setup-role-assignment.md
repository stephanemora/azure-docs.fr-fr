---
author: baanders
description: Fichier include pour l’étape des autorisations d’accès dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: e49abce74f5f4ab3a624def8ec1eb1d674711462
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108759917"
---
Azure Digital Twins utilise [Azure Active Directory (AAD)](../articles/active-directory/fundamentals/active-directory-whatis.md) pour le contrôle d’accès en fonction du rôle (RBAC). Cela signifie qu’avant qu’un utilisateur puisse effectuer des appels de plan de données à votre instance Azure Digital Twins, il doit se voir attribuer un rôle disposant des autorisations appropriées.

Pour Azure Digital Twins, ce rôle est **Propriétaire de données Azure Digital Twins**. Pour en savoir plus sur les rôles et la sécurité, consultez [Concepts : Sécurité pour les solutions Azure Digital Twins](../articles/digital-twins/concepts-security.md).

> [!NOTE]
> Ce rôle diffère du rôle Azure AD **Propriétaire** qui peut également être attribué à l’étendue de l’instance Azure Digital Twins. Il s’agit de deux rôles de gestion distincts. Le rôle Propriétaire n’octroie pas d’accès aux fonctionnalités de plan de données qui sont accordées avec le rôle **Propriétaire de données Azure Digital Twins**.

Cette section montre comment créer une attribution de rôle pour un utilisateur dans votre instance Azure Digital Twins, en utilisant l’adresse e-mail de cet utilisateur dans le locataire Azure AD sur votre abonnement Azure. Selon votre rôle dans votre organisation, vous pouvez configurer cette autorisation pour vous-même ou pour le compte d’une autre personne appelée à gérer l’instance Azure Digital Twins.