---
title: Versions des contrats de pages et JavaScript - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment activer JavaScript et utiliser des versions de contrats de pages dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511974"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Versions des contrats de pages et JavaScript dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fournit un ensemble de contenu packagé contenant des données HTML, CSS et JavaScript pour les éléments d’interface utilisateur dans vos flux d’utilisateurs et stratégies personnalisées. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](active-directory-b2c-overview-custom.md) ou l’activer dans le portail des flux d’utilisateurs, puis sélectionner un contrat de page et utiliser le paramètre [b2clogin.com](b2clogin.md) dans vos requêtes.

Si vous prévoyez d’activer du code [JavaScript](javascript-samples.md) côté client, vérifiez que les éléments sur lesquels il s’appuie sont immuables. Dans le cas contraire, la moindre modification pourrait provoquer un comportement inattendu sur vos pages d’utilisateurs. Pour éviter ces problèmes, vous pouvez imposer l’utilisation d’un contrat de pages et spécifier une version du contrat de pages. Toutes les définitions de contenu sur lesquelles se basent votre code JavaScript seront ainsi immuables. Même si vous ne souhaitez pas activer JavaScript, vous pouvez spécifier une version du contrat pour vos pages.

## <a name="user-flows"></a>Flux d’utilisateurs

Dans les propriétés du flux d’utilisateurs, vous pouvez activer JavaScript, ce qui impose également l’utilisation d’un contrat de pages. Vous pouvez alors définir la version du contrat de pages (voir la section suivante).

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Que vous activiez ou non JavaScript dans les propriétés de votre flux d’utilisateurs, vous pouvez spécifier une version du contrat pour vos pages de flux d’utilisateurs. Ouvrez le flux d’utilisateur et sélectionnez **Mises en page**. Sous **Nom de la mise en page**, sélectionner une page de flux d’utilisateurs et choisissez **Version du contrat de pages**.

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Stratégies personnalisées

Pour activer JavaScript dans des stratégies personnalisées, vous ajoutez l’élément **ScriptExecution** dans l’élément **RelyingParty** de votre fichier de stratégie personnalisée. Pour en savoir plus, voir [Exemples JavaScript dans Azure Active Directory B2C](javascript-samples.md).

Que vous activiez ou non JavaScript dans vos stratégies personnalisées, vous pouvez spécifier une version du contrat pour vos pages. Pour en savoir plus sur la spécification d’un contrat de page, voir [Sélectionner un contrat de page dans Azure Active Directory B2C à l’aide de stratégies personnalisées](page-contract.md).

## <a name="next-steps"></a>Étapes suivantes

Voir [Exemples JavaScript dans Azure Active Directory B2C](javascript-samples.md).
