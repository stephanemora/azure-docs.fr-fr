---
title: Fournisseur d’identité de compte Microsoft (MSA) dans Azure AD
description: Utilisez Azure AD pour permettre à un utilisateur externe (invité) de se connecter à vos applications Azure AD à l’aide de son compte Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692866"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Fournisseur d’identité de compte Microsoft (MSA) pour External Identities (préversion)

> [!NOTE]
> Le fournisseur d’identité de compte Microsoft est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vos utilisateurs invités B2B peuvent utiliser leurs propres comptes Microsoft à des fins de collaboration B2B sans configuration supplémentaire. Les utilisateurs invités peuvent échanger vos invitations de collaboration B2B ou compléter vos flux d’utilisateurs d’inscription à l’aide de leur compte Microsoft personnel.

Les comptes Microsoft sont configurés par un utilisateur pour accéder aux produits et services cloud Microsoft orientés consommateurs, comme Outlook, OneDrive, Xbox LIVE ou Microsoft 365. Le compte est créé et stocké dans le système de comptes d’identité des consommateurs de Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Connexion d’invité à l’aide de comptes Microsoft

Le compte Microsoft est disponible dans la liste des fournisseurs d’identité External Identitites par défaut. Aucune autre configuration n’est requise pour permettre aux utilisateurs invités de se connecter avec leur compte Microsoft en utilisant le flux d’invitation ou un flux d’utilisateur d’inscription en libre-service.

![Compte Microsoft dans la liste des fournisseurs d’identité](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Compte Microsoft dans le flux d’invitation

Lorsque vous [invitez un utilisateur invité](add-users-administrator.md) à des fins de collaboration B2B, vous pouvez spécifier son compte Microsoft comme adresse e-mail qu’il utilisera pour se connecter.

![Inviter à l’aide d’un compte Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Compte Microsoft dans les flux d’utilisateurs d’inscription en libre-service

Le compte Microsoft est une option de fournisseur d’identité pour vos flux d’utilisateurs d’inscription en libre-service. Les utilisateurs peuvent s’inscrire à vos applications à l’aide de leurs propres comptes Microsoft. Dans un premier temps, vous devez [activer l’inscription en libre-service](self-service-sign-up-user-flow.md) pour votre locataire. Puis, vous devez configurer un flux d’utilisateurs pour l’application et sélectionner Compte Microsoft parmi les options de connexion.

![Compte Microsoft dans un flux d’inscription en libre-service](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs de collaboration B2B Azure Active Directory](add-users-administrator.md)
- [Ajouter l’inscription en libre-service à une application](self-service-sign-up-user-flow.md)