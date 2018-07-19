---
title: Demander l’affectation d’utilisateurs - Azure AD | Microsoft Docs
description: Demande de l’affectation d’utilisateurs pour les applications Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859460"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD et applications : Demander l’affectation d’utilisateurs
## <a name="requiring-user-assignment"></a>Demande de l’affectation de l’utilisateur
1. Connectez-vous au portail Azure avec un compte administrateur.
2. Dans le menu principal, cliquez sur **Tous les services**.
3. Choisissez le répertoire que vous utilisez pour l’application.
4. Cliquez sur l’onglet **Applications d’entreprise**.
5. Sélectionnez l'application dans la liste des applications associées à ce répertoire.
6. Cliquez sur l’onglet **Propriétés**.
7. Définissez **Affectation de l’utilisateur requise ?** sur Oui.
8. Cliquez sur le bouton **Enregistrer** en haut de l’écran.

Vous devez alors affecter des utilisateurs et/ou des groupes à l’application. Voir [Affectation d’utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md) et [Affectation de groupes à une application](active-directory-applications-guiding-developers-assigning-groups.md).

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
