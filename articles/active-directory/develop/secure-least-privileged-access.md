---
title: Bonnes pratiques pour les droits d’accès minimal sur Azure AD - Plateforme d’identités Microsoft
description: Découvrez un ensemble de bonnes pratiques et de conseils généraux concernant les droits d’accès minimal.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/26/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: db2eb2dcda894b997f485795ab62d983966a7f2f
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372757"
---
# <a name="best-practices-for-least-privileged-access-for-applications"></a>Bonnes pratiques pour les droits d’accès minimal pour les applications

Le principe du privilège minimum (ou du moindre privilège) est un concept de sécurité des informations reposant sur l’idée que les utilisateurs et les applications ne doivent disposer que du niveau d’accès minimum qui leur est strictement nécessaire pour effectuer leurs tâches. Avoir une bonne compréhension de ce principe vous aidera à créer des applications fiables pour vos clients.

L’adoption du principe du privilège minimum ne se résume pas à une simple bonne pratique de sécurité. Le concept vous aide à préserver l’intégrité et la sécurité de vos données. Il protège également la confidentialité de vos données et réduit les risques en empêchant les applications d’accéder aux données autrement que ce qui est absolument nécessaire. Plus largement, l’adoption du principe du privilège minimum constitue l’une des méthodes que les organisations peuvent utiliser pour appliquer une sécurité proactive avec le modèle [Confiance Zéro](https://www.microsoft.com/security/business/zero-trust).

Cet article décrit un ensemble de bonnes pratiques à suivre pour adopter le principe du privilège minimum et ainsi renforcer la sécurité de vos applications pour les utilisateurs finaux. Les explications ci-dessous vous permettront de comprendre les aspects suivants du principe du privilège minimum :
- Comment fonctionne le consentement avec les autorisations
- Ce qu’est une application avec des privilèges excessifs ou minimum
- Comment aborder le principe du privilège minimum en tant que développeur
- Comment aborder le principe du privilège minimum en tant qu’organisation

## <a name="using-consent-to-control-access-permissions-to-data"></a>Utilisation du consentement pour contrôler les autorisations d’accès aux données

Pour accéder à des données protégées, le [consentement](../develop/application-consent-experience.md#consent-and-permissions) de l’utilisateur final est obligatoire. Quand une application exécutée sur l’appareil de l’utilisateur demande l’accès à des données protégées, elle doit toujours demander le consentement de l’utilisateur avant d’accorder l’accès à ces données. L’utilisateur final doit donner (ou refuser) son consentement pour l’autorisation demandée afin que l’application puisse continuer. En tant que développeur d’application, il est préférable de demander une autorisation d’accès avec le niveau de privilège minimum.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Capture d’écran du portail Azure qui montre le volet des autorisations de l’API pour l’inscription d’une application.":::

## <a name="overprivileged-and-least-privileged-applications"></a>Applications avec des privilèges excessifs ou minimum

Une application avec des privilèges excessifs peut présenter l’une des caractéristiques suivantes :
- **Autorisations inutilisées** : une application peut se retrouver avec des autorisations inutilisées lorsqu’elle ne parvient pas à effectuer des appels d’API qui se servent de toutes les autorisations qui lui sont accordées. Par exemple, dans [MS Graph](/graph/overview), une application peut parfois uniquement lire des fichiers OneDrive (en utilisant l’autorisation *Files.Read.All*), mais avoir également reçu l’autorisation *Calendars.Read* alors qu’elle n’est intégrée à aucune API Calendrier.
- **Autorisations réductibles** : une application dispose d’une autorisation réductible quand l’autorisation accordée peut être remplacée par une autorisation de privilège moindre pour effectuer l’appel d’API souhaité. Par exemple, une application qui lit uniquement des profils utilisateur, mais qui a reçu l’autorisation *User.ReadWrite.All* sera considérée comme ayant des privilèges excessifs. Dans ce cas, l’application devrait avoir l’autorisation *User.Read.All* à la place, qui est l’autorisation accordant le privilège minimum suffisant pour satisfaire la demande.

Pour qu’une application soit considérée comme à privilège minimum, elle doit avoir les :
- **Autorisations strictement nécessaires** : n’accordez que l’ensemble minimal d’autorisations dont l’utilisateur final d’une application, d’un service ou d’un système a absolument besoin pour effectuer les tâches requises.

## <a name="approaching-least-privilege-as-an-application-developer"></a>Approche du principe de privilège minimum en tant que développeur d’application

En tant que développeur, vous avez la responsabilité de contribuer à la sécurité des données de votre client. Pendant le développement de vos applications, vous devez adopter le principe du privilège minimum. Nous vous recommandons de respecter les étapes ci-dessous pour éviter d’accorder des privilèges excessifs à votre application :
- Déterminez avec précision quelles autorisations sont nécessaires à votre application pour effectuer les appels d’API requis.
- Identifiez l’autorisation avec le privilège minimum pour chaque appel d’API que votre application doit effectuer. Pour cela, aidez-vous de l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer).
- Recherchez les [autorisations](/graph/permissions-reference) correspondantes, du niveau de privilège le moins élevé au plus élevé.
- Supprimez les ensembles d’autorisations qui font double emploi si votre application effectue des appels d’API qui ont des autorisations redondantes.
- Appliquez uniquement l’ensemble d’autorisations avec le moins de privilèges à votre application en choisissant l’autorisation avec le privilège minimum dans la liste des autorisations.

## <a name="approaching-least-privilege-as-an-organization"></a>Approche du principe de privilège minimum en tant qu’organisation

Les organisations hésitent souvent à modifier des applications existantes, car elles craignent que cela impacte leurs activités. Cela pose problème quand il faut révoquer des autorisations qui ont été précédemment accordées avec des privilèges excessifs. C’est pourquoi, en tant qu’organisation, une bonne pratique consiste à vérifier et revoir les autorisations existantes de façon régulière. Nous vous recommandons de respecter ces étapes pour que vos applications restent saines :
- Évaluez les appels d’API effectués à partir de vos applications.
- Aidez-vous de l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) et de la documentation [Microsoft Graph](/graph/overview) pour déterminer les autorisations offrant les privilèges minimum nécessaires.
- Vérifiez les privilèges accordés aux utilisateurs ou aux applications.
- Modifiez vos applications en leur accordant l’ensemble d’autorisations avec les privilèges minimum.
- Revoyez régulièrement les autorisations déjà accordées pour vérifier qu’elles sont toujours pertinentes.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le consentement et les autorisations dans Azure Active Directory, consultez [Comprendre les expériences de consentement de l’application Azure AD](../develop/application-consent-experience.md).
- Pour plus d’informations sur les autorisations et le consentement dans la plateforme d’identités Microsoft, consultez [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md).
- Pour plus d’informations sur le modèle Confiance Zéro, consultez [Centre de conseils sur la Confiance Zéro](/security/zero-trust/).
