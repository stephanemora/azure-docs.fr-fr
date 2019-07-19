---
title: 'Azure Active Directory Domain Services : Fonctionnalités | Microsoft Docs'
description: Caractéristiques des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e49a37ec95a8cf26a2c63bd90759da35fc537e41
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474241"
---
# <a name="azure-ad-domain-services"></a>Services de domaine Azure AD
## <a name="features"></a>Caractéristiques
Les fonctionnalités suivantes sont disponibles dans les domaines gérés des services de domaine Azure AD.

* **Expérience de déploiement simple :** Vous pouvez activer Azure Active Directory Domain Services pour répertoire Azure AD en quelques clics. Votre domaine géré inclut des comptes d’utilisateur uniquement dans le cloud et des comptes d’utilisateur synchronisés à partir d’un répertoire local.
* **Prise en charge de la jonction de domaine :** Vous pouvez facilement joindre des ordinateurs au domaine dans le réseau virtuel Azure où votre domaine géré est disponible. La jonction de domaine sur les systèmes d’exploitation serveur et client Windows fonctionne en toute transparence par rapport aux domaines pris en charge par les services de domaine Azure AD. Vous pouvez également recourir aux outils de jonction de domaine automatisée par rapport à ces domaines.
* **Une instance de domaine par annuaire Azure AD :** Vous pouvez créer un seul domaine Active Directory par annuaire Azure AD.
* **Créer des domaines avec des noms personnalisés :** Vous pouvez créer des domaines avec des noms personnalisés (par exemple, « contoso100.com ») à l’aide d’Azure AD Domain Services. Vous pouvez utiliser des noms de domaine vérifiés ou non vérifiés. Si vous le souhaitez, vous pouvez également créer un domaine avec le suffixe de domaine intégré (c’est-à-dire, * .onmicrosoft.com) proposé par votre annuaire Azure AD.
* **Intégration à Azure AD :** Vous n’avez pas besoin de configurer ou de gérer la réplication sur Azure AD Domain Services. Les comptes d’utilisateur, les appartenances aux groupes et les informations d’identification (mots de passe) issus de votre annuaire Azure AD sont automatiquement disponibles dans les services de domaine Azure AD. Les nouveaux utilisateurs ou groupes ou les modifications apportées aux attributs dans votre client Azure AD ou dans votre annuaire local sont automatiquement synchronisés avec les services de domaine Azure AD.
* **Authentification NTLM et Kerberos :** Grâce à la prise en charge de l’authentification NTLM et Kerberos, vous pouvez déployer des applications qui reposent sur l’authentification intégrée de Windows.
* **Utilisation de vos informations d’identification/mots de passe d’entreprise :** Les mots de passe des utilisateurs dans votre client Azure AD fonctionnent avec Azure AD Domain Services. Les utilisateurs peuvent utiliser leurs informations d’identification d’entreprise pour joindre des machines à des domaines, se connecter de manière interactive ou via le bureau à distance et s’authentifier sur le domaine managé.
* **Prise en charge des liaisons LDAP et des lectures LDAP :** Vous pouvez utiliser des applications qui reposent sur les liaisons LDAP pour authentifier les utilisateurs dans des domaines pris en charge par Azure AD Domain Services. En outre, les applications qui utilisent des opérations de lecture LDAP pour interroger les attributs des utilisateurs ou ordinateurs à partir de l’annuaire peuvent également fonctionner auprès des services de domaine Azure AD.
* **LDAP sécurisé (LDAPS) :** Vous pouvez activer l’accès au répertoire via LDAP sécurisé (LDAPS). L’accès LDAP sécurisé est disponible au sein du réseau virtuel par défaut. Toutefois, vous pouvez activer également l’accès LDAP sécurisé via internet.
* **Stratégie de groupe :** Vous pouvez utiliser un seul objet de stratégie de groupe intégré pour les conteneurs des utilisateurs et des ordinateurs afin d’imposer la conformité aux stratégies de sécurité nécessaires pour les comptes d’utilisateur et aux ordinateurs joints au domaine. Vous pouvez également créer vos propres objets de stratégie de groupe personnalisés et les affecter à des unités d’organisation personnalisées pour [gérer la stratégie de groupe](manage-group-policy.md).
* **Gérer DNS :** Les membres du groupe AAD DC Administrators peuvent gérer les DNS de votre domaine géré à l’aide d'outils d’administration DNS courants comme le composant logiciel enfichable DNS Administration MMC.
* **Créer des unités organisationnelles (UO) personnalisées :** Les membres du groupe « AAD DC Administrators » peuvent créer des unités d’organisation personnalisées dans le domaine géré. Ces utilisateurs bénéficient de privilèges administratifs complets sur les unités organisationnelles personnalisées et peuvent donc ajouter/supprimer des comptes de service, des ordinateurs, des groupes, etc. au sein de ces unités organisationnelles personnalisées.
* **Disponible dans plusieurs régions Azure :** Pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .
* **Haute disponibilité :** Azure AD Domain Services offre une haute disponibilité pour votre domaine. Vous bénéficiez ainsi d’une disponibilité de service et d’une tolérance aux pannes plus élevées. La surveillance intégrée de l’intégrité permet de corriger automatiquement les défaillances en configurant de nouvelles instances pour remplacer les instances défectueuses et fournir un service continu pour votre domaine.
* **Protection par verrouillage de compte Active Directory :** Les comptes d’utilisateurs sont verrouillés pendant 30 minutes si cinq mots de passe incorrects sont utilisés en l’espace de 2 minutes. Les comptes sont déverrouillés automatiquement après 30 minutes.
* **Utilisation d’outils de gestion familiers :** Vous pouvez utiliser les outils de gestion Windows Server Active Directory familiers tels que le Centre d’administration Active Directory ou Active Directory PowerShell pour administrer des domaines gérés.
