---
title: Résoudre les problèmes relatifs aux appareils Azure Percept DK
description: Obtenir des conseils de dépannage pour certains des problèmes les plus courants avec Azure Percept DK et IoT Edge
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e644732a90652b1672dc77bfc6db86d7a6b7295a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221076"
---
# <a name="troubleshoot-the-azure-percept-dk-device"></a>Résoudre les problèmes relatifs aux appareils Azure Percept DK

L’objectif de cet article de résolution des problèmes est d’aider les utilisateurs Azure Percept DK à résoudre rapidement les problèmes courants liés à leurs kits de développement. Il fournit également des conseils sur la collecte des journaux lorsque vous avez besoin d’une prise en charge supplémentaire.

## <a name="log-collection"></a>Collection de journaux
Dans cette section, vous allez obtenir des conseils sur les journaux à collecter et sur leur mode de collecte.

### <a name="how-to-collect-logs"></a>Comment collecter les journaux
1. Connectez-vous à votre kit de développement via une [connexion SSH](./how-to-ssh-into-percept-dk.md).
1. Exécutez les commandes nécessaires dans la fenêtre de terminal SSH. Consultez la section suivante pour obtenir la liste des commandes de collecte des journaux.
1. Redirigez une sortie vers un fichier .txt de façon à l’analyser de façon plus approfondie, utilisez la syntaxe suivante :
    ```console
    sudo [command] > [file name].txt
    ```
1. Modifiez les autorisations du fichier .txt pour qu’il puisse être copié :
    ```console
    sudo chmod 666 [file name].txt
    ```
1. Copiez le fichier sur votre PC hôte via SCP :
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]``` fait référence à l’emplacement sur votre PC hôte où vous voulez copier le fichier .txt. ```[remote username]``` est le nom d’utilisateur SSH choisi lors de l’[expérience de configuration](./quickstart-percept-dk-set-up.md).

### <a name="log-types-and-commands"></a>Types de journaux et commandes

|Objectif des journaux      |Quand les collecter         |Commande                     |
|-----------------|---------------------------|----------------------------|
|*Support bundle* : fournit un ensemble de journaux nécessaires à la plupart des demandes de support technique.|Collecter à chaque demande de support.|```sudo iotedge support-bundle --since 1h``` <br><br>*« --since 1h » peut être modifié selon n’importe quelle heure, par exemple, « 6h » (6 heures), « 6d » (6 jours) ou « 6m » (6 minutes)*|
|*OOBE logs* : enregistre des détails sur l’expérience de configuration.|Collecte lorsque vous rencontrez des problèmes lors de la configuration.|```sudo journalctl -u oobe -b```|
|*edgeAgent logs* : enregistre les numéros de version de tous les modules en cours d’exécution sur votre appareil.|Collecte lorsqu’un ou plusieurs modules ne fonctionnent pas.|```sudo iotedge logs edgeAgent```|
|*Module container logs* : enregistre des détails sur des conteneurs de modules IoT Edge spécifiques|Collecter lorsque vous rencontrez des problèmes avec un module|```sudo iotedge logs [container name]```|
|*Journaux réseau* : ensemble de journaux couvrant les services Wi-Fi et la pile réseau.|Collectez lorsque vous rencontrez des problèmes de Wi-Fi ou de réseau.|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>Exécutez les deux commandes. Chaque commande collecte plusieurs journaux et les place dans une seule sortie.|

## <a name="troubleshooting-commands"></a>Commandes de dépannage
Voici un ensemble de commandes qui peuvent être utilisées pour résoudre les problèmes que vous pouvez rencontrer avec le kit de développement. Pour exécuter ces commandes, vous devez d’abord vous connecter à votre kit de développement [via SSH](./how-to-ssh-into-percept-dk.md). 

Pour plus d’informations sur les commandes Azure IoT Edge, consultez la [documentation sur la résolution des problèmes liés aux appareils Azure IoT Edge](../iot-edge/troubleshoot.md). 

|Fonction         |Quand l’utiliser                    |Commande                 |
|------------------|----------------------------|---------------------------|
|Vérifie la version du logiciel sur le kit de développement.|Utilisez chaque fois que vous avez besoin de vérifier la version du logiciel qui se trouve dans votre kit de développement.|```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version```|
|Vérifie la température du kit de développement|Utilisez dans les cas où vous pensez que le kit de développement peut être à l’origine d’une surchauffe.|```cat /sys/class/thermal/thermal_zone0/temp```|
|Vérifie l’ID de télémétrie du kit de développement|Utilisez dans les cas où vous devez connaître l’identificateur de télémétrie unique des kits de développement.|```sudo azure-device-health-id```|
|Vérifie le statut d’IoT Edge|À utiliser en cas de problème avec les modules IoT Edge se connectant au cloud.|```sudo iotedge check```|
|Redémarre le démon de sécurité Azure IoT Edge|À utiliser lorsque IoT Edge ne répond pas ou ne fonctionne pas correctement.|```sudo systemctl restart iotedge``` |
|Liste les modules Azure IoT Edge déployés|À utiliser lorsque vous avez besoin de voir tous les modules déployés sur le kit de développement|```sudo iotedge list``` |
|Affiche l’espace disponible/total dans le ou les systèmes de fichiers spécifiés|À utiliser si vous avez besoin de connaître le stockage disponible sur le kit de développement.|```df [option] [file]```|
|Affiche les informations relatives à l’interface et à l’adresse IP du kit de développement|À utiliser lorsque vous avez besoin de connaître l’adresse IP du kit de développement.|`ip route get 1.1.1.1`        | 
|Afficher l’adresse IP du kit de développement uniquement|À utiliser lorsque vous souhaitez uniquement l’adresse IP du kit de développement et pas les autres informations de l’interface.|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>Erreurs de mise à jour USB

|Erreur :                                    |Solution :                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX pendant la mise à jour flash USB via UUU |Cette erreur est le résultat d’un échec de connexion USB lors de la mise à jour UUU. Si le câble USB n’est pas correctement connecté aux ports USB du PC ou de la carte Percept DK, une erreur de ce type se produit. Essayez de débrancher et de reconnecter les deux extrémités du câble USB, et de réajuster le câble pour garantir une connexion sécurisée.|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>Effacement de l’espace sur disque dur sur Azure Percept DK
Il existe deux composants qui occupent l’espace du disque dur sur Azure Percept DK, les journaux de conteneur Docker et les conteneurs Docker eux-mêmes. Pour vous assurer que les journaux de conteneur n’occupent pas tout l’espace disponible, Azure Percept DK présente une rotation de journaux intégrée qui effectue une rotation des anciens journaux à mesure que de nouveaux journaux sont générés.

Dans les cas où le nombre de conteneurs Docker provoque des problèmes d’espace disque, vous pouvez supprimer les conteneurs inutilisés en procédant comme suit :
1. [Établissez une session SSH avec le kit de développement](./how-to-ssh-into-percept-dk.md)
1. Exécutez cette commande : `docker system prune`

Cette opération supprime tous les conteneurs, réseaux, images et, éventuellement, volumes inutilisés. Pour plus d’informations, consultez [cette page](https://docs.docker.com/engine/reference/commandline/system_prune/).

## <a name="azure-percept-dk-carrier-board-led-states"></a>États des LED de la carte mère du DK Azure Percept

Il y a trois petites LED en haut du support de la carte mère. Une icône de nuage figure à côté de la LED 1, une icône de Wi-Fi figure à côté de la LED 2 et une icône de point d’exclamation figure à côté de la LED 3. Pour plus d’informations sur chaque LED d’état, consultez le tableau ci-dessous.

|LED             |State      |Description                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |Activé (allumée en continu) |L’appareil est connecté à un hub IoT. |
|LED 2 (Wi-Fi)   |Clignotement lent |L'appareil est prêt à être configuré par Wi-Fi Easy Connect et annonce sa présence à un configurateur. |
|LED 2 (Wi-Fi)   |Clignotement rapide |L’authentification a réussi, l’association de l’appareil est en cours. |
|LED 2 (Wi-Fi)   |Activé (allumée en continu) |L’authentification et l’association ont réussi ; l’appareil est connecté à un réseau Wi-Fi. |
|LED 3           |N/D         |LED non utilisée. |