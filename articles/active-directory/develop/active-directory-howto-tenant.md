---
title: Obtention d’un client Azure AD | Microsoft Docs
description: Obtention d’un client Azure Active Directory pour l'inscription et la génération d'applications.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 1f866d3ee56b0c9a1e7a986d3ac951764b6a1cae
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399512"
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Obtention d’un client Azure Active Directory

Dans Azure Active Directory (Azure AD), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) est représentatif d'une organisation. Il s’agit d’une instance dédiée du service Azure AD qu’une organisation reçoit et possède lorsqu’elle établit une relation avec Microsoft, comme en s’inscrivant à un service cloud de Microsoft tel qu’Azure, Microsoft Intune ou Office 365. Chaque client Azure AD est distinct et indépendant des autres clients Azure AD. 

Un locataire héberge les utilisateurs d’une entreprise et leurs informations, c’est-à-dire les mots de passe, les données de profil utilisateur, les autorisations, etc. Il contient également des groupes, des applications et d’autres informations relatives à une organisation et à sa sécurité.

Pour permettre aux utilisateurs d'Azure AD de se connecter à votre application, vous devez enregistrer votre application dans un de vos clients. La création d’un locataire Azure AD et la publication d’une application dans celui-ci sont **totalement gratuites** bien que vous puissiez choisir de payer pour des fonctionnalités premium dans votre locataire. En fait, de nombreux développeurs créent plusieurs locataires et applications à des fins d’expérimentation, de développement, d’organisation et de test.

## <a name="use-an-existing-azure-ad-tenant"></a>Utiliser un locataire Azure AD existant

De nombreux développeurs comptent déjà des locataires par le biais de services ou d’abonnements liés à des locataires Azure AD comme des abonnements Office 365 ou Azure. Pour vérifier si vous disposez déjà d’un locataire, connectez-vous au [portail Azure](https://portal.azure.com) avec le compte que vous voulez utiliser pour gérer votre application et vérifiez dans le coin supérieur droit où les informations de votre compte sont affichées. Si vous disposez d’un locataire, vous êtes automatiquement connecté à celui-ci, et vous verrez le nom du locataire directement sous le nom de votre compte. Si vous pointez sur le nom de votre compte en haut à droite du portail Azure, vous voyez votre nom, votre adresse e-mail, votre annuaire et votre ID de locataire (GUID), ainsi que votre domaine. Si votre compte est associé à plusieurs locataires, vous pouvez sélectionner le nom de votre compte pour ouvrir un menu dans lequel vous pouvez passer d’un locataire à l’autre. Chaque locataire a son propre ID.

> [!TIP]
> Pour déterminer un ID de locataire, plusieurs méthodes sont possibles. Vous pouvez pointer sur le nom de votre compte pour obtenir l’ID de locataire ou sélectionner **Azure Active Directory > Propriétés > ID de répertoire** dans le portail Azure.

Si vous ne disposez pas d’un locataire associé à votre compte, vous voyez un GUID sous le nom de votre compte et vous ne pouvez pas effectuer certaines actions comme l’inscription d’applications tant que vous n’avez pas [créé un locataire](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD

Si vous ne disposez pas déjà d’un locataire Azure AD ou si vous voulez en créer un nouveau, vous pouvez le faire à l’aide de l’[expérience de création de répertoire](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) dans le [portail Azure](https://portal.azure.com). Le processus prendra une minute environ et, une fois terminé, vous serez invité à accéder à votre nouveau locataire.
