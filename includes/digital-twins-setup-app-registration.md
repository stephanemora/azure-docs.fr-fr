---
author: baanders
description: Fichier include pour l’étape des autorisations d’accès dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009645"
---
Une fois que vous avez configuré une instance Azure Digital Twins, il est courant d’interagir avec cette instance par le biais d’une application cliente. Pour créer une application cliente opérationnelle, vous devez vous assurer que l’application cliente sera en mesure de s’authentifier auprès d’Azure Digital Twins. Pour ce faire, configurez une **inscription d’application** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) que votre application cliente doit utiliser.

Cette inscription d’application est l’emplacement où vous configurez les autorisations d’accès aux [API d’Azure Digital Twins](../articles/digital-twins/how-to-use-apis-sdks.md). Plus tard, l’application cliente s’authentifiera par rapport à l’inscription d’application et les autorisations d’accès configurées seront accordées aux API.

>[!TIP]
> En tant que Propriétaire/Administrateur de l’abonnement, vous préférerez peut-être configurer une nouvelle inscription d’application pour chaque nouvelle instance Azure Digital Twins *ou* n’effectuer cette opération qu’une seule fois en établissant une inscription d’application unique qui sera partagée entre toutes les instances Azure Digital Twins dans l’abonnement.

### <a name="create-the-registration"></a>Création de l’inscription