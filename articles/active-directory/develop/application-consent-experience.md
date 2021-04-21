---
title: Expériences de consentement de l’application Azure AD
titleSuffix: Microsoft identity platform
description: En savoir plus sur les expériences de consentement Azure AD pour voir comment vous pouvez les utiliser lors du développement et de la gestion d’applications sur Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105411"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Comprendre les expériences de consentement de l’application Azure AD

Apprenez-en davantage sur l’expérience utilisateur de consentement de l’application Azure Active Directory (Azure AD). Vous pouvez ainsi gérer de manière intelligente des applications pour votre organisation et/ou développer des applications avec une expérience de consentement plus transparente.

## <a name="consent-and-permissions"></a>Consentement et autorisations

Le consentement est le processus via lequel un utilisateur autorise une application à accéder à des ressources protégées en son nom. Un administrateur ou un utilisateur peut être invité à donner son consentement pour autoriser l’accès à ses données personnelles/professionnelles.

L’expérience utilisateur réelle d’octroi du consentement varie selon les stratégies définies au niveau du locataire de l’utilisateur, le niveau d’autorité (ou rôle) de l’utilisateur et le type d’[autorisation](v2-permissions-and-consent.md) demandé par l’application cliente. Cela signifie que les développeurs d’applications et les administrateurs de locataires contrôlent certains aspects de l’expérience de consentement. Les administrateurs ont la possibilité de configurer et de désactiver des stratégies au niveau d’un locataire ou d’une application afin de contrôler l’expérience de consentement au sein de leur locataire. Les développeurs d’applications peuvent définir les types d’autorisations demandées et s’ils souhaitent guider les utilisateurs vers le flux de consentement administrateur ou vers le flux de consentement utilisateur.

- **Flux de consentement utilisateur** : intervient lorsqu’un développeur d’application dirige les utilisateurs vers le point de terminaison d’autorisation avec l’intention d’enregistrer le consentement pour l’utilisateur actif uniquement.
- **Flux de consentement administrateur** : intervient lorsqu’un développeur d’application dirige les utilisateurs vers le point de terminaison de consentement administrateur avec l’intention d’enregistrer le consentement pour le locataire dans son intégralité. Pour s’assurer que le flux de consentement administrateur fonctionne correctement, les développeurs d’application doivent répertorier toutes les autorisations dans la propriété `RequiredResourceAccess` dans le manifeste de l’application. Pour plus d’informations, consultez l’article [Manifeste d’application](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocs de construction de l’invite de consentement

L’invite de consentement vise à s’assurer que les utilisateurs disposent de suffisamment d’informations pour décider de faire confiance ou non à l’application cliente afin de l’autoriser à accéder aux ressources protégées en leur nom. Comprendre les blocs de construction permet aux utilisateurs invités à donner leur consentement de prendre des décisions plus avisées et aide les développeurs à créer de meilleures expériences utilisateur.

Le diagramme et le tableau suivants fournissent des informations sur les blocs de construction de l’invite de consentement.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Blocs de construction de l’invite de consentement":::

| # | Composant | Objectif |
| ----- | ----- | ----- |
| 1 | Identificateur de l’utilisateur | Cet identificateur représente l’utilisateur au nom duquel l’application cliente demande à accéder aux ressources protégées. |
| 2 | Intitulé | L’intitulé change selon que les utilisateurs suivent le flux de consentement utilisateur ou administrateur. Dans le flux de consentement utilisateur, l’intitulé sera « Permissions requested » (Autorisations demandées), tandis que dans le flux de consentement administrateur, l’intitulé aura une ligne supplémentaire « Accept for your organization » (Accepter pour votre organisation). |
| 3 | Logo de l’application | Cette image donne aux utilisateurs un indice visuel pour déterminer si cette application est bien celle à laquelle ils souhaitaient accéder. Cette image est fournie par les développeurs d’application et la propriété de cette image n’est pas validée. |
| 4 | Nom de l’application | Cette valeur indique aux utilisateurs quelle application demande à accéder à leurs données. Ce nom est fourni par les développeurs et la propriété de ce nom d’application n’est pas validée. |
| 5 | Domaine de l’éditeur | Cette valeur fournit aux utilisateurs un nom de domaine dont ils peuvent évaluer la fiabilité. Ce domaine est fourni par les développeurs et la propriété de ce domaine d’éditeur est validée. |
| 6 | Éditeur vérifié | Le badge bleu « Vérifié » signifie que l’éditeur de l’application a vérifié son identité à l’aide d’un compte Microsoft Partner Network et a terminé le processus de vérification.|
| 7 | Informations sur l’éditeur  | Indique si l’application est publiée par Microsoft ou par votre organisation. |
| 8 | Autorisations | Cette liste contient les autorisations demandées par l’application cliente. Les utilisateurs doivent systématiquement évaluer les types d’autorisations demandées pour savoir à quelles données l’application cliente aura accès en leur nom s’ils donnent leur consentement. En tant que développeur d’application, nous vous recommandons de demander des autorisations d’accès avec le niveau de privilège minimum. |
| 9 | Description de l’autorisation | Cette valeur est fournie par le service exposant les autorisations. Pour afficher les descriptions d’autorisation, vous devez cliquer sur la flèche de développement en regard de l’autorisation. |
| 10| Conditions générales de l’application | Ces conditions contiennent des liens vers les conditions d’utilisation du service et la déclaration de confidentialité de l’application. L’éditeur est chargé de mentionner ses règles dans les conditions d’utilisation du service. L’éditeur doit également indiquer de quelle manière il utilise et partage les données des utilisateurs dans sa déclaration de confidentialité. Si l’éditeur ne fournit pas de liens vers ces valeurs pour les applications mutualisées, un message d’avertissement s’affiche en gras dans l’invite de consentement. |
| 11 | https://myapps.microsoft.com | Il s’agit du lien via lequel les utilisateurs peuvent afficher et supprimer les applications non Microsoft qui ont actuellement accès à leurs données. |
| 12 | Signalez-le ici | Ce lien est utilisé pour signaler une application suspecte si vous n’avez pas confiance en elle, si vous pensez qu’elle se fait passer pour une autre application, si vous pensez qu’elle va utiliser vos données à mauvais escient ou pour toute autre raison. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>L’application requiert une autorisation relevant de l’autorité de l’utilisateur

Un scénario de consentement courant est que l’utilisateur accède à une application qui requiert un ensemble d’autorisations relevant de l’autorité de l’utilisateur. L’utilisateur est dirigé vers le flux de consentement de l’utilisateur.

Les administrateurs bénéficient d’une option de contrôle supplémentaire par rapport à l’invite de consentement standard qui leur permet de donner un accord pour l’intégralité du locataire. Cette option est désactivée par défaut. Ainsi, le consentement sera accordé au nom du locataire dans son ensemble uniquement si l’administrateur coche la case correspondante. Pour le moment, cette case à cocher s’affiche uniquement pour le rôle Administrateur général, et non pour les rôles Administrateur du cloud et Administrateur de l’application.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Invite de consentement pour le scénario 1a":::

Les utilisateurs voient l’invite de consentement standard.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Capture d'écran représentant l'invite de consentement standard.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>L’application requiert une autorisation qui ne relève pas de l’autorité de l’utilisateur

Un autre scénario de consentement courant est que l’utilisateur accède à une application qui requiert au moins une autorisation qui n’est pas du ressort de l’utilisateur.

Les administrateurs bénéficient d’une option de contrôle supplémentaire par rapport à l’invite de consentement standard qui leur permet de donner un accord pour l’intégralité du locataire.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Invite de consentement pour le scénario 1a":::

Les utilisateurs qui ne sont pas administrateurs ne peuvent pas donner leur consentement à l’application et sont invités à contacter leur administrateur pour autoriser l’accès à l’application.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Capture d'écran de l'invite de consentement demandant à l'utilisateur de contacter un administrateur pour accéder à l'application.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>L’utilisateur est dirigé vers le flux de consentement de l’administrateur

Un autre scénario courant est celui où l’utilisateur accède au flux de consentement de l’administrateur ou est dirigé vers celui-ci.

Les utilisateurs administrateurs voient l’invite de consentement administrateur. L’intitulé et les descriptions d’autorisation ont été modifiés sur cette invite. Les modifications indiquent qu’en acceptant cette invite, l’utilisateur autorise l’application à accéder aux données demandées pour le compte de l’ensemble du locataire.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Invite de consentement pour le scénario 3a":::

Les utilisateurs qui ne sont pas administrateurs ne peuvent pas donner leur consentement à l’application et sont invités à contacter leur administrateur pour autoriser l’accès à l’application.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Capture d'écran de l'invite de consentement demandant à l'utilisateur de contacter un administrateur pour accéder à l'application.":::

## <a name="next-steps"></a>Étapes suivantes

- Obtenez une présentation détaillée de [la façon dont l’infrastructure de consentement Azure AD implémente le consentement](./quickstart-register-app.md).
- Pour plus de détails, découvrez [la façon dont une application mutualisée peut utiliser l’infrastructure de consentement](./howto-convert-app-to-be-multi-tenant.md) pour implémenter un consentement de type « utilisateur » et « admin », en prenant en charge des modèles d’applications mutualisées plus avancés.
- Découvrez [comment configurer le domaine de l’éditeur de l’application](howto-configure-publisher-domain.md).