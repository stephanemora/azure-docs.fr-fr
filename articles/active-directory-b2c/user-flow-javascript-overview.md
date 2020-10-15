---
title: JavaScript et versions de mise en page
titleSuffix: Azure AD B2C
description: Découvrez comment activer JavaScript et utiliser des versions de mise en page dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50644ad3be226648accba6a2f43d4ea068ff977c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258844"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versions de mise en page et JavaScript dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fournit un ensemble de contenu packagé contenant des données HTML, CSS et JavaScript pour les éléments d’interface utilisateur dans vos flux d’utilisateurs et stratégies personnalisées.

Pour activer JavaScript pour vos applications :

* Activez-le sur le flux d'utilisateurs à l'aide du portail Azure.
* Sélectionnez une [mise en page](page-layout.md).
* Utilisez [b2clogin.com](b2clogin.md) dans vos requêtes.

Si vous prévoyez d'activer du code [JavaScript](javascript-samples.md) côté client, les éléments sur lesquels vous basez votre JavaScript doivent être immuables. S'ils ne le sont pas, la moindre modification pourrait provoquer un comportement inattendu sur vos pages d'utilisateurs. Pour prévenir ce type de problème, imposez l'utilisation d'une mise en page et spécifiez une version de mise en page afin de veiller à ce que les définitions de contenu sur lesquelles vous avez basé votre JavaScript soient immuables. Même si vous ne souhaitez pas activer JavaScript, vous pouvez spécifier une version de mise en page pour vos pages.

## <a name="enable-javascript"></a>Activer JavaScript

Dans le flux d'utilisateurs **Propriétés**, vous pouvez activer JavaScript. L'activation de JavaScript impose également l'utilisation d’une mise en page. Vous pouvez ensuite définir la version de mise en page du flux d'utilisateur comme décrit dans la section suivante.

![Page des propriétés du flux d'utilisateur avec le paramètre Activer JavaScript en surbrillance](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Sélectionner une version de mise en page

Que vous activiez ou non JavaScript dans les propriétés de votre flux d’utilisateur, vous pouvez spécifier une version de mise en page pour vos pages de flux d’utilisateur. Ouvrez le flux d’utilisateur et sélectionnez **Mises en page**. Sous **Nom de la disposition**, sélectionnez une page de flux utilisateur et choisissez **Version Mise en page (préversion)** .

Pour plus d'informations sur les différentes versions de mise en page, consultez le [Journal des modifications des versions de mise en page](page-layout.md).

![Paramètres de mise en page dans le portail montrant la liste déroulante des versions de mise en page](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des exemples d'utilisation de JavaScript dans [Exemples JavaScript pour une utilisation dans Active Directory B2C](javascript-samples.md).
