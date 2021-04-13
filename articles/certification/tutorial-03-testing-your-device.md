---
title: Programme Azure Certified Device - Tutoriel - Test de votre appareil
description: Guide pas à pas pour tester votre appareil avec le service AIC sur le portail Azure Certified Device
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 4cc9e37e95c6402bc535d818e994327e7d526047
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975416"
---
# <a name="tutorial-test-and-submit-your-device"></a>Tutoriel : Tester et soumettre votre appareil

La phase majeure suivante du processus de certification (même si elle peut être effectuée avant l’ajout des détails de votre appareil) implique le test de votre appareil. Par le biais du portail, vous utilisez le service de certification Azure IoT (AIC, Azure IoT Certification) pour démontrer les performances de votre appareil en fonction de nos exigences de certification. Une fois la phase de test réussie, vous soumettez votre appareil pour examen et approbation finales par l’équipe de certification Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Connecter votre appareil à IoT Hub à l’aide de DPS (service Device Provisioning)
> * Tester votre appareil en fonction du ou des programmes de certification sélectionnés
> * Soumettre votre appareil pour examen par l’équipe de certification Azure

## <a name="prerequisites"></a>Prérequis

- Vous devez être connecté et disposer d’un projet pour votre appareil sur le [portail Azure Certified Device](https://certify.azure.com). Pour plus d’informations, consultez ce [tutoriel](tutorial-01-creating-your-project.md).
- (Facultatif) Nous vous recommandons de préparer votre appareil et de vérifier manuellement ses performances en fonction des exigences de certification. En effet, si vous souhaitez effectuer un nouveau test avec un code d’appareil ou un programme de certification différent, vous devez créer un projet.

## <a name="connecting-your-device-using-dps"></a>Connexion de votre appareil en utilisant DPS

Tous les appareils certifiés doivent démontrer la possibilité de se connecter à IoT Hub avec DPS. Les étapes suivantes vous expliquent comment connecter votre appareil à des fins de test sur le portail.

1. Pour commencer la phase de test, sélectionnez le lien `Connect & test` dans la page du récapitulatif du projet :  

    ![Lien Connecter et tester](./media/images/connect-and-test-link.png)

1. En fonction de la ou des certifications sélectionnées, vous verrez les tests requis dans la page « Connecter & tester ». Examinez-les afin de vous assurer que vous postulez pour le programme de certification approprié.  

    ![Page Connecter et tester](./media/images/connect-and-test.png)

1. Connectez votre appareil à IoT Hub avec DPS (service Device Provisioning). DPS prend en charge les options de connectivité pour les clés symétriques, la certification X.509 et un module de plateforme sécurisée. Cela est nécessaire pour toutes les certifications.

    - *Pour plus d’informations sur la connexion de votre appareil à Azure IoT Hub avec DPS, consultez [Présentation du provisionnement des appareils](../iot-dps/about-iot-dps.md "Vue d’ensemble du service Device Provisioning").*
    
1. Si vous utilisez des clés symétriques, vous êtes invité à configurer DPS avec l’étendue d’ID DPS, l’ID d’appareil, la clé d’authentification et le point de terminaison DPS fournis. Dans le cas contraire, vous êtes invité à fournir un certificat X.509 ou une paire de clés de type EK (Endorsement Key).

1. Après avoir configuré votre appareil avec DPS, confirmez la connexion en cliquant sur le bouton `Connect` (Se connecter) situé en bas de la page. Une fois la connexion établie, vous pouvez passer à la phase de test en cliquant sur le bouton `Next` (Suivant).  

    ![Connexion et confirmation de la connexion](./media/images/connected.png)

## <a name="testing-your-device"></a>Test de votre appareil

Après avoir correctement connecté votre appareil au service AIC, vous êtes prêt à exécuter les tests de certification propres au programme de certification pour lequel vous postulez.

1. **Pour la certification Azure Certified Device** : sous l’onglet « Sélectionner la fonctionnalité de l’appareil », vous examinez et sélectionnez les tests à exécuter sur votre appareil.
1. **Pour la certification IoT Plug-and-Play** : examinez attentivement les paramètres qui seront vérifiés pendant le test que vous avez déclarés dans votre modèle d’appareil.
1. **Pour la certification Edge Managed** : aucune étape supplémentaire n’est requise au-delà de la démonstration de la connectivité.
1. Après avoir terminé les préparatifs nécessaires pour le programme de certification spécifié, sélectionnez `Next` (Suivant) pour passer à la phase « Test ».
1. Sélectionnez `Run tests` (Exécuter les tests) dans la page pour commencer à exécuter le service AIC avec votre appareil.
1. Une fois que vous avez reçu une notification indiquant que vous avez réussi les tests, sélectionnez `Finish` (Terminer) pour revenir à la page du récapitulatif.

![Test réussi](./media/images/test-pass.png)

7. Si vous avez des questions supplémentaires ou si vous avez besoin d’aide pour la résolution de problèmes liés au service AIC, consultez notre guide de résolution des problèmes.

> [!NOTE]
> Même si vous pouvez effectuer le processus de certification en ligne pour IoT Plug-and-Play et Edge Managed sans avoir à soumettre votre appareil en vue d’un examen manuel, vous pouvez être contacté par un membre de l’équipe Azure Certified Device pour une validation plus poussée de l’appareil au-delà de ce qui est testé par le biais de notre service d’automatisation.

## <a name="submitting-your-device-for-review"></a>Soumission de votre appareil pour examen

Une fois que vous avez rempli tous les champs obligatoires dans la section « Détails de l’appareil » et réussi les tests automatisés dans le processus « Connecter & tester », vous pouvez informer l’équipe Azure Certified Device que vous êtes prêt pour l’examen de certification.

1. Sélectionnez `Submit for review` (Soumettre pour examen) dans la page du récapitulatif du projet :  

    ![Lien Vérifier et certifier](./media/images/review-and-certify.png)

1. Confirmez votre envoi dans la fenêtre contextuelle. Une fois qu’un appareil a été soumis, tous les détails de l’appareil sont accessibles en lecture seule jusqu’à ce qu’une modification soit demandée. (Consultez [Guide pratique pour modifier les informations de votre appareil après publication](./how-to-edit-published-device.md).)  

    ![Boîte de dialogue Démarrer l’examen de certification](./media/images/start-certification-review.png)

1. Une fois le projet soumis, la page du récapitulatif du projet indique que le projet fait l’objet d’un examen de certification (`Under Certification Review`) par l’équipe de certification Azure :  

    ![En cours d’examen](./media/images/review-and-certify-under-review.png)

1. Dans un délai de 5 à 7 jours ouvrables, vous devriez recevoir par e-mail une réponse de l’équipe de certification Azure à l’adresse fournie dans votre profil d’entreprise au sujet de l’état de l’envoi de votre appareil.

    - Envoi approuvé  
        Une fois votre projet examiné et approuvé, vous recevez un e-mail. L’e-mail inclut un ensemble de fichiers, notamment le badge Azure Certified Device, les instructions d’utilisation du badge et d’autres informations sur la façon de mettre en valeur le message selon lequel votre appareil est certifié. Félicitations !

    - Envoi en attente  
        Si votre projet n’est pas approuvé, vous pouvez apporter des modifications aux détails du projet, puis soumettre de nouveau l’appareil à des fins de certification une fois que vous êtes prêt. Un e-mail est envoyé comportant des informations sur la raison pour laquelle le projet n’a pas été approuvé et les étapes à suivre pour le soumettre de nouveau à des fins de certification.

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Votre appareil a réussi tous les tests et a été approuvé par le biais du programme Azure Certified Device. Vous pouvez désormais publier votre appareil sur notre catalogue Azure Certified Device, où les clients peuvent acheter vos produits en toute confiance avec Azure.
> [!div class="nextstepaction"]
> [Tutoriel : Publication de votre appareil](tutorial-04-publishing-your-device.md)

