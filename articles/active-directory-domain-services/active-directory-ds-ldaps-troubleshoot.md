---
title: Dépanner le protocole LDAP sécurisé (LDAPS) dans Azure Active Directory Domain Services | Microsoft Docs
description: Dépanner le protocole LDAPS (LDAP sécurisé) pour un domaine managé Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 97a2ee23435d0b29565bc3bf1dcb426e9e83fd31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416753"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Dépanner le protocole LDAPS (LDAP sécurisé) pour un domaine managé Azure AD Domain Services

## <a name="connection-issues"></a>Problèmes de connexion
Si vous éprouvez des difficultés à vous connecter au domaine managé à l’aide du protocole LDAP sécurisé :

* La chaîne émetteur du certificat LDAP sécurisé doit être approuvée sur le client. Vous pouvez choisir d’ajouter l’autorité de certification racine au magasin de certificats sur le client pour établir la relation d’approbation.
* Vérifiez que le client LDAP (par exemple, ldp.exe) se connecte au point de terminaison LDAP sécurisé à l’aide d’un nom DNS, et non de l’adresse IP.
* Vérifiez le nom de DNS auquel la client LDAP se connecte. Il doit correspondre à l’adresse IP publique pour le protocole LDAP sécurisé sur le domaine managé.
* Vérifiez que le certificat LDAP sécurisé pour votre domaine managé présente le nom DNS dans l’attribut Subject ou Subject Alternative Names.
* Les paramètres de groupe de sécurité réseau pour le réseau virtuel doivent autoriser le trafic vers le port 636 à partir d’Internet. Cette étape s’applique uniquement si vous avez activé l’accès LDAP sécurisé via Internet.


## <a name="need-help"></a>Vous avez besoin d’aide ?
Si vous ne parvenez toujours pas à vous connecter au domaine managé à l’aide du protocole LDAP sécurisé, [contactez l’équipe produit](active-directory-ds-contact-us.md) pour obtenir de l’aide. Pour que l’équipe produit puisse diagnostiquer au mieux le problème, fournissez les informations suivantes :
* Une capture d’écran de ldp.exe essayant d’établir la connexion et échouant.
* Votre ID de locataire Azure AD et le nom de domaine DNS de votre domaine managé.
* Nom d’utilisateur exact avez lequel vous essayez d’effectuer la liaison.


## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP query basics](https://technet.microsoft.com/library/aa996205.aspx)
* [Gérer la stratégie de groupe sur un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Groupes de sécurité réseau](../virtual-network/security-overview.md)
* [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/tutorial-filter-network-traffic.md)
