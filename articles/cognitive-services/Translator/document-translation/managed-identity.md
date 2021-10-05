---
title: Créer et utiliser une identité managée
titleSuffix: Azure Cognitive Services
description: Comprendre comment créer et utiliser une identité managée dans le portail Azure
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 09/09/2021
ms.author: lajanuar
ms.openlocfilehash: 688fd2391d12f74b46a16954706b3c9e0ee1fb8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771823"
---
# <a name="create-and-use-managed-identity"></a>Créer et utiliser une identité managée

> [!IMPORTANT]
>
> L’identité managée pour la traduction de document n’est pas disponible actuellement dans la région globale. Si vous comptez utiliser une identité managée pour des opérations de traduction de document, [créez votre ressource Traducteur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) dans une région Azure non globale.

## <a name="what-is-managed-identity"></a>Qu’est-ce qu’une identité managée ?

 L’identité managée Azure est un principal de service qui crée une identité Azure Active Directory (Azure AD) et des autorisations spécifiques pour les ressources managées Azure. Vous pouvez utiliser une identité managée pour accorder l’accès à une ressource qui prend en charge l’authentification Azure AD. Pour accorder l’accès, attribuez un rôle à une identité managée en utilisant le [contrôle d’accès en fonction du rôle Azure](../../../role-based-access-control/overview.md) (Azure RBAC).  Il n’y a aucun coût supplémentaire pour utiliser l’identité managée dans Azure.

L’identité managée prend en charge les comptes de stockage Blob Azure accessibles en privé et publiquement.  Pour les comptes de stockage **avec accès public**, vous pouvez choisir d’utiliser une signature d’accès partagé (SAS) pour accorder un accès limité.  Cet article explique comment gérer l’accès à des documents de traduction dans votre compte de stockage Blob Azure à l’aide d’une identité managée affectée par le système.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif : si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

* Une ressource [**Traducteur monoservice**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (non une ressource Cognitive Services multiservice) attribuée à une région **non globale**. Pour des instructions détaillées, _consultez_ [Créer une ressource Cognitive Services dans le portail Azure](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Connaissances générales sur le [**contrôle d’accès en fonction du rôle Azure (Azure RBAC)**](../../../role-based-access-control/role-assignments-portal.md) à l’aide du portail Azure.

* Un [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) dans la même région que votre ressource Traducteur. Vous allez créer des conteneurs pour stocker et organiser vos données d’objet blob dans votre compte de stockage. 

* **Si votre compte de stockage se trouve derrière un pare-feu, vous devez activer la configuration suivante** : </br>

  * Dans la page de votre compte de stockage, dans le menu de gauche, sélectionnez **Sécurité + réseau** → **Mise en réseau**.
    :::image type="content" source="../media/managed-identities/security-and-networking-node.png" alt-text="Capture d’écran : onglet Sécurité + réseau.":::

  * Dans la fenêtre principale, sélectionnez **Autoriser l’accès à partir de réseaux sélectionnés**.
  :::image type="content" source="../media/managed-identities/firewalls-and-virtual-networks.png" alt-text="Capture d’écran : case d’option Réseaux sélectionnés activée.":::

  * Dans la page Réseaux sélectionnés, accédez à la catégorie **Exceptions** et assurez-vous que la case à cocher [**Autoriser les services Azure de la liste des services approuvés à accéder à ce compte de stockage**](/azure/storage/common/storage-network-security?tabs=azure-portal#manage-exceptions) est activée.

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Capture d’écran : case à cocher Autoriser les services approuvés, vue du portail":::

## <a name="managed-identity-assignments"></a>Attributions d’identités managées

Il existe deux types d’identités managées : celles qui sont **affectées par le système** et celles qui sont **affectées par l’utilisateur**.  Actuellement, la traduction de document est prise en charge par l’identité managée affectée par le système. Une identité managée affectée par le système est **activée** directement sur une instance de service. Elle n’est pas activée par défaut. Vous devez accéder à votre ressource et mettre à jour le paramètre d’identité. L’identité managée affectée par le système est liée à votre ressource tout au long de son cycle de vie. Si vous supprimez votre ressource, l’identité managée est également supprimée.

Dans les étapes suivantes, nous allons activer une identité managée affectée par le système et accorder à votre ressource Traducteur un accès limité à votre compte de stockage Blob Azure.

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>Activer une identité managée affectée par le système à l’aide du portail Azure

>[!IMPORTANT]
>
> Pour activer une identité managée affectée par le système, vous avez besoin d’autorisations **Microsoft.Authorization/roleAssignments/write**, telles que [**Propriétaire**](../../../role-based-access-control/built-in-roles.md#owner) ou [**Administrateur de l’accès utilisateur**](../../../role-based-access-control/built-in-roles.md#user-access-administrator). Vous pouvez spécifier une étendue à quatre niveaux : groupe d’administration, abonnement, groupe de ressources ou ressource.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à votre abonnement Azure.

1. Accédez à la page de votre ressource **Translator** dans le portail Azure.

1. Dans le rail de gauche, sélectionnez **Identité** dans la liste **Gestion des ressources** :

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="Capture d’écran : onglet identité de gestion des ressources dans le portail Azure.":::

1. Dans la fenêtre principale, basculez le curseur **État affecté par le système** sur **Activé**.

1. Sous **Autorisations**, sélectionnez **Attributions des rôles Azure** :

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Capture d’écran : activer une identité managée affectée par le système dans le portail Azure":::

1. Une page des attributions de rôles Azure s’ouvre. Choisissez votre abonnement dans le menu déroulant, puis sélectionnez **&plus; Ajouter une attribution de rôle**.

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Capture d’écran : page des attributions de rôles Azure dans le portail Azure":::

    >[!NOTE]
    >
    > Si vous ne parvenez pas à attribuer un rôle dans le portail Azure parce que l’option Ajouter > Ajouter une attribution de rôle est désactivée ou que vous recevez l’erreur d’autorisation « Vous ne disposez pas des autorisations nécessaires pour ajouter une attribution de rôle à cette étendue », vérifiez que vous êtes actuellement connecté en tant qu’utilisateur avec un rôle attribué qui dispose des autorisations Microsoft.Authorization/roleAssignments/write, telles que [**Propriétaire**](../../../role-based-access-control/built-in-roles.md#owner) ou [**Administrateur de l’accès utilisateur**](../../../role-based-access-control/built-in-roles.md#user-access-administrator) dans l’étendue de stockage de la ressource de stockage.

1. Ensuite, vous allez attribuer un rôle **Contributeur aux données Blob du stockage** à votre ressource de service Traducteur. Dans la fenêtre contextuelle **Ajouter une attribution de rôle**, renseignez les champs comme suit et sélectionnez **Enregistrer** :

    | Champ | Valeur|
    |------|--------|
    |**Étendue**| **_Stockage_**.|
    |**Abonnement**| **_L’abonnement associé à votre ressource de stockage_**.|
    |**Ressource**| **_Le nom de votre ressource de stockage_**.|
    |**Rôle** | **_Contributeur aux données Blob du stockage_**.|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="Capture d’écran : page d’ajout d’attributions de rôles dans le portail Azure.":::

1. Une fois que vous avez reçu le message de confirmation _Attribution de rôle ajoutée_, actualisez la page pour voir l’attribution de rôle ajoutée. 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="Capture d’écran : message contextuel de confirmation de l’ajout de l’attribution de rôle":::

1. Si vous ne voyez pas immédiatement la modification, patientez et essayez d’actualiser la page une nouvelle fois. Lorsque vous attribuez des rôles ou supprimez des attributions de rôle, un délai maximal de 30 minutes peut être nécessaire avant que les modifications prennent effet.

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="Capture d’écran : fenêtre des attributions de rôles Azure.":::

 Très bien ! Vous avez terminé les étapes pour activer une identité managée affectée par le système. Avec ces informations d’identification de l’identité, vous pouvez accorder au Traducteur des droits d’accès spécifiques à vos ressources de stockage.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Identités managées pour les ressources Azure : Forum aux questions](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[Utiliser des identités managées pour acquérir un jeton d’accès](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)
