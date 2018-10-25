---
title: 'Azure Active Directory Domain Services : Dépannage de la configuration du groupe de sécurité réseau | Microsoft Docs'
description: Dépannage de la configuration du groupe de sécurité réseau pour les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: bca92e933b1f75c330999f0d55723eb9f26a7382
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426080"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Résoudre les problèmes de configuration de mise en réseau non valide pour votre domaine géré
Cet article vous aide à dépanner et résoudre les erreurs liées à la configuration réseau qui produisent le message d’alerte suivant :

## <a name="alert-aadds104-network-error"></a>Alerte AADDS104 : Erreur réseau
**Message d’alerte :** *Microsoft ne peut pas atteindre les contrôleurs de domaine pour ce domaine géré. Cela peut se produire si un groupe de sécurité réseau (NSG) configuré sur votre réseau virtuel bloque l’accès à un domaine géré. Une autre raison possible est s’il existe un itinéraire défini par l’utilisateur qui bloque le trafic entrant à partir d’Internet.*

Les configurations de groupe de sécurité réseau non valides sont la cause la plus courante d’erreurs réseau pour Azure AD Domain Services. Le groupe de sécurité réseau (NSG) configuré pour votre réseau virtuel doit autoriser l’accès à des [ports spécifiques](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Si ces ports sont bloqués, Microsoft ne peut pas analyser ou mettre à jour votre domaine géré. En outre, la synchronisation entre votre répertoire Azure AD et votre domaine géré est interrompue. Lors de la création de votre groupe de sécurité réseau, gardez ces ports ouverts pour éviter les interruptions de service.

### <a name="checking-your-nsg-for-compliance"></a>Vérification de la conformité de votre groupe de sécurité réseau

1. Dans le Portail Azure, accédez à la page [Groupes de sécurité réseau](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)
2. À partir de la table, choisissez le groupe de sécurité réseau associé au sous-réseau dans lequel votre domaine géré est activé.
3. Sous **Paramètres** dans le panneau gauche, cliquez sur **Règles de sécurité entrantes**
4. Passez en revue les règles en place et identifiez celles qui bloquent l’accès à [ces ports](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Modifiez le groupe de sécurité réseau pour assurer la conformité en supprimant la règle, en ajoutant une règle ou en créant entièrement un nouveau groupe de sécurité réseau. Les étapes pour [ajouter une règle](#add-a-rule-to-a-network-security-group-using-the-azure-portal) ou [créer un groupe de sécurité réseau conforme](#create-a-nsg-for-azure-ad-domain-services-using-powershell) sont présentées ci-dessous.

## <a name="sample-nsg"></a>Exemple de groupe de sécurité réseau
Le tableau suivant illustre un exemple de groupe de sécurité réseau qui sécuriserait votre domaine géré tout en permettant à Microsoft de surveiller, gérer et mettre à jour ses informations.

![exemple de groupe de sécurité réseau](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Les services de domaine Azure AD nécessitent un accès sortant non restreint depuis le réseau virtuel. Nous recommandons de ne pas créer de règle de groupe de sécurité réseau supplémentaire pouvant restreindre l’accès sortant pour le réseau virtuel.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Ajout d’une règle à un groupe de sécurité réseau à l’aide du portail Azure
Si vous ne souhaitez pas utiliser PowerShell, vous pouvez ajouter manuellement des règles uniques pour les groupes de sécurité réseau à l’aide du portail Azure. Pour créer des règles dans votre groupe de sécurité réseau, procédez comme suit :

1. Dans le Portail Azure, accédez à la page [Groupes de sécurité réseau](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups).
2. À partir de la table, choisissez le groupe de sécurité réseau associé au sous-réseau dans lequel votre domaine géré est activé.
3. Sous **Paramètres** dans le panneau de gauche, cliquez sur **Règles de sécurité de trafic entrant** ou **Règles de sécurité sortantes**.
4. Créez la règle en cliquant sur **Ajouter** et en remplissant les informations. Cliquez sur **OK**.
5. Vérifiez que votre règle a été créée en la recherchant dans la table de règles.


## <a name="need-help"></a>Vous avez besoin d’aide ?
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
