---
author: baanders
description: Fichier include pour l’étape des autorisations d’accès dans la configuration d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408146"
---
Une fois que vous avez configuré une instance Azure Digital Twins, il est courant d’interagir avec cette instance par le biais d’une application cliente. Pour créer une application cliente opérationnelle, vous devez vous assurer que l’application cliente sera en mesure de s’authentifier auprès d’Azure Digital Twins. Pour ce faire, configurez une **inscription d’application** [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) que votre application cliente doit utiliser.

Cette inscription d’application est l’emplacement où vous configurez les autorisations d’accès aux [API d’Azure Digital Twins](../articles/digital-twins/how-to-use-apis-sdks.md). Plus tard, l’application cliente s’authentifiera par rapport à l’inscription d’application et les autorisations d’accès configurées seront accordées aux API.

>[!TIP]
> En tant que Propriétaire de l’abonnement, vous préférerez peut-être configurer une nouvelle inscription d’application pour chaque nouvelle instance Azure Digital Twins, *ou*, pour n’effectuer cette opération qu’une seule fois, en établissant une inscription d’application unique qui sera partagée entre toutes les instances Azure Digital Twins dans l’abonnement.

### <a name="create-the-registration"></a>Création de l’inscription