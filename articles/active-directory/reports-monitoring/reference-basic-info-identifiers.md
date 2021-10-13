---
title: Identificateurs d’informations de base dans le journal de connexion Azure AD | Microsoft Docs
description: Découvrez à quoi servent les identificateurs d’informations de base.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368322"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>Identificateurs d’informations de base dans le journal de connexion Azure AD

Azure AD enregistre toutes les connexions dans un locataire Azure à des fins de conformité. En tant qu’administrateur informatique, vous devez savoir ce que signifient les valeurs dans un journal de connexion pour pouvoir interpréter correctement les valeurs de journal.
Cet article explique les identificateurs sous l’onglet Informations de base du journal des connexions.

## <a name="unique-identifiers"></a>Identificateurs uniques 

Dans Azure AD, un accès aux ressources a trois composants pertinents :

- **Qui** - L'identité (l'utilisateur) qui effectue l'ouverture de session. 
- **Comment** - Le client (application) utilisé pour l'accès.  
- **Quoi** - La cible (ressource) à laquelle l'identité a accès.


Chaque composant est associé à un identificateur unique (ID). Voici un exemple d’utilisateur qui utilise l’API Gestion des services Windows Azure pour accéder au portail Azure.

![Ouvrir les journaux d’audit](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>Identificateurs de locataire

Le journal de connexion effectue le suivi de deux identificateurs de locataire :

- **Locataire d'origine** - Le locataire qui possède l'identité de l'utilisateur. 
- **Locataire de la ressource** - Le locataire qui possède la ressource (cible).

Ces identificateurs sont pertinents dans les scénarios inter-locataires. Par exemple, pour savoir comment les utilisateurs en dehors de votre locataire accèdent à vos ressources, sélectionnez toutes les entrées où le locataire d’origine ne correspond pas au locataire de la ressource.

## <a name="request-id"></a>ID de la demande

L’ID de demande est un identificateur qui correspond à un jeton émis. Si vous recherchez des connexions qui ont été effectuées avec un jeton spécifique, vous devez d’abord extraire l’ID de la requête à partir du jeton.


## <a name="correlation-id"></a>ID de corrélation :

L’ID de corrélation est un identificateur qui met en corrélation des connexions à partir de la même session de connexion. L’identificateur a été implémenté pour des raisons pratiques. Sa précision n’est pas garantie, car la valeur est basée sur les paramètres passés par un client. 




## <a name="next-steps"></a>Étapes suivantes

* [Journaux des connexions dans Azure Active Directory](concept-sign-ins.md)
* [Qu’est-ce que le diagnostic de connexion dans Azure AD ?](overview-sign-in-diagnostics.md)
