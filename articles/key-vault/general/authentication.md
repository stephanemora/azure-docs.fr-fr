---
title: S’authentifier auprès d’Azure Key Vault
description: Apprendre à s’authentifier auprès d’Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: d247e657e93afd0c43ecee1154c542398304d8dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89481373"
---
# <a name="authenticate-to-azure-key-vault"></a>S’authentifier auprès d’Azure Key Vault

Azure Key Vault offre la possibilité de stocker des secrets et de contrôler leur distribution dans un référentiel cloud centralisé et sécurisé, ce qui évite d’avoir à stocker des informations d’identification dans les applications. Il suffit aux applications de s’authentifier auprès de Key Vault à l’exécution pour accéder à ces secrets.

## <a name="app-identity-and-security-principals"></a>Identité et principaux de sécurité des applications

L’authentification auprès de Key Vault fonctionne conjointement avec [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), qui est chargé d’authentifier l’identité de chaque **principal de sécurité** donné.

Un principal de sécurité est un objet qui représente un utilisateur, un groupe, un service ou une application demandant l’accès aux ressources Azure. Azure affecte un **ID d’objet** unique à chaque principal de sécurité.

* Un principal de sécurité **utilisateur** identifie une personne disposant d’un profil dans Azure Active Directory.

* Un principal de sécurité **groupe** identifie un ensemble d’utilisateurs créés dans Azure Active Directory. Tous les rôles et autorisations attribués au groupe sont accordés à tous les utilisateurs du groupe.

* Un **principal de service** est un type de principal de sécurité qui identifie une application ou un service, c’est-à-dire un morceau de code plutôt qu’un utilisateur ou un groupe. L’ID d’objet d’un principal de service, appelé **ID client**, lui sert de nom d’utilisateur. La **clé secrète client** du principal de service fonctionne comme un mot de passe.

Pour les applications, il existe deux façons d’obtenir un principal de service :

* Méthode recommandée : activez une **identité managée** affectée par le système pour l’application.

    Avec l’identité managée, Azure gère en interne le principal de service de l’application et authentifie automatiquement l’application auprès d’autres services Azure. L’identité managée est disponible pour les applications déployées sur un large éventail de services.

    Pour plus d’informations, consultez [Vue d’ensemble de l’identité managée](/azure/active-directory/managed-identities-azure-resources/overview). Consultez également [Services Azure qui prennent en charge l’identité managée](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), qui comporte des liens vers des articles expliquant comment activer l’identité managée pour des services spécifiques (par exemple, App Service, Azure Functions ou Machines Virtuelles).

* Si vous ne pouvez pas utiliser l’identité managée, **inscrivez** l’application auprès de votre locataire Azure AD (cf. [Démarrage rapide : Inscription d’une application avec la Plateforme d’identités Azure](/azure/active-directory/develop/quickstart-register-app)). L’inscription a également pour effet de créer un deuxième objet d’application qui identifie l’application sur tous les locataires.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autoriser un principal de sécurité à accéder à Key Vault

Key Vault fonctionne avec deux niveaux d’autorisation distincts :

- Les **stratégies d’accès** contrôlent si un utilisateur, un groupe ou un principal de service est autorisé à accéder aux secrets, aux clés et aux certificats *au sein* d’une ressource Key Vault existante (opérations parfois appelées « plan de données »). Ces stratégies sont généralement accordées à des utilisateurs, groupes et applications.

    Pour affecter des stratégies d’accès, consultez les articles suivants :

    - [Azure portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- Les **autorisations de rôle** contrôlent si un utilisateur, un groupe ou un principal de service est autorisé à créer, à supprimer et, plus généralement, à gérer une ressource Key Vault (opérations parfois appelées « plan de gestion »). Ces rôles sont la plupart du temps accordés uniquement aux administrateurs.
 
    Pour affecter et gérer les rôles, consultez les articles suivants :

    - [Azure portal](/azure/role-based-access-control/role-assignments-portal)
    - [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Key Vault prend actuellement en charge le rôle [Contributeur](/azure/role-based-access-control/built-in-roles#key-vault-contributor), qui autorise les opérations de gestion sur les ressources Key Vault. D’autres rôles sont actuellement en préversion. Vous pouvez également créer des rôles personnalisés (cf. [Rôles personnalisés Azure](/azure/role-based-access-control/custom-roles)).

    Pour des informations générales sur les rôles, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (RBAC) ?](/azure/role-based-access-control/overview).


> [!IMPORTANT]
> Pour une sécurité optimale, suivez toujours le principe des privilèges minimum : accordez uniquement les stratégies d’accès et les rôles les plus spécifiques nécessaires. 
    
## <a name="configure-the-key-vault-firewall"></a>Configuration du pare-feu Key Vault

Par défaut, Key Vault autorise l’accès aux ressources par le biais d’adresses IP publiques. Pour une sécurité optimale, vous pouvez aussi limiter l’accès à des plages d’adresses IP, des points de terminaison de service, des réseaux virtuels ou des points de terminaison privés spécifiques.

Pour plus d’informations, consultez [Accès à Azure Key Vault derrière un pare-feu](/azure/key-vault/general/access-behind-firewall).


## <a name="the-key-vault-authentication-flow"></a>Flux d’authentification Key Vault

1. Un principal de service demande à s’authentifier auprès d’Azure AD, par exemple :
    * Un utilisateur se connecte au Portail Azure avec un nom d’utilisateur et un mot de passe.
    * Une application appelle une API REST Azure, en présentant un ID client et une clé secrète ou un certificat client.
    * Une ressource Azure, comme une machine virtuelle avec identité managée, contacte le point de terminaison REST [Azure Instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) pour obtenir un jeton d’accès.

1. Si l’authentification auprès d’Azure AD réussit, un jeton OAuth est accordé au principal de service.

1. Le principal de service effectue un appel à l’API REST Key Vault par le biais du point de terminaison (URI) de Key Vault.

1. Le pare-feu Key Vault vérifie les critères suivants. Si un critère est respecté, l’appel est autorisé. Dans le cas contraire, l’appel est bloqué et une réponse Interdit est retournée.

    * Le pare-feu Key Vault est désactivé et le point de terminaison public de Key Vault est accessible sur l’Internet public.
    * L’appelant est un [service approuvé Key Vault](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), ce qui lui permet de contourner le pare-feu.
    * L’appelant est listé dans le pare-feu par adresse IP, réseau virtuel ou point de terminaison de service.
    * L’appelant peut accéder à Key Vault au moyen d’une connexion de liaison privée configurée.    

1. Si le pare-feu autorise l’appel, Key Vault appelle Azure AD pour valider le jeton d’accès du principal de service.

1. Key Vault vérifie si le principal de service dispose de la stratégie d’accès nécessaire pour l’opération demandée. Si ce n’est pas le cas, Key Vault retourne une réponse Interdit.

1. Key Vault effectue l’opération demandée et retourne le résultat.

Le diagramme suivant illustre le processus pour une application qui appelle une API Key Vault « Obtenir le secret » :

![Flux d’authentification Azure Key Vault](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Exemples de code

Le tableau suivant contient des liens vers différents articles qui montrent comment utiliser Key Vault dans le code d’application à l’aide des bibliothèques Azure SDK du langage en question. D’autres interfaces, comme Azure CLI et le Portail Azure, sont incluses pour des raisons pratiques.

| Secrets Key Vault | Clés Key Vault | Certificats Key Vault |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (SDK v4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (SDK v3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure portal](/azure/key-vault/secrets/quick-create-portal) | [Azure portal](/azure/key-vault/keys/quick-create-portal) | [Azure portal](/azure/key-vault/certificates/quick-create-portal) |
| [Azure CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure CLI](/azure/key-vault/keys/quick-create-cli) | [Azure CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [Modèle ARM](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de stratégie d’accès à Key Vault](troubleshooting-access-issues.md)
- [Codes d'erreur de l'API REST Key Vault](rest-error-codes.md)
- [Guide du développeur Key Vault](developers-guide.md)
- [Présentation du contrôle d’accès en fonction du rôle (RBAC) Azure](/azure/role-based-access-control/overview)
