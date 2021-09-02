---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: b0b66a910e762b5c14abb661c966fdb21015544e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867904"
---
Les enregistrements SPF (Sender Policy Framework) servent à spécifier les serveurs de messagerie qui peuvent envoyer des e-mails pour un nom de domaine. Il est important de configurer correctement les enregistrements SPF pour éviter que des destinataires les marquent comme courrier indésirable.

Les RFC sur DNS ont initialement introduit un type d’enregistrement SPF pour prendre en charge ce scénario. Pour prendre en charge des serveurs de noms plus anciens, elles autorisaient également l’utilisation du type d’enregistrement TXT pour spécifier les enregistrements SPF. Cette ambiguïté a entraîné une certaine confusion qui a été résolue par la norme [RFC 7208](https://datatracker.ietf.org/doc/html/rfc7208#section-3.1). Elle stipule que les enregistrements SPF doivent être créés à l’aide du type d’enregistrement TXT. Elle stipule également que le type d’enregistrement SPF est déprécié.

**Les enregistrements SPF sont pris en charge par le DNS Azure et doivent être créés à l’aide du type d’enregistrement TXT.** Le type d’enregistrement SPF obsolète n’est pas pris en charge. Quand vous [importez un fichier de zone DNS](../articles/dns/dns-import-export.md), tous les enregistrements SPF qui utilisent le type d’enregistrement SPF sont convertis au type d’enregistrement TXT.
