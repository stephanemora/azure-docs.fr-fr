---
title: Comprendre comment les utilisateurs sont affectés aux applications dans Azure Active Directory
description: Comprendre comment les utilisateurs sont affectés à une application qui utilise Azure Active Directory pour la gestion des identités.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604153"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Comprendre comment les utilisateurs sont affectés aux applications dans Azure Active Directory
Cet article vous explique comment les utilisateurs sont affectés à une application dans votre client.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Comment les utilisateurs sont-ils affectés à une application dans Azure AD ?
Pour qu’un utilisateur puisse accéder à une application, il doit tout d’abord être affecté à cette application d’une certaine manière. L’affectation peut être effectuée par un administrateur, un délégué de l’entreprise ou, parfois, par l’utilisateur lui-même. Voici une description des méthodes permettant d’affecter des utilisateurs à des applications :

*  Un administrateur [affecte un utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) directement à l’application
*  Un administrateur [affecte un groupe](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) dont l’utilisateur est membre à l’application, y compris :
    * Un groupe synchronisé localement
    * Un groupe de sécurité statique créé dans le cloud
    * Un [groupe de sécurité dynamique](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) créé dans le cloud
    * Un groupe Microsoft 365 créé dans le cloud
    * Le groupe [Tous les utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Un administrateur active l’[accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) pour autoriser un utilisateur à ajouter une application à l’aide de la fonctionnalité **Ajouter une application** de [Mes applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **sans approbation de l’entreprise**.
*  Un administrateur active l’[accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) pour autoriser un utilisateur à ajouter une application à l’aide de la fonctionnalité **Ajouter une application** de [Mes accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction), mais uniquement **après approbation d’un ensemble sélectionné d’approbateurs d’entreprise**.
*  Un administrateur active la [gestion des groupes en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) pour permettre à un utilisateur de joindre un groupe auquel une application est affectée, **sans approbation d’entreprise**
*  Un administrateur active la [gestion des groupes en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) pour permettre à un utilisateur de joindre un groupe auquel une application est affectée, mais uniquement **après approbation d’un ensemble sélectionné d’approbateurs d’entreprise**
*  Un administrateur affecte une licence à un utilisateur pour une première application, par exemple [Microsoft 365](https://products.office.com/)
*  Un administrateur affecte une licence à un groupe dont l’utilisateur est membre pour une première application, par exemple [Microsoft 365](https://products.office.com/)
*  Un [administrateur donne son consentement à une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) pour être utilisée par tous les utilisateurs, puis un utilisateur se connecte à l’application
* Un utilisateur [donne lui-même son consentement à une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) en se connectant à l’application

## <a name="next-steps"></a>Étapes suivantes
* [Série de guides de démarrage rapide sur la gestion des applications](view-applications-portal.md)
* [Qu’est-ce que la gestion des applications ?](what-is-application-management.md)
* [Qu’est-ce que l’authentification unique ?](what-is-single-sign-on.md)
