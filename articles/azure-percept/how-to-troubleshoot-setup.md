---
title: Résoudre des problèmes dans le cadre de la configuration d’Azure Percept DK
description: Découvrez des conseils de dépannage pour certains des problèmes les plus courants rencontrés lors de l’expérience de configuration
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608491"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Guide de résolution des problèmes rencontrés lors de l’expérience de configuration d’Azure Percept DK

Reportez-vous au tableau ci-dessous pour obtenir des solutions de contournement aux problèmes courants rencontrés lors de la [configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md). Si votre problème persiste, contactez le service clientèle d’Azure.

|Problème|Motif|Solution de contournement|
|:-----|:------|:----------|
|Lorsque vous vous connectez aux pages d’inscription à un compte Azure ou au portail Azure, vous pouvez vous connecter automatiquement avec un compte mis en cache. S’il ne s’agit pas du compte que vous avez prévu d’utiliser, il peut en résulter une expérience incohérente avec la documentation.|Cela est généralement dû à un paramètre dans le navigateur pour « mémoriser » un compte que vous avez déjà utilisé.|Dans la page Azure, cliquez sur le nom de votre compte dans le coin supérieur droit, puis sélectionnez **Se déconnecter**. Vous pourrez alors vous connecter avec le compte approprié.|
|Le point d’accès Wi-Fi d’Azure Percept DK (scz-xxxx ou apd-xxxx) n’apparaît pas dans la liste des réseaux Wi-Fi disponibles.|Il s’agit généralement d’un problème temporaire qui se résout sous 15 minutes.|Attendez que le réseau s’affiche. S’il n’apparaît pas après plus de 15 minutes, redémarrez l’appareil.|
|La connexion au point d’accès Wi-Fi d’Azure Percept DK s’interrompt fréquemment.|Cela peut être dû à une connexion médiocre entre l’appareil et l’ordinateur hôte. Cela peut également être dû à des interférences avec d’autres connexions Wi-Fi sur l’ordinateur hôte.|Assurez-vous que les antennes sont correctement attachées au kit de développement. Si le kit de développement est éloigné de l’ordinateur hôte, essayez de le rapprocher. Désactivez toutes les autres connexions Internet, telles que LTE/5G, si elles s’exécutent sur l’ordinateur hôte.|
|L’ordinateur hôte affiche un avertissement de sécurité concernant la connexion au point d’accès du DK Azure Percept.|Il s’agit d’un problème connu qui sera résolu dans une prochaine mise à jour.|Il est possible de procéder à la configuration en toute sécurité.|
|Le point d’accès Wi-Fi d’Azure Percept DK (scz-xxxx ou apd-xxxx) apparaît dans la liste des réseaux, mais ne parvient pas à se connecter.|Cela peut être dû à un endommagement temporaire du point d’accès Wi-Fi du kit de développement.|Redémarrez le kit de développement et réessayez.|
|Impossible de se connecter à un réseau Wi-Fi lors de l’expérience d’installation.|Le réseau Wi-Fi doit disposer actuellement d’une connectivité Internet pour pouvoir communiquer avec Azure. EAP[PEAP/MSCHAP], les portails captifs et la connectivité EAP-TLS d’entreprise ne sont actuellement pas pris en charge.|Vérifiez que votre type de réseau Wi-Fi est pris en charge et qu’il dispose d’une connectivité Internet.|