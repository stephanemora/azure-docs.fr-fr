---
title: Que sont les révisions d’accès ? - Azure Active Directory | Microsoft Docs
description: À l’aide de révisions d’accès Azure Active Directory, vous pouvez contrôler l’accès de l’appartenance et l’application de groupe pour répondre à la gouvernance, de gestion des risques et d’initiatives de conformité de votre organisation.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1563a023f397999deb5c6abd40843d6a376b0492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351441"
---
# <a name="what-are-azure-ad-access-reviews"></a>Passe en revue ce que sont les accès Azure AD ?

Révisions d’accès Azure Active Directory (Azure AD) permettent aux organisations efficacement gérer les appartenances aux groupes, l’accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les personnes appropriées continuent de bénéficier d’un accès.

Voici une vidéo qui donne une vue d’ensemble rapide des révisions d’accès :

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Pourquoi les révisions d’accès sont-elles importantes ?

Azure AD vous permet de collaborer en interne au sein de votre organisation et avec les utilisateurs d’organisations externes, comme des partenaires. Les utilisateurs peuvent se joindre à des groupes, inviter des personnes, se connecter à des applications cloud et travailler à distance à partir de leurs appareils personnels ou professionnels. L’intérêt de bénéficier de la puissance du libre-service a conduit à la nécessité de meilleures fonctionnalités de gestion des accès.

- Quand de nouveaux employés arrivent, comment vérifier qu’ils disposent des bons accès leur permettant d’être productifs ?
- Quand des personnes changent d’équipe ou quittent l’entreprise, comment vérifier que leur ancien accès est bien supprimé, en particulier quand il implique des invités ?
- Des droits d’accès excessifs peuvent amener à auditer les résultats et les compromissions, car ils signalent un manque de contrôle des accès.
- Vous devez inviter de façon proactive les propriétaires des ressources à vérifier régulièrement les utilisateurs qui ont accès à leurs ressources.

## <a name="when-to-use-access-reviews"></a>Quand utiliser les révisions d’accès ?

- **Trop d’utilisateurs dans des rôles privilégiés :** Il est judicieux pour vérifier combien d’utilisateurs ont un accès administratif, combien d'entre eux sont des administrateurs généraux, et s’il y en a invité invités ou des partenaires qui n’ont pas été supprimés après leur attribution pour effectuer une tâche administrative. Vous pouvez le recertifier les utilisateurs de l’attribution de rôle dans [rôles Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) tels que les administrateurs généraux, ou [les rôles de ressources Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) telles qu’administrateur des accès utilisateur dans le [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) rencontrer.
- **Quand l’automatisation est irréalisable :** Vous pouvez créer des règles pour l’appartenance dynamique sur les groupes de sécurité ou les groupes Office 365, mais que se passe-t-il si les données des ressources humaines ne se trouvent pas dans Azure AD, ou si des utilisateurs ont toujours besoin d’un accès après avoir quitté le groupe pour former les personnes qui les remplacent ? Vous pouvez alors créer une révision sur ce groupe pour que ceux qui ont encore besoin d’un accès puissent encore en bénéficier.
- **Quand un groupe est utilisé pour un nouvel objectif :** Si vous avez un groupe qui va être synchronisé avec Azure AD, ou si vous prévoyez d’activer l’application Salesforce pour tout le monde dans le groupe de l’équipe commerciale, il est utile de demander au propriétaire du groupe de passer en revue l’appartenance au groupe avant d’utiliser le groupe dans un autre contenu à risques.
- **Accès aux données critiques de l’entreprise :** pour certaines ressources, il peut être nécessaire à des fins d’audit de demander aux personnes en dehors du département informatique de se déconnecter régulièrement et de justifier la raison pour laquelle ils ont besoin d’un accès.
- **Pour gérer la liste d’exceptions d’une stratégie :** Dans l’idéal, tous les utilisateurs doivent respecter les stratégies d’accès pour sécuriser l’accès aux ressources de votre organisation. Toutefois, certains scénarios métiers nécessitent que vous fassiez des exceptions. En tant qu’administrateur informatique, vous pouvez gérer cette tâche, éviter d’oublier les exceptions à la stratégie et fournir aux auditeurs la preuve que ces exceptions sont révisées régulièrement.
- **Demander aux propriétaires de groupe de confirmer qu’ils ont encore besoin d’invités dans leurs groupes :** L’accès des employés peut être automatisé avec certains localement IAM, mais pas les invités. Si un groupe donne à des invités l’accès à du contenu sensible de l’entreprise, il est de la responsabilité du propriétaire du groupe de confirmer que les invités ont encore un besoin métier légitime de cet accès.
- **Procédez régulièrement à des révisions d’accès :** Vous pouvez configurer des révisions d’accès des utilisateurs récurrentes à des fréquences définies, hebdomadaires, mensuelles, trimestrielles ou annuelles, les réviseurs étant alors informés au début de chaque révision. Les réviseurs peuvent approuver ou refuser l’accès avec une interface conviviale et avec l’aide de recommandations intelligentes.

## <a name="where-do-you-create-reviews"></a>Où créer des révisions ?

Selon ce que vous souhaitez examiner, vous allez créer votre révision d’accès dans Azure AD accéder aux révisions, les applications d’entreprise Azure AD (en version préliminaire) ou Azure AD PIM.

| Droits d’accès des utilisateurs | Les réviseurs peuvent être | Révision créée dans | Expérience des réviseurs |
| --- | --- | --- | --- |
| Membres des groupes de sécurité</br>Membres du groupe Office | Réviseurs spécifiés</br>Propriétaires de groupe</br>Autorévision | Révisions d’accès Azure AD</br>Groupes Azure AD | Panneau d’accès |
| Affecté à une application connectée | Réviseurs spécifiés</br>Autorévision | Révisions d’accès Azure AD</br>Applications d’entreprise Azure AD (en préversion) | Panneau d’accès |
| Rôle Azure AD | Réviseurs spécifiés</br>Autorévision | Azure AD PIM | Portail Azure |
| Rôle de ressource Azure | Réviseurs spécifiés</br>Autorévision | Azure AD PIM | Portail Azure |

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser les révisions d’accès, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [Guide pratique pour s’inscrire à Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) ou [Essai d’Enterprise Mobility + Security E5](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Bien démarrer avec les révisions d’accès

Pour en savoir plus sur la création et la réalisation des révisions d’accès, regardez cette courte démonstration :

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Si vous êtes prêt à déployer des révisions d’accès dans votre organisation, effectuez les étapes décrites dans la vidéo pour effectuer l’intégration, former vos administrateurs et créer votre première révision d’accès !

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Activer les révisions d’accès

Pour activer les révisions d’accès, effectuez ces étapes.

1. Comme un administrateur général ou un administrateur de l’utilisateur, connectez-vous à la [Azure portal](https://portal.azure.com) où vous souhaitez utiliser l’accès révisions.

1. Cliquez sur **Tous les services** et recherchez le service Révisions d’accès.

1. Cliquez sur **révisions d’accès**.

    ![Tous les services - révisions d’accès](./media/access-reviews-overview/all-services-access-reviews.png)

1. Dans la liste de navigation, cliquez sur **Intégrer** pour ouvrir la page **Intégrer les révisions d’accès**.

    ![Intégrer des révisions d’accès](./media/access-reviews-overview/onboard-button.png)

1. Cliquez sur **Créer** pour activer les révisions d’accès dans l’annuaire actif.

    ![Intégrer les révisions d’accès](./media/access-reviews-overview/onboard-access-reviews.png)

    La prochaine fois que vous démarrez access passe en revue, les options de révision d’accès seront activées.

    ![Révisions d’accès](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès des groupes ou des applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
- [Réviser l’accès à des groupes ou des applications](perform-access-review.md)
- [Effectuer une révision d’accès des groupes ou des applications](complete-access-review.md)
