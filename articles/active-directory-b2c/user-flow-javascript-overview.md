---
title: JavaScript et versions de mise en page
titleSuffix: Azure AD B2C
description: Découvrez comment activer JavaScript et utiliser des versions de mise en page dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840092"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versions de mise en page et JavaScript dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fournit un ensemble de contenu packagé contenant des données HTML, CSS et JavaScript pour les éléments d’interface utilisateur dans vos flux d’utilisateurs et stratégies personnalisées. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](custom-policy-overview.md) ou l’activer dans le portail des flux d’utilisateurs, puis sélectionner une mise en page et utiliser le paramètre [b2clogin.com](b2clogin.md) dans vos requêtes.

Si vous prévoyez d’activer du code [JavaScript](javascript-samples.md) côté client, vérifiez que les éléments sur lesquels il s’appuie sont immuables. Dans le cas contraire, la moindre modification pourrait provoquer un comportement inattendu sur vos pages d’utilisateurs. Pour éviter ces problèmes, vous pouvez imposer l’utilisation d’une mise en page et spécifier une version de mise en page. Toutes les définitions de contenu sur lesquelles se basent votre code JavaScript seront ainsi immuables. Même si vous ne souhaitez pas activer JavaScript, vous pouvez spécifier une version de mise en page pour vos pages.

## <a name="user-flows"></a>Flux d’utilisateurs

Dans les **Propriétés** du flux d’utilisateurs, vous pouvez activer JavaScript, ce qui impose également l’utilisation d’une mise en page. Vous pouvez ensuite définir la version de mise en page du flux d'utilisateur comme décrit dans la section suivante.

![Page des propriétés du flux d'utilisateur avec le paramètre Activer JavaScript en surbrillance](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Sélectionner une version de mise en page

Que vous activiez ou non JavaScript dans les propriétés de votre flux d’utilisateur, vous pouvez spécifier une version de mise en page pour vos pages de flux d’utilisateur. Ouvrez le flux d’utilisateur et sélectionnez **Mises en page**. Sous **NOM DE LA MISE EN PAGE**, sélectionnez une page de flux d’utilisateur et choisissez **Version de mise en page**.

Pour plus d’informations sur les différentes versions de mise en page, consultez le [Journal des modifications de version](page-layout.md#version-change-log).

![Paramètres de mise en page dans le portail montrant la liste déroulante des versions de mise en page](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Stratégies personnalisées

Pour activer JavaScript dans des stratégies personnalisées, vous ajoutez l’élément **ScriptExecution** dans l’élément **RelyingParty** de votre fichier de stratégie personnalisée. Pour en savoir plus, voir [Exemples JavaScript dans Azure Active Directory B2C](javascript-samples.md).

Que vous activiez ou non JavaScript dans vos stratégies personnalisées, vous pouvez spécifier une version de mise en page pour vos pages. Pour en savoir plus sur la spécification d’une mise en page, consultez [Sélectionner une mise en page dans Azure Active Directory B2C à l’aide de stratégies personnalisées](page-layout.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différentes versions de mise en page, consultez la section **Journal des modifications de version** de l'article [Sélectionner une mise en page dans Azure Active Directory B2C à l’aide de stratégies personnalisées](page-layout.md#version-change-log).

Vous trouverez des exemples d'utilisation de JavaScript dans [Exemples JavaScript pour une utilisation dans Active Directory B2C](javascript-samples.md).
