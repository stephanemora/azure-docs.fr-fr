---
title: Guide pratique pour créer des abonnements dans la Gestion des API Azure | Microsoft Docs
description: Apprenez à créer des abonnements dans la Gestion des API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621704"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Guide pratique pour créer des abonnements dans la Gestion des API Azure

La façon la plus simple et la plus courante de sécuriser l’accès à des API publiées avec la Gestion des API Azure est d’utiliser des clés d’abonnement. En d’autres termes, les applications clientes qui utilisent les API publiées doivent inclure une clé d’abonnement valide dans les requêtes HTTP lorsqu’elles appellent ces API. Un abonnement est nécessaire pour obtenir une clé d’abonnement permettant d’accéder aux API. Pour plus d’informations sur les abonnements, voir [Abonnements dans la Gestion des API Azure](api-management-subscriptions.md).

Cet article décrit la procédure de création d’abonnements sur le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez :

+ [Créer une instance APIM](get-started-create-service-instance.md)
+ Comprendre le principe des [Abonnements dans la Gestion des API Azure](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Créer un abonnement

1. Cliquez sur **Abonnements** dans le menu de gauche.
2. Cliquez sur **Ajouter un abonnement**.
3. Donnez un nom à l’abonnement et sélectionnez l’étendue.
4. Cliquez sur **Enregistrer**.

![Abonnements flexibles](./media/api-management-subscriptions/flexible-subscription.png)

Une fois l’abonnement créé, une paire de clés API (primaire et secondaire) est configurée pour permettre d’accéder aux API.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la Gestion des API :

+ Découvrez les autres [concepts](api-management-terminology.md) de la Gestion des API.
+ Suivez nos [tutoriels](import-and-publish.md) sur la Gestion des API.
+ Consultez notre [page de FAQ](api-management-faq.md) pour connaître les questions courantes.