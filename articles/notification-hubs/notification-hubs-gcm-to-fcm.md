---
title: Azure Notification Hubs et la migration de Google Firebase Cloud Messaging (FCM)
description: Décrit comment Azure Notification Hubs résout le Google GCM à la migration de FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787013"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs et la migration de Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>État actuel

Lorsque Google a annoncé sa migration à partir de Google Cloud Messaging (GCM) pour Firebase Cloud Messaging (FCM), les services push comme la nôtre deviez ajuster la façon dont nous envoyé des notifications à des appareils Android pour prendre en charge la modification.

Nous mis à jour de notre serveur principal de service, puis mises à jour publiées à nos API et les kits de développement logiciel en fonction des besoins. Avec notre implémentation, nous avons pris la décision pour assurer la compatibilité avec les schémas de notification GCM existants afin de minimiser l’impact sur le client. Cela signifie que nous avons actuellement envoyer des notifications aux appareils Android à l’aide de FCM en Mode hérité de FCM. Au final, nous souhaitons ajouter la prise en charge true pour FCM, y compris les nouvelles fonctionnalités et le format de charge utile. C’est une modification à long terme et la migration en cours se concentre sur la conservation de la compatibilité avec les applications existantes et les kits de développement logiciel. Vous pouvez utiliser les kits de développement logiciel FCM ou GCM dans votre application (ainsi que notre kit de développement logiciel) et nous nous assurons que la notification est envoyée correctement.

Certains clients a récemment reçu un message électronique à partir de l’avertissement de Google sur les applications à l’aide d’un point de terminaison GCM pour les notifications. Il s’agit simplement d’un avertissement et rien n’est pas rompu : notifications Android de votre application sont toujours envoyées à Google pour le traitement et Google traite toujours les. Certains clients qui spécifié le point de terminaison GCM explicitement dans leur configuration de service à utiliser le point de terminaison déconseillée. Nous avait déjà identifié cet écart et que vous utilisiez sur le problème corrigé lors de l’e-mail envoyé à Google.

Nous avons remplacé ce point de terminaison déconseillée et le correctif est déployé.

## <a name="going-forward"></a>À l’avenir

Forum aux questions de FCM de Google indique que vous n’avez rien à faire. Dans le [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google dit « kits SDK clients et les jetons GCM continue de fonctionner indéfiniment. Toutefois, vous ne pourrez cibler la dernière version des Services Google Play dans votre application Android, sauf si vous migrez vers FCM. »

Si votre application utilise la bibliothèque GCM, continuez et suivez les instructions de Google pour mettre à niveau vers la bibliothèque FCM dans votre application. Notre kit de développement logiciel est compatible avec deux, donc vous ne devrez pas mettre à jour quoi que ce soit dans votre application de notre côté (tant que vous êtes à jour avec la version du Kit de développement logiciel).

## <a name="questions-and-answers"></a>Questions et réponses

Voici les réponses aux questions les plus fréquentes que nous avons entendu des clients :

**Q :** Que dois-je faire pour être compatible avant la date limite (actuel du Google date de coupure est 29 mai et peut changer) ?

**R :** Rien. Nous conservons la compatibilité avec le schéma de notification GCM existant. Votre clé GCM continueront à fonctionner normalement ainsi que des kits de développement logiciel GCM et des bibliothèques utilisées par votre application.

Si/quand vous décidez de mettre à niveau vers les kits de développement logiciel FCM et les bibliothèques pour tirer parti des nouvelles fonctionnalités, votre clé GCM continueront de fonctionner. Vous pouvez basculer vers à l’aide d’une clé FCM si vous le souhaitez, mais assurez-vous que vous ajoutez Firebase à votre projet GCM existant lors de la création du nouveau projet Firebase. Cela garantit la compatibilité descendante avec les clients qui exécutent des versions antérieures de l’application qui utilisent toujours des bibliothèques et les kits de développement logiciel GCM.

Si vous créez un nouveau projet FCM et ne pas attacher au projet GCM existant, une fois que vous mettez à jour les concentrateurs de Notification avec le nouveau secret FCM vous perdez la possibilité pour envoyer des notifications push à vos installations actuelles de l’application, dans la mesure où la nouvelle clé FCM dispose d’aucun lien vers l’ancien GCM projet.

**Q :** Pourquoi ai-je reçu cet e-mail sur les anciens points de terminaison GCM utilisé ? Que dois-je faire ?

**R :** Rien. Nous ont été migration vers les nouveaux points de terminaison et sera bientôt terminés, aucune modification n’est nécessaire. Rien n’est rompue, notre point de terminaison vous avez manqué un dû simplement des messages d’avertissement à partir de Google.

**Q :** Comment puis-je transition vers le nouveau SDK de FCM et bibliothèques sans interrompre les utilisateurs existants ?

R : Mettre à niveau à tout moment. Google n’a pas encore annoncé toute dépréciation des bibliothèques et les kits de développement logiciel existant GCM. Afin de vous empêcher le fractionnement des notifications push aux utilisateurs existants, assurez-vous que lorsque vous créez le nouveau projet Firebase que vous associez à votre projet GCM existant. Ainsi, les nouveaux Firebase secrets fonctionnera pour les utilisateurs qui exécutent les anciennes versions de votre application avec les bibliothèques et les kits de développement logiciel GCM, ainsi que les nouveaux utilisateurs de votre application avec les bibliothèques et les kits de développement logiciel FCM.

**Q :** Quand puis-je utiliser des schémas et des nouvelles fonctionnalités FCM pour mes notifications ?

**R :** Une fois que nous publions une mise à jour à nos API et les kits de développement logiciel, restez connecté – devrait avoir quelque chose pour vous dans les prochains mois.
