---
title: Configurer des paramètres réseau avancés sur l’appareil Azure Percept DK
description: Cet article guide l’utilisateur parmi les paramètres réseau avancés dans le cadre de l’installation de l’appareil Azure Percept DK
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: ac06d331bcd587c1294c21388faac75cc6f8cfd6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219642"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>Configurer des paramètres réseau avancés sur l’appareil Azure Percept DK

L’appareil Azure Percept DK vous permet de contrôler divers composants réseau sur le kit de développement. Pour cela, il faut définir les paramètres réseau avancés lors de l’installation. Pour accéder à ces paramètres, vous devez [démarrer l’installation](./quickstart-percept-dk-set-up.md) et sélectionner **Accéder aux paramètres réseau avancés** dans la page **Connexion réseau**.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="Lancer les paramètres réseau avancés à partir de la page Connexion réseau":::

## <a name="select-the-security-setting"></a>Sélectionner le paramètre de sécurité
IPv4 et IPv6 sont tous les deux pris en charge sur l’appareil Azure Percept DK pour la connectivité locale.

> [!NOTE]
> Azure IoTHub [ne prend pas en charge IPv6](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6). IPv4 doit être utilisé pour communiquer avec IoTHub.
1. Sélectionnez la case d’option IPv4, puis sélectionnez un élément sous Paramètres réseau pour modifier ses paramètres IPv4.
1. Sélectionnez la case d’option IPv6, puis sélectionnez un élément sous Paramètres réseau pour modifier ses paramètres IPv6.
1. Les options de **Paramètre réseau** peuvent varier en fonction de votre sélection.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="Sélectionner un protocole de sécurité pour afficher la liste des options réseau":::

## <a name="define-a-static-ip-address"></a>Définir une adresse IP statique

1. Dans la page **Paramètres réseau avancés**, sélectionnez **Définir une adresse IP statique** dans la liste.
1. Sélectionnez votre **interface réseau** dans le menu déroulant.
1. Décochez **Adresse IP dynamique**.
1. Entrez votre adresse IP statique.
1. Entrez l’adresse IP de votre sous-réseau (également appelée « masque de sous-réseau »).
1. Entrez l’adresse IP de votre passerelle (également appelée « passerelle par défaut »).
1. Le cas échéant, entrez votre adresse DNS.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Précédent** pour revenir à la page **Paramètres réseau avancés**.

## <a name="define-dns-server-for-docker"></a>Définir le serveur DNS pour Docker
Ces paramètres vous permettent de modifier ou d’ajouter de nouvelles adresses IP DNS Docker.

> [!NOTE]
> Le service Docker est configuré pour accepter uniquement les entrées DNS IPv4.  Les entrées ajoutées à partir des écrans IPv6 seront ignorées.

1. Dans la page **Paramètres réseau avancés**, sélectionnez **Définir un serveur DNS pour Docker** dans la liste.
1. Entrer votre adresse DNS IPv4 Docker.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Précédent** pour revenir à la page **Paramètres réseau avancés**.

## <a name="define-bridge-internet-protocol-for-docker"></a>Définir le protocole Internet de pont pour Docker
Les écrans du protocole Internet de pont vous permettent de modifier l’espace d’adressage IPv4 pour les conteneurs Docker.

Si l’adresse IP de votre appareil partage la même route que le service Docker de l’appareil Azure Percept DevKit (172.17.x.x), vous devez modifier le pont de Docker pour permettre les communications entre les conteneurs Docker et Azure IoTHub.  

1. Dans la page **Paramètres réseau avancés**, sélectionnez **Définir un protocole Internet de pont pour Docker** dans la liste.
1. Entrez l’adresse IPv4 du protocole Internet de pont (BIP) Docker.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Précédent** pour revenir à la page **Paramètres réseau avancés**.

## <a name="define-an-internet-proxy-server"></a>Définir un serveur proxy Internet
Cette option vous permet de définir un serveur proxy.    

1. Dans la page **Paramètres réseau avancés**, sélectionnez **Définir un serveur proxy Internet** dans la liste.
1. Cochez la case **Utiliser un serveur proxy** pour activer cette option.
1. Entrez l’**adresse HTTP** de votre serveur proxy (le cas échéant).
1. Entrez l’**adresse HTTPS** de votre serveur proxy (le cas échéant).
1. Entrez l’**adresse FTP** de votre serveur proxy (le cas échéant).
1. Dans la zone **Aucune adresse de proxy**, entrez les adresses IP pour lesquelles le serveur proxy ne doit pas être utilisé.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Précédent** pour revenir à la page **Paramètres réseau avancés**.

## <a name="setup-zero-touch-provisioning"></a>Configuration d’un provisionnement sans contact

> [!IMPORTANT]
> Le paramètre **Configurer un provisionnement sans contact** n’est pas opérationnel actuellement.

Cette option vous permet de transformer votre appareil Azure Percept DK en un [configurateur en bloc Wi-Fi Easy Connect<sup>TM</sup>](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.) pour l’intégration de plusieurs appareils en même temps à votre infrastructure Wi-Fi.  

## <a name="define-access-point-passphrase"></a>Définir la phrase secrète du point d’accès 
Cette option vous permet de mettre à jour la phrase secrète du point d’accès Wi-Fi de l’appareil Azure Percept DK.  

> [!CAUTION]
> Vous serez immédiatement déconnecté du point d’accès Wi-Fi après avoir enregistré votre nouvelle phrase secrète.  Reconnectez-vous à l’aide de la nouvelle phrase secrète pour récupérer l’accès.  

Conditions relatives à la phrase secrète :
- Elle doit comprendre entre 12 et 123 caractères.
- Elle doit contenir au moins une minuscule, une majuscule, un chiffre et un caractère spécial.

1. Dans la page **Paramètres réseau avancés**, sélectionnez **Définir la phrase secrète du point d’accès** dans la liste.
1. Entrez une nouvelle phrase secrète.
1. Sélectionnez **Enregistrer**.
1. Sélectionnez **Précédent** pour revenir à la page **Paramètres réseau avancés**.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez fini d’apporter des modifications dans **Paramètres réseau avancés**, sélectionnez le bouton **Précédent** pour [poursuivre l’installation de l’appareil Azure Percept DK](./quickstart-percept-dk-set-up.md).