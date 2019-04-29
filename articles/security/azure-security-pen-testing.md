---
title: Test de pénétration | Microsoft Docs
description: Cet article fournit une vue d’ensemble du processus de test de pénétration (pentest) et explique comment effectuer ce test sur vos applications exécutées dans l’infrastructure Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 8835c4534b6dab1e8dbfb3546257ae4bc3b9d7af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610927"
---
# <a name="penetration-testing"></a>Test de pénétration
L’un des avantages liés à l’utilisation d’Azure pour tester et déployer des applications est que vous pouvez créer rapidement des environnements. Vous n’avez pas à vous soucier des aspects liés à la demande, à l’acquisition, à la mise en rack et à l’empilage de votre propre matériel en local.

Tout cela est très bien, certes, mais il vous reste toujours à effectuer les contrôles de sécurité standard. L’une des choses que vous voulez probablement faire est de pénétration tester les applications que vous déployez dans Azure.

Vous savez sans doute déjà que Microsoft effectue le [test de pénétration de l’environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ceci permet de dynamiser les améliorations d’Azure.

Nous n’avons pas test de pénétration votre application pour vous, mais nous savons parfaitement que vous souhaitez et avez besoin effectuer des tests sur vos propres applications. C’est une bonne chose, étant donné que vous pouvez améliorer la sécurité de vos applications vous aider à renforcer la sécurité de tout l’écosystème Azure.

À compter du 15 juin 2017, Microsoft ne nécessite plus d’approbation préalable d’effectuer un test de pénétration auprès des ressources Azure. Les clients qui souhaitent officiellement attester de leurs engagements relatifs aux tests d’intrusion à venir auprès de Microsoft Azure sont invités à remplir le [formulaire Azure Service Penetration Testing Notification](https://portal.msrc.microsoft.com/en-us/engage/pentest) (Notification de tests d’intrusion sur les services Azure). Ce processus concerne uniquement Microsoft Azure et ne s’applique à aucun autre service cloud de Microsoft.

>[!IMPORTANT]
>Même s’il n’est plus nécessaire de prévenir Microsoft d’une activité de test d’intrusion, les clients doivent toujours respecter les [Règles d’engagement pour les tests d’intrusion unifiés du cloud Microsoft](https://technet.microsoft.com/mt784683).

Vous avez la possibilité d’effectuer plusieurs tests :

* Tests sur vos points de terminaison visant à détecter les [10 principales vulnérabilités de l’OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
* [Balayage des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

En revanche, vous ne pouvez effectuer aucune forme [d’attaque par déni de service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), c’est-à-dire que nous ne pouvez ni initier une attaque par déni de service proprement dite, ni effectuer les tests associés permettant de déterminer, démontrer ou simuler n’importe quel type d’attaque de déni de service.

## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez documenter formellement un intrusion à venir test par rapport à vos applications hébergées dans Microsoft Azure, rendez-vous sur le [pénétration test règles d’Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) et remplissez le formulaire de notification de test.
