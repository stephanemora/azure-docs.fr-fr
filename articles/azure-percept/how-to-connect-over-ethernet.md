---
title: Guide pratique pour lancer l’installation d’Azure Percept DK via Ethernet
description: Ce guide montre comment lancer l’installation d’Azure Percept DK par le biais d’une connexion Ethernet.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 01a1c2fcb5981681c504e3e61fc68a10922e15d5
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441910"
---
# <a name="how-to-launch-the-azure-percept-dk-setup-experience-over-ethernet"></a>Guide pratique pour lancer l’installation d’Azure Percept DK via Ethernet

Dans ce guide pratique, vous allez voir comment lancer l’installation d’Azure Percept DK via une connexion Ethernet. Il vient en complément du [Guide de démarrage rapide : Configurer Azure Percept DK et déployer votre premier modèle IA](./quickstart-percept-dk-set-up.md). Étudiez chacune des options ci-dessous et choisissez celle qui convient le mieux à votre environnement.

## <a name="prerequisites"></a>Prérequis

- Azure Percept DK ([à télécharger ici](https://go.microsoft.com/fwlink/?linkid=2155270))
- Un ordinateur hôte Windows, Linux ou OS X avec prise en charge du Wi-Fi ou d’Ethernet, et un navigateur web.
- Un câble réseau

## <a name="identify-your-dev-kits-ip-address"></a>Trouver l’adresse IP du devkit

Pour lancer l’installation d’Azure Percept DK via une connexion Ethernet, vous devez connaître l’adresse IP de votre devkit. Cet article aborde les trois options suivantes :
1. Sur le routeur réseau
1. Via une connexion SSH
1. Via l’outil Nmap

### <a name="from-your-network-router"></a>Sur le routeur réseau
Le moyen le plus rapide de trouver l’adresse IP de votre devkit est de regarder sur votre routeur réseau.
1. Connectez le devkit et le routeur à l’aide du câble Ethernet.
1. Mettez votre Azure Percept DK sous tension.
1. Sur le routeur réseau, recherchez l’autocollant où se trouvent les instructions d’accès.

    **Voici des exemples d’autocollants de routeur**

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="Exemple d’autocollant de routeur réseau":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="Autre exemple d’autocollant de routeur réseau":::

1. Sur votre ordinateur connecté par Ethernet ou Wi-Fi, ouvrez un navigateur web.
1. Tapez l’adresse du routeur dans la barre du navigateur, telle qu’elle est indiquée sur l’autocollant.
1. Lorsque vous y êtes invité, entrez le nom et le mot de passe du routeur tels qu’ils sont indiqués sur l’autocollant.
1. Une fois dans l’interface du routeur, sélectionnez Mes appareils (ou une option au nom similaire sur votre routeur).
1. Recherchez le devkit Azure Percept DK dans la liste des appareils.
1. Copiez l’adresse IP du devkit Azure Percept DK.

### <a name="via-ssh"></a>Via une connexion SSH
Il est possible de trouver l’adresse IP de votre devkit en vous y connectant via une connexion SSH.

> [!NOTE]
> L’utilisation de la méthode SSH pour trouver l’adresse IP de votre devkit nécessite que vous puissiez vous connecter au point d’accès Wi-Fi de votre devkit. Si cela vous est impossible, vous devez utiliser les autres méthodes disponibles.

1. Connectez le devkit et le routeur à l’aide du câble Ethernet.
1. Mettez sous tension le devkit Azure Percept DK.
1. Connectez-vous à votre devkit via une connexion SSH. Pour obtenir des instructions détaillées sur la connexion au devkit via une connexion SSH, consultez [Se connecter à Azure Percept DK via une connexion SSH](./how-to-ssh-into-percept-dk.md).
1. Pour obtenir la liste des adresses IP du réseau local Ethernet, tapez la commande ci-dessous dans la fenêtre de votre terminal SSH :

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="Exemple de recherche d’une adresse IP sur le réseau local dans le terminal SSH":::


1. L’adresse IP du devkit s’affiche après « inet ». Copiez l’adresse IP.

### <a name="using-the-nmap-tool"></a>Via l’outil Nmap
Vous pouvez également utiliser des outils gratuits disponibles sur le web pour trouver l’adresse IP de votre devkit. Dans ces instructions, nous aborderons un outil nommé Nmap.
1. Connectez le devkit et le routeur à l’aide du câble Ethernet.
1. Mettez sous tension le devkit Azure Percept DK.
1. Sur votre ordinateur hôte, téléchargez et installez le [scanneur de sécurité Nmap gratuit](https://nmap.org/download.html) qui est compatible avec votre plateforme (Windows/Mac/Linux).
1. Recherchez la passerelle par défaut de votre ordinateur : [How to Find Your Default Gateway](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/)
1. Ouvrez l’application Nmap. 
1. Entrez votre passerelle par défaut dans le champ *Target* (Cible) et ajoutez **/24** à la fin de celle-ci. Pour *Profile*, sélectionnez **Quick scan** (Analyse rapide), puis sélectionnez le bouton **Scan** (Analyser).
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Exemple d’entrée dans l’outil Nmap":::
 
1. Dans les résultats, recherchez le devkit Azure Percept DK dans la liste des appareils (**apd-xxxxxxxx**).
1. Copiez l’adresse IP du devkit Azure Percept DK. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Lancer l’installation d’Azure Percept DK
1. Connectez le devkit et le routeur à l’aide du câble Ethernet.
1. Mettez sous tension le devkit Azure Percept DK.
1. Ouvrez un navigateur web et collez-y l’adresse IP du devkit. L’installation doit démarrer dans le navigateur.

## <a name="next-steps"></a>Étapes suivantes
- [Terminer l’installation](./quickstart-percept-dk-set-up.md)