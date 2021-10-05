---
title: fichier descriptif
description: Fichier include
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212479"
---
## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [abonnement gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Un [locataire Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) associé à votre abonnement.

* Le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être membre du rôle *Contributeur* ou *Propriétaire*, ou être *administrateur* de l’abonnement Azure. Pour voir les autorisations dont vous disposez dans l’abonnement, accédez au [portail Azure](https://portal.azure.com), sélectionnez votre nom d’utilisateur en haut à droite, sélectionnez l’icône «  **...**  » pour plus d’options, puis sélectionnez **Mes autorisations**. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié.

* Pas de [Stratégies Azure](../../governance/policy/overview.md) empêchant la création de **comptes de stockage** ou d’**espaces de noms Event Hub**. Purview déploie un compte Stockage géré et un Event Hub lors de sa création. Si une stratégie de blocage existe et doit rester en place, suivez notre [guide sur les étiquettes d’exception Purview](../create-purview-portal-faq.md) ainsi que les étapes permettant de créer une exception pour les comptes Purview.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.
