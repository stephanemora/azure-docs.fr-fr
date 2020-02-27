---
title: Connecter les données d’événement de sécurité Windows à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’événement de sécurité Windows à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 70190eeb3adec239d1e52f51afcd173497d08e6a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588006"
---
# <a name="connect-windows-security-events"></a>Connecter les événements de sécurité Windows 



Vous pouvez transmettre en continu tous les événements de sécurité des serveurs Windows connectés à votre espace de travail Azure Sentinel. Cette connexion vous permet d’afficher des tableaux de bord, de créer des alertes personnalisées et d’améliorer les investigations. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité.  Vous pouvez sélectionner les événements à transmettre en continu :

- **Tous les événements** : tous les événements AppLocker et de sécurité Windows.
- **Courant** : un ensemble d’événements à des fins d’audit. Une piste d’audit utilisateur complète est fournie dans cet ensemble. Par exemple, cet ensemble contient les événements de connexion et de déconnexion de l’utilisateur (ID d’événement 4634). Nous incluons les actions d’audit, telles que les modifications de groupe de sécurité, les opérations Kerberos du contrôleur de domaine clé et les autres événements recommandés par les organisations du secteur.

Les événements qui ont un volume très faible ont été inclus dans l’ensemble Commun, car la motivation principale à préférer cet ensemble à l’ensemble Tous les événements est de réduire le volume et de ne pas filtrer d’événements spécifiques.
- **Minimal** : un petit ensemble d’événements qui peuvent indiquer des menaces potentielles. En activant cette option, vous ne pourrez pas avoir de piste d’audit complète.  Cet ensemble couvre uniquement les événements qui peuvent indiquer une violation avérée et des événements importants qui ont un volume très faible. Par exemple, cet ensemble contient une connexion utilisateur ayant réussi et ayant échoué (ID d’événement 4624, 4625), mais il ne contient pas d’informations de déconnexion, qui sont importantes pour l’audit, mais pas pour la détection, et dont le volume est relativement élevé. Le plus gros du volume de données de cet ensemble est constitué d’événements de connexion et d’un événement de création de processus (ID d’événement 4688).
- **Aucun** : aucun événement AppLocker ni de sécurité.

> [!NOTE]
> 
> - Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.
> - Si Azure Security Center et Azure Sentinel s’exécutent sur le même espace de travail, le connecteur des événements de sécurité ne peut être connecté qu’à partir d’Azure Security Center ou d’Azure Sentinel. Pour gérer ces événements à partir d’Azure Sentinel, nous vous recommandons de le déconnecter d’Azure Security Center et de ne le connecter qu’à Azure Sentinel.


La liste suivante fournit le détail complet des ID d’événement App Locker et de sécurité pour chaque ensemble :

| Couche Données | Indicateurs d’événements collectés |
| --- | --- |
| Minimales | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Courant | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Configurer le connecteur d’événements de sécurité Windows

Pour intégrer complètement vos événements de sécurité Windows à Azure Sentinel :

1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données**, puis cliquez sur la vignette **Événements de sécurité**. 
1. Sélectionnez les types de données que vous souhaitez transmettre en continu.
1. Cliquez sur **Update**.
6. Pour utiliser le schéma pertinent dans Log Analytics pour les événements de sécurité Windows, recherchez **SecurityEvent**.

## <a name="validate-connectivity"></a>Valider la connectivité

Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les événements de sécurité Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

