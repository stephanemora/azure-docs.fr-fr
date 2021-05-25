---
title: Vue d’ensemble du portefeuille d’agents et prise en charge des OS (préversion)
description: Azure Defender pour IoT fournit un vaste portefeuille d’agents en fonction du type d’appareil.
ms.date: 05/02/2021
ms.topic: conceptual
ms.openlocfilehash: 5449f0ac3f3c2e37a79ba156f11042b640786959
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108735008"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Vue d’ensemble du portefeuille d’agents et prise en charge des OS (préversion)

Azure Defender pour IoT fournit un vaste portefeuille d’agents en fonction du type d’appareil. 

## <a name="standalone-agent"></a>Agent autonome

L’agent autonome couvre la plupart des systèmes d’exploitation Linux, qui peuvent être déployés sous la forme d’un package binaire ou d’un code source qui peut être incorporé dans le cadre du microprogramme, et autoriser la modification et la personnalisation en fonction des besoins des clients. Voici quelques exemples de systèmes d’exploitation pris en charge : 

| Système d’exploitation | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Pour plus d’informations, pour connaître les systèmes d’exploitation pris en charge ou pour demander l’accès au code source afin de pouvoir l’incorporer en tant que partie du microprogramme de l’appareil, contactez votre responsable de compte ou envoyez un e-mail à <defender_micro_agent@microsoft.com>. 

## <a name="azure-rtos-micro-agent"></a>Micro-agent Azure RTOS

Le micro-agent Azure Defender pour IoT fournit une solution de sécurité complète et légère pour les appareils qui utilisent Azure RTOS. Le micro-agent Azure Defender pour IoT permet de couvrir les menaces courantes et les activités malveillantes potentielles sur les appareils avec un système d’exploitation en temps réel (RTOS). Le micro-agent est intégré au composant Azure RTOS NetX Duo. Il supervise l’activité réseau de l’appareil. 

Le micro-agent Azure Defender pour IoT est intégré au composant Azure RTOS NetX Duo. Il supervise l’activité réseau de l’appareil. Le micro-agent se compose d’une solution de sécurité complète et légère qui permet de couvrir les menaces courantes et les activités malveillantes potentielles sur les appareils avec un système d’exploitation en temps réel (RTOS).

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur la [Vue d’ensemble du micro-agent autonome (préversion)](concept-standalone-micro-agent-overview.md).
