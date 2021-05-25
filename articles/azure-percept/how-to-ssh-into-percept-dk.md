---
title: Établir une connexion SSH à votre DK Azure Percept
description: Découvrir comment établir une connexion SSH à votre DK Azure Percept avec PuTTY
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ee194e6d501d967d37453763c9cd4b25d1bedbe
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754196"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Établir une connexion SSH à votre DK Azure Percept

Effectuez les étapes ci-dessous pour configurer une connexion SSH à votre DK Azure Percept avec OpenSSH ou [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Prérequis

- Un ordinateur hôte basé sur Windows, Linux ou OS X avec fonctionnalité Wi-Fi
- Un client SSH (voir la section suivante pour obtenir des instructions d’installation)
- Un Azure Percept DK (devkit)
- Un compte SSH créé dans le cadre de la [configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Installer votre client SSH préféré

Si votre ordinateur hôte exécute Linux ou OS X, les services SSH sont inclus dans ces systèmes d’exploitation et peuvent être exécutés sans application cliente distincte. Pour plus d’informations sur l’exécution des services SSH, consultez la documentation de votre système d’exploitation.

Si votre ordinateur hôte exécute Windows, vous pouvez choisir parmi deux options de client SSH : OpenSSH et PuTTY.

### <a name="openssh"></a>OpenSSH

Windows 10 comprend un client SSH intégré, appelé OpenSSH, qui peut être exécuté à l’aide d’une simple commande dans une invite de commandes. Nous vous recommandons d’utiliser OpenSSH avec Azure Percept, si vous y avez accès. Pour vérifier si OpenSSH est installé sur votre ordinateur Windows, procédez comme suit :

1. Accédez à **Démarrer** -> **Paramètres**.

1. Sélectionnez **Applications**.

1. Sous **Applications et fonctionnalités**, sélectionnez **Fonctionnalités facultatives**.

1. Entrez **Client OpenSSH** dans la barre de recherche **Fonctionnalités installées**. Si OpenSSH s’affiche, le client est déjà installé et vous pouvez passer à la section suivante. Si OpenSSH ne s’affiche pas, sélectionnez **Ajouter une fonctionnalité**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Capture d’écran des paramètres montrant l’état d’installation d’OpenSSH.":::

1. Sélectionnez **Client OpenSSH** et sélectionnez **Installer**. Vous pouvez maintenant passer à la section suivante. Si OpenSSH n’est pas disponible pour l’installation sur votre ordinateur, suivez les étapes ci-dessous pour installer PuTTY, un client SSH tiers.

### <a name="putty"></a>PuTTY ;

Si votre ordinateur Windows n’inclut pas OpenSSH, nous vous recommandons d’utiliser [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Pour télécharger et installer PuTTY, effectuez les étapes suivantes :

1. Accédez à la [page de téléchargement de PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Sous **Fichiers de package**, sélectionnez le fichier .msi 32 ou 64 bits pour télécharger le programme d’installation. Si vous ne savez pas quelle version choisir, consultez le [Forum aux questions](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Sélectionnez le fichier d’installation pour démarrer le processus d’installation. Suivez les invites si nécessaire.

1. Félicitations ! Vous avez correctement installé le client SSH PuTTY.

## <a name="initiate-the-ssh-connection"></a>Lancer la connexion SSH

1. Mettez votre Azure Percept DK sous tension.

1. Si votre devkit est déjà connecté à un réseau via Ethernet ou Wi-Fi, passez à l’étape suivante. Sinon, connectez votre ordinateur hôte directement au point d’accès Wi-Fi du devkit. Comme pour la connexion à tout autres réseaux Wi-Fi, ouvrez les paramètres réseaux et Internet sur votre ordinateur, sélectionnez le réseau suivant, puis entrez le mot de passe réseau lorsque vous y êtes invité :

    - **Nom réseau** : selon la version du système d’exploitation de votre kit de développement, le nom du point d’accès Wi-Fi est **scz-xxxx** ou **apd-xxxx** (où « xxxx » correspond aux quatre derniers chiffres de l’adresse MAC du kit de développement)
    - **Mot de passe** : disponible sur la carte de bienvenue fournie avec le kit de développement

    > [!WARNING]
    > Une fois connecté au point d’accès Wi-Fi du DK Azure Percept, votre ordinateur hôte perd temporairement sa connexion à Internet. Les appels de visioconférence actifs, la diffusion en continu sur le web ou d’autres expériences basées sur le réseau seront interrompus.

1. Terminez le processus de connexion SSH pour votre client SSH.

### <a name="using-openssh"></a>Utilisation d’OpenSSH

1. Ouvrez une invite de commandes (**Démarrer** -> **Invite de commandes**).

1. À l’invite de commandes, entrez ce qui suit :

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Si votre ordinateur est connecté au point d’accès Wi-Fi du devkit, l’adresse IP est 10.1.1.1. Si votre devkit est connecté via Ethernet, utilisez l’adresse IP locale de l’appareil, que vous pouvez récupérer à partir du routeur ou du hub Ethernet. Si votre devkit est connecté via Wi-Fi, vous devez utiliser l’adresse IP qui a été attribuée à votre devkit pendant l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Si votre devkit est connecté à un réseau Wi-Fi mais que vous ne connaissez pas son adresse IP, accédez à Azure Percept Studio et [ouvrez le flux vidéo de votre appareil](./how-to-view-video-stream.md). La barre d’adresses de l’onglet Explorateur de flux vidéo affiche l’adresse IP de votre appareil.

1. Entrez votre mot de passe SSH lorsque vous y êtes invité.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Capture d’écran de la connexion en invite de commandes OpenSSH.":::

1. S’il s’agit de la première fois que vous vous connectez à votre devkit via OpenSSH, vous pouvez également être invité à accepter la clé de l’hôte. Entrez **Oui** pour accepter la clé.

1. Félicitations ! Vous vous êtes correctement connecté à votre devkit via SSH.

### <a name="using-putty"></a>Utilisation de PuTTY

1. Ouvrez PuTTY. Entrez ce qui suit dans la fenêtre **Configuration de PuTTY**, puis sélectionnez **Ouvrir** pour accéder à votre devkit par SSH :

    1. Nom d’hôte : [adresse IP]
    1. Port : 22
    1. Type de connexion : SSH

    Le **Nom d’hôte** est l’adresse IP de votre devkit. Si votre ordinateur est connecté au point d’accès Wi-Fi du devkit, l’adresse IP est 10.1.1.1. Si votre devkit est connecté via Ethernet, utilisez l’adresse IP locale de l’appareil, que vous pouvez récupérer à partir du routeur ou du hub Ethernet. Si votre devkit est connecté via Wi-Fi, vous devez utiliser l’adresse IP qui a été attribuée à votre devkit pendant l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Si votre devkit est connecté à un réseau Wi-Fi mais que vous ne connaissez pas son adresse IP, accédez à Azure Percept Studio et [ouvrez le flux vidéo de votre appareil](./how-to-view-video-stream.md). La barre d’adresses de l’onglet Explorateur de flux vidéo affiche l’adresse IP de votre appareil.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Capture d’écran de la fenêtre Configuration de PuTTY.":::

1. Un terminal PuTTY s’ouvre. Lorsque vous y êtes invité, entrez votre nom d’utilisateur et votre mot de passe SSH dans le terminal.

1. Félicitations ! Vous vous êtes correctement connecté à votre devkit via SSH.

## <a name="next-steps"></a>Étapes suivantes

Après vous être connecté à votre Azure Percept DK via SSH, vous pouvez effectuer diverses tâches, notamment la [résolution des problèmes des appareils](./troubleshoot-dev-kit.md) et les [mises à jour USB](./how-to-update-via-usb.md).