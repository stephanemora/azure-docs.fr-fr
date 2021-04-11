---
title: 'Procédure : Supprimer une application inscrite de la plateforme d’identités Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de procédure, vous allez découvrir comment supprimer une application inscrite de la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079724"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Guide pratique pour supprimer une application inscrite à l’aide de la plateforme d’identités Microsoft

Les développeurs d’entreprise et les fournisseurs SaaS qui ont inscrit des applications dans la plateforme d’identités Microsoft peuvent avoir besoin de supprimer l’inscription d’une application.

Dans les sections suivantes, vous allez apprendre à :

* Supprimer une application créée par vous ou votre organisation
* Supprimer une application créée par une autre organisation

## <a name="prerequisites"></a>Prérequis

* Une [application inscrite dans votre locataire Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Supprimer une application créée par vous ou votre organisation

Les applications que vous ou votre organisation avez enregistrées sont représentées par un objet d’application et un objet de principal de service au sein de votre locataire. Pour plus d’informations, voir [Objets principal du service et application](./app-objects-and-service-principals.md).

> [!NOTE]
> La suppression d’une application entraîne également la suppression de son objet de principal de service dans le répertoire de base de l’application. Pour les applications multilocataires, les objets de principal de service situés dans les autres répertoires ne sont pas supprimés.

Pour supprimer une application, vous devez être listé en tant que propriétaire de l’application ou disposer de privilèges d’administrateur.

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Annuaire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu supérieur pour sélectionner le locataire dans lequel l’application est inscrite.
1. Recherchez et sélectionnez **Azure Active Directory**. 
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa page **Vue d’ensemble**.
1. À partir de la page **Vue d’ensemble**, sélectionnez **Supprimer**.
1. Lisez les conséquences de la suppression.  Activez la case à cocher si elle apparaît en bas du volet.
1. Sélectionnez **Supprimer** pour confirmer que vous souhaitez supprimer l’application.

## <a name="remove-an-application-authored-by-another-organization"></a>Supprimer une application créée par une autre organisation

Si les **Inscriptions d’applications** s’affichent dans le contexte d’un locataire, un sous-ensemble des applications qui s’affichent sous l’onglet **Toutes les applications** provient d’un autre locataire et a été inscrit dans votre locataire lors du processus de consentement. Ces applications sont représentées dans votre locataire par un objet principal du service, sans objet d’application correspondant. Pour plus d’informations sur les différences entre les objets de principal du service et d’application, consultez [Objets application et principal du service dans Azure AD](./app-objects-and-service-principals.md).

Afin de pouvoir supprimer l’accès d’une application à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit supprimer son principal du service. L’administrateur doit disposer du rôle Administrateur général. Il peut supprimer l’application via le portail Azure ou utiliser les [applets de commande PowerShell pour Azure AD](/previous-versions/azure/jj151815(v=azure.100)) afin de retirer tout accès.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [objets d’application et de principal de service](app-objects-and-service-principals.md) dans la plateforme d’identités Microsoft.
