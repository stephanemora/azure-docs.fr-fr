---
title: Pratiques de sécurité recommandées
description: Lorsque vous utilisez Azure Lighthouse, il est important de prendre en compte la sécurité et le contrôle d’accès.
ms.date: 07/06/2020
ms.topic: conceptual
ms.openlocfilehash: 2c620feabf5bcedc35a36104c3ba305ac9337ff0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105419"
---
# <a name="recommended-security-practices"></a>Pratiques de sécurité recommandées

Lorsque vous utilisez [Azure Lighthouse](../overview.md), il est important de prendre en compte la sécurité et le contrôle d’accès. Les utilisateurs de votre locataire ayant un accès direct aux abonnements et aux groupes de ressources du client, il est souhaitable de prendre des mesures pour maintenir la sécurité de votre locataire. Vous devez également vous assurer que vous autorisez uniquement l’accès nécessaire pour gérer efficacement les ressources de vos clients. Cette rubrique fournit des recommandations pour vous y aider.

## <a name="require-azure-multi-factor-authentication"></a>Exiger l’utilisation d’Azure Multi-Factor Authentication

La solution [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (également appelée vérification en deux étapes) aide à empêcher des attaquants d’accéder à un compte en exigeant plusieurs étapes d’authentification. Vous devez exiger l’application de la solution Microsoft Azure Multi-Factor Authentication à tous les utilisateurs de votre locataire de fournisseur de services, notamment aux utilisateurs qui auront accès aux ressources du client.

Nous vous suggérons de demander à vos clients d’implémenter également Microsoft Azure Multi-Factor Authentication dans leurs locataires.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Affecter des autorisations à des groupes selon le principe de privilège minimum

Pour faciliter la gestion, nous vous recommandons d’utiliser des groupes d’utilisateurs Azure AD pour chaque rôle requis pour gérer les ressources de vos clients. Cela vous permet d’ajouter ou de supprimer un utilisateur individuel dans un groupe en fonction des besoins, plutôt que d’attribuer directement des autorisations à cet utilisateur.

> [!IMPORTANT]
> Pour que vous puissiez ajouter des autorisations pour un groupe Azure AD, le **Type de groupe** doit être **Sécurité** et non **Office 365**. Cette option est sélectionnée lors de la création du groupe. Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Lors de la création de votre structure d’autorisation, veillez à suivre le principe du privilège minimum afin que les utilisateurs disposent uniquement des autorisations nécessaires pour accomplir leur travail, ce qui contribue à réduire le risque d’erreurs accidentelles.

Par exemple, vous pouvez utiliser une structure telle que celle-ci :

|Nom du groupe  |Type  |principalId  |Définition de rôle  |ID de définition de rôle  |
|---------|---------|---------|---------|---------|
|Architectes     |Groupe d'utilisateurs         |\<principalId\>         |Contributeur         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Évaluation     |Groupe d'utilisateurs         |\<principalId\>         |Lecteur         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Spécialistes des machines virtuelles     |Groupe d'utilisateurs         |\<principalId\>         |Contributeur de machine virtuelle         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatisation     |Nom du principal du service (SPN)         |\<principalId\>         |Contributeur         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Une fois que vous avez créé ces groupes, vous pouvez affecter des utilisateurs en fonction des besoins. Ajoutez uniquement les utilisateurs qui ont vraiment besoin d’un accès. Veillez à vérifier régulièrement l’appartenance aux groupes et à supprimer tous les utilisateurs dont l’inclusion n’est plus appropriée ou nécessaire.

N’oubliez pas que, lorsque vous [intégrez des clients via une offre de service managé public](../how-to/publish-managed-services-offers.md), tout groupe (ou utilisateur ou principal de service) que vous incluez aura les mêmes autorisations pour chaque client achetant le plan. Pour affecter différents groupes afin de collaborer avec chaque client, vous devez publier un plan privé distinct exclusif pour chaque client, ou intégrer des clients individuellement à l’aide de modèles Azure Resource Manager. Par exemple, vous pouvez publier un plan public disposant d’un accès très limité, puis travailler directement avec le client pour intégrer ses ressources afin d’obtenir un accès supplémentaire à l’aide d’un modèle de ressource Azure personnalisé qui accorde un accès supplémentaire en fonction des besoins.


## <a name="next-steps"></a>Étapes suivantes

- [Déployer Microsoft Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Découvrez les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
