---
title: Créer un plan de sécurité pour l’accès externe à Azure Active Directory
description: Planifiez la sécurité pour un accès externe aux ressources de votre organisation.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554018"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. Créer un plan de sécurité pour l’accès externe 

Maintenant que vous avez [déterminé la position de sécurité souhaitée pour l’accès externe](1-secure-access-posture.md) et [découvert votre état de collaboration actuel](2-secure-access-current-state.md), vous pouvez créer un plan de sécurité et de gouvernance pour les utilisateurs externes. 

Ce plan doit documenter les éléments suivants :

* Les applications et autres ressources qui doivent être regroupées pour l’accès.

* Les conditions de connexion appropriées pour les utilisateurs externes. Celles-ci peuvent inclure l’état de l’appareil, l’emplacement de connexion, les exigences de l’application cliente et le risque de l’utilisateur.

* Des stratégies d’entreprise pour déterminer quand réviser et supprimer les accès. 

* Populations d’utilisateurs à regrouper et traiter de la même façon.

Une fois ces points documentés, vous pouvez utiliser des stratégies de gestion des identités et des accès de Microsoft ou de tout autre fournisseur d’identité (IdP) pour implémenter ce plan.

## <a name="document-resources-to-be-grouped-for-access"></a>Documenter les ressources à regrouper pour l’accès

Il existe plusieurs façons de regrouper les ressources pour l’accès. 

* Microsoft Teams regroupe des fichiers, fils de conversation et autres ressources dans un même emplacement. Vous devez formuler une stratégie d’accès externe pour Microsoft Teams. Consultez [Sécuriser l’accès à Teams, SharePoint et OneDrive](9-secure-access-teams-sharepoint.md).

* Les packages d’accès à la gestion des droits d’utilisation permettent de créer un package unique d’applications et d’autres ressources auxquelles vous pouvez accorder l’accès. 

* Les stratégies d’accès conditionnel peuvent être appliquées à jusqu’à 250 applications avec les mêmes exigences d’accès.

Peu importe la façon dont vous allez gérer l’accès, vous devez documenter les applications qui doivent être regroupées. Les éléments à prendre en considération sont les suivants :

* **Profil de risque**. Quel est le risque pour votre entreprise si une personne malveillante obtenait l’accès à une application ? Envisagez de coder chaque application comme étant à risque élevé, moyen ou faible. Faites preuve de prudence lorsque vous regroupez des applications à haut risque avec des applications à faible risque. 

   * Documentez aussi les applications qui ne doivent jamais être partagées avec des utilisateurs externes.

* **Cadres de conformité**. Que se passe-t-il si une application doit répondre à des cadres de conformité ? Quelles sont les exigences en matière d’accès et de révision ?

* **Applications pour des rôles de travail ou services spécifiques**. Y a-t-il des applications qui doivent être regroupées, car tous les utilisateurs d’un service ou d’un rôle de travail spécifique auront besoin d’un accès ?

* **Applications axées sur la collaboration**. À quelles applications axées sur la collaboration les utilisateurs externes doivent-ils pouvoir accéder ? Microsoft Teams et SharePoint devront peut-être être accessibles. Pour les applications de productivité dans Office 365, telles que Word et Excel, les utilisateurs externes apportent-ils leurs propres licences, ou devrez-vous leur en accorder une et leur fournir un accès ?

Pour chaque groupe d’applications et de ressources que vous souhaitez rendre accessibles aux utilisateurs externes, documentez les éléments suivants :

* Nom descriptif du groupe, par exemple *Accès_Externe_Haut_Risque_Finance*. 

* Liste complète de toutes les applications et ressources du groupe.

* Propriétaires d’applications et de ressources et informations de contact.

* Si l’accès est contrôlé par le service informatique ou délégué au propriétaire de l’entreprise.

* Toute condition requise pour l’accès, par exemple une vérification d’antécédents ou une formation.

* Toutes les exigences de conformité pour accéder aux ressources.

* Tout autre obstacle supplémentaire, par exemple exiger l’authentification multifacteur pour des ressources spécifiques.

* La fréquence à laquelle l’accès sera révisé, par qui et à quel endroit cela sera documenté.

Ce type de plan de gouvernance peut et doit également être complété pour l’accès interne.

## <a name="document-sign-in-conditions-for-external-users"></a>Documenter les conditions de connexion pour les utilisateurs externes.

Dans le cadre de votre plan, vous devez déterminer les exigences de connexion de vos utilisateurs externes au fur et à mesure qu’ils accèdent aux ressources. Les exigences de connexion sont souvent basées sur le profil de risque des ressources et l’évaluation des risques de la connexion des utilisateurs.

Les conditions de connexion sont configurées dans l’[Accès conditionnel Azure AD](../conditional-access/overview.md) et sont constituées d’une condition et d’un résultat. Par exemple, quand exiger l’authentification multifacteur

**Conditions de connexion basées sur les risques de ressources.**

| Profil de risque d’application| Tenez compte des stratégies suivantes pour le déclenchement de l’authentification multifacteur |
| - |-|
| Faible risque| Exiger MFA pour des ensembles d’applications spécifiques |
| Risque moyen| Exiger l’authentification multifacteur lorsque d’autres risques sont présents |
| Risque élevé| Toujours exiger l’authentification multifacteur pour les utilisateurs externes |


Aujourd’hui, vous pouvez [appliquer l’authentification multifacteur pour les utilisateurs B2B dans votre locataire](../external-identities/b2b-tutorial-require-mfa.md). 

**Conditions de connexion basées sur l’utilisateur et l’appareil**.

| Risque de connexion ou d’utilisateur| Tenez compte de ces stratégies |
| - | - |
| Appareil| Exiger un appareil conforme |
| Applications mobiles| Exiger des applications approuvées |
| La protection d’identité présente un risque élevé| Demander à l’utilisateur de modifier le mot de passe |
| Emplacement réseau| Exiger la connexion à partir d’une plage d’adresses IP spécifique pour les projets hautement confidentiels |

Aujourd’hui, pour utiliser l’état de l’appareil en tant qu’entrée d’une stratégie, l’appareil doit être inscrit ou joint à votre locataire. 

Les [stratégies basées sur les risques Identity Protection](../conditional-access/howto-conditional-access-policy-risk.md) peuvent être utilisées. Toutefois, les problèmes doivent être atténués dans le locataire de base de l’utilisateur.

Pour les [emplacements réseau](../conditional-access/howto-conditional-access-policy-location.md), vous pouvez restreindre l’accès à toutes les plages d’adresses IP que vous possédez. Vous pouvez utiliser cela si vous souhaitez que les partenaires externes accèdent à une application quand ils sont sur le site de votre organisation.

[Apprenez-en davantage sur les stratégies d’accès conditionnel](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Documenter les stratégies de révision d’accès

Documentez vos stratégies d’entreprise lorsque vous devez passer en revue l’accès aux ressources et lorsque vous devez supprimer l’accès au compte pour les utilisateurs externes. Les entrées de ces décisions peuvent inclure :

* Exigences détaillées dans les cadres de conformité.

* Processus et stratégies métier internes

* Comportement de l’utilisateur

Si vos stratégies sont hautement personnalisées pour vos besoins, tenez compte des points suivants :

* **Révisions de l’accès à la gestion des droits d’utilisation**. Utilisez la fonctionnalité de gestion des droits d’utilisation pour

   * [Faire expirer automatiquement les packages d’accès](../governance/entitlement-management-access-package-lifecycle-policy.md), ainsi que l’accès des utilisateurs externes aux ressources incluses.

   * Définissez une [fréquence de révision requise](../governance/entitlement-management-access-reviews-create.md) pour les révisions d’accès.

   * Si vous utilisez des [organisations connectées](../governance/entitlement-management-organization.md) pour regrouper tous les utilisateurs d’un même partenaire, planifiez des révisions régulières avec le propriétaire de l’entreprise et le représentant du partenaire.

* **Groupes Microsoft 365**. Définissez une [stratégie d’expiration de groupe](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) pour les groupes Microsoft 365 auxquels les utilisateurs externes sont invités. 

* **Autres options**. Si les utilisateurs externes ont un accès extérieur à des packages d’accès de la gestion des droits d’utilisation ou à des groupes Microsoft 365, configurez le processus d’entreprise pour déterminer quand les comptes doivent être rendus inactifs ou supprimés. Par exemple :

   * Supprimer la capacité de connexion pour un compte qui ne s’est pas connecté depuis 90 jours.

   * Évaluez les besoins d’accès et prenez des mesures à la fin de chaque projet avec des utilisateurs externes.

 

## <a name="determine-your-access-control-methods"></a>Déterminer vos méthodes de contrôle d’accès

Maintenant que vous savez ce à quoi vous souhaitez contrôler l’accès, comment ces ressources doivent être regroupées pour l’accès courant et quelles sont les stratégies de vérification d’accès et de connexion requises, vous pouvez décider de la façon d’accomplir votre plan. 

Certaines fonctionnalités, comme la [Gestion des droits d’utilisation](../governance/entitlement-management-overview.md), sont uniquement disponibles avec une licence Azure AD Premium 2 (P2). Les licences Microsoft 365 E5 et Office 365 E5 incluent des licences Azure AD P2. 

D’autres combinaisons de Microsoft 365, Office 365 et Azure AD activent également certaines fonctionnalités de gestion des utilisateurs externes. Pour plus d’informations, consultez [Protection des informations](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance).

> [!NOTE]
> Les licences sont par utilisateur. Par conséquent, vous pouvez avoir des utilisateurs spécifiques, y compris des administrateurs et responsables d’entreprise, qui délèguent le contrôle d’accès au niveau Azure AD P2 ou Microsoft 365 E5 sans activer ces licences pour tous les utilisateurs. Vos 50 000 premiers utilisateurs externes sont gratuits. Si vous n’activez pas les licences P2 pour vos autres utilisateurs internes, ils ne seront pas en mesure d’utiliser les fonctionnalités de gestion des droits d’utilisation, comme les packages d’accès. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Régir l’accès avec Azure AD P2 et Microsoft/Office 365 E5
Azure AD P2 et Microsoft 365 E5 disposent de la suite complète d’outils de sécurité et de gouvernance.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Approvisionnement, connexion, révision de l’accès et annulation de l’approvisionnement. Les entrées en gras sont les méthodes préférées

| Fonctionnalité| Approvisionner des utilisateurs externes| Appliquez les exigences de connexion.| Revoir l’accès| Annuler l’approvisionnement de l’accès |
| - | - | - | - | - |
| Azure AD B2B Collaboration| Inviter par e-mail, OTP, libre-service| | **Révision périodique par partenaire**| Supprimer un compte<br>Restreindre la connexion |
| Gestion des droits d’utilisation| **Ajouter un utilisateur par le biais d’une attribution ou d’un accès libre-service**| | Révisions d’accès|**Expiration ou suppression du package d’accès**|
| Groupes Office 365| | | Vérifier les appartenances aux groupes| Expiration ou suppression du groupe<br> Retrait du groupe |
| Groupes de sécurité Azure AD| | **Stratégies d’accès conditionnel** (ajouter des utilisateurs externes aux groupes de sécurité quand cela est nécessaire)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Accès aux ressources. Les entrées en gras sont les méthodes préférées

|Fonctionnalité | Accès aux ressources et à l’application| Accès à SharePoint et OneDrive| Accès à Teams| Sécurité de la messagerie et des documents |
| - |-|-|-|-|
| Gestion des droits d’utilisation| **Ajouter un utilisateur par le biais d’une attribution ou d’un accès libre-service**| **Packages d’accès**| **Packages d’accès**|  |
| Groupe Office 365| | Accès aux sites (et au contenu associé) inclus dans le groupe| Accès aux équipes (et au contenu associé) incluses dans le groupe|  |
| Étiquette de confidentialité| | **Classer et restreindre les accès manuellement et automatiquement**| **Classer et restreindre les accès manuellement et automatiquement**| **Classer et restreindre les accès manuellement et automatiquement** |
| Groupes de sécurité Azure AD| **Stratégies d’accès conditionnel pour l’accès non incluses dans les packages d’accès**| | |  |


### <a name="entitlement-management"></a>Gestion des droits d’utilisation 

Les [packages d’accès à la gestion des droits d’utilisation](../governance/entitlement-management-access-package-create.md) activent l’approvisionnement et l’annulation de l’approvisionnement pour les groupes et équipes, applications et sites SharePoint. Vous pouvez définir quelles organisations connectées ont droit à l’accès, si les demandes en libre-service sont autorisées et quels flux de travail d’approbation sont requis (le cas échéant) pour accorder l’accès. Pour vous assurer que l’accès n’est pas accordé plus longtemps que nécessaire, vous pouvez définir des stratégies d’expiration et des révisions d’accès pour chaque package d’accès. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Régir l’accès avec Azure AD P1 et Microsoft/Office 365 E3
Vous pouvez accomplir une gouvernance robuste avec Azure AD P1 et Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Approvisionnement, connexion, révision de l’accès et annulation de l’approvisionnement


|Fonctionnalité | Approvisionner des utilisateurs externes| Appliquer les exigences de connexion| Revoir l’accès| Annuler l’approvisionnement de l’accès |
| - |-|-|-|-|
| Azure AD B2B Collaboration| **Inviter par e-mail, OTP, libre-service**| Fédération B2B directe| **Révision périodique par partenaire**| Supprimer un compte<br>Restreindre la connexion |
| Groupes Microsoft ou Office 365| | | | Expiration ou suppression du groupe.<br>Retrait du groupe. |
| Groupes de sécurité| | **Ajouter des utilisateurs externes à des groupes de sécurité (organisation, équipe, projet, etc.)**| |  |
| Stratégies d’accès conditionnel| | **Stratégies d’accès conditionnel à la connexion pour les utilisateurs externes**| |  |


 ### <a name="access-to-resources"></a>Accès aux ressources.

|Fonctionnalité | Accès aux ressources et à l’application| Accès à SharePoint et OneDrive| Accès à Teams| Sécurité de la messagerie et des documents |
| - |-|-|-|-|
| Groupes Microsoft ou Office 365| | **Accès aux sites inclus avec le groupe (et au contenu associé)**|**Accès aux équipes incluses avec le groupe Microsoft 365 (et au contenu associé)**|  |
| Étiquette de confidentialité| | Classer et limiter manuellement l’accès| Classez et limitez manuellement l’accès.| Classer manuellement pour limiter et chiffrer |
| Stratégies d’accès conditionnel| Stratégies d’accès conditionnel pour le contrôle d’accès| | |  |
| Autres méthodes| | Limitez l’accès au site SharePoint de façon granulaire avec les groupes de sécurité.<br>Interdisez le partage direct.| **Limiter les invitations externes au sein des équipes**|  |


### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md) (Vous en êtes à cette étape.)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)