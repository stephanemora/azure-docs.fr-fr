---
title: 'Démarrage rapide : Supprimer une application inscrite de la plateforme d’identités Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment supprimer une application inscrite de la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612385"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Démarrage rapide : Supprimer une application inscrite à l’aide de la plateforme d’identités Microsoft

Les développeurs d’entreprise et les fournisseurs SaaS qui ont inscrit des applications avec la plateforme d’identités Microsoft peuvent avoir besoin de supprimer l’inscription d’une application.

Dans ce guide de démarrage rapide, vous apprenez à :

* Supprimer une application créée par vous ou votre organisation
* Supprimer une application créée par une autre organisation

## <a name="prerequisites"></a>Prérequis

* Exécution du [Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Supprimer une application créée par vous ou votre organisation

Les applications que vous ou votre organisation avez enregistrées sont représentées par un objet d’application et un objet de principal de service au sein de votre locataire. Pour plus d’informations, voir [Objets principal du service et application](./app-objects-and-service-principals.md).

Pour supprimer une application, vous devez être répertorié en tant que propriétaire de l’application ou disposer des privilèges d’administrateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis **Inscriptions d’applications**. Recherchez et sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa page **Vue d’ensemble**.
1. À partir de la page **Vue d’ensemble**, sélectionnez **Supprimer**.
1. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’application.

## <a name="remove-an-application-authored-by-another-organization"></a>Supprimer une application créée par une autre organisation

Si les **Inscriptions d’applications** s’affichent dans le contexte d’un locataire, un sous-ensemble des applications qui s’affichent sous l’onglet **Toutes les applications** provient d’un autre locataire et a été inscrit dans votre locataire lors du processus de consentement. Ces applications sont représentées dans votre locataire par un objet principal du service, sans objet d’application correspondant. Pour plus d’informations sur les différences entre les objets de principal du service et d’application, consultez [Objets application et principal du service dans Azure AD](./app-objects-and-service-principals.md).

Afin de pouvoir supprimer l’accès d’une application à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit supprimer son principal du service. L’administrateur doit avoir un accès d’administrateur général et peut supprimer l’application ou utiliser les [cmdlets PowerShell Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151) au sein du portail Azure pour supprimer l’accès.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les objets d’application et de principal de service dans la plateforme d’identités Microsoft :

> [!div class="nextstepaction"]
> [Objets application et principal du service dans Azure Active Directory](app-objects-and-service-principals.md)