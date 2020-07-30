---
title: Que sont les révisions d’accès ? - Azure Active Directory | Microsoft Docs
description: Avec les révisions d’accès Azure Active Directory, vous pouvez contrôler que l’appartenance à des groupes et l’accès aux applications répondent aux initiatives de gouvernance, de gestion des risques et de conformité de votre organisation.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88d795d0e05c62f07ff415364ced651ad8f4bc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87034610"
---
# <a name="what-are-azure-ad-access-reviews"></a>Présentation des révisions d’accès Azure AD

Les révisions d’accès Azure Active Directory (Azure AD) permettent aux organisations de gérer efficacement les appartenances à des groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les personnes appropriées continuent de bénéficier d’un accès.

Voici une vidéo qui donne une vue d’ensemble rapide des révisions d’accès :

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Pourquoi les révisions d’accès sont-elles importantes ?

Azure AD vous permet de collaborer en interne au sein de votre organisation et avec les utilisateurs d’organisations externes, comme des partenaires. Les utilisateurs peuvent se joindre à des groupes, inviter des personnes, se connecter à des applications cloud et travailler à distance à partir de leurs appareils personnels ou professionnels. L’intérêt de bénéficier de la puissance du libre-service a conduit à la nécessité de meilleures fonctionnalités de gestion des accès.

- Quand de nouveaux employés arrivent, comment vérifier qu’ils disposent des bons accès leur permettant d’être productifs ?
- Quand des personnes changent d’équipe ou quittent l’entreprise, comment vérifier que leur ancien accès est bien supprimé, en particulier quand il implique des invités ?
- Des droits d’accès excessifs peuvent amener à auditer les résultats et les compromissions, car ils signalent un manque de contrôle des accès.
- Vous devez inviter de façon proactive les propriétaires des ressources à vérifier régulièrement les utilisateurs qui ont accès à leurs ressources.

## <a name="when-to-use-access-reviews"></a>Quand utiliser les révisions d’accès ?

- **Trop d’utilisateurs dans des rôles privilégiés :** Il est judicieux de vérifier combien d’utilisateurs ont un accès d’administration, combien d’entre eux ont le rôle d’administrateur général, et s’il existe des invités ou des partenaires qui n’ont pas été supprimés après qu’une tâche d’administration leur a été attribuée. Vous pouvez recertifier les utilisateurs ayant fait l’objet d’une attribution de rôle dans les [rôles Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), comme les administrateurs généraux, ou [les rôles de ressources Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), comme les administrateur de l’accès utilisateur dans l’expérience [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quand l’automatisation est irréalisable :** Vous pouvez créer des règles pour l’appartenance dynamique sur les groupes de sécurité ou les groupes Office 365, mais que se passe-t-il si les données des ressources humaines ne se trouvent pas dans Azure AD, ou si des utilisateurs ont toujours besoin d’un accès après avoir quitté le groupe pour former les personnes qui les remplacent ? Vous pouvez alors créer une révision sur ce groupe pour que ceux qui ont encore besoin d’un accès puissent encore en bénéficier.
- **Quand un groupe est utilisé pour un nouvel objectif :** Si vous avez un groupe qui va être synchronisé avec Azure AD, ou si vous prévoyez d’activer l’application Salesforce pour tout le monde dans le groupe de l’équipe commerciale, il est utile de demander au propriétaire du groupe de passer en revue l’appartenance au groupe avant d’utiliser le groupe dans un autre contenu à risques.
- **Accès aux données critiques de l’entreprise :** pour certaines ressources, il peut être nécessaire à des fins d’audit de demander aux personnes en dehors du département informatique de se déconnecter régulièrement et de justifier la raison pour laquelle ils ont besoin d’un accès.
- **Pour gérer la liste d’exceptions d’une stratégie :** Dans l’idéal, tous les utilisateurs doivent respecter les stratégies d’accès pour sécuriser l’accès aux ressources de votre organisation. Toutefois, certains scénarios métiers nécessitent que vous fassiez des exceptions. En tant qu’administrateur informatique, vous pouvez gérer cette tâche, éviter d’oublier les exceptions à la stratégie et fournir aux auditeurs la preuve que ces exceptions sont révisées régulièrement.
- **Demander aux propriétaires de groupe de confirmer qu’ils ont encore besoin d’invités dans leurs groupes :** L’accès des employés peut être automatisé avec certains systèmes IAM locaux, mais pas l’accès des invités. Si un groupe donne à des invités l’accès à du contenu sensible de l’entreprise, il est de la responsabilité du propriétaire du groupe de confirmer que les invités ont encore un besoin métier légitime de cet accès.
- **Procédez régulièrement à des révisions d’accès :** Vous pouvez configurer des révisions d’accès des utilisateurs récurrentes à des fréquences définies, hebdomadaires, mensuelles, trimestrielles ou annuelles, les réviseurs étant alors informés au début de chaque révision. Les réviseurs peuvent approuver ou refuser l’accès avec une interface conviviale et avec l’aide de recommandations intelligentes.

## <a name="where-do-you-create-reviews"></a>Où créer des révisions ?

Selon ce que vous voulez réviser, vous créez votre révision d’accès dans Révisions d’accès Azure AD, dans Applications d’entreprise Azure AD (en préversion) ou dans Azure AD PIM.

| Droits d’accès des utilisateurs | Les réviseurs peuvent être | Révision créée dans | Expérience des réviseurs |
| --- | --- | --- | --- |
| Membres des groupes de sécurité</br>Membres du groupe Office | Réviseurs spécifiés</br>Propriétaires de groupe</br>Révision indépendante | Révisions d’accès Azure AD</br>Groupes Azure AD | Panneau d’accès |
| Affecté à une application connectée | Réviseurs spécifiés</br>Révision indépendante | Révisions d’accès Azure AD</br>Applications d’entreprise Azure AD (en préversion) | Panneau d’accès |
| Rôle Azure AD | Réviseurs spécifiés</br>Révision indépendante | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portail Azure |
| Rôle de ressource Azure | Réviseurs spécifiés</br>Révision indépendante | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portail Azure |


## <a name="create-access-reviews"></a>Créer des révisions d’accès

Pour créer des révisions d’accès, effectuez les étapes suivantes :

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer les révisions d'accès et connectez-vous en tant qu'administrateur général ou administrateur d'utilisateurs.

1. Recherchez et sélectionnez **Azure Active Directory**.

      ![Recherche d'Azure Active Directory sur le portail Azure](media/access-reviews-overview/search-azure-active-directory.png)

1. Sélectionnez **Identity Governance**.

1. Dans la page de démarrage, cliquez sur le bouton **Créer une révision d’accès**.

   ![Page de démarrage des révisions d'accès](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Création de la révision d’accès sur un groupe pouvant être affecté au rôle Azure AD
Si vous êtes dans la version la plus récente des révisions d’accès (vos réviseurs sont dirigés vers **Mon accès** par défaut), seul l’administrateur général peut donc créer une révision d’accès sur les groupes assignables par rôle. Toutefois, si vous utilisez une version antérieure des révisions d’accès (vos réviseurs sont dirigés vers le **Volet d’accès** par défaut), l’administrateur général et l’administrateur d’utilisateurs peuvent donc créer une révision d’accès sur des groupes assignables par rôle.  

La nouvelle expérience sera déployée pour tous les clients le 1er août 2020, mais si vous souhaitez procéder à une mise à niveau plus tôt, veuillez effectuer une demande ici [Révisions d’accès Azure AD – Expérience du réviseur mise à jour dans Inscription à Mon Accès](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

[En savoir plus sur l’affectation de groupes aux rôles Azure AD](https://go.microsoft.com/fwlink/?linkid=2103037).

## <a name="learn-about-access-reviews"></a>En savoir plus sur les révisions d’accès

Pour en savoir plus sur la création et la réalisation des révisions d’accès, regardez cette courte démonstration :

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Si vous êtes prêt à déployer des révisions d’accès dans votre organisation, effectuez les étapes décrites dans la vidéo pour effectuer l’intégration, former vos administrateurs et créer votre première révision d’accès !

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>De combien de licences avez-vous besoin ?

Vérifiez que votre répertoire comporte au moins autant de licences Azure AD Premium P2 que vous avez d’employés effectuant les tâches suivantes :

- Utilisateurs membres et utilisateurs invités auxquels le rôle de réviseur a été attribué
- Utilisateurs membres et utilisateurs invités qui effectuent une auto-révision
- Propriétaires de groupe qui effectuent une révision d’accès
- Propriétaires d’applications qui effectuent une révision d’accès

Les licences Azure AD Premium P2 ne sont **pas** nécessaires pour les tâches suivantes :

- Aucune licence n’est nécessaire pour les utilisateurs disposant des rôles Administrateur général ou Administrateur d’utilisateurs qui configurent des révisions d’accès et des paramètres, ou qui appliquent les décisions prises à partir des révisions.

Pour chaque licence Azure AD Premium P2 que vous attribuez à un utilisateur de votre organisation, vous pouvez utiliser Azure AD B2B pour inviter jusqu’à cinq utilisateurs dans le cadre d’une allocation d’utilisateur externe. Ces utilisateurs invités peuvent également utiliser les fonctionnalités d’Azure AD Premium P2. Pour plus d’informations, consultez les conseils sur l’[affectation de licences Azure AD B2B Collaboration](../b2b/licensing-guidance.md).

Pour plus d’informations sur les licences, consultez [Attribuer ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemples de scénarios de licence

Voici quelques exemples de scénarios de licence pour vous permettre de déterminer le nombre de licences dont vous devez disposer.

| Scénario | Calcul | Nombre de licences |
| --- | --- | --- |
| Un administrateur crée une révision d’accès du groupe A comportant 75 utilisateurs et 1 propriétaire du groupe, et affecte le propriétaire du groupe en tant que réviseur. | 1 licence pour le propriétaire du groupe en tant que réviseur | 1 |
| Un administrateur crée une révision d’accès du groupe B comportant 500 utilisateurs et 3 propriétaires de groupe, et affecte les 3 propriétaires du groupe en tant que réviseurs. | 3 licences pour chaque propriétaire du groupe en tant que réviseurs | 3 |
| Un administrateur crée une révision d’accès du groupe B comportant 500 utilisateurs. Effectue une révision indépendante. | 500 licences pour chaque utilisateur en tant qu’auto-réviseurs | 500 |
| Un administrateur crée une révision d’accès du groupe C comportant 50 utilisateurs membres et 25 utilisateurs invités. Effectue une révision indépendante. | 50 licences pour chaque utilisateur en tant qu’auto-réviseurs.<br/>(les utilisateurs invités sont traités dans le rapport 1 à 5 requis) | 50 |
| Un administrateur crée une révision d’accès du groupe D comportant 6 utilisateurs membres et 108 utilisateurs invités. Effectue une révision indépendante. | 6 licences pour chaque utilisateur en tant qu’auto-réviseurs + 16 licences supplémentaires pour couvrir l’ensemble des 108 utilisateurs invités dans le ratio 1:5 exigé. 6 licences, qui couvrent 6\*5 = 30 utilisateurs invités. Pour les (108-6\*5) = 78 utilisateurs invités restants, 78/5 = 16 licences supplémentaires sont nécessaires. Ainsi, au total, 6 + 16 = 22 licences sont requises. | 22 |

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès de groupes ou d’applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
- [Réviser l’accès à des groupes ou à des applications](perform-access-review.md)
- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
