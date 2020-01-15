---
title: Instructions de nommage des mots clés – Service Speech
titleSuffix: Azure Cognitive Services
description: Il est essentiel de créer un mot clé efficace pour que votre appareil réponde bien avec logique et précision.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453324"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Instructions de création d’un mot clé efficace

Il est essentiel de créer un mot clé efficace pour que votre appareil réponde bien avec logique et précision. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé. Cet article présente quelques principes fondamentaux pour la création d’un mot clé efficace.

## <a name="choose-an-effective-keyword"></a>Choisir un mot clé efficace

Vous devez respecter les consignes suivantes lors du choix d’un mot clé :

* Votre mot clé doit être un mot ou une expression en anglais.

* Le/la prononcer ne doit pas prendre plus de deux secondes.

* Il est préférable d’utiliser un mot de quatre à sept syllabes. Par exemple, « Hey, Computer » est un bon mot clé, ce qui n’est pas le cas du mot « Hey » seul.

* Les mots clés doivent suivre les règles courantes de prononciation de la langue anglaise.

* Un mot unique ou même inventé qui suit les règles courantes de prononciation de la langue anglaise peut réduire les faux positifs. Par exemple, « computerama » pourrait être un bon mot clé.

* Ne choisissez pas un mot courant, comme « eat » et « go » qui sont prononcés très fréquemment dans les conversations. Ces faux déclencheurs activeraient à tort votre appareil.

* Évitez d’utiliser un mot clé qui a plusieurs prononciations. Sinon, les utilisateurs devront connaître la « bonne » prononciation pour obtenir une réponse de leur appareil. Par exemple, « 509 » peut se prononcer « five zero nine », « five oh nine » ou « five hundred and nine ». « R.E.I. » peut être prononcé « r-e-i » ou « ray ». « Live » peut être prononcé « /līv/ » ou « /liv/ ».

* N’utilisez pas de caractères spéciaux, de symboles ou de chiffres. Par exemple, « Go# » et « 20+ cats » peuvent être des mots clés problématiques. Cependant, « go sharp » ou « twenty plus cats » peuvent fonctionner. Pour rendre évidente la bonne prononciation, vous pouvez utiliser les symboles de votre marque et vous servir du marketing et de la documentation.

> [!NOTE]
> Si vous choisissez un mot commercial comme mot clé, vous devez être propriétaire de cette marque commerciale ou avoir l’autorisation de son propriétaire pour utiliser le mot. Microsoft n’est pas responsable des problèmes juridiques que pourrait provoquer votre choix de mot clé.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer un mot clé personnalisé avec Speech Studio](speech-devices-sdk-create-kws.md).
