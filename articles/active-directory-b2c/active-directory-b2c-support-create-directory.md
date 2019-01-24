---
title: Résoudre les problèmes liés à la création de locataires dans Azure Active Directory B2C | Microsoft Docs
description: Problèmes et résolutions pour la création d’un locataire Azure Active Directory ou Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1abd25f9c12c949b1c807aca3a4378ac493bd877
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842356"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Résoudre les problèmes de création d’un locataire Azure Active Directory ou Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Créer un locataire Azure AD
Si vous ne pouvez pas créer de locataire Azure Active Directory (Azure AD) lors de votre première tentative, réessayez. Si le problème persiste, contactez le support Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C
Si vous rencontrez des problèmes quand vous [créez un locataire Azure Active Directory B2C (Azure AD B2C)](active-directory-b2c-get-started.md), essayez les options suivantes :

* Si le locataire Azure AD B2C n’apparaît pas dans votre liste de locataires, réessayez de le créer.
* Si le locataire Azure AD B2C apparaît dans la liste des locataires et que le message d’erreur suivant s’affiche, supprimez le locataire et recréez-le :

    « Impossible de terminer la création du locataire B2C ’contosob2c’. Visitez ce [lien](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) pour plus d’informations. »
* Il existe des problèmes connus liés à la suppression d’un locataire Azure AD B2C existant et à sa recréation à l’aide du même nom de domaine. Quand vous créez un locataire Azure AD B2C, vous devez utiliser un nom de domaine différent.
* Si aucune de ces solutions ne résout votre problème, contactez le support technique Azure. Pour plus d’informations, consultez [Prise en charge des demandes d’assistance pour Azure AD B2C](active-directory-b2c-support.md).

