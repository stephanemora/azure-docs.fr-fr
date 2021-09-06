---
title: Authentifier les événements de publication des clients dans des rubriques, des domaines et des espaces de noms de partenaires personnalisés Event Grid.
description: Cet article décrit les différentes façons d’authentifier les événements de publication des clients dans des rubriques, des domaines et des espaces de noms de partenaires personnalisés Event Grid.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524900"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>Authentification du client lors de la publication d’événements sur Event Grid
L’authentification pour les clients publiant des événements sur Event Grid est prise en charge à l’aide des méthodes suivantes :

- Azure Active Directory (Azure AD)
- Clé d’accès ou signature d’accès partagé (SAP)

## <a name="authenticate-using-azure-active-directory-preview"></a>Authentifier à l’aide d’Azure Active Directory (préversion)
L’intégration Azure AD pour les ressources Event Grid fournit un contrôle d’accès en fonction du rôle (RBAC) Azure, qui permet un contrôle affiné de l’accès d’un client aux ressources. Vous pouvez utiliser Azure RBAC pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut être utilisé pour autoriser une requête d’accès aux ressources Event Grid (rubriques, domaines ou espaces de noms de partenaires). Pour plus d’informations, consultez [Authentifier et autoriser avec la plateforme d’identités Microsoft](authenticate-with-active-directory.md).


> [!IMPORTANT]
> Le fait d’authentifier et d’autoriser des utilisateurs ou des applications en utilisant des identités Azure AD offre davantage de sécurité et de facilité d’utilisation par rapport à l’authentification basée sur une clé et avec des signatures d’accès partagé (SAP). Azure AD vous évite d’avoir à stocker les secrets utilisés pour l’authentification dans votre code. Vous êtes ainsi moins exposé au risque de failles de sécurité. Nous vous recommandons vivement d’utiliser Azure AD avec vos applications de publication d’événements Azure Event Grid.

> [!NOTE]
> La prise en charge de l’authentification Azure AD par Azure Event Grid a été publiée en préversion. Azure Event Grid sur Kubernetes ne prend pas encore en charge l’authentification Azure AD. 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>Authentifier avec des clés d’accès et des signatures d’accès partagé
Vous pouvez authentifier des clients qui publient des événements dans des rubriques, des domaines ou des espaces de noms de partenaires Azure Event Grid à l’aide d’une **clé d’accès** ou d’un jeton de **signature d’accès partagé (SAP)** . Pour plus d’informations, consultez [Utilisation de clés d'accès ou de signatures d’accès partagé (SAP)](authenticate-with-access-keys-shared-access-signatures.md). 
   

## <a name="next-steps"></a>Étapes suivantes
Cet article traite de l’authentification lors de la **publication** d’événements sur Event Grid (entrée d’événements). Pour en savoir plus sur l’authentification lors de la [remise](security-authentication.md) d’événements (sortie d’événements), consultez **Authentifier la remise des événements aux gestionnaires d’événements**. 

