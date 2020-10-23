---
title: Gérer des inscriptions des services IoT Hub Device Provisioning sur le Portail Azure
description: Guide pratique de gestion des inscriptions d’appareil avec votre service Device Provisioning (DPS) dans le Portail Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2fbcacd7f2094f9b0b9dcea3fea4d804fd96923e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165313"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Guide pratique pour gérer les inscriptions d’appareils avec le portail Azure

Une *inscription d’appareil* crée un enregistrement d’un appareil ou d’un groupe d’appareils susceptibles d’être inscrits au service Azure IoT Hub Device Provisioning à un moment donné. L’enregistrement contient la configuration initiale souhaitée pour le ou les appareils dans le cadre de cette inscription, y compris le hub IoT souhaité. Cet article explique comment gérer les inscriptions d’appareil dans votre service d’approvisionnement.


## <a name="create-a-device-enrollment"></a>Créer une inscription d’appareil

Il existe deux façons de procéder à l’inscription de vos appareils auprès du service d’approvisionnement :

* Un **groupe d’inscriptions** est une entrée pour un groupe d’appareils partageant un mécanisme commun d’attestation de certificats X.509 signés par le certificat de signature, qui peut être le [certificat racine](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou le [certificat intermédiaire](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), utilisé pour produire un certificat d’appareil sur un appareil physique. Nous recommandons l’utilisation d’un groupe d’inscriptions pour un grand nombre d’appareils partageant une configuration initiale souhaitée, ou pour des appareils destinés au même locataire. 

    Vous pouvez créer un groupe d’inscriptions dans le portail pour un groupe d’appareils en effectuant les étapes suivantes :

  1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.  
  1. Dans la liste des ressources, cliquez sur le service Device Provisioning auquel vous souhaitez inscrire votre appareil.  
  1. Dans votre service d’approvisionnement :  
     a. Cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.  
     b. Cliquez sur le bouton **Ajouter** en haut.  
     c. Lorsque le panneau « Ajouter un groupe d’inscription » s’affiche, saisissez les informations pour l’entrée de liste d’inscription.  Le **nom de groupe** est obligatoire. Sélectionnez également « CA or Intermediate » comme **type de certificat**et télécharger le **certificat principal** racine pour le groupe d’appareils.  
     d. Cliquez sur **Enregistrer**. Dès que votre groupe d’inscriptions est créé, vous voyez son nom s’afficher sous l’onglet **Groupes d’inscriptions**.  

     [![Groupe d’inscriptions dans le portail](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* L’**inscription individuelle** est une entrée permettant d’enregistrer un seul appareil. Les inscriptions individuelles peuvent utiliser des certificats x509 ou des jetons SAP (à partir d’un module de plateforme sécurisée réel ou virtuel) comme mécanismes d’attestation. Nous recommandons d’utiliser les inscriptions individuelles pour les appareils qui nécessitent une configuration initiale unique, ou pour les appareils ne pouvant utiliser que des jetons SAP par le biais du module de plateforme sécurisée (TPM) ou du TPM virtuel comme mécanisme d’attestation. Dans le cas d’inscriptions individuelles, vous pouvez spécifier l’ID de l’appareil IoT Hub souhaité.

    Vous pouvez créer une inscription individuelle dans le portail en suivant les étapes décrites ici :

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    1. Dans la liste des ressources, cliquez sur le service Device Provisioning auquel vous souhaitez inscrire votre appareil.
    1. Dans votre service d’approvisionnement :  
       a. Cliquez sur **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**.  
       b. Cliquez sur le bouton **Ajouter** en haut.   
       c. Lorsque le panneau « Ajouter une inscription » s’affiche, saisissez les informations pour l’entrée de liste d’inscription. Sélectionnez d’abord le **mécanisme** d’attestation pour l’appareil (X.509 ou un module de plateforme sécurisée). L’attestation de X.509 vous demande de télécharger le **certificat principal** feuille pour l’appareil. Le module de plateforme sécurisée vous demande de saisir la **clé d’attestation** et **l’ID d’inscription** du périphérique.  
       d. Cliquez sur **Enregistrer**. Dès que votre groupe d’inscriptions est créé, vous voyez votre appareil s’afficher sous l’onglet **Inscriptions individuelles**.  

       [![Inscription individuelle dans le portail](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Mettre à jour une entrée d’inscription
Vous pouvez mettre à jour une entrée d’inscription existante dans le portail en effectuant les étapes suivantes :

1. Ouvrez votre service Device Provisioning dans le portail Azure et cliquez sur **Gérer les inscriptions**. 
1. Accédez à l’entrée d’inscription que vous souhaitez modifier. Cliquez sur l’entrée pour afficher des informations récapitulatives sur votre inscription d’appareil. 
1. Dans cette page, vous pouvez modifier d’autres éléments que le type de sécurité ou les informations d’identification, par exemple le hub IoT auquel l’appareil doit être lié ainsi que l’ID de l’appareil. Vous pouvez également modifier l’état initial du jumeau d’appareil. 
1. Lorsque vous avez terminé, cliquez sur **Enregistrer** pour mettre à jour votre inscription d’appareil. 

    ![Mise à jour d’une inscription dans le portail](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Supprimer une inscription d’appareil
Si votre ou vos appareils n’ont pas besoin d’être provisionnés sur un hub IoT, vous pouvez supprimer l’entrée d’inscription correspondante dans le portail en effectuant les étapes suivantes :

1. Ouvrez votre service Device Provisioning dans le portail Azure et cliquez sur **Gérer les inscriptions**. 
1. Accédez à l’entrée d’inscription que vous souhaitez supprimer et sélectionnez-la. 
1. Cliquez sur le bouton **Supprimer** tout en haut et sélectionnez **Oui** lorsque vous êtes invité à confirmer l’action. 
1. Lorsque l’opération est terminée, votre entrée a été supprimée de la liste des inscriptions d’appareil. 
 
    ![Supprimer une inscription dans le portail](./media/how-to-manage-enrollments/remove-enrollment.png)


