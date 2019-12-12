---
title: Ajouter un appareil Azure IoT Edge à Azure IoT Central | Microsoft Docs
description: Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central en qualité d’opérateur
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979068"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Didacticiel : Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel vous montre comment ajouter et configurer un appareil Azure IoT Edge à votre application Azure IoT Central. Dans ce tutoriel, nous avons choisi une machine virtuelle Linux prenant en charge IoT Edge dans la Place de marché Azure.

Le didacticiel se déroule en deux parties :

* Tout d’abord, en tant qu’opérateur, vous allez apprendre à procéder à un provisionnement en priorité sur le cloud pour un appareil IoT Edge.
* Ensuite, vous apprendrez à procéder à un provisionnement « en priorité sur l’appareil » pour un appareil IoT Edge.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un nouvel appareil IoT Edge
> * Configurer l’appareil IoT Edge pour le provisionnement à l’aide d’une clé de signature d’accès partagé (SAS)
> * Afficher les tableaux de bord et le module health dans IoT Central
> * Envoyer des commandes à un module en cours d’exécution sur l’appareil IoT Edge
> * Définir les propriétés d’un module en cours d’exécution sur l’appareil IoT Edge

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer d’une application Azure IoT Central. Suivez [ce guide de démarrage rapide pour créer une application Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Activer le groupe d’inscriptions Azure IoT Edge
Dans la page **Administration**, activez les clés SAS pour le groupe d’inscriptions Azure IoT Edge.

![Capture d’écran de la page Administration, avec mise en évidence de Connexion de l’appareil](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Provisionner un appareil Azure IoT Edge « en priorité sur le cloud »  
Dans cette section, vous créez un appareil IoT Edge à partir du modèle de capteur environnemental et vous provisionnez un appareil. Sélectionnez **Appareils** > **Environment Sensor Template** (Modèle Capteur environnemental). 

![Capture d’écran de la page Appareils, avec mise en évidence du Modèle Capteur environnemental](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Sélectionnez **+ Nouveau** et entrez l’ID et le nom d’appareil de votre choix. Sélectionnez **Create** (Créer).

![Capture d’écran de la boîte de dialogue Créer un appareil, avec mise en évidence du bouton Créer](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

L’appareil passe en mode **Inscrit**.

![Capture d’écran de la page Modèle Capteur environnemental, avec mise en évidence de l’élément État de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Déployer une machine virtuelle Linux prenant en charge IoT Edge

> [!NOTE]
> Vous pouvez utiliser n’importe quel ordinateur ou appareil. Le système d’exploitation peut être Linux ou Windows.

Pour ce tutoriel, nous utilisons une machine virtuelle Linux prenant en charge Azure IoT, créée sur Azure. Dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview), sélectionnez **OBTENIR**. 

![Capture d’écran de la Place de marché Azure, avec mise en évidence de l’option OBTENIR](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Sélectionnez **Continuer**.

![Capture d’écran de la boîte de dialogue Créer cette application dans Azure, avec mise en évidence de l’élément Continuer](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Vous êtes redirigé vers le portail Azure. Sélectionnez **Create** (Créer).

![Capture d’écran du portail Azure, avec mise en évidence de l’élément Créer](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Sélectionnez **Abonnement**, créez un groupe de ressources et sélectionnez **(États-Unis) USA Ouest 2** pour la disponibilité des machines virtuelles. Entrez ensuite les informations relatives à l’utilisateur et au mot de passe. Mémorisez-les, car vous en aurez besoin aux étapes suivantes. Sélectionnez **Revoir + créer**.

![Capture d’écran de la page Créer une machine virtuelle, avec mise en évidence de différentes options](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Une fois la validation effectuée, sélectionnez **Créer**.

![Capture d’écran de la page Créer une machine virtuelle, avec mise en évidence des éléments Validation réussie et Créer](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

La création des ressources prend quelques minutes. Sélectionnez **Accéder à la ressource**.

![Capture d’écran de la page de fin du déploiement, avec mise en évidence de l’élément Accéder à la ressource](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Provisionner la machine virtuelle en tant qu’appareil IoT Edge 

Sous **Support + dépannage**, sélectionnez **Console série**.

![Capture d’écran des options Support + dépannage, avec mise en évidence de l’élément Console série](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Vous verrez un écran semblable au suivant :

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Appuyez sur Entrée, fournissez le nom d’utilisateur et le mot de passe demandés, puis réappuyez sur Entrée. 

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Pour exécuter une commande en tant qu’administrateur (utilisateur « racine »), entrez : **sudo su –**

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Vérifiez la version du runtime IoT Edge. Au moment de la rédaction de cet article, la version en disposition générale actuelle est 1.0.8.

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installez l’éditeur vim, ou utilisez nano si vous préférez. 

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Modifiez le fichier config.yaml d’IoT Edge.

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Faites défiler vers le bas et mettez en commentaire la partie « connection string » du fichier yaml. 

**Avant**

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Après** (Appuyez sur Échap et appuyez sur le a minuscule pour commencer la modification.)

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Supprimez les marques de commentaire de la partie « symmetric key » du fichier yaml. 

**Avant**

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Après**

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Accédez à IoT Central. Obtenez l’ID d’étendue, l’ID d’appareil et la clé symétrique de l’appareil IoT Edge.
![Capture d’écran d’IoT Central, avec mise en évidence de différentes options de connexion d’appareil](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Accédez à l’ordinateur Linux et remplacez l’ID d’étendue et l’ID d’inscription par l’ID d’appareil et la clé symétrique.

Appuyez sur Échap, puis tapez **:wq!** . Appuyez sur Entrée pour enregistrer vos modifications.

Redémarrez IoT Edge pour traiter vos modifications et appuyez sur Entrée.

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Tapez **iotedge list**. Après quelques minutes, vous verrez trois modules déployés.

![Capture d’écran de la console](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Explorateur d’appareils IoT Central 

Dans IoT Central, votre appareil passe à l’état Provisionné.

![Capture d’écran des options Appareils d’IoT Central, avec mise en évidence de l’élément État de l’appareil](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

L’onglet **Modules** indique l’état de l’appareil et du module dans IoT Central. 

![Capture d’écran de l’onglet Modules d’IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Vous verrez les propriétés cloud dans un formulaire, en provenance du modèle d’appareil que vous avez créé aux étapes précédentes. Entrez les valeurs et sélectionnez **Enregistrer**. 

![Capture d’écran du formulaire My Linux Edge Device (Mon appareil Edge Linux)](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Voici une vue présentée sous la forme d’une vignette de tableau de bord.

![Capture d’écran des vignettes de tableau de bord My Linux Edge Device (Mon appareil Edge Linux)](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser et gérer les appareils IoT Edge dans IoT Central, nous vous suggérons l’étape suivante :

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Configurer une passerelle transparente](../../iot-edge/how-to-create-transparent-gateway.md)
