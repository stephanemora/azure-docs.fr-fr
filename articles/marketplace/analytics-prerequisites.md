---
title: Conditions préalables à l’accès programmatique aux données d’analyse
description: Découvrez les exigences qu’il vous faut respecter pour accéder par programme aux données d’analyse de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b61608c0cb53ab808c5d3d789ec5ddc318c6923d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106800"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Conditions préalables à l’accès programmatique aux données d’analyse

Avant de pouvoir accéder par programme aux données d’analyse de la place de marché commerciale, vous devez respecter les exigences suivantes.

## <a name="commercial-marketplace-enrollment"></a>Inscription à la place de marché commerciale

Pour accéder aux données d’analyse de la place de marché commerciale par programme, vous devez être inscrit au programme de la place de marché commerciale et disposer d’un compte Espace partenaires. Pour en savoir plus, consultez [Créer un compte Place de marché commerciale dans l’Espace partenaires](create-account.md).

## <a name="create-azure-active-directory-application"></a>Création d’une application Azure Active Directory

Les informations d’identification habituelles de l’utilisateur ne peuvent pas être utilisées pour accéder par programme aux données d’analyse de la place de marché commerciale. Une application Azure Active Directory (Azure AD) doit être créée, de même qu’un secret, pour accéder aux API d’analyse. Pour savoir comment créer une application Azure AD et un secret, consultez [Guide de démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Associer l’application Azure AD au locataire de l’Espace partenaires

L’application Azure AD que vous avez créée dans le portail Azure doit être liée à votre compte Espace partenaires. La procédure comporte trois étapes :

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard).
1. En haut à droite, sélectionnez l’icône d’engrenage, puis **Paramètres du compte**.
1. Dans le menu **Paramètres du compte** , sélectionnez **Gestion des utilisateurs**.
1. Sélectionnez **Applications Azure AD** , puis **+ Créer une application Azure AD**.
1. Sélectionnez l’application Azure AD que vous avez créée sur le portail Azure, puis **Suivant**.
1. Sélectionnez la case à cocher **Gestionnaire (Windows)** , puis **Ajouter**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Illustre la page Créer une application Azure AD avec les cases à cocher pour sélectionner des rôles.":::

## <a name="generate-an-azure-ad-token"></a>Générer un jeton Azure AD

Vous devez générer un jeton Azure AD à l’aide de l’ID d’application (client). Cet ID permet d’identifier de manière unique votre application cliente dans la plateforme d’identités Microsoft et la clé secrète client de l’étape précédente. Pour connaître la procédure permettant de générer un jeton Azure AD, consultez [Appels de service à service à l’aide des informations d’identification du client (secret partagé ou certificat)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).

> [!NOTE]
> Ce jeton est valide une heure.

## <a name="next-steps"></a>Étapes suivantes

- [Paradigme de l’accès programmatique](analytics-programmatic-access.md)