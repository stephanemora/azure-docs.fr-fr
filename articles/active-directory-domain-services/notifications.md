---
title: 'Azure Active Directory Domain Services : paramètres de notification | Microsoft Docs'
description: Paramètres de notification pour Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: fab680e8e26584e6beeaf4bdb205c721e1b0b91e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246958"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Paramètres de notification dans Azure AD Domain Services

Les notifications pour Azure AD Domain Services vous permettent d’être informé dès qu’une alerte d’intégrité est détectée sur votre domaine managé.  

Cette fonctionnalité est uniquement disponible pour les domaines gérés qui ne sont pas sur des réseaux virtuels classiques.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Comment vérifier les paramètres de notification par e-mail Azure AD Domain Services

1. Accédez à la [page Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
2. Choisissez votre domaine managé dans le tableau
3. Dans le volet de navigation de gauche, choisissez **paramètres de Notification**

Cette page présente la liste de tous les destinataires des notifications par e-mail pour Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>À quoi ressemblent les notifications par e-mail ?

L’image suivante est un exemple d’une notification par e-mail :

![Exemple de notification par e-mail](./media/active-directory-domain-services-alerts/email-alert.png)

Ce message spécifie le domaine managé sur laquelle l’alerte est présente et fournit l’heure de détection et un lien vers la page de contrôle d’intégrité d’Azure AD Domain Services dans le portail Azure.

> [!WARNING]
> Assurez-vous toujours que le message provient d’un expéditeur Microsoft vérifié avant de cliquer sur les liens qu’il contient. Les messages sont toujours envoyés par azure-noreply@microsoft.com


## <a name="why-would-i-receive-email-notifications"></a>Pourquoi est-ce que je recevrais des notifications par e-mail ?

Azure AD Domain Services envoie des notifications par e-mail pour les informations importantes concernant votre domaine.  Ces notifications sont uniquement pour des questions urgentes qui ont un impact sur votre service et qui doivent être traitées immédiatement. Chaque notification par e-mail est déclenchée par une alerte sur votre domaine managé. Ces alertes s’affichent également sur le portail Azure et peut être consultées sur la [page de contrôle d’intégrité Azure AD Domain Services](check-health.md).

Azure AD Domain Services n’envoie pas de messages à cette liste pour la publicité, les mises à jour ou la vente.

## <a name="when-will-i-receive-email-notifications"></a>Quand est-ce que je recevrai des notifications par e-mail ?

Les notification sont envoyées immédiatement quand une [nouvelle alerte](troubleshoot-alerts.md) est détectée sur votre domaine managé. Si l’alerte n’est pas résolue, une notification par e-mail est envoyée en guise de rappel tous les quatre jours.

## <a name="who-should-receive-the-email-notifications"></a>Qui doit recevoir les notifications par e-mail ?


 Nous vous recommandons que la liste des destinataires pour les Azure AD Domain Services comporte des personnes qui sont en mesure d’administrer et d’apporter des modifications au domaine managé. Cette liste doit être considérée comme celle des « premiers intervenants » pour tout type de problème. Si vous souhaitez ajouter plus de cinq autres adresses, nous vous recommandons de créer une liste de distribution à ajouter à la liste de notification.

Vous pouvez ajouter jusqu’à cinq adresses pour les notifications Azure AD Domain Services. En outre, vous pouvez également choisir que tous les administrateurs généraux de votre annuaire et tous les membres du groupe « AAD DC Administrators » reçoivent les notifications par e-mail envoyées par Azure AD Domain Services. Azure AD Domain Services enverra uniquement des notifications à 100 adresses e-mail, y compris la liste des administrateurs généraux et des administrateurs AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Comment ajouter un destinataire

> [!WARNING]
> Lorsque vous modifiez les paramètres de notification, vous modifiez les paramètres de notification de tout le domaine managé, et non pas uniquement pour vous-même.

1. Accédez à la [page Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
2. Cliquez sur votre domaine managé.
3. Dans le volet de navigation de gauche, choisissez **Paramètres de notification**.
4. Pour ajouter une adresse e-mail, tapez l’adresse dans le tableau des destinataires supplémentaires.
5. Cliquez sur « Enregistrer » dans le volet de navigation en haut.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>J’ai reçu une notification par e-mail pour une alerte, mais lorsque j’ai ouvert une session, le portail Azure ne contenait pas d’alerte. Que s'est-il passé ?

Si une alerte est résolue, elle disparaît du portail Azure. La raison la plus probable est qu’une autre personne qui reçoit ces notifications par e-mail a résolu l’alerte sur votre domaine managé, ou bien elle a été résolue automatiquement par Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Pourquoi ne puis-je pas modifier les paramètres de notification ?

Si vous ne parvenez pas à accéder à la page des paramètres de notification dans le portail Azure, vous n’avez pas les autorisations pour modifier les Azure AD Domain Services. Vous devez contacter votre administrateur général soit pour obtenir les autorisations pour modifier les ressources Azure AD Domain Services ou pour être retiré de la liste des destinataires.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Je ne reçois pas de notifications par e-mail, même si j’ai fourni mon adresse e-mail. Pourquoi ?

Recherchez les notifications dans le dossier de courrier indésirable de votre boîte de réception et assurez-vous que l’expéditeur est bien dans la liste verte (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes
- [Résoudre les alertes sur votre domaine managé](troubleshoot-alerts.md)
- [En savoir plus sur Azure AD Domain Services](overview.md)
- [Contacter l’équipe produit](contact-us.md)

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](contact-us.md).
