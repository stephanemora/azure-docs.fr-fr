---
title: JavaScript et page de contrat de versions - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment activer JavaScript et utiliser des versions de contrat de page dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570629"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript et page de contrat de versions dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fournit un ensemble de contenu du package contenant le HTML, CSS et JavaScript pour les éléments d’interface utilisateur dans votre flux d’utilisateurs et les stratégies personnalisées. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](active-directory-b2c-overview-custom.md) ou l’activer dans le portail pour les flux d’utilisateurs, sélectionnez un contrat de page et utilisez [b2clogin.com](b2clogin.md) dans vos requêtes.

Si vous prévoyez d’activer [JavaScript](javascript-samples.md) code côté client, vous voudrez pour être sûr que les éléments que vous créez à partir de votre code JavaScript sont immuables. Dans le cas contraire, toute modification peut entraîner un comportement inattendu sur vos pages de l’utilisateur. Pour éviter ces problèmes, vous pouvez imposer l’utilisation d’un contrat de page et spécifier une version de contrat de page. Cette opération garantit que toutes les définitions de contenu que vous avez selon votre code JavaScript sont immuables. Même si vous ne souhaitez pas activer JavaScript, vous pouvez spécifier une version de contrat de page pour vos pages.

## <a name="user-flows"></a>Flux d’utilisateurs

Dans les propriétés du flux d’utilisateurs, vous pouvez activer JavaScript, ce qui impose également l’utilisation d’un contrat de pages. Vous pouvez alors définir la version du contrat de pages (voir la section suivante).

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Que vous activiez ou non JavaScript dans les propriétés de votre flux d’utilisateurs, vous pouvez spécifier une version du contrat pour vos pages de flux d’utilisateurs. Ouvrez le flux d’utilisateur et sélectionnez **Mises en page**. Sous **Nom de la mise en page**, sélectionner une page de flux d’utilisateurs et choisissez **Version du contrat de pages**.

![Paramètre Activer JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Stratégies personnalisées

Pour activer JavaScript dans les stratégies personnalisées, vous ajoutez le **ScriptExecution** élément à la **RelyingParty** élément dans votre fichier de stratégie personnalisée. Pour plus d’informations, consultez [exemples JavaScript pour une utilisation dans Azure Active Directory B2C](javascript-samples.md).

Si vous activez JavaScript dans vos stratégies personnalisées, vous pouvez spécifier une version de contrat de page pour vos pages. Pour plus d’informations sur la spécification d’un contrat de page, consultez [sélectionner un contrat de la page à l’aide de stratégies personnalisées dans Azure Active Directory B2C](page-contract.md).

## <a name="next-steps"></a>Étapes suivantes

Voir [Exemples JavaScript dans Azure Active Directory B2C](javascript-samples.md).
