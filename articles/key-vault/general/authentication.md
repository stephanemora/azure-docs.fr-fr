---
title: S’authentifier auprès d’Azure Key Vault
description: Apprendre à s’authentifier auprès d’Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 1fa4f4b4b9b56b5acc3a3d3d9e75177889a4a8af
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479232"
---
# <a name="authentication-in-azure-key-vault"></a>Authentification dans Azure Key Vault

L’authentification auprès de Key Vault fonctionne conjointement avec [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), qui est chargé d’authentifier l’identité de chaque **principal de sécurité** donné.

Un principal de sécurité est un objet qui représente un utilisateur, un groupe, un service ou une application demandant l’accès aux ressources Azure. Azure affecte un **ID d’objet** unique à chaque principal de sécurité.

* Un principal de sécurité **utilisateur** identifie une personne disposant d’un profil dans Azure Active Directory.

* Un principal de sécurité **groupe** identifie un ensemble d’utilisateurs créés dans Azure Active Directory. Tous les rôles et autorisations attribués au groupe sont accordés à tous les utilisateurs du groupe.

* Un **principal de service** est un type de principal de sécurité qui identifie une application ou un service, c’est-à-dire un morceau de code plutôt qu’un utilisateur ou un groupe. L’ID d’objet d’un principal de service, appelé **ID client**, lui sert de nom d’utilisateur. La **clé secrète client** du principal de service fonctionne comme un mot de passe.

Pour les applications, il existe deux façons d’obtenir un principal de service :

* Méthode recommandée : activez une **identité managée** affectée par le système pour l’application.

    Avec l’identité managée, Azure gère en interne le principal de service de l’application et authentifie automatiquement l’application auprès d’autres services Azure. L’identité managée est disponible pour les applications déployées sur un large éventail de services.

    Pour plus d’informations, consultez [Vue d’ensemble de l’identité managée](../../active-directory/managed-identities-azure-resources/overview.md). Consultez également [Services Azure qui prennent en charge l’identité managée](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), qui comporte des liens vers des articles expliquant comment activer l’identité managée pour des services spécifiques (par exemple, App Service, Azure Functions ou Machines Virtuelles).

* Si vous ne pouvez pas utiliser l’identité managée, **inscrivez** l’application auprès de votre locataire Azure AD (cf. [Démarrage rapide : Inscription d’une application avec la Plateforme d’identités Azure](../../active-directory/develop/quickstart-register-app.md)). L’inscription a également pour effet de créer un deuxième objet d’application qui identifie l’application sur tous les locataires.

## <a name="configure-the-key-vault-firewall"></a>Configuration du pare-feu Key Vault

Par défaut, Key Vault autorise l’accès aux ressources par le biais d’adresses IP publiques. Pour une sécurité optimale, vous pouvez aussi limiter l’accès à des plages d’adresses IP, des points de terminaison de service, des réseaux virtuels ou des points de terminaison privés spécifiques.

Pour plus d’informations, consultez [Accès à Azure Key Vault derrière un pare-feu](./access-behind-firewall.md).

## <a name="the-key-vault-request-operation-flow-with-authentication"></a>Déroulement de l’opération de demande Key Vault avec l’authentification

L’authentification Key Vault se produit dans le cadre de chaque opération de demande sur Key Vault. Une fois le jeton récupéré, il peut être réutilisé pour les appels suivants. Exemple de flux d’authentification :

1. Un jeton demande à s’authentifier auprès d’Azure AD, par exemple :
    * Une ressource Azure, comme une machine virtuelle ou une application App Service avec identité managée, contacte le point de terminaison REST pour obtenir un jeton d’accès.
    * Un utilisateur se connecte au Portail Azure avec un nom d’utilisateur et un mot de passe.

1. Si l’authentification auprès d’Azure AD réussit, un jeton OAuth est accordé au principal de sécurité.

1. Un appel à l’API REST Key Vault est effectué par le biais du point de terminaison (URI) de Key Vault.

1. Le pare-feu Key Vault vérifie les critères suivants. Si un critère est respecté, l’appel est autorisé. Dans le cas contraire, l’appel est bloqué et une réponse Interdit est retournée.

    * Le pare-feu Key Vault est désactivé et le point de terminaison public de Key Vault est accessible sur l’Internet public.
    * L’appelant est un [service approuvé Key Vault](./overview-vnet-service-endpoints.md#trusted-services), ce qui lui permet de contourner le pare-feu.
    * L’appelant est listé dans le pare-feu par adresse IP, réseau virtuel ou point de terminaison de service.
    * L’appelant peut accéder à Key Vault au moyen d’une connexion de liaison privée configurée.    

1. Si le pare-feu autorise l’appel, Key Vault appelle Azure AD pour valider le jeton d’accès du principal de sécurité.

1. Key Vault vérifie si le principal de sécurité dispose de l’autorisation nécessaire pour l’opération demandée. Si ce n’est pas le cas, Key Vault retourne une réponse Interdit.

1. Key Vault effectue l’opération demandée et retourne le résultat.

Le diagramme suivant illustre le processus pour une application qui appelle une API Key Vault « Obtenir le secret » :

![Flux d’authentification Azure Key Vault](../media/authentication/authentication-flow.png)

> [!NOTE]
> Les clients du SDK Key Vault pour les secrets, les certificats et les clés effectuent un appel supplémentaire en direction de Key Vault sans jeton d’accès, ce qui entraîne une réponse 401 pour récupérer les informations du locataire. Pour plus d’informations, consultez [Authentification, requêtes et réponses](authentication-requests-and-responses.md).

## <a name="authentication-to-key-vault-in-application-code"></a>Authentification auprès de Key Vault dans le code d’application

Le SDK Key Vault utilise la bibliothèque de client Azure Identity, qui permet une authentification fluide auprès de Key Vault dans différents environnements avec le même code.

**Bibliothèques clientes d’identité Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[SDK de l’identité Azure .NET](/dotnet/api/overview/azure/identity-readme)|[SDK de l’identité Azure Python](/python/api/overview/azure/identity-readme)|[SDK de l’identité Azure Java](/java/api/overview/azure/identity-readme)|[SDK de l’identité Azure Javascript](/javascript/api/overview/azure/identity-readme)|   

Pour plus d’informations sur les bonnes pratiques et des exemples de développement, consultez [S’authentifier auprès de Key Vault dans le code](developers-guide.md#authenticate-to-key-vault-in-code).

## <a name="next-steps"></a>Étapes suivantes

- [Guide du développeur Key Vault](developers-guide.md)
- [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](assign-access-policy-portal.md)
- [Attribuer un rôle RBAC Azure à Key Vault](rbac-guide.md)
- [Résolution des problèmes de stratégie d’accès à Key Vault](troubleshooting-access-issues.md)
- [Codes d'erreur de l'API REST Key Vault](rest-error-codes.md)

- [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC Azure) ?](../../role-based-access-control/overview.md)
