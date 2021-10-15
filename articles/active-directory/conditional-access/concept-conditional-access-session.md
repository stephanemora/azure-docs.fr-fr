---
title: Contrôles de session dans une stratégie d’accès conditionnel - Azure Active Directory
description: Que sont les contrôles de session dans une stratégie d’accès conditionnel Azure AD ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a30dc564dced8d3306c4645ca48b6331e1f6959b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355768"
---
# <a name="conditional-access-session"></a>Accès conditionnel : session

Dans une stratégie d’accès conditionnel, un administrateur peut utiliser des contrôles de session pour autoriser des expériences limitées dans des applications cloud spécifiques.

![Stratégie d’accès conditionnel avec un contrôle d’octroi nécessitant l’authentification multifacteur](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrictions appliquées par l’application

Les organisations peuvent utiliser ce contrôle pour demander à Azure AD de transmettre les informations d’appareil aux applications cloud sélectionnées. Les informations d’appareil permettent aux applications cloud de savoir si une connexion est établie à partir d’un appareil conforme ou joint au domaine et après l’expérience de la session. Ce contrôle prend uniquement en charge SharePoint Online et Exchange Online comme applications cloud sélectionnées. Lorsque cette option est sélectionnée, l’application cloud utilise les informations de l’appareil pour fournir aux utilisateurs, en fonction de l’état de l’appareil, une expérience limitée (lorsque l’appareil n’est pas géré) ou complète (lorsque l’appareil est géré et conforme).

Pour plus d’informations sur l’utilisation et la configuration de restrictions appliquées par l’application, consultez les articles suivants :

- [Activation d’un accès limité avec SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Activation d’un accès limité avec Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Contrôle d’application par accès conditionnel

Le contrôle d’application par accès conditionnel utilise une architecture de proxy inverse et est intégré de manière unique à l’accès conditionnel Azure AD. L’accès conditionnel Azure AD vous permet d’appliquer des contrôles d’accès aux applications de votre organisation en fonction de certaines conditions. Les conditions définissent à qui (utilisateur ou groupe d’utilisateurs), à quoi (quelles applications cloud) et où (quels emplacements et réseaux) s’applique une stratégie d’accès conditionnel. Une fois que vous avez déterminé les conditions, vous pouvez router les utilisateurs vers [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) afin de protéger les données avec un contrôle d’application par accès conditionnel en appliquant des contrôles d’accès et de session.

Le contrôle d’application par accès conditionnel permet de superviser et de contrôler en temps réel les sessions et accès utilisateur aux applications en fonction des stratégies d’accès et de session. Les stratégies d’accès et de session s’utilisent dans le portail Cloud App Security pour affiner davantage les filtres et définir les actions à effectuer sur un utilisateur. Avec les stratégies d’accès et de session, vous pouvez :

- Empêcher l’exfiltration de données : vous pouvez bloquer le téléchargement, les opérations couper, les opérations copier et l’impression des documents à contenu sensible sur les appareils non managés, par exemple.
- Protéger au téléchargement : au lieu de bloquer le téléchargement de documents à contenu sensible, vous pouvez exiger l’étiquetage et la protection des documents avec Azure Information Protection. Cette action garantit que les documents sont protégés et que l’accès utilisateur est limité dans une session potentiellement à risque.
- Empêcher le chargement de fichiers sans étiquette : avant qu’un fichier à contenu sensible puisse être chargé, distribué et utilisé par d’autres personnes, il est important de s’assurer que le fichier est configuré avec l’étiquette et la protection appropriées. Vous pouvez bloquer le chargement des fichiers sans étiquette qui ont du contenu sensible tant que l’utilisateur n’a pas classifié leur contenu.
- Vérifier la conformité des sessions utilisateur (préversion) : les utilisateurs à risque font l’objet d’une supervision lorsqu’ils se connectent à des applications et les actions qu’ils effectuent durant la session sont journalisées. Vous pouvez examiner et analyser le comportement des utilisateurs pour comprendre où et dans quelles conditions les stratégies de session doivent être appliquées à l’avenir.
- Bloquer l’accès (préversion) : vous pouvez bloquer l’accès de façon précise pour des applications et des utilisateurs spécifiques en fonction de plusieurs facteurs de risque. Par exemple, vous pouvez les bloquer s’ils utilisent des certificats clients comme moyen de gérer les appareils.
- Bloquer des activités personnalisées : certaines applications ont des scénarios uniques qui comportent un risque, par exemple, l’envoi de messages avec du contenu sensible dans des applications telles que Microsoft Teams ou Slack. Dans ce genre de scénarios, vous pouvez analyser les messages pour rechercher la présence de contenu sensible et les bloquer en temps réel.

Pour plus d’informations, consultez l’article [Déployer le contrôle d’application par accès conditionnel pour les applications proposées](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Fréquence de connexion

La fréquence de connexion définit la durée à l’issue de laquelle un utilisateur est invité à se reconnecter lorsqu’il tente d’accéder à une ressource.

Le paramètre de fréquence de connexion fonctionne avec les applications qui ont implémenté les protocoles OAUTH2 ou OIDC conformément aux standards. La plupart des applications natives de Microsoft pour Windows, Mac et Mobile, notamment les applications web suivantes, sont conformes au paramètre.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portail d’administration Microsoft 365
- Exchange Online
- SharePoint et OneDrive
- Client web Teams
- Dynamics CRM en ligne
- Portail Azure

Pour plus d’informations, consultez l’article [Configurer la gestion de session d’authentification avec l’accès conditionnel](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Session de navigateur persistante

Une session de navigateur persistante permet aux utilisateurs de rester connectés après la fermeture et la réouverture de la fenêtre du navigateur.

Pour plus d’informations, consultez l’article [Configurer la gestion de session d’authentification avec l’accès conditionnel](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

- [Mode Rapport uniquement](concept-conditional-access-report-only.md)
