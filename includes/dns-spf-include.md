---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563335"
---
Les enregistrements SPF (Sender Policy Framework) servent à spécifier les serveurs de messagerie qui peuvent envoyer des e-mails pour un nom de domaine. Il est important de configurer correctement les enregistrements SPF pour éviter que des destinataires les marquent comme courrier indésirable.

Les RFC sur DNS ont initialement introduit un type d’enregistrement SPF pour prendre en charge ce scénario. Pour prendre en charge des serveurs de noms plus anciens, elles autorisaient également l’utilisation du type d’enregistrement TXT pour spécifier les enregistrements SPF. Cette ambiguïté a entraîné une certaine confusion qui a été résolue par la norme [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Elle stipule que les enregistrements SPF doivent être créés à l’aide du type d’enregistrement TXT. Elle stipule également que le type d’enregistrement SPF est déprécié.

**Les enregistrements SPF sont pris en charge par le DNS Azure et doivent être créés à l’aide du type d’enregistrement TXT.** Le type d’enregistrement SPF obsolète n’est pas pris en charge. Quand vous [importez un fichier de zone DNS](../articles/dns/dns-import-export.md), tous les enregistrements SPF qui utilisent le type d’enregistrement SPF sont convertis au type d’enregistrement TXT.
