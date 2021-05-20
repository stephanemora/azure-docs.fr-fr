---
title: Partager un lien pour demander un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment partager un lien pour demander un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25cbef009f73ac788da36d60f2313dcfe9b49ebe
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713555"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Partager un lien pour demander un package d’accès dans la gestion des droits d’utilisation Azure AD

La plupart des utilisateurs de votre répertoire peuvent se connecter au portail Mon Accès et voir automatiquement la liste des packages d’accès qu’ils peuvent demander. Toutefois, pour les utilisateurs partenaires externes qui ne figurent pas encore dans votre répertoire, vous devrez leur envoyer un lien qui leur servira à demander un package d'accès. 

Tant que le catalogue du package d'accès est [activé pour les utilisateurs externes](entitlement-management-catalog-create.md) et que vous avez défini une [stratégie pour le répertoire de l'utilisateur externe](entitlement-management-access-package-request-policy.md), ce dernier peut utiliser le lien du portail Mon Accès afin de demander le package d'accès.

## <a name="share-link-to-request-an-access-package"></a>Partager un lien pour demander un package d’accès

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès.

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Sur la page Vue d’ensemble, copiez le **lien du portail Mon Accès**.

    ![Vue d’ensemble du package d’accès - Lien du portail Mon Accès](./media/entitlement-management-shared/my-access-portal-link.png)

    Il est important de copier l'intégralité du lien du portail Mon Accès lors de son envoi à un partenaire interne. Cela garantit que le partenaire aura accès au portail de votre répertoire pour effectuer sa demande. Le lien commence par `myaccess`, comprend une indication de répertoire et se termine par un ID de package d’accès.  (Pour le gouvernement des États-Unis, le domaine figurant dans le lien du portail Mon Accès sera `myaccess.microsoft.us`.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envoyez par e-mail ou tout autre moyen le lien à votre partenaire commercial externe. Ce dernier peut partager le lien avec ses utilisateurs afin de demander le package d'accès.

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)