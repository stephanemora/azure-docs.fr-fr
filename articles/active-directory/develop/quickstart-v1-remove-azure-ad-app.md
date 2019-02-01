---
title: Supprimer une application d’Azure Active Directory
description: Découvrez comment supprimer une application d’Azure Active Directory (Azure AD).
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
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 805a917cbb4d867ee866ec99c8e90ea883527856
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100952"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Démarrage rapide : Supprimer une application d’Azure Active Directory

Les développeurs d’entreprise et les fournisseurs SaaS qui ont inscrit des applications avec Azure Active Directory (Azure AD) peuvent avoir besoin de supprimer l’inscription d’une application de leur locataire Azure AD.

Dans ce guide de démarrage rapide, vous apprenez à :

* [Supprimer une application créée par votre organisation](#removing-an-application-authored-by-your-organization)
* [Supprimer une application multilocataire autorisée par une autre organisation](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin d’un locataire Azure AD auprès duquel des applications sont inscrites.

* Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](quickstart-create-new-tenant.md).
* Pour apprendre à ajouter et inscrire des applications dans votre locataire, consultez [Intégration d’applications dans Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Suppression d’une application créée par votre organisation

Les applications inscrites par votre organisation sont affichées sous le filtre **Mes applications** sur la page **Inscriptions d’application** principale de votre locataire. Ces applications sont celles que vous avez inscrites soit manuellement au moyen du portail Azure, soit par un programme par le biais de PowerShell ou de l’API Microsoft Graph. Plus précisément, ces applications sont représentées dans votre locataire par des objets Application et Principal du service. Pour plus d’informations sur ces éléments, voir [Objets principal du service et application](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Pour supprimer une application à locataire unique de votre répertoire

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Si votre compte vous propose plusieurs accès, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez **Inscriptions des applications**, puis recherchez et sélectionnez l’application que vous souhaitez configurer.
    Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
1. Dans la page d’inscription principale de l’application, sélectionnez **Supprimer**.
1. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’application.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Pour supprimer une application multilocataire de son répertoire de base

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Si votre compte vous propose plusieurs accès, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez **Inscriptions des applications**, puis recherchez et sélectionnez l’application que vous souhaitez configurer.
    Vous accédez à la page d’inscription principale de l’application, qui ouvre la page **Paramètres** pour l’application.
1. À partir de la page **Paramètres**, choisissez **Propriétés** et basculez **Multilocataire** sur **Non** pour commencer par passer votre application en locataire unique, puis sélectionnez **Enregistrer**.
    Les objets principal du service de l’application restent dans les locataires de toutes les organisations qui ont déjà consenti.
1. Dans la page d’inscription principale de l’application, sélectionnez **Supprimer**.
1. Sélectionnez **Oui** pour confirmer que vous souhaitez supprimer l’application.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Suppression d’une application multilocataire autorisée par une autre organisation

Un sous-ensemble des applications qui s’affichent sous le filtre **Toutes les applications** (en excluant les inscriptions de **Mes applications**) sur la page **Inscriptions d’application** principale de votre locataire, est constitué des applications multilocataires.

En termes techniques, ces applications multilocataires proviennent d’un autre locataire et ont été enregistrées dans votre locataire au cours du processus de consentement. Ces applications sont représentées dans votre locataire par un objet principal du service, sans objet d’application correspondant. Pour plus d’informations sur les différences entre les objets de principal du service et d’application, consultez [Objets application et principal du service dans Azure AD](app-objects-and-service-principals.md).

Pour supprimer l’accès d’une application multilocataire à votre répertoire (après avoir donné son consentement), l’administrateur de l’entreprise doit supprimer son principal du service. L’administrateur doit avoir un accès d’administrateur général et peut supprimer ou utiliser les [applets de commande PowerShell Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications pour les applications utilisant le point de terminaison Azure AD v1.0 :

- [Intégration d’applications dans Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Mise à jour d’une application dans Azure Active Directory](quickstart-v1-update-azure-ad-app.md)
