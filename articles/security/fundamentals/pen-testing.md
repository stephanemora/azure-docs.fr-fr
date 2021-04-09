---
title: Test d'intrusion | Microsoft Docs
description: Cet article fournit une vue d'ensemble du processus de test d'intrusion et explique comment l'appliquer à une application exécutée dans l'infrastructure Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550199"
---
# <a name="penetration-testing"></a>Test d’intrusion

L’un des avantages liés à l’utilisation d’Azure pour tester et déployer des applications est que vous pouvez créer rapidement des environnements. Vous n’avez pas à vous soucier des aspects liés à la demande, à l’acquisition, à la mise en rack et à l’empilage de votre propre matériel en local.

Créer rapidement des environnements, c'est très bien, mais vous devez toujours effectuer les contrôles de sécurité standard. Vous souhaiterez notamment soumettre les applications que vous déployez dans Azure à un test de pénétration.

Nous n'effectuons pas les tests d'intrusion à votre place, mais nous savons parfaitement qu'il est important pour vous d'exécuter ces tests sur vos propres applications. Et nous ne pouvons que vous en remercier car, lorsque vous améliorez la sécurité de vos applications, vous renforcez la sécurité de l’ensemble de l’écosystème Azure.

Depuis le 15 juin 2017, une approbation préalable n’est plus exigée par Microsoft pour réaliser un test d’intrusion sur les ressources Azure. Ce processus concerne uniquement Microsoft Azure et ne s’applique à aucun autre service cloud de Microsoft.

>[!IMPORTANT]
>Même s’il n’est plus nécessaire de prévenir Microsoft d’une activité de test d’intrusion, les clients doivent toujours respecter les [Règles d’engagement pour les tests d’intrusion unifiés du cloud Microsoft](https://technet.microsoft.com/mt784683).

Vous avez la possibilité d’effectuer plusieurs tests :

* Tests sur vos points de terminaison visant à détecter les [10 principales vulnérabilités de l’OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
* [Balayage des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

En revanche, vous ne pouvez effectuer aucune forme d'[attaque par déni de service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) en guise de test d'intrusion. Cela signifie que vous ne pouvez ni initier une attaque par déni de service proprement dite, ni effectuer les tests associés permettant de déterminer, de démontrer ou de simuler un type d'attaque par déni de service.

>[!Note]
>Microsoft travaille en collaboration avec BreakingPoint Cloud pour créer une interface à l’aide de laquelle vous pouvez générer du trafic sur les adresses IP publiques dotées de DDoS Protection à des fins de simulation. Pour en savoir plus sur la simulation de BreakingPoint Cloud, consultez [les tests par simulations](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [Règles d'engagement des tests d'intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
