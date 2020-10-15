---
author: baanders
description: Fichier include pour l’étape des autorisations d’accès dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87408148"
---
Azure Digital Twins utilise [Azure Active Directory (AAD)](../articles/active-directory/fundamentals/active-directory-whatis.md) pour le contrôle d’accès en fonction du rôle (RBAC). Cela signifie qu’avant qu’un utilisateur puisse effectuer des appels de plan de données à votre instance Azure Digital Twins, il doit se voir attribuer un rôle disposant des autorisations appropriées.

Pour Azure Digital Twins, ce rôle est _**Propriétaire Azure Digital Twins (préversion)**_. Pour en savoir plus sur les rôles et la sécurité, consultez [*Concepts : Sécurité pour les solutions Azure Digital Twins*](../articles/digital-twins/concepts-security.md).

Cette section montre comment créer une attribution de rôle pour un utilisateur dans votre instance Azure Digital Twins, en utilisant l’adresse e-mail de cet utilisateur dans le locataire Azure AD sur votre abonnement Azure. Selon votre rôle dans votre organisation, vous pouvez configurer cette autorisation pour vous-même ou pour le compte d’une autre personne appelée à gérer l’instance Azure Digital Twins.

### <a name="assign-the-role"></a>Attribuer le rôle

Pour accorder à un utilisateur les autorisations nécessaires pour gérer une instance Azure Digital Twins, vous devez lui attribuer le rôle _**Propriétaire Azure Digital Twins (préversion)**_ au sein de l’instance.

> [!NOTE]
> Notez que ce rôle diffère du rôle Azure AD *Propriétaire* qui peut également être attribué à l’étendue de l’instance Azure Digital Twins. Il s’agit de deux rôles de gestion distincts. Le rôle Azure AD *Propriétaire* n’octroie pas d’accès aux fonctionnalités de plan de données qui sont accordées avec le rôle *Propriétaire Azure Digital Twins (préversion)* .