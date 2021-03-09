---
title: Établir une connexion SSH à votre DK Azure Percept
description: Découvrir comment établir une connexion SSH à votre DK Azure Percept avec PuTTY
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096613"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Établir une connexion SSH à votre DK Azure Percept

Effectuez les étapes ci-dessous pour configurer une connexion SSH à votre DK Azure Percept avec [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Prérequis

- Un ordinateur hôte basé sur Windows, Linux ou OS X avec fonctionnalité Wi-Fi
- Un client SSH
    - Si votre ordinateur hôte exécute Windows, [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) est un client SSH efficace qui sera utilisé tout au long de ce guide.
    - Si votre ordinateur hôte exécute Linux ou OS X, les services SSH sont inclus dans ces systèmes d’exploitation et peuvent être exécutés sans application cliente distincte. Pour plus d’informations sur l’exécution des services SSH, consultez la documentation de votre système d’exploitation.
- DK Azure Percept
- Configurer un compte de connexion SSH au cours de [l’expérience d’intégration d’Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>Lancer la connexion SSH

1. Mettez votre DK (devkit) Azure Percept sous tension.

1. Si votre devkit est déjà connecté à un réseau via Ethernet ou Wi-Fi, passez à l’étape suivante. Dans le cas contraire, connectez votre ordinateur hôte directement au point d’accès Wi-Fi du devkit, comme pour vous connecter à n’importe quel autre réseau Wi-Fi :
    - **nom du réseau** : scz-xxxx (où « xxxx » correspond aux quatre derniers chiffres de l’adresse réseau Mac du devkit)
    - **mot de passe** : disponible sur la carte de bienvenue fournie avec le devkit

    > [!WARNING]
    > Une fois connecté au point d’accès Wi-Fi du DK Azure Percept, votre ordinateur hôte perd temporairement sa connexion à Internet. Les appels de vidéoconférence actifs, le streaming web et les autres expériences réseau sont interrompus jusqu’à la fin de l’étape 3 de l’expérience d’intégration du DK Azure Percept.

1. Ouvrez PuTTY. Entrez les informations suivantes, puis cliquez sur **Ouvrir** pour établir une connexion SSH à votre devkit :

    1. Nom d’hôte : 10.1.1.1
    1. Port : 22
    1. Type de connexion : SSH

    > [!NOTE]
    > Le **Nom d’hôte** est l’adresse IP de votre appareil. Si votre devkit est connecté au point d’accès Wi-Fi du devkit, l’adresse IP est 10.1.1.1. Si votre devkit est connecté via Ethernet, utilisez l’adresse IP locale de l’appareil, que vous pouvez récupérer à partir du routeur ou du hub Ethernet. Si votre appareil est connecté via Wi-Fi, vous devez utiliser l’adresse IP qui a été fournie pendant l’[expérience d’intégration du DK Azure Percept](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Image.":::

1. Connectez-vous au terminal PuTTY avec le nom d’utilisateur SSH et le mot de passe créé au cours de l’expérience d’intégration.

## <a name="next-steps"></a>Étapes suivantes

Une fois la connexion SSH établie à votre DK Azure Percept, vous pouvez effectuer diverses tâches, notamment résoudre des problèmes, effectuer des mises à jour USB et exécuter DiagTool ou SoftAP Tool.