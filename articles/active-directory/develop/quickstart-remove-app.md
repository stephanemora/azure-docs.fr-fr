---
title: Supprimer une application inscrite à l’aide de la plateforme d’identités Microsoft | Azure
description: Découvrez comment supprimer une application inscrite à l’aide de la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d71489194422a785ee98ec70a07c765c3d686808
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182610"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform-preview"></a>Démarrage rapide : Supprimer une application inscrite auprès de la plateforme d’identités Microsoft (préversion)

Les développeurs d’entreprise et les fournisseurs SaaS qui ont inscrit des applications avec la plateforme d’identités Microsoft peuvent avoir besoin de supprimer l’inscription d’une application.

Dans ce guide de démarrage rapide, vous apprenez à :

* Supprimer une application créée par vous ou votre organisation
* Supprimer une application créée par une autre organisation

## <a name="prerequisites"></a>Prérequis

Pour commencer, assurez-vous de remplir ces conditions préalables :

* Disposez d’un locataire auprès duquel des applications sont inscrites. Pour savoir comment ajouter et inscrire des applications, consultez [Inscrire une application avec la plateforme d’identités Microsoft](quickstart-register-app.md).
* Intégrez l’expérience de préversion pour les inscriptions d’applications dans le portail Azure. Les étapes figurant dans ce démarrage rapide correspondent à la nouvelle interface utilisateur et ne fonctionnent que si vous participez à l’expérience de préversion.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Supprimer une application créée par vous ou votre organisation

Les applications que vous ou votre organisation avez enregistrées sont représentées par un objet d’application et un objet de principal de service au sein de votre locataire. Pour plus d’informations, voir [Objets principal du service et application](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Supprimer une application

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
2. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
3. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**,puis **Inscriptions d’applications (préversion)**. Recherchez et sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa page **Vue d’ensemble**.
4. À partir de la page **Vue d’ensemble**, sélectionnez **Supprimer**.
5. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’application.

  > [!NOTE]
  > Pour supprimer une application, vous devez être répertorié en tant que propriétaire de l’application ou disposer des privilèges d’administrateur.

## <a name="remove-an-application-authored-by-another-organization"></a>Supprimer une application créée par une autre organisation

Si les **Inscriptions d’applications** s’affichent dans le contexte d’un locataire, un sous-ensemble des applications qui s’affichent sous l’onglet **Toutes les applications** provient d’un autre locataire et a été inscrit dans votre locataire lors du processus de consentement. Ces applications sont représentées dans votre locataire par un objet principal du service, sans objet d’application correspondant. Pour plus d’informations sur les différences entre les objets de principal du service et d’application, consultez [Objets application et principal du service dans Azure AD](active-directory-application-objects.md).

Afin de pouvoir supprimer l’accès d’une application à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit supprimer son principal du service. L’administrateur doit avoir un accès d’administrateur général et peut supprimer l’application ou utiliser les [cmdlets PowerShell Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151) au sein du portail Azure pour supprimer l’accès.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications :

* [Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)
* [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md)
* [Configurer une application pour exposer les API web](quickstart-configure-app-expose-web-apis.md)
* [Modifier les comptes pris en charge par une application](quickstart-modify-supported-accounts.md)
