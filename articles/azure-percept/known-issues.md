---
title: Problèmes connus d’Azure Percept
description: En savoir plus sur les problèmes connus d’Azure Percept et leurs solutions de rechange
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193330"
---
# <a name="known-issues"></a>Problèmes connus

Si vous rencontrez l’un de ces problèmes, il n’est pas nécessaire d’ouvrir un bogue. Si vous rencontrez des problèmes avec l’une des solutions de rechange, veuillez ouvrir un problème.

|Domaine|Description du problème|Solution de contournement|
|-------|---------|---------|
| Expérience d’intégration | Impossible de terminer l’intégration, sauf si le Wi-Fi de l’appareil est configuré (échec de la connexion Azure). | 1. SSH vers le point d’accès de l’appareil (10.1.1.1) <br> 2. Identifier et copier l’adresse IP Ethernet de l’appareil <br> 3. Connectez-vous à l’expérience d’intégration à l’aide de l’URL IP Ethernet copiée |
| Expérience d’intégration | Le fait de cliquer sur les liens du CLUF au cours de l’intégration peut parfois ne pas ouvrir de nouvelle page Web. | Copiez le lien et ouvrez-le dans une autre fenêtre du navigateur. |
| Expérience d’intégration | Impossible d’effectuer une expérience d’intégration en cas de connexion à niveau d’accès chaud Wi-Fi mobile. | Connectez votre appareil directement à SoftAP, à un réseau Wi-Fi ou à un réseau via Ethernet. |
| Wi-Fi/SoftAP | Le SoftAP peut parfois se déconnecter ou disparaître. | Nous étudions actuellement ce problème.  En général, le redémarrage résout le problème. |
| Wi-Fi | Le bouton matériel qui active ou désactive le Wi-Fi SoftAP ne fonctionne pas. | Continuez à essayer d’appuyer sur le bouton ou redémarrez l’appareil. |
| Wi-Fi | Les utilisateurs peuvent voir un message après s’être connectés au Wi-Fi indiquant « Ce réseau Wi-Fi utilise une norme de sécurité plus ancienne ». | Le niveau d’accès chaud/SoftAP utilise l’algorithme de chiffrement WEOP. |
| Wi-Fi | Impossible de se connecter à SoftAP à partir du PC Windows 10 avec le message d’erreur suivant : <br> « Impossible de se connecter à ce réseau » | Redémarrez le kit de développement et l’ordinateur. |
| Mise à jour d’appareil | Les conteneurs ne s’exécutent pas après une mise à jour OTA. | Utilisez SSH dans l’appareil et redémarrez le conteneur IoT Edge à l’aide de cette commande `systemctl restart iotedge`. Cette opération redémarre tous les conteneurs. |
| Mise à jour d’appareil | Les utilisateurs peuvent recevoir un message indiquant que la mise à jour a échoué, même si ce n’est pas le cas. | Confirmez l’appareil mis à jour en accédant au jumeau d’appareil dans IoT Hub. Ce problème est résolu après la première mise à jour. |
| Mise à jour d’appareil | Les utilisateurs peuvent perdre leurs paramètres de connexion Wi-Fi après la première mise à jour. | Exécutez le processus d’intégration après la mise à jour pour configurer la connexion Wi-Fi. Ce problème est résolu après la première mise à jour. |
| Mise à jour d’appareil | Après l’exécution d’une mise à jour OTA, les utilisateurs ne peuvent plus se connecter via SSH à l’aide des comptes d’utilisateur créés précédemment. Les nouveaux utilisateurs SSH ne peuvent pas être créés via l’expérience d’intégration. Ce problème affecte les systèmes effectuant des mises à jour OTA à partir des versions d’images préinstallées suivantes : 2020.110.114.105 et 2020.109.101.105. | Pour récupérer vos profils utilisateur, suivez les étapes suivantes après la mise à jour OTA : <br> Utilisez [SSH dans votre kit de développement](./how-to-ssh-into-percept-dk.md) en utilisant « root » comme nom d’utilisateur. Si vous avez désactivé la connexion de l’utilisateur « root » SSH, par le biais de l’expérience d’intégration, vous devez la réactiver. Exécutez cette commande après vous être connecté avec succès : <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Pour récupérer les données d’origine de l’utilisateur précédent, exécutez la commande suivante : <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Mise à jour d’appareil | Après avoir effectué une mise à jour OTA, les groupes de mises à jour sont perdus. | Mettez à jour la balise de l’appareil en suivant [ces instructions](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group). |
| Dev Tools Pack Installer | L’installation facultative de Caffe peut échouer si Docker ne s’exécute pas correctement sur le système. | Assurez-vous que Docker est installé et en cours d’exécution, puis recommencez l’installation de Caffe. |
| Dev Tools Pack Installer | L’installation de CUDA facultative échoue sur les systèmes incompatibles. | Vérifiez la compatibilité du système avec CUDA avant d’exécuter le programme d’installation. |
| Docker, Réseau, IoT Edge | Si votre réseau interne utilise 172.x.x.x, les conteneurs de l’ancrage ne pourront pas se connecter à la périphérie. | Ajoutez une section Bip spéciale au fichier /etc/docker/daemon.json comme suit : `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Les liens « Afficher le flux » dans Azure Percept Studio ne s’ouvrent pas dans une nouvelle fenêtre qui affiche le flux web de l’appareil. | 1. Ouvrez le [Portail Azure](https://portal.azure.com) et sélectionnez **IoT Hub**. <br> 2. Cliquez sur l’IoT Hub auquel votre appareil est connecté. <br> 3. Sélectionnez **IoT Edge** sous **Gestion automatique des appareils** sur votre page IoT Hub. <br> 4. Sélectionnez votre appareil dans la liste. <br> 5. Sélectionnez **Définir les modules** en haut de la page de votre appareil. <br> 6. Cliquez sur l’icône Corbeille en regard de **HostIpModule** pour supprimer le module. <br> 7. Pour confirmer l’action, cliquez sur **Examiner + créer**, puis sur **Créer**. <br> 8. Ouvrez [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) et cliquez sur **Appareils** dans le panneau de menu à gauche. <br> 9. Sélectionnez votre appareil dans la liste. <br> 10. Sous l’onglet **Vision**, cliquez sur **Afficher votre flux d’appareil**. Votre appareil va télécharger une nouvelle version de HostIpModule et ouvrir un onglet de navigateur avec le flux web de votre appareil. |