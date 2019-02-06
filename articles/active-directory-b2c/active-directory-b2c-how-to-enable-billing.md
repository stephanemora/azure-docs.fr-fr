---
title: Guide pratique pour lier un abonnement Azure - Azure Active Directory B2C | Microsoft Docs
description: Guide détaillé pour activer la facturation pour un client Azure AD B2C dans un abonnement Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c914b3a3ab40971cf9318cafc787d358dab2faff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196172"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Lier un abonnement Azure à un locataire Azure Active Directory B2C

> [!IMPORTANT]
> Pour obtenir les informations les plus récentes sur les prix et la facturation de l’utilisation d’Azure Active Directory (Azure AD) B2C, consultez [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Les frais d’utilisation pour Azure AD B2C sont facturés à un abonnement Azure. Quand un locataire Azure AD B2C est créé, l’administrateur du locataire doit lier explicitement le locataire Azure AD B2C à un abonnement Azure. Cet article vous montre comment procéder.

> [!NOTE]
> Un abonnement lié à un locataire Azure AD B2C peut être utilisé pour la facturation de l’utilisation d’Azure AD B2C ou d’autres ressources Azure, y compris des ressources Azure AD B2C supplémentaires.  Il ne peut pas être utilisé pour ajouter d’autres services Azure basés sur une licence ou des licences Office 365 au sein du locataire Azure AD B2C.

Le lien de l’abonnement est obtenu en créant une « ressource » Azure AD B2C dans l’abonnement Azure cible. De nombreuses « ressources » Azure AD B2C peuvent être créées dans à un abonnement Azure, ainsi que d’autres ressources Azure (par exemple, machines virtuelles, stockage de données, LogicApps). Vous pouvez voir toutes les ressources au sein de l’abonnement en accédant au locataire Azure AD auquel l’abonnement est associé.

Les abonnements aux fournisseurs de solutions Cloud Azure sont pris en charge dans Azure AD B2C. La fonctionnalité est disponible à l’aide des API ou du portail Azure pour Azure AD B2C et pour toutes les ressources Azure. Les administrateurs des abonnements aux fournisseurs de solutions cloud peuvent lier, déplacer et supprimer des relations avec Azure AD B2C de la même façon que pour toutes les ressources Azure. La gestion d’Azure AD B2C à l’aide du contrôle d’accès en fonction du rôle n’est pas affectée par l’association entre le locataire Azure AD B2C et un abonnement à un fournisseur de solutions cloud Azure. Le contrôle d’accès en fonction du rôle est obtenu à l’aide de rôles basés sur le locataire, et non de rôles basés sur l’abonnement.

Un abonnement Azure valide est nécessaire pour continuer.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

Vous devez d’abord [créer un locataire Azure AD B2C](active-directory-b2c-get-started.md) auquel vous souhaitez lier un abonnement. Ignorez cette étape si vous avez déjà créé un locataire Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Ouvrir le portail Azure dans le locataire Azure AD qui affiche votre abonnement Azure

Accédez au locataire Azure AD qui affiche votre abonnement Azure. Ouvrez le [portail Azure](https://portal.azure.com), puis basculez vers le locataire Azure AD affichant l’abonnement Azure que vous souhaitez utiliser.

![Basculement vers votre locataire Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Rechercher Azure AD B2C dans la Place de marché Microsoft Azure

Cliquez sur le bouton **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, entrez `B2C`.

![Ajouter le bouton en surbrillance et le texte Azure AD B2C dans le champ Rechercher dans le marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Dans la liste des résultats, cliquez sur **Azure AD B2C**.

![Azure AD B2C sélectionné dans la liste des résultats](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Des informations détaillées sur Azure AD B2C sont affichées. Pour commencer la configuration de votre nouveau client Azure Active Directory B2C, cliquez sur le bouton **Créer**.

Dans l’écran de création de ressources, sélectionnez **Lier un locataire Azure AD B2C existant à mon abonnement Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Créer une ressource Azure AD B2C au sein de l’abonnement Azure

Dans la boîte de dialogue de création de ressources, sélectionnez un locataire Azure AD B2C dans la liste déroulante. Apparaissent tous les locataires dont vous êtes administrateur général et ceux qui ne sont pas déjà liés à un abonnement.

Le nom de la ressource Azure AD B2C est présélectionné et correspond au nom de domaine du locataire Azure AD B2C.

Pour Abonnement, sélectionnez un abonnement Azure actif dont vous êtes administrateur.

Sélectionnez un groupe de ressources et un emplacement de groupe de ressources. La sélection effectuée ici n’a aucun impact sur l’emplacement, les performances ou l’état de facturation du locataire Azure AD B2C.

![Créer une ressource B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gérer vos ressources de locataire Azure AD B2C

Une fois que vous avez correctement créé une ressource Azure AD B2C dans l’abonnement Azure, une nouvelle ressource du type « Locataire B2C » doit apparaître aux côtés de vos autres ressources Azure.

Vous pouvez utiliser cette ressource pour :

- accéder à l’abonnement pour afficher les informations de facturation ;
- accéder à votre locataire Azure AD B2C ;
- envoyer une demande de support ;
- déplacer votre ressource de locataire Azure AD B2C vers un autre abonnement Azure ou un autre groupe de ressources.

![Paramètres de ressource B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problèmes connus

### <a name="self-imposed-restrictions"></a>Restrictions imposées automatiquement

Un utilisateur peut avoir établi une restriction régionale pour la création de ressources Azure. Cette restriction peut empêcher la création de ressources Azure AD B2C. Pour atténuer ce problème, assouplissez cette restriction.

## <a name="next-steps"></a>Étapes suivantes

Une fois ces étapes effectuées pour chacun de vos locataires Azure AD B2C, votre abonnement Azure est facturé conformément aux détails de votre contrat Azure Direct ou Enterprise.

Vous pouvez consulter les détails de l’utilisation et de la facturation dans l’abonnement Azure sélectionné. Vous pouvez également consulter les rapports détaillés d’utilisation quotidienne à l’aide de [l’API de création de rapports d’utilisation](active-directory-b2c-reference-usage-reporting-api.md).
