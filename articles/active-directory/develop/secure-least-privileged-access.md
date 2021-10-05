---
title: Renforcer la sécurité des applications avec le principe des privilèges minimum
titleSuffix: Microsoft identity platform
description: Découvrez comment le principe des privilèges minimum peut contribuer à renforcer la sécurité de votre application et de ses données, et les fonctionnalités de la plateforme d’identités Microsoft que vous pouvez utiliser pour implémenter le droit d’accès minimal.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: a7e504bfd13d89ba417067faf88cf17cf12e1d0b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786481"
---
# <a name="enhance-security-with-the-principle-of-least-privilege"></a>Renforcer la sécurité avec le principe des privilèges minimum

Dans le cadre de la sécurité des informations, le principe des privilèges minimum affirme que les utilisateurs et applications ne doivent être autorisés à accéder qu’aux seules données et opérations dont ils ont besoin pour effectuer leurs tâches.

Suivez les recommandations fournies ici pour réduire la surface d’attaque de votre application et l’impact d’une violation de la sécurité (*rayon d’impact*) si un tel incident devait se produire dans votre application intégrée à la plateforme d’identités Microsoft.

## <a name="recommendations-at-a-glance"></a>Aperçu des recommandations

- Empêchez l’**octroi de privilèges excessifs** aux applications en révoquant les autorisations *inutilisées* et *réductibles*.
- Utilisez l’infrastructure de **consentement** de la plateforme d’identités pour exiger qu’un humain accepte la demande d’accès aux données protégées formulée par l’application.
- **Créez** des applications en gardant les privilèges minimum à l’esprit à toutes les étapes du développement.
- **Auditez** régulièrement vos applications déployées pour identifier les applications dotées de privilèges excessifs.

## <a name="whats-an-overprivileged-application"></a>Qu’est-ce qu’une application avec des *privilèges excessifs* ?

Toute application disposant d’une autorisation **inutilisée** ou **réductible** est considérée comme dotée de privilèges excessifs. Les autorisations inutilisées et réductibles peuvent potentiellement offrir un accès non autorisé ou involontaire à des données ou opérations non requises par l’application ou ses utilisateurs pour effectuer leurs tâches.

:::row:::
   :::column span="":::
      ### <a name="unused-permissions"></a>Autorisations non utilisées

        Une autorisation inutilisée est une autorisation accordée à une application, mais dont l’API ou l’opération exposée par cette autorisation n’est pas invoquée par l’application lorsqu’elle est utilisée comme prévu.

        - **Exemple** : une application affiche la liste des fichiers stockés dans l’espace OneDrive de l’utilisateur connecté en invoquant l’API Microsoft Graph et en tirant parti de l’autorisation [Files.Read](/graph/permissions-reference). L’application a également reçu l’autorisation [Calendars.Read](/graph/permissions-reference#calendars-permissions), mais elle ne fournit pas de fonctionnalités de calendrier et n’invoque pas l’API Calendars.

        - **Risque de sécurité** : les autorisations inutilisées présentent un risque de sécurité en termes d’*élévation horizontale des privilèges*. Une entité qui exploite une faille de sécurité dans votre application peut utiliser une autorisation inutilisée pour accéder à une API ou une opération qui n’est pas normalement prise en charge ou autorisée par l’application lorsqu’elle est utilisée comme prévu.

        - **Atténuation** : supprimez les autorisations qui ne sont pas utilisées dans les appels d’API effectués par votre application.
   :::column-end:::
   :::column span="":::
      ### <a name="reducible-permissions"></a>Autorisations réductibles

        Une autorisation réductible est une autorisation avec un équivalent à privilèges inférieurs qui permettrait de fournir à l’application et à ses utilisateurs l’accès dont ils ont besoin pour effectuer les tâches nécessaires.

        - **Exemple** : une application affiche les informations de profil de l’utilisateur connecté en appelant l’API Microsoft Graph, mais ne prend pas en charge la modification de profil. Toutefois, l’autorisation [User.ReadWrite.All](/graph/permissions-reference#user-permissions) a été accordée à l’application. L’autorisation *User.ReadWrite.All* est considérée comme étant réductible ici, car l’autorisation *User.Read.All* moins permissive accorde un accès en lecture seule suffisant aux données de profil de l’utilisateur.

        - **Risque de sécurité** : les autorisations réductibles présentent un risque de sécurité en termes d’*élévation verticale des privilèges*. Une entité qui exploite une faille de sécurité dans votre application pourrait utiliser l’autorisation réductible pour établir un accès non autorisé aux données ou effectuer des opérations qui ne sont pas normalement autorisées par le rôle de cette entité.

        - **Atténuation** : remplacez chaque autorisation réductible dans votre application par son équivalent le moins permissif, qui permet toujours d’activer la fonctionnalité prévue de l’application.
   :::column-end:::
:::row-end:::

Évitez les risques de sécurité posés par les autorisations inutilisées et réductibles en accordant l’autorisation *juste suffisante* : l’autorisation avec l’accès le moins permissif requis par une application ou un utilisateur pour effectuer les tâches nécessaires.

## <a name="use-consent-to-control-access-to-data"></a>Utiliser le consentement pour contrôler l’accès aux données

La plupart des applications que vous développez nécessitent d’accéder à des données protégées et le propriétaire de ces données doit autoriser cet accès en donnant son [consentement](application-consent-experience.md#consent-and-permissions). Le consentement peut être accordé de plusieurs façons, notamment par un administrateur client qui peut donner son consentement pour *tous* les utilisateurs d’un locataire Azure AD, ou par les utilisateurs de l’application eux-mêmes qui peuvent accorder l’accès

Quand une application exécutée sur l’appareil de l’utilisateur demande l’accès à des données protégées, elle doit toujours demander le consentement de l’utilisateur avant d’accorder l’accès à ces données. L’utilisateur final doit donner (ou refuser) son consentement pour l’autorisation demandée afin que l’application puisse continuer.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Capture d’écran du portail Azure montrant le volet des autorisations de l’API pour l’inscription d’une application.":::

## <a name="least-privilege-during-app-development"></a>Privilèges minimum lors du développement de l’application

En tant que développeur créant une application, considérez que la sécurité de votre application et des données de ses utilisateurs relèvent de *votre* responsabilité.

Suivez ces recommandations lorsque vous développez une application afin d’éviter de créer une application avec des privilèges excessifs :

- Déterminez avec précision quelles autorisations sont nécessaires à votre application pour effectuer les appels d’API requis.
- Identifiez l’autorisation avec le privilège minimum pour chaque appel d’API que votre application doit effectuer à l’aide de l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer).
- Recherchez les [autorisations](/graph/permissions-reference) correspondantes, du niveau de privilège le moins élevé au plus élevé.
- Supprimez les ensembles d’autorisations qui font double emploi si votre application effectue des appels d’API qui ont des autorisations redondantes.
- Appliquez uniquement l’ensemble d’autorisations avec le moins de privilèges à votre application en choisissant l’autorisation avec le privilège minimum dans la liste des autorisations.

## <a name="least-privilege-for-deployed-apps"></a>Privilèges minimum pour les applications déployées

Les organisations hésitent souvent à modifier les applications en cours d’exécution afin d’éviter d’impacter le cours normal de leur activité. Toutefois, votre organisation doit considérer que l’atténuation du risque d’un incident de sécurité rendu possible ou plus sérieux par les autorisations excessives de votre application mérite une mise à jour planifiée de l’application.

Appliquez ces pratiques standard dans votre organisation pour garantir que vos applications déployées ne sont pas excessivement pourvues en privilèges et ne le deviennent pas avec le temps :

- Évaluez les appels d’API effectués à partir de vos applications.
- Aidez-vous de l’[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) et de la documentation [Microsoft Graph](/graph/overview) pour déterminer les autorisations offrant les privilèges minimum nécessaires.
- Vérifiez les privilèges accordés aux utilisateurs ou aux applications.
- Modifiez vos applications en leur accordant l’ensemble d’autorisations avec les privilèges minimum.
- Revoyez régulièrement les autorisations déjà accordées pour vérifier qu’elles sont toujours pertinentes.

## <a name="next-steps"></a>Étapes suivantes

**Accès aux ressources protégées et consentement**

Pour plus d’informations sur la configuration de l’accès aux ressources protégées et l’expérience utilisateur d’autorisation de l’accès à ces ressources protégées, consultez les articles suivants :

- [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md)
- [Comprendre les expériences de consentement de l’application Azure AD](../develop/application-consent-experience.md)

**Confiance Zéro** : envisagez d’utiliser les mesures de privilège minimum décrites ici dans le cadre de la [stratégie de sécurité Confiance Zéro](/security/zero-trust/) proactive de votre organisation.
