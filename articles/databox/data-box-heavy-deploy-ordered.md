---
title: Tutoriel - Commander Azure Data Box Heavy | Microsoft Docs
description: Dans ce tutoriel, découvrez Azure Data Box Heavy, une solution hybride qui vous permet d’importer des données locales dans Azure, et comment commander Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: 0bf9eb2eab829e22e72f19fc4da4575166ea2755
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743355"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutoriel : Commander Azure Data Box Heavy


Azure Data Box Heavy est une solution hybride qui vous permet d’importer vos données locales dans Azure de manière rapide, simple et fiable. Vous transférez vos données vers un dispositif de stockage de 770 To (capacité utilisable approximative) fourni par Microsoft, puis vous réexpédiez l’appareil. Ces données sont ensuite chargées dans Azure.

Ce tutoriel explique comment vous pouvez commander un Azure Data Box Heavy. Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * Prérequis pour le Data Box Heavy
> * Commander un Data Box Heavy
> * Suivre la commande
> * Annuler la commande

## <a name="prerequisites"></a>Prérequis

Effectuez les prérequis de configuration suivants pour l’appareil et le service Data Box avant de déployer l’appareil.

### <a name="for-installation-site"></a>Pour l’emplacement d’installation

Avant de commencer, assurez-vous que :

- L’appareil passe dans les portes et les entrées standard. Toutefois, vérifiez que l’appareil peut passer dans toutes vos entrées. Les dimensions de l’appareil sont les suivantes : largeur : 26 pouces (66,04 cm) longueur : 48 pouces (121,92 cm) hauteur : 28 pouces (71,12 cm).
- Si son installation est effectuée à un étage autre que le rez-de-chaussée, vous avez besoin d’un accès par le biais d’un ascenseur ou d’une rampe pour l’appareil. L’appareil pèse environ 500 livres (226,8 kg).
- Veillez à avoir une surface plane dans le centre de données à proximité d’une connexion réseau disponible et pouvant loger un appareil avec cet encombrement.

### <a name="for-service"></a>Pour le service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Pour l’appareil

Avant de commencer, assurez-vous que :
- Votre appareil est déballé.
- Vous disposez d’un ordinateur hôte connecté au réseau du centre de données. Le Data Box Heavy va copier les données de cet ordinateur. Votre ordinateur hôte doit exécuter un système d’exploitation pris en charge comme décrit dans [Configuration requise pour le système Azure Data Box Heavy](data-box-system-requirements.md).
- Vous devez disposer d’un ordinateur portable équipé d’un câble RJ-45 pour vous connecter à l’interface utilisateur locale et configurer l’appareil. Utilisez l’ordinateur portable pour configurer une fois chaque nœud de l’appareil.
- Votre centre de données doit avoir un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE.
- Vous devez disposer d’un câble 40 Gbits/s ou 10 Gbits/s par nœud d’appareil. Choisissez des câbles compatibles avec l’interface réseau [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) :

    - Concernant le câble 40 Gbits/s, l’extrémité du câble côté appareil doit être QSFP+.
    - Concernant le câble 10 Gbits/s, vous devez disposer d’un câble SFP+ dont une extrémité est branchée à un commutateur 10 G, avec une carte QSFP+ à SFP+ (ou une carte QSA) pour l’extrémité qui est branchée sur l’appareil.
    - Les câbles d’alimentation sont inclus avec l’appareil.

## <a name="order-data-box-heavy"></a>Commander un Data Box Heavy

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

L’équipe Data Box vous contactera pour obtenir plus d’informations sur vos exigences afin de déterminer si le service Data Box Heavy est disponible à l’emplacement requis. La commande reste dans l’état **Commandé** jusqu’à ce qu’elle ait été examinée. L’équipe vous avertira si, pour une raison quelconque, elle ne peut pas honorer votre commande.

## <a name="track-the-order"></a>Suivre la commande

Une fois la commande passée, vous pouvez suivre son état à partir du portail Azure. Accédez à votre commande Data Box Heavy, puis à **Vue d’ensemble** pour voir l’état. Le portail affiche la commande avec l’état **Commandé**.

Si l’appareil n’est pas disponible, vous recevez une notification. Si l’appareil est disponible, Microsoft identifie l’appareil à expédier et le prépare. Pendant la préparation de l’appareil, les actions suivantes se produisent :

- Des partages SMB sont créés pour chaque compte de stockage associé à l’appareil.
- Pour chaque partage, des informations d’identification d’accès (nom d’utilisateur et mot de passe) sont générées.
- Un mot de passe qui permet de déverrouiller l’appareil est également généré.
- Le Data Box Heavy est verrouillé pour empêcher tout accès non autorisé à l’appareil à tout moment.

Une fois l’appareil préparé, le portail affiche la commande avec l’état **Traité**.

![Commande Data Box Heavy traitée](media/data-box-overview/data-box-order-status-processed.png)

Ensuite, Microsoft prépare et achemine l’appareil via un transporteur régional. Vous recevez un numéro de suivi une fois l’appareil expédié. Le portail affiche la commande dont l’état est **Distribué**.

![Commande Data Box Heavy expédiée](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Annuler la commande

Pour annuler cette commande, dans le portail Azure, accédez à **Vue d’ensemble** et cliquez sur **Annuler** dans la barre de commandes.

Après avoir passé commande, vous pouvez l’annuler à tout moment tant qu’elle n’a pas été traitée.
 
Pour supprimer une commande annulée, accédez à **Vue d’ensemble** et cliquez sur **Supprimer** à partir de la barre de commandes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur Azure Data Box Heavy, notamment concernant les points suivants :

> [!div class="checklist"]
> * Prérequis
> * Commander un Data Box Heavy
> * Suivre la commande
> * Annuler la commande

Passez au tutoriel suivant pour apprendre à configurer votre Data Box Heavy.

> [!div class="nextstepaction"]
> [Configurer votre Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
