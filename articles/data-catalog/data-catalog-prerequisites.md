---
title: Configuration requise pour Azure Data Catalog
description: Découvrez les prérequis dont vous avez besoin pour bien démarrer avec Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 788e7a453e9879d9c901448659ff1c8cfa7201ff
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548306"
---
# <a name="azure-data-catalog-prerequisites"></a>Configuration requise pour Azure Data Catalog

Vous devez vous occuper de certaines choses avant de configurer Azure Data Catalog. Ce processus ne sera pas long.

## <a name="azure-subscription"></a>Abonnement Azure
Pour configurer Data Catalog, vous devez être le propriétaire ou le copropriétaire d’un abonnement Azure.

Les abonnements Azure vous permettent d’organiser l’accès aux ressources du service cloud telles que Data Catalog. Ils vous permettent également de contrôler le signalement, la facturation et le paiement des ressources utilisées. Chaque abonnement peut disposer d’une configuration de facturation et de paiement différente. Vous pouvez donc avoir différents abonnements et différents plans par département, projet, bureau régional, etc. Chaque service cloud appartient à un abonnement. Vous devez donc avoir un abonnement avant de configurer Data Catalog. Pour plus d’informations, consultez [Manage Accounts, Subscriptions, and Administrative Roles](../active-directory/users-groups-roles/directory-assign-admin-roles.md) (Gérer les comptes, les abonnements et les rôles d’administrateur).

## <a name="azure-active-directory"></a>Azure Active Directory
Pour configurer Data Catalog, vous devez être connecté avec un compte d’utilisateur Azure Active Directory (Azure AD).

Azure AD permet à votre entreprise de gérer facilement les identités et les accès, à la fois dans le cloud et en local. Les utilisateurs peuvent se servir du même compte professionnel ou scolaire pour utiliser l’authentification unique sur n’importe quelle application web locale ou cloud. Data Catalog utilise Azure AD pour authentifier la connexion. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Active Directory ?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Le [portail Azure](https://portal.azure.com/) permet de se connecter à l’aide d’un compte Microsoft personnel ou d’un compte professionnel ou scolaire Azure Active Directory. Pour configurer Data Catalog à l’aide du portail Azure ou du [portail Data Catalog](https://www.azuredatacatalog.com), vous devez vous connecter avec un compte Azure Active Directory, et non avec un compte personnel.
>
>

## <a name="active-directory-policy-configuration"></a>Configuration de la stratégie Active Directory
Il est possible que vous puissiez vous connecter au portail Data Catalog, mais que lorsque vous tentez de vous connecter à l’outil d’inscription de source de données, un message d’erreur s’affiche et vous empêche de vous connecter. Ce problème peut aussi se produire quand l’utilisateur se trouve sur le réseau d’entreprise ou quand il se connecte en dehors du réseau d’entreprise.

L’outil d’inscription de source de données utilise l’authentification par formulaire pour valider les informations d’identification par rapport à Active Directory. Pour que vous puissiez vous connecter, un administrateur Active Directory doit activer l’authentification par formulaire dans la stratégie d’authentification globale.

La stratégie d’authentification globale vous permet d’activer séparément des méthodes d’authentification pour les connexions intranet et extranet, comme le montre la capture d’écran suivante. Des erreurs de connexion peuvent survenir si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel vous vous connectez.

 ![Stratégie d’authentification globale d’Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).
