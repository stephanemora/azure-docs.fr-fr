---
title: Résoudre les problèmes rencontrés lors de l’expérience d’intégration du DK Azure Percept
description: Découvrez des conseils de dépannage pour certains des problèmes les plus courants rencontrés lors de l’expérience d’intégration
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101660240"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Guide de résolution des problèmes rencontrés lors de l’expérience d’intégration du DK Azure Percept

Voici quelques problèmes que vous pouvez rencontrer lors de l’expérience d’intégration du DK Azure Percept. Si, après avoir utilisé les étapes de ce guide, le problème persiste, contactez le support client Azure.

|Problème|Motif|Solution de contournement|
|:-----|:------|:----------|
|Lorsque vous vous connectez aux pages d’inscription à un compte Azure ou au portail Azure, vous pouvez vous connecter automatiquement avec un compte mis en cache. S’il ne s’agit pas du compte que vous avez prévu d’utiliser, il peut en résulter une expérience incohérente avec la documentation.|Cela est généralement dû à un paramètre dans le navigateur pour « mémoriser » un compte que vous avez déjà utilisé.|Dans la page Azure, cliquez sur le coin supérieur droit où le nom de votre compte est affiché, puis cliquez sur Se déconnecter. Vous pourrez alors vous connecter avec le compte approprié.|
|Le réseau de point d’accès (scz-xxxx) du DK Azure Percept n’apparaît pas dans la liste des réseaux Wi-Fi disponibles.|Il s’agit généralement d’un problème temporaire qui se résout de lui-même avec un peu de temps.|Attendez que le réseau s’affiche. S’il ne s’affiche pas après plus de 15 minutes, redémarrez l’appareil.|
|La connexion au point d’accès du DK Azure Percept s’interrompt fréquemment.|Cela peut être dû à une connexion médiocre entre l’appareil et l’ordinateur hôte. Cela peut également être dû à des interférences avec d’autres connexions Wi-Fi sur l’ordinateur hôte.|Assurez-vous que les antennes sont correctement attachées au kit de développement. Si le kit de développement est éloigné de l’ordinateur hôte, essayez de le rapprocher. Désactivez toutes les autres connexions Internet, telles que LTE/5G, si elles s’exécutent sur l’ordinateur hôte.|
|L’ordinateur hôte affiche un avertissement de sécurité concernant la connexion au point d’accès du DK Azure Percept.|Il s’agit d’un problème connu qui sera résolu dans une prochaine mise à jour.|Il est possible de poursuivre en toute sécurité l’expérience d’intégration sur le point d’accès Wi-Fi du kit de développement.|
|Le réseau de point d’accès (scz-xxxx) du DK Azure Percept apparaît dans la liste des réseaux, mais ne parvient pas à se connecter.|Cela peut être dû à un endommagement temporaire du point d’accès Wi-Fi du kit de développement.|Redémarrez le kit de développement et réessayez.|
|Impossible de se connecter à un réseau Wi-Fi lors de l’expérience d’installation.|Le réseau Wi-Fi doit disposer actuellement d’une connectivité Internet afin de pouvoir communiquer avec Azure. EAP[PEAP/MSCHAP], les portails captifs et la connectivité EAP-TLS d’entreprise ne sont actuellement pas pris en charge.|Vérifiez que le type de réseau Wi-Fi que vous connectez est pris en charge et qu’il dispose d’une connectivité Internet.|