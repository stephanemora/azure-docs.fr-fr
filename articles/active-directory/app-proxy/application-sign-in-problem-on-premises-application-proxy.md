---
title: Problèmes rencontrés lors de la connexion à une application locale à l'aide du Proxy d'application Azure Active Directory
description: Résolution des problèmes couramment rencontrés lorsque vous ne parvenez pas à vous connecter à une application locale intégrée à l'aide du Proxy d'application Azure AD
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 8c3070e42b8ccf1d73d2466524d272b73f4e8d8b
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227089"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-active-directory-application-proxy"></a>Problèmes rencontrés lors de la connexion à une application locale à l'aide du Proxy d'application Azure Active Directory

Si vous rencontrez un problème de connexion à une application locale, essayez de le résoudre en suivant les étapes ci-dessous.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Je parviens à charger mon application, mais quelque chose semble ne pas fonctionner sur la page

Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.

  * [Je peux accéder à mon application, mais la page de l’application ne s’affiche pas correctement](application-proxy-page-appearance-broken-problem.md)
  * [Je peux accéder à mon application, mais l’application met trop de temps à charger](application-proxy-page-load-speed-problem.md)
  * [Je peux accéder à mon application, mais le lien affiché sur la page de l’application ne fonctionne pas](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Je rencontre un problème de connectivité avec mon application
  Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.
  * [Je ne sais pas quels ports ouvrir pour mon application](application-proxy-add-on-premises-application.md)
  * [J’ai rencontré un problème, car aucun connecteur de travail ne fonctionnait dans un groupe de connecteurs pour mon application](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Je rencontre un problème lors de la configuration du proxy d’application Azure AD dans le portail d’administration
  Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.
  * [J’ai des difficultés pour configurer une application du proxy d’application](application-proxy-config-how-to.md)
  * [Je ne sais pas comment configurer l’authentification unique pour mon application du proxy d’application](application-proxy-config-sso-how-to.md)
  * [J’ai rencontré un problème lors de la création de mon application dans le portail d’administration](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Je rencontre un problème lors de la configuration de l’authentification back-end pour mon application
  Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.
  * [Je ne sais pas comment configurer la délégation Kerberos contrainte](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Je ne sais pas comment configurer mon application avec PingAccess](application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Je rencontre un problème lors de la connexion à mon application
  Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.
  * [J’obtiens une erreur « Impossible d’accéder à cette application d’entreprise »](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Je rencontre un problème avec le connecteur d’agent du proxy d’application
  Les documents suivants peuvent vous aider à résoudre certains des problèmes les plus courants de cette catégorie.
  * [Je rencontre des problèmes lors de l’installation du connecteur d’agent de proxy d’application](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Étapes suivantes
[Offrir un accès à distance sécurisé aux applications locales](application-proxy.md)