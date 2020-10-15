---
title: Désactiver la vérification par e-mail lors de l’inscription du client
titleSuffix: Azure AD B2C
description: Découvrez comment désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384207"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Pour désactiver la vérification par e-mail, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Utilisez le filtre **Annuaire + abonnement** dans le menu du haut pour sélectionner l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail. Par exemple, *B2C_1_signinsignup*.
1. Sélectionnez **Mises en page**.
1. Sélectionnez **Page d’inscription à un compte Local**.
1. Sous **Attributs utilisateur**, sélectionnez **Adresse e-mail**.
1. Dans la liste déroulante **REQUIERT UNE VÉRIFICATION**, sélectionnez **Non**.
1. Sélectionnez **Enregistrer**. La vérification par e-mail est maintenant désactivée pour ce flux d’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser l’interface utilisateur dans Azure Active Directory B2C](customize-ui-overview.md)

