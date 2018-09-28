---
title: Configurer une Azure Data Box | Microsoft Docs
description: En savoir plus sur la configuration logicielle et réseau requise pour une Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6623744a26d79ccbc8b68ba84baf3a37024e0ed7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952305"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Didacticiel : branchement des câbles et connexion à une Azure Data Box

Ce didacticiel explique le raccordement, la connexion et le démarrage d’une Azure Data Box.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Raccordement de la Data Box
> * Connexion à la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez terminé le [didacticiel : Commander une Azure Data Box](data-box-deploy-ordered.md).
2. Vous avez reçu votre Data Box et l’état de la commande dans le portail est **Remis**. 
3. Vous avez consulté les [consignes de sécurité de la Data Box](data-box-safety.md).
4. Vous avez reçu un cordon d’alimentation avec mise à la terre à utiliser avec votre périphérique de stockage de 100 To.
5. Vous disposez d’un ordinateur hôte contenant les données à copier sur la Data Box. Votre ordinateur hôte doit
    - Exécuter un [système d’exploitation pris en charge](data-box-system-requirements.md)
    - Être connecté à un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, vous pouvez utiliser une liaison de données 1 GbE. Cependant, cela a une incidence sur les vitesses de copie. 
6. Vous devez avoir accès à une surface plane sur laquelle placer la Data Box. Si vous souhaitez placer l’appareil sur un rack standard, vous avez besoin d’un emplacement 7U dans le rack de votre centre de données. Vous pouvez placer l’appareil à plat ou en position verticale dans le rack.
7. Vous disposez des câbles suivants pour connecter votre Data Box à l’ordinateur hôte.
    - Deux câbles en cuivre Twinax SFP+ 10 GbE (à utiliser avec les interfaces réseau DATA1 et DATA2)
    - Un câble réseau RJ-45 CAT 6 (à utiliser avec l’interface réseau MGMT)
    - Un câble réseau RJ-45 CAT 6A OU RJ-45 CAT 6 (à utiliser avec l’interface réseau DATA3 et configurés en mode 10 Gbit/s ou 1 Gbit/s, respectivement)

## <a name="cable-your-device"></a>Raccordement de l'appareil

Procédez comme suit pour brancher les câbles de l’appareil.

1. Vérifiez que l’appareil n’a pas été endommagé. Si l’appareil est altéré ou gravement endommagé, ne continuez pas. Contactez immédiatement le Support Microsoft pour savoir si vous pouvez utiliser l’appareil ou si Microsoft doit vous envoyer un appareil de remplacement.
2. Transportez l’appareil à l’emplacement où vous souhaitez le mettre sous tension. Placez l’appareil sur une surface plane. L’appareil peut également être placé sur une étagère de rack standard.
3. Branchez les câbles d’alimentation et réseau. La vue arrière d’un appareil connecté avec une configuration commune est présentée ci-dessous. 
    
    ![Vue arrière d’une Data Box raccordée](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Branchez le câble d’alimentation à l’emplacement d’entrée étiqueté. L’autre extrémité du câble d’alimentation doit être branché sur une unité de distribution d’alimentation.
    2. Utilisez le câble RJ-45 CAT 6pour raccorder le port MGNT à un ordinateur portable.            
    3. Utilisez le câble réseau RJ-45 CAT 6A pour brancher le port DATA 3 à une extrémité. DATA 3 est configuré en tant que 10 GbE si vous utilisez un câble RJ-45 CAT 6A a et en tant que 1 GbE si vous utilisez un câble RJ-45 CAT 6.
    4. Utilisez les câbles en cuivre 10 GbE SFP + Twinax pour raccorder respectivement les ports DATA 1 et DATA 2. 
    5. L’autre extrémité des câbles des ports de données est branchée sur l’ordinateur hôte via un commutateur 10 GbE.

4. Localisez le bouton d’alimentation sur le panneau de commande avant de l’appareil. Allumez l'appareil.

    ![Bouton d’alimentation de la Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Connexion à l’appareil

Procédez comme suit pour configurer votre appareil à l’aide de l’interface utilisateur web locale et de l’interface utilisateur du portail.

1. Configurez la carte Ethernet sur l'ordinateur portable que vous utilisez pour vous connecter à l’appareil avec l'adresse IP statique 192.168.100.5 et le sous-réseau 255.255.255.0. 
2. Connectez-vous au port MGMT de votre appareil et accédez à l’interface utilisateur web locale à l’adresse https://192.168.100.10. Une fois l’appareil sous tension, cela peut prendre jusqu'à 5 minutes.
3. Cliquez sur **Détails** puis **accédez à la page web**.

   ![Connexion à l'interface utilisateur web locale](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. Vous voyez une page de **connexion** pour l’interface utilisateur web locale. Assurez-vous que le numéro de série de l’appareil correspond dans l’interface utilisateur du portail et l’interface utilisateur web locale. L’appareil est verrouillé à ce stade.
5. Connectez-vous au [portail Azure](https://portal.azure.com).
6. Téléchargez les informations d’identification de l’appareil à partir du portail. Accédez à **Général > Détails de l’appareil**. Copiez le **mot de passe de l'appareil**. Le mot de passe est lié à une commande spécifique dans le portail. 

    ![Obtention des informations d’identification d’un appareil](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Indiquez le mot de passe que vous avez obtenu à partir du portail Azure à l’étape précédente pour vous connecter à l’interface utilisateur web locale de l’appareil. Cliquez sur **Se connecter**.
8. Sur le **tableau de bord**, assurez-vous que les interfaces réseau sont configurées. 
    - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. 
    - Si DHCP n’est pas activé, accédez à **Définir des interfaces réseau** et affectez des adresses IP statiques si nécessaire.

    ![Page du tableau de bord d’un appareil](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Une fois que l’appareil est configuré, vous pouvez vous connecter aux partages de l’appareil et copier les données de votre ordinateur vers l’appareil. 

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Raccordement de la Data Box
> * Connexion à la Data Box

Passez au didacticiel suivant pour découvrir comment copier des données sur votre Data Box.

> [!div class="nextstepaction"]
> [Copier des données sur une Azure Data Box](./data-box-deploy-copy-data.md)

