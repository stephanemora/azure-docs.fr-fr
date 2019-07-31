---
title: 'Azure Active Directory Domain Services : Activer le service Profil utilisateur SharePoint | Microsoft Docs'
description: Configurer les domaines gérés des services de domaine Azure Active Directory pour prendre en charge la synchronisation de profils pour SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234097"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurer un domaine géré pour prendre en charge la synchronisation de profils pour SharePoint Server
SharePoint Server inclut un service Profil utilisateur qui est utilisé pour la synchronisation des profils utilisateurs. Pour configurer le service Profil utilisateur, les autorisations appropriées doivent être accordées sur un domaine Active Directory. Pour plus d’informations, consultez [Accorder des autorisations Active Directory Domain Services pour la synchronisation de profils dans SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Cet article explique comment vous pouvez configurer des domaines gérés des services de domaine Azure Active Directory pour déployer le service de synchronisation de profils utilisateurs SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Le groupe « Comptes de Service de contrôleur de domaine AAD »
Un groupe de sécurité appelé « **Comptes de service de contrôleur de domaine AAD** » est disponible dans l’unité d’organisation « Utilisateurs » sur votre domaine géré. Vous pouvez voir ce groupe dans le composant logiciel enfichable MMC **Utilisateurs et ordinateurs Active Directory** sur votre domaine géré.

![Groupe de sécurité Comptes de service de contrôleur de domaine AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Les privilèges suivants sont délégués aux membres de ce groupe de sécurité :
- Le privilège « Répliquer les modifications de répertoire » à la racine DSE du domaine géré.
- Le privilège « Répliquer les changements de répertoire » sur le contexte de nommage de configuration (cn=conteneur de configuration) du domaine géré.

Ce groupe de sécurité est également membre du groupe prédéfini **Accès compatible pré-Windows 2000**.

![Groupe de sécurité Comptes de service de contrôleur de domaine AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Activer votre domaine géré pour prendre en charge la synchronisation de profils utilisateurs SharePoint Server
Vous pouvez ajouter le compte de service utilisé pour la synchronisation de profils utilisateurs SharePoint au groupe **Comptes de service de contrôleur de domaine AAD**. Par conséquent, le compte de synchronisation obtient les privilèges appropriés pour répliquer les modifications apportées au répertoire. Cette étape de configuration permet à la synchronisation de profils utilisateurs SharePoint Server de fonctionner correctement.

![Comptes de service de contrôleur de domaine AAD – ajouter des membres](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Comptes de service de contrôleur de domaine AAD – ajouter des membres](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Contenu connexe
* [Informations techniques de référence – Accorder des autorisations Active Directory Domain Services pour la synchronisation de profils dans SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
