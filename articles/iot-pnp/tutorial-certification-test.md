---
title: Certifier votre appareil IoT Plug-and-Play (préversion) | Microsoft Docs
description: Ce tutoriel explique comment ajouter vos informations de produit au catalogue d’appareils Microsoft Azure Certified pour IoT, connecter votre appareil au service de certification Azure IoT, puis exécuter les tests de certification IoT Plug-and-Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550160"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Tutoriel : Certifier votre appareil IoT Plug-and-Play (préversion)

Pour publier un appareil IoT Plug-and-Play (préversion) dans le [catalogue d’appareils Microsoft Azure Certified pour IoT](https://aka.ms/iotdevcat), il doit réussir un ensemble de tests de certification. Utilisez le portail [Microsoft Azure Certified pour IoT](https://aka.ms/ACFI) pour soumettre votre appareil pour certification. Le [service de certification Azure IoT](https://aka.ms/azure-iot-aics) exécute les tests de certification.

Dans ce tutoriel, vous allez voir comment :

> [!div class="checklist"]
> * Quelles sont les conditions de certification IoT Plug-and-Play
> * Comment ajouter le nom et les informations de votre produit au portail
> * Comment connecter et découvrir les interfaces IoT Plug-and-Play
> * Comment passer en revue les interfaces IoT Plug-and-Play et exécuter les tests de certification
> * Comment publier l’appareil IoT Plug-and-Play certifié sur le catalogue

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Microsoft Partner Center
* L’ID Microsoft Partner Network

Pour plus d’informations, consultez [Prendre en main le portail Microsoft Azure Certified pour IoT](howto-onboard-portal.md).

## <a name="certification-requirements"></a>Conditions de certification

Pour certifier votre appareil IoT Plug-and-Play, il doit remplir les conditions suivantes :

* Votre code d’appareil IoT Plug-and-Play doit être installé sur votre appareil.
* Votre code d’appareil IoT Plug-and-Play est compilé avec le SDK Azure IoT.
* Votre code d’appareil doit prendre en charge le [service Azure IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md).
* Votre code d’appareil doit implémenter l’[interface Informations sur l’appareil](concepts-common-interfaces.md).
* Le modèle de capacité et le code d’appareil fonctionnent avec IoT Central.

Tous les appareils figurant actuellement dans le catalogue sont considérés comme des appareils IoT Plug-and-Play précertifiés. Ceci ne garantit pas la qualité du produit final et sa conformité aux composants logiciels IoT Plug-and-Play (SDK et langage de définition pour les jumeaux numériques, par exemple).

Tous les appareils IoT Plug-and-Play précertifiés devront être recertifiés lors de la disponibilité générale d’IoT Plug-and-Play, selon la version finale des conditions de certification et des composants logiciels fournis par Microsoft.

## <a name="add-product-name"></a>Ajouter un nom de produit

**Produit** est un nom convivial pour un modèle de produit qu’un client peut acheter. Pour ajouter un produit :

1. Connectez-vous au portail [Microsoft Azure Certified pour IoT](https://aka.ms/ACFI).
1. Accédez à la page **Produits** du portail à partir du menu de gauche, puis sélectionnez **+ Nouveau**.
1. Entrez le nom convivial du produit, puis sélectionnez **Créer**. Le nom entré ici est différent de celui qui s’affiche dans le catalogue d’appareils.

## <a name="add-product-information"></a>Ajouter des informations sur le produit

Une fois que vous avez créé le produit dans le portail, celui-ci apparaît dans la page **Produits**. Pour ajouter les informations sur le produit :

1. Sélectionnez le lien de produit créé, figurant dans la colonne du produit, sur la page **Produit**. Il doit être à l’état de brouillon.
1. Sélectionnez le lien **Modifier** en regard de l’en-tête **Informations sur le produit**. Entrez les informations relatives à votre produit dans la page d’informations sur le produit et veillez à renseigner tous les champs obligatoires.
1. Sélectionnez **Enregistrer**. Le bouton **Enregistrer** s’affiche uniquement quand tous les champs obligatoires sont renseignés. Si ce n’est pas le cas, le bouton indique **Enregistrer et terminer ultérieurement**.
1. Vérifiez le contenu que vous avez entré. Renseignez tous les champs obligatoires pour publier l’appareil sur le catalogue d’appareils. Vous pouvez toujours revenir en arrière pour apporter des modifications aux informations sur le produit dans la page des détails du produit en cliquant sur le lien **Modifier** en regard de l’en-tête **Informations sur le produit**.

## <a name="connect-and-discover-interfaces"></a>Connecter et découvrir des interfaces

Pour exécuter les tests de certification, connectez votre appareil au [service de certification Azure IoT](https://aka.ms/azure-iot-aics) (AICS, Azure IoT certification service), disponible dans le portail.

Ces étapes constituent l’unique procédure à appliquer pour l’exécution de tests de certification. Il n’est pas nécessaire de changer le code d’appareil de votre produit. Tout d’abord, connectez-vous au service AICS en procédant comme suit :

1. Connectez-vous au portail à l’aide de votre compte Partner Center.
1. Cliquez sur **Connecter + tester** pour démarrer le processus de certification.
1. Choisissez la [méthode d’authentification](../iot-dps/concepts-security.md#attestation-mechanism) pour provisionner votre appareil sur le service AICS avec le [service Azure IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md).
   * Si vous utilisez un [certificat X.509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), chargez votre certificat X.509 généré. Vous pouvez consulter l’exemple de code montrant comment utiliser des certificats X.509 : [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Si vous utilisez une [clé symétrique](../iot-dps/concepts-symmetric-key-attestation.md), copiez-la et collez-la dans votre code d’appareil.
   * La méthode d’authentification TPM n’est pas prise en charge pour l’instant.
1. Copiez les ID générés suivants et collez-les dans votre code d’appareil.
   * [ID d'inscription](../iot-dps/use-hsm-with-sdk.md)
   * [ID DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Point de terminaison DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Une fois votre code d’appareil compilé et déployé sur l’appareil, sélectionnez **Se connecter** pour découvrir les interfaces IoT Plug-and-Play.
1. Une fois la connexion au service AICS établie, sélectionnez **Suivant** pour passer en revue les interfaces IoT Plug-and-Play découvertes.

## <a name="run-tests-and-publish-the-device"></a>Exécuter les tests et publier l’appareil

Vous pouvez passer en revue les interfaces IoT Plug-and-Play découvertes sur la page de revue. Utilisez cette page pour vérifier que les primitives implémentées dans l’interface s’affichent correctement. Vous pouvez également vérifier l’emplacement de l’interface.

1. Assurez-vous que les entrées de charge utile sont indiquées dans les champs obligatoires. Ces champs incluent des informations sur la charge utile pour la primitive de commande pour l’interface spécifiée.
1. Une fois que vous avez entré toutes les informations requises, cliquez sur **Suivant**.
1. Pour exécuter les tests sur les interfaces IoT Plug-and-Play implémentées, sélectionnez **Exécuter les tests**.
1. Tous les tests sont exécutés automatiquement. En cas d’échec d’un test, sélectionnez **Afficher les journaux** pour voir les messages d’erreur du service AICS et les données de télémétrie brutes envoyées à IoT Hub.
1. Pour terminer les tests de certification, sélectionnez **Terminer**.
1. Publiez l’appareil IoT Plug-and-Play certifié sur le catalogue. Pour ajouter l’appareil certifié au catalogue, sélectionnez **Ajouter au catalogue** dans la barre d’outils. Si l’option **Ajouter au catalogue** est grisée, cela signifie que les informations sur le produit sont incomplètes ou que les tests ont échoué. 
1. Sélectionnez le lien « Certifié et dans le catalogue » pour voir votre appareil publié dans le catalogue d’appareils.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment certifier un appareil IoT Plug-and-Play, nous vous recommandons d’en apprendre davantage sur la gestion des modèles de capacité :

> [!div class="nextstepaction"]
> [Gérer les modèles](./howto-manage-models.md)
