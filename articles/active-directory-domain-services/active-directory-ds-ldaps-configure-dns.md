---
title: Configurer DNS pour accéder à un domaine managé à l’aide du protocole LDAPS sur Internet | Microsoft Docs
description: Configurer DNS pour accéder à un domaine managé Azure AD Domain Services à l’aide du protocole LDAPS sur Internet
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 669e0392cb77434c372c9af3c4d467d19cff8abd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501732"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Configurer DNS pour accéder à un domaine managé Azure AD Domain Services à l’aide du protocole LDAP sécurisé (LDAPS)

## <a name="before-you-begin"></a>Avant de commencer
Effectuez la [Tâche 3 : Activer LDAP sécurisé pour le domaine managé à l’aide du portail Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tâche 4 : Configurer DNS pour accéder au domaine managé à partir d’Internet
> [!TIP]
> **Tâche facultative** : ignorez cette étape de configuration si vous n’envisagez pas d’accéder au domaine géré via le protocole LDAP sécurisé sur Internet.
>
>

Avant de commencer cette tâche, effectuez les étapes décrites dans la [Tâche 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Après avoir activé l’accès LDAP sécurisé via Internet, vous devez mettre à jour DNS afin que les ordinateurs clients puissent détecter ce domaine managé. Une adresse IP externe s’affiche sur l’onglet **Propriétés** dans **ADRESSE IP EXTERNE POUR L’ACCÈS LDAPS**.

Configurez votre fournisseur DNS externe afin que le nom DNS du domaine géré (par exemple, 'ldaps.contoso100.com') pointe vers cette adresse IP externe. Par exemple, créons l’entrée DNS suivante :

    ldaps.contoso100.com  -> 52.165.38.113

Et voilà ! Vous êtes maintenant prêt à vous connecter au domaine managé à l’aide du protocole LDAP sécurisé sur Internet.

> [!WARNING]
> N’oubliez pas que les ordinateurs clients doivent approuver l’émetteur du certificat LDAP sécurisé afin d’être en mesure de se connecter au domaine géré à l’aide du protocole LDAP sécurisé. Si vous utilisez une autorité de certification approuvée publiquement, vous n’avez rien à faire, car les ordinateurs clients approuvent ces émetteurs de certificats. Si vous utilisez un certificat auto-signé, installez la partie publique du certificat auto-signé dans le magasin de certificats de confiance sur l’ordinateur client.
>
>

## <a name="next-step"></a>Étape suivante
[Tâche 5 : Effectuer une liaison au domaine managé et bloquer l’accès LDAP sécurisé](active-directory-ds-ldaps-bind-lockdown.md)
