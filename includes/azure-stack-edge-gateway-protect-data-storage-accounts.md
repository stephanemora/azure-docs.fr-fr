---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285193"
---
Votre appareil est associé à un compte de stockage qui est utilisé en tant que destination pour vos données dans Azure. L’accès au compte de stockage est contrôlé par l’abonnement et deux clés d’accès au stockage de 512 bits associées à ce compte de stockage.

L’une des clés est utilisée pour l’authentification quand l’appareil Azure Stack Edge accède au compte de stockage. L’autre clé est gardée en réserve, ce qui permet une rotation régulière des clés.

De nombreux centres de données ont recours à la rotation des clés pour des raisons de sécurité. Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :

- Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Protégez soigneusement votre clé de compte. Ne communiquez pas le mot de passe à d’autres utilisateurs, ne le codez pas en dur et ne l’enregistrez pas en texte brut, où que ce soit.
- [Régénérez votre clé de compte](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) via le portail Azure si vous pensez qu’elle a été compromise.
- Votre administrateur Azure doit changer ou régénérer régulièrement la clé primaire ou secondaire à l’aide de la section Stockage du portail Azure pour accéder directement au compte de stockage.
- Vous pouvez aussi utiliser votre propre clé de chiffrement pour protéger les données de votre compte de stockage Azure. Quand vous spécifiez une clé gérée par le client, cette clé est utilisée pour protéger et contrôler l’accès à la clé qui chiffre vos données. Pour plus d’informations sur la sécurisation de vos données, consultez [Activer les clés gérées par le client pour votre compte de stockage Azure](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
