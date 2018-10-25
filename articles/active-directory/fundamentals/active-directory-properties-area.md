---
title: Ajouter les informations de confidentialité de votre organisation dans Azure Active Directory | Microsoft Docs
description: Comment ajouter les informations de confidentialité de votre organisation à la zone Propriétés d’Azure Active Directory (Azure AD).
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: f32eeee8f02cee32eb05e14aa98387a20cb2e626
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48903826"
---
# <a name="how-to-add-your-privacy-info-using-azure-active-directory"></a>Comment : ajouter vos informations de confidentialité à l’aide d’Azure Active Directory
Cet article explique comment un administrateur de locataires peut ajouter des informations de confidentialité au locataire Azure Active Directory (Azure AD) d’une organisation, par le biais du portail Azure.

Nous vous recommandons vivement d’ajouter votre contact international chargé de la confidentialité et la déclaration de confidentialité de votre organisation, pour que vos employés internes et invités externes puissent consulter vos stratégies. Étant donné que les déclarations de confidentialité sont particulièrement créées et adaptées à chaque entreprise, nous vous recommandons vivement de contacter un conseil juridique à des fins d’assistance.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Ajouter vos informations de confidentialité sur Azure AD
Vous ajoutez les informations de confidentialité de votre organisation dans la zone **Propriétés** d’Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Accéder à la zone Propriétés et ajouter vos informations de confidentialité

1.  Connectez-vous au portail Azure en tant qu’administrateur de locataires.

2.  Dans la barre de navigation gauche, sélectionnez **Azure Active Directory**, puis sélectionnez **Propriétés**.

    La zone **Propriétés** s’affiche.

    ![Zone Propriétés Azure AD avec la zone d’informations de confidentialité mise en exergue](media/active-directory-properties-area/properties-area.png)

3.  Ajoutez vos informations de confidentialité pour vos employés :

    - **Contact technique.** Tapez l’adresse e-mail de la personne à contacter pour obtenir un support technique au sein de votre organisation.
    
    - **Contact international chargé de la confidentialité.** Tapez l’adresse e-mail de la personne à contacter pour toute question concernant la confidentialité des données personnelles. Cette personne est également la personne que Microsoft contacte en cas de fuite de données. Si aucune personne n’est répertoriée ici, Microsoft contacte vos administrateurs généraux.

    - **URL de la déclaration de confidentialité.** Tapez le lien vers le document de votre organisation qui décrit la façon dont elle gère la confidentialité des données des utilisateurs internes et invités externes.

        >[!Important]
        >Si vous n’incluez pas votre propre déclaration de confidentialité ou contact chargé de la confidentialité, vos invités externes voient le texte inclus dans la zone **Révision des autorisations**, à savoir **<_nom de votre organisation_> n’a fourni aucun lien vers ses conditions pour vous permettre de les examiner**. Par exemple, un utilisateur invité voit ce message quand il reçoit une invitation à accéder à une organisation par le biais d’une B2B.

        ![Zone Révision des autorisations B2B Collaboration avec message](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
- [Utilisation d’invitations Azure Active Directory B2B Collaboration](https://aka.ms/b2bredemption)
- [Ajouter ou modifier les informations de profil pour un utilisateur dans Azure Active Directory](active-directory-users-profile-azure-portal.md)