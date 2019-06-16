---
title: Résoudre les problèmes liés aux vues des coûts d’entreprise Azure | Microsoft Docs
description: Découvrez comment résoudre les problèmes que vous pourriez rencontrer avec les vues des coûts d’organisation dans le portail Azure.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615757"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Résoudre les problèmes liés aux vues des coûts d’entreprise

Dans les inscriptions d’entreprise, plusieurs paramètres peuvent empêcher les utilisateurs au sein de l’inscription d’afficher les coûts.  Ces paramètres sont gérés par l’administrateur d’inscription. Ou, si l’inscription n’est pas achetée directement via Microsoft, les paramètres sont gérés par le partenaire.  Cet article vous aide à comprendre quels sont les paramètres et dans quelle mesure ils impactent l’inscription. Ces paramètres sont indépendants des rôles de contrôle d’accès en fonction du rôle (RBAC) Azure.

## <a name="enabling-access-to-costs"></a>Activation de l’accès aux coûts

Voyez-vous un message « Non autorisé » ou *« Les vues des coûts sont désactivées dans votre inscription. »* quand vous recherchez des informations sur les coûts ?
![Capture d’écran affichant « non autorisé » dans le champ Coût actuel, pour l’abonnement.](media/billing-enterprise-mgmt-groups/unauthorized.png)

L’une des raisons suivantes peut en être la cause :

1. Vous avez acheté Azure par l’intermédiaire d’un partenaire d’entreprise qui n’a pas encore publié de tarifs. Contactez votre partenaire afin qu’il mette à jour le paramètre des tarifs dans [Enterprise portal](https://ea.azure.com).
2. Si vous êtes client EA Direct, les possibilités sont les suivantes :
    * Vous êtes propriétaire du compte et l’administrateur en charge de votre inscription a désactivé le paramètre **AO view charges** (Afficher les frais pour le propriétaire du compte).  
    * Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre **DA view charges** (Afficher les frais pour l’administrateur de service).
    * Contactez l’administrateur en charge de votre inscription pour obtenir l’accès. L’administrateur en charge de l’inscription peut mettre à jour les paramètres dans [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Capture d’écran montrant les paramètres d’Enterprise Portal pour l’affichage des frais.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Une ressource est indisponible

L’affichage du message d’erreur « La ressource n’est pas disponible » quand vous essayez d’accéder à un abonnement ou à un groupe d’administration signifie que vous n’avez pas le rôle approprié pour afficher cet élément.  

![Capture d’écran montrant le message qu’une « ressource n’est pas disponible ».](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Demandez l’accès à votre administrateur de l’abonnement ou du groupe d’administration Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide de RBAC et du portail Azure](../role-based-access-control/role-assignments-portal.md).
