---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a3b6616edbe1678cb18f78ec9025fc0286bf124f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684432"
---
Gardez à l'esprit les meilleures pratiques suivantes :

- Le service de gestion ne peut pas récupérer les mots de passe existants : il peut uniquement réinitialiser via le portail Azure. Nous vous recommandons de stocker tous les mots de passe dans un endroit sûr, afin de ne pas être obligé de réinitialiser un mot de passe en cas d’oubli. Si vous réinitialisez un mot de passe, veillez à informer tous les utilisateurs avant que vous le réinitialisez.
- Vous pouvez accéder à distance à l’interface Windows PowerShell de votre appareil via HTTP. Pour des raisons de sécurité, vous devez utiliser HTTP uniquement sur des réseaux approuvés.
- Assurez-vous que les mots de passe de périphérique sont forts et bien protégés. Suivez le [meilleures pratiques de mot de passe](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).