---
title: Guide pratique pour retirer l’accès d’un utilisateur à une application dans Azure Active Directory
description: Découvrir comment retirer l’accès d’un utilisateur à une application dans Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 8544c35d77dfc32ece9b21a602f301ab15c22439
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288317"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Guide pratique pour retirer l’accès d’un utilisateur à une application

Cet article vous explique comment supprimer l’accès d’un utilisateur à une application.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Je souhaite supprimer l’attribution d’une application à un utilisateur ou groupe spécifique

Pour supprimer l’attribution d’une application à un utilisateur ou groupe, suivez les étapes répertoriées dans l’article [Supprimer l’attribution d’une application à un utilisateur ou groupe à partir d’une application d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Je souhaite désactiver tous les accès à une application pour chaque utilisateur

Pour désactiver toutes les connexions des utilisateurs à une application, suivez les étapes répertoriées dans l’article [Désactiver les connexions des utilisateurs à une application d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Je veux complètement supprimer une passerelle d’application

La [Série de guides de démarrage rapide sur la gestion des applications](delete-application-portal.md) contient des instructions sur la suppression d’une application de votre locataire Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Je veux désactiver toutes les futures opérations de consentement de l’utilisateur pour n’importe quelle application

La désactivation du consentement de l’utilisateur pour votre annuaire entier empêche les utilisateurs finaux de donner leur consentement pour n’importe quelle application. Les administrateurs peuvent toujours donner leur consentement au nom de l’utilisateur. Pour plus d’informations sur le consentement de l’application et sur les conditions pour donner ou refuser ce consentement, lisez la rubrique [Comprendre le consentement de l’utilisateur et de l’administrateur](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Voir aussi [Autorisations et consentement](../develop/v2-permissions-and-consent.md).

Pour **désactiver toutes les futures opérations de consentement de l’utilisateur dans l’ensemble de votre annuaire** , procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrir le panneau **Extension Azure Active Directory** 

3.  Cliquez sur **Applications d’entreprise** dans le menu de navigation.

5.  Cliquez sur **Paramètres utilisateur**.

6.  Définissez l’option **Les utilisateurs peuvent autoriser les applications à accéder à leurs données** sur **Non** , puis cliquez sur le bouton Enregistrer.


## <a name="next-steps"></a>Étapes suivantes

[Gestion de l’accès aux applications](what-is-access-management.md)
