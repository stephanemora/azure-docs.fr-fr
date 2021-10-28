---
title: Utilisation d’Azure Policy pour appliquer l’authentification Azure Active Directory uniquement
description: Cet article explique comment appliquer l’authentification Azure AD (Azure Active Directory) uniquement avec Azure SQL Database et Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 6ad1e2819a2bd71d00943e4df11b50eb2f0ceb18
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256142"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Utilisation d’Azure Policy pour appliquer l’authentification Azure Active Directory uniquement avec Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> L’**authentification Azure AD uniquement** et la fonctionnalité Azure Policy associée présentée dans cet article sont en **préversion publique**. 

Cet article explique comment créer une stratégie Azure qui applique l’authentification Azure AD uniquement lorsque les utilisateurs créent une instance managée Azure SQL ou un [serveur logique](logical-servers.md) pour Azure SQL Database. Pour en savoir plus sur l’authentification Azure AD uniquement lors de la création de ressources, consultez [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> - Créer une stratégie Azure qui applique la création d’un serveur logique ou d’une instance managée avec l’[authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md) activée.
> - Vérifier la conformité Azure Policy.

## <a name="prerequisite"></a>Prérequis

- Disposer des autorisations nécessaires pour gérer Azure Policy Pour plus d’informations, consultez [Autorisations Azure RBAC dans Azure Policy](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="create-an-azure-policy"></a>Créer une stratégie Azure

Commencez par créer une stratégie Azure appliquant le provisionnement SQL Database ou Managed Instance avec l’authentification Azure AD uniquement activée.

1. Accédez au [portail Azure](https://portal.azure.com).
1. Recherchez le service **Stratégie**.
1. Sous les paramètres Création, sélectionnez **Définitions**.
1. Dans la zone **Rechercher**, recherchez *Authentification Azure Active Directory uniquement*.

   Deux stratégies intégrées sont disponibles pour appliquer l’authentification Azure AD uniquement. L’une concerne SQL Database, tandis que l’autre concerne Managed Instance.

   - Azure SQL Database doit avoir uniquement l’authentification Azure Active Directory activée
   - Azure SQL Managed Instance doit avoir uniquement l’authentification Azure Active Directory activée

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Capture d’écran d’Azure Policy pour l’authentification Azure AD uniquement":::

1. Sélectionnez le nom de stratégie pour votre service. Dans cet exemple, nous utiliserons Azure SQL Database. Sélectionnez **Azure SQL Database doit avoir uniquement l’authentification Azure Active Directory activée**.
1. Sélectionnez **Attribuer** dans le nouveau menu.

   > [!NOTE]
   > Le script JSON dans le menu affiche la définition de stratégie intégrée qui peut être utilisée comme modèle pour créer une stratégie Azure personnalisée pour SQL Database. La valeur par défaut est `Audit`.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran de l’affectation de stratégie Azure pour l’authentification Azure AD uniquement":::

1. Sous l’onglet **De base**, ajoutez une **Étendue** à l’aide du sélecteur ( **...** ) situé sur le côté.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran de la sélection de l’étendue de stratégie Azure pour l’authentification Azure AD uniquement":::

1. Dans le volet **Étendue**, sélectionnez votre **Abonnement** dans le menu déroulant, puis sélectionnez un **Groupe de ressources** pour cette stratégie. Une fois que vous avez terminé, utilisez le bouton **Sélectionner** pour enregistrer la sélection.

   > [!NOTE]
   > Si vous ne sélectionnez pas de groupe de ressources, la stratégie s’applique à tout l’abonnement.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran de l’ajout de l’étendue de stratégie Azure pour l’authentification Azure AD uniquement":::

1. De retour sous l’onglet **De base**, personnalisez le **Nom de l’affectation** et indiquez éventuellement une **Description**. Vérifiez que l’**Application de la stratégie** est **Activée**.
1. Accédez à l’onglet **Paramètres**. Désélectionnez l’option **Afficher uniquement les paramètres qui nécessitent une entrée**.
1. Sous **Effet**, sélectionnez **Refuser**. Ce paramètre empêche la création d’un serveur logique sans que l’authentification Azure AD uniquement soit activée.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran du paramètre d’effet de stratégie Azure pour l’authentification Azure AD uniquement":::

1. Sous l’onglet **Messages de non-conformité**, vous pouvez personnaliser le message de stratégie qui s’affiche si une violation de la stratégie s’est produite. Le message indique aux utilisateurs quelle stratégie a été appliquée lors de la création du serveur.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran du message de non-conformité de stratégie Azure pour l’authentification Azure AD uniquement":::

1. Sélectionnez **Revoir + créer**. Passez en revue la stratégie, puis sélectionnez le bouton **Créer**.

> [!NOTE]
> L’application de la nouvelle stratégie peut prendre un certain temps.

## <a name="check-policy-compliance"></a>Vérifier la conformité de la stratégie

Vous pouvez vérifier le paramètre de **Conformité** sous le service **Stratégie** pour voir l’état de conformité.

Recherchez le nom de l’affectation que vous avez donné à la stratégie.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Capture d’écran de la conformité Azure Policy pour l’authentification Azure AD uniquement":::

Une fois le serveur logique créé avec l’authentification Azure AD uniquement, le rapport de stratégie augmente le compteur sous le visuel **Ressources par état de conformité**. Vous pourrez voir quelles ressources sont conformes ou non conformes.

Si le groupe de ressources que la stratégie doit couvrir contient des serveurs déjà créés, le rapport de stratégie indique les ressources qui sont conformes et non conformes.

> [!NOTE]
> La mise à jour du rapport de conformité peut prendre un certain temps. Les modifications relatives à la création de ressources ou aux paramètres d’authentification Azure AD uniquement ne sont pas signalées immédiatement.    

## <a name="provision-a-server"></a>Provisionner un serveur

Vous pouvez ensuite essayer de provisionner un serveur logique ou une instance managée dans le groupe de ressources auquel vous avez attribué la stratégie Azure. Si l’authentification Azure AD uniquement est activée lors de la création du serveur, le provisionnement réussit. Lorsque l’authentification Azure AD uniquement n’est pas activée, la configuration échoue.

Pour plus d’informations, consultez [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

## <a name="next-steps"></a>Étapes suivantes

- Vue d’ensemble d’[Azure Policy pour l’authentification Azure AD uniquement](authentication-azure-ad-only-authentication-policy.md)
- [Créer un serveur avec l’authentification Azure AD uniquement activée dans Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
- Vue d’ensemble de l’[Authentification Azure AD uniquement](authentication-azure-ad-only-authentication.md)