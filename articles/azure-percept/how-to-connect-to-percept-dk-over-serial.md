---
title: Établir une connexion série avec Azure Percept DK
description: Comment configurer une connexion série à votre DK Azure Percept avec un câble série USB à TTL
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: fa28fc845e8f15f06170a31d1d22892eca061bb7
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220405"
---
# <a name="connect-to-azure-percept-dk-over-serial"></a>Établir une connexion série avec Azure Percept DK

Effectuez les étapes ci-dessous pour configurer une connexion série à votre DK Azure Percept avec [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> **Si vous disposez d’un kit de développement privé en préversion**, nous vous **déconseillons** de tenter de connecter votre kit de développement en série, sauf en cas de défaillance extrême (par exemple, vous avez installé votre appareil). La connexion en série nécessite que le kit de développement privé en préversion soit désassemblé pour accéder aux broches GPIO. Il est très difficile de démonter le boîtier de la carte porteuse et cela peut entraîner la rupture des câbles de l’antenne Wi-Fi.

## <a name="prerequisites"></a>Prérequis

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- PC hôte
- DK Azure Percept
- [Câble série USB à TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Câble série USB à TTL.":::

## <a name="start-the-serial-connection"></a>Lancer la connexion série

1. Connectez le [câble série USB à TTL](https://www.adafruit.com/product/954) aux trois broches GPIO sur la carte mère comme indiqué ci-dessous.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="Connexions des broches série de la carte de base.":::

1. Mettez sous tension votre kit de développement et connectez le côté USB du câble série à votre PC.

1. Dans Windows, accédez à **Démarrer** -> **Paramètres de Windows Update** -> **Afficher les mises à jour facultatives** -> **Mises à jour de pilotes** Recherchez une mise à jour Série à USB dans la liste, cochez la case en regard de celle-ci, puis sélectionnez **Télécharger et installer**.  

1. Ensuite, ouvrez le gestionnaire de périphériques Windows (**Démarrer** -> **Gestionnaire de périphériques**). Accédez à **Ports**, puis sélectionnez **USB à UART** pour ouvrir **Propriétés**. Notez le port COM auquel votre appareil est connecté.

1. Sélectionnez l’onglet **Paramètres du port**. Vérifiez que **Bits par seconde** a la valeur 115200.

1. Ouvrez PuTTY. Entrez les informations suivantes, puis sélectionnez **Ouvrir** pour établir une connexion série à votre devkit :

    1. Ligne série : COM[port #]
    1. Vitesse : 115200
    1. Type de connexion : Série

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Fenêtre de session PuTTY avec les paramètres de connexion série sélectionnés.":::
