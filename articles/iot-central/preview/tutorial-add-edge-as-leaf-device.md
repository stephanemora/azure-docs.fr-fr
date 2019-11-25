---
title: Ajouter un appareil Azure IoT Edge à Azure IoT Central | Microsoft Docs
description: Ajouter un appareil Azure IoT Edge à Azure IoT Central en qualité d’opérateur
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892635"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Didacticiel : Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel vous montre comment ajouter et configurer un *appareil Azure IoT Edge* à votre application Microsoft Azure IoT Central. Dans ce tutoriel, nous avons choisi une machine virtuelle Linux prenant en charge Azure IoT Edge à partir de la Place de marché Azure.

Le didacticiel se déroule en deux parties :

* Tout d’abord, en tant qu’opérateur, vous allez apprendre à procéder à un provisionnement en priorité sur le cloud pour un appareil Azure IoT Edge.
* Ensuite, vous apprendrez à procéder à un provisionnement en priorité sur l’appareil pour un appareil Azure IoT Edge.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un nouvel appareil Azure IoT Edge
> * Configurer l’appareil Azure IoT Edge pour faciliter son provisionnement à l’aide d’une clé SAS
> * Afficher les tableaux de bord, le module Health dans IoT Central
> * Envoyer des commandes à un module en cours d’exécution sur l’appareil Azure IoT Edge
> * Définir les propriétés d’un module en cours d’exécution sur l’appareil Azure IoT Edge

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer d’une application Azure IoT Central. Suivez ce guide de démarrage rapide pour [créer une application Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Activer le groupe d’inscriptions Azure IoT Edge
Activez les clés SAS pour le groupe d’inscriptions Azure IoT Edge à partir de la page Administration.

![Modèle d’appareil – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Provisionnement de l’appareil Azure IoT Edge en priorité sur le cloud   
Dans cette section, vous allez créer un appareil Azure IoT Edge à partir du **modèle de capteur environnemental** et provisionner un appareil. Cliquez sur Devices (Appareils) dans le volet de navigation gauche, puis sur Environment Sensor Template (Modèle de capteur environnemental). 

![Modèle d’appareil – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Cliquez sur **+ New** (Nouveau) et entrez l’ID et le nom d’appareil de votre choix. 

![Modèle d’appareil – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

L’appareil passe en mode **Registered** (Inscrit).

![Modèle d’appareil – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Déployer une machine virtuelle Linux prenant en charge Azure IoT Edge

>Remarque : Vous pouvez utiliser n’importe quel ordinateur ou appareil. Système d’exploitation : Linux ou Windows

Pour ce tutoriel, nous avons opté pour une machine virtuelle Linux prenant en charge Azure IoT, qui peut être créée sur Azure. Sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview), où vous êtes dirigé, cliquez sur le bouton **Obtenir**. 

![Place de marché Azure](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Cliquez sur **Continuer**.

![Place de marché Azure](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Vous accédez alors au Portail Azure. Cliquez sur le bouton **Créer**.

![Place de marché Azure](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Sélectionnez Abonnement, créez de préférence un nouveau groupe de ressources, sélectionnez USA Ouest 2 pour la disponibilité des machines virtuelles, entrez l’utilisateur et le mot de passe. Mémorisez le nom d’utilisateur et le mot de passe, vous en aurez besoin aux étapes suivantes. Cliquez sur **Vérifier + créer**.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Après validation, cliquez sur **Créer**.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

La création des ressources prend quelques minutes. Cliquez sur **Accéder à la ressource**.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Provisionner la machine virtuelle en tant qu’appareil Azure IoT Edge 

Dans le volet de navigation de gauche, sous Support et résolution des problèmes, cliquez sur Console série.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Un écran semblable au suivant s’affiche.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Appuyez sur Entrée, fournissez le nom d’utilisateur et le mot de passe demandés, puis appuyez sur Entrée. 

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Pour exécuter une commande en tant qu’administrateur/racine, exécutez la commande : **sudo su –**

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Vérifiez la version du runtime Azure IoT Edge. La version en disposition générale actuelle est 1.0.8.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installez l’éditeur vim ou utilisez nano si vous préférez. 

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Modifiez le fichier config.yaml d’Azure IoT Edge.

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Faites défiler vers le bas et mettez en commentaire la partie « connection string » du fichier yaml. 

**Avant**

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Après** (Appuyez sur Échap et appuyez sur le a minuscule pour commencer la modification)

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Supprimez les marques de commentaire de la partie « symmetric key » du fichier yaml. 

**Avant**

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Après**

![Machine virtuelle Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Accédez à IoT Central et obtenez l’ID d’étendue (Scope ID), l’ID d’appareil (Device ID) et la clé primaire (Primary Key) de l’appareil Azure IoT Edge. ![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Accédez à la zone Linux et remplacez les valeurs d’ID d’étendue et d’ID d’inscription par l’ID d’appareil et la clé symétrique.

Appuyez sur **Échap**, tapez **:wq!** et appuyez sur **Entrée** pour enregistrer vos modifications.

Redémarrez Azure IoT Edge pour traiter vos modifications et appuyez sur **Entrée**.

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Tapez **iotedge list**. Après quelques minutes, les trois modules déployés sont indiqués.

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Explorateur d’appareils IoT Central 

Dans IoT Central, votre appareil passe à l’état Provisioned (Provisionné).

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

L’onglet Modules indique l’état de l’appareil et du module dans IoT Central. 

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Les propriétés Cloud s’affichent dans un formulaire (à partir du modèle d’appareil que vous avez créé aux étapes précédentes). Entrez les valeurs et cliquez sur **Save** (Enregistrer). 

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Vignette du tableau de bord

![Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Dans ce tutoriel, vous avez appris à :

* Ajouter un nouvel appareil Azure IoT Edge
* Configurer l’appareil Azure IoT Edge pour faciliter son provisionnement à l’aide d’une clé SAS
* Afficher les tableaux de bord, le module Health dans IoT Central
* Envoyer des commandes à un module en cours d’exécution sur l’appareil Azure IoT Edge
* Définir les propriétés d’un module en cours d’exécution sur l’appareil Azure IoT Edge

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment gérer les appareils Azure IoT Edge dans IoT Central, voici l’étape suivante suggérée :

<!-- Next how-tos in the sequence -->

Configurer une passerelle transparente. Pour cela, suivez ce tutoriel :

> [!div class="nextstepaction"]
> [Configurer une passerelle transparente](../../iot-edge/how-to-create-transparent-gateway.md)
