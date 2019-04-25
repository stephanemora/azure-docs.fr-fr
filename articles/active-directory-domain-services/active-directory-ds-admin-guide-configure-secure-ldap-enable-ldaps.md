---
title: Activer le protocole LDAP sécurisé (LDAPS) dans Azure Active Directory Domain Services | Microsoft Docs
description: Activer le protocole LDAP sécurisé (LDAPS) pour un domaine managé Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359829"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Activer le protocole LDAP sécurisé (LDAPS) pour un domaine managé Azure AD Domain Services

## <a name="before-you-begin"></a>Avant de commencer
Accomplissez la [Tâche 2 : Exporter le certificat du protocole LDAP sécurisé vers un fichier .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tâche 3 : Activer le protocole LDAP sécurisé pour le domaine géré à l’aide du portail Azure
Exécutez les étapes de configuration suivantes pour activer le protocole LDAP sécurisé :

1. Accédez au **[portail Azure](https://portal.azure.com)**.

2. Recherchez « Domain Services » dans la zone de recherche **Rechercher des ressources**. Sélectionnez **Azure AD Domain Services** dans les résultats de la recherche. La page **Azure AD Domain Services** répertorie votre domaine géré.

    ![Rechercher le domaine managé en cours d’approvisionnement](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Pour plus d’informations sur le domaine, cliquez sur le nom du domaine managé (par exemple, « contoso100.com »).

    ![Domain Services - État d’approvisionnement](./media/getting-started/domain-services-provisioning-state.png)

3. Cliquez sur **LDAP sécurisé** dans le volet de navigation.

    ![Domain Services - Page LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Par défaut, l’accès LDAP sécurisé à votre domaine managé est désactivé. Basculez **LDAP sécurisé** sur **Activer**.

    ![Activer LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Par défaut, l’accès LDAP sécurisé à votre domaine managé via Internet est désactivé. Si nécessaire, basculez **Autorisez l’accès LDAP sécurisé sur Internet** sur **Activer**.

    > [!WARNING]
    > Quand vous activez l’accès LDAP sécurisé sur Internet, votre domaine est vulnérable aux attaques par force brute via Internet. Par conséquent, nous vous conseillons de définir un groupe de sécurité réseau pour bloquer l’accès aux plages d’adresses IP source requises. Consultez les instructions pour [Verrouiller l’accès LDAPS à votre domaine géré via internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Cliquez sur l’icône de dossier suivant le fichier **.PFX avec certificat LDAP sécurisé**. Spécifiez le chemin d’accès au fichier PFX avec le certificat pour l’accès LDAP sécurisé au domaine managé.

7. Spécifiez le **mot de passe pour déchiffrer le fichier .PFX**. Fournissez le mot de passe que vous avez utilisé lors de l’exportation du certificat vers le fichier PFX.

8. Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer**.

9. Vous voyez une notification vous informant que le LDAP sécurisé est en cours de configuration pour le domaine managé. Tant que cette opération n’est pas terminée, vous ne pouvez pas modifier d’autres paramètres du domaine.

    ![Configuration de LDAP sécurisé pour le domaine managé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> L’activation du protocole LDAP sécurisé pour votre domaine géré dure 10 à 15 minutes. Si le certificat LDAP sécurisé fourni ne correspond pas aux critères requis, le protocole LDAP sécurisé n’est pas activé pour votre annuaire et une erreur s’affiche. Par exemple, le nom de domaine est incorrect, le certificat a expiré ou arrivera bientôt à expiration. Dans ce cas, réessayez avec un certificat valide.
>
>

## <a name="next-step"></a>Étape suivante
[Tâche 4 : Configurer DNS pour accéder au domaine managé à partir d’Internet](active-directory-ds-ldaps-configure-dns.md)
