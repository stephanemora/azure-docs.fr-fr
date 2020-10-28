---
title: Synchronisation d’annuaires avec Azure Active Directory
description: Guide architectural pour atteindre ce modèle de synchronisation
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113891"
---
# <a name="directory-synchronization"></a>Synchronisation de répertoires

De nombreuses organisations disposent d’une infrastructure hybride englobant les composants locaux et cloud. La synchronisation des identités des utilisateurs entre les annuaires locaux et dans le cloud permet aux utilisateurs d’accéder aux ressources avec un ensemble unique d’informations d’identification. 

La synchronisation est le processus visant à : 

* créer un objet sous certaines conditions ;
* tenir l’objet à jour ;
* supprimer l’objet lorsque les conditions ne sont plus remplies. 

L’approvisionnement local implique l’approvisionnement de sources locales (par exemple Active Directory) vers Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Cas d'utilisation

Vous devez synchroniser les données d’identité de vos environnements Active Directory locaux vers Azure AD.

![Diagramme architectural](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Composants du système

* **Utilisateur**  : Accède à une application à l’aide d’Azure AD.

* **Navigateur web**  : Composant avec lequel l’utilisateur interagit pour accéder à l’URL externe de l’application.

* **Application**  : Application web qui s’appuie sur l’utilisation d’Azure AD à des fins d’authentification et d’autorisation.

* **Azure AD**  : Synchronise les informations d’identité à partir de l’annuaire local de l’organisation via Azure AD Connect. 

* **Azure AD Connect**  : Outil permettant de se connecter à des infrastructures d’identité locales pour Microsoft Azure AD. L’Assistant et les expériences guidées vous aident à déployer et à configurer les conditions préalables et les composants requis pour la connexion, notamment la synchronisation et l’ouverture de session d’annuaires Active Directory vers Azure AD. 

* **Active Directory** : Active Directory est un service d’annuaire inclus dans la plupart des systèmes d’exploitation Windows Server. Les serveurs qui exécutent Active Directory Domain Services (AD DS) sont appelés contrôleurs de domaine. Ils authentifient et autorisent tous les utilisateurs et ordinateurs du domaine.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implémenter la synchronisation d’annuaires avec Azure AD

* [Qu’est-ce que le provisionnement d’identités ?](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [Identité hybride : outils d’intégration d’annuaire](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Feuille de route d’installation d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
