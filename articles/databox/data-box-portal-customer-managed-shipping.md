---
title: Expédition autogérée Microsoft Azure Data Box | Microsoft Docs sur les données
description: Décrit le flux de travail d’expédition autogérée pour les appareils Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 07/22/2021
ms.author: alkohli
ms.openlocfilehash: cafea36f45dcc063fadc1562428735ee0ac9fe20
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652817"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Utilisez l’expédition autogérée pour Azure Data Box dans le Portail Azure

Cet article décrit les tâches d’expédition autogérées pour la commande, l’enlèvement et le dépôt d’une appareil Azure Data Box. Vous pouvez gérer l’appareil Data Box à l’aide du Portail Azure.

> [!NOTE]
> Pour obtenir des réponses aux questions fréquemment posées sur les commandes et les livraisons de Data Box, consultez [Questions fréquentes (FAQ) sur Data Box](data-box-faq.yml).

## <a name="prerequisites"></a>Prérequis

L’expédition autogérée est disponible comme option lorsque vous [commandez Azure Data Box](data-box-deploy-ordered.md). L’expédition autogérée est disponible uniquement dans les régions suivantes :

* Gouvernement américain
* Royaume-Uni
* Europe de l’Ouest
* Japon
* Singapour
* Corée du Sud
* Inde
* Afrique du Sud
* Australie
* Brésil

## <a name="use-self-managed-shipping"></a>Utiliser l’expédition autogérée

Quand vous passez une commande de Data Box, vous pouvez choisir l’option d’expédition autogérée.

1. Dans votre commande Azure Data Box, sous **Détails du contact**, sélectionnez **+ Ajouter une adresse de livraison**.
 
   ![Expédition auto-gérée, Ajouter une adresse de livraison](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Lorsque vous choisissez le type d’expédition, sélectionnez l’option **Expédition autogérée**. Cette option est disponible uniquement si vous êtes dans une des régions prises en charge, comme cela est décrit dans les prérequis.

3. Une fois que vous avez fourni votre adresse de livraison, vous devez la valider et terminer votre commande.

   ![Expédition auto-gérée, valider et ajouter une adresse](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Quand l’appareil a été préparé et que vous en avez été informé par e-mail, vous pouvez planifier un prélèvement.

   Dans votre commande Azure Data Box, accédez à **Vue d’ensemble** puis sélectionnez **Planifier l’enlèvement**.

   ![Commande Data Box, option Planifier la collecte](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Suivez les instructions dans **Planifier la collecte pour Azure**.

   Avant de pouvoir obtenir votre code d’autorisation, vous devez envoyer un courrier électronique à [adbops@microsoft.com](mailto:adbops@microsoft.com) pour planifier l’enlèvement de l’appareil à partir du centre de données de votre région.

   ![Instructions pour Planifier la collecte pour Azure](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

   **Instructions pour le Brésil :** si vous planifiez un enlèvement d’appareil au Brésil, incluez les informations suivantes dans votre e-mail. Le centre de données planifie l’enlèvement après réception d’une entrée `Nota Fiscal`, ce qui peut prendre jusqu’à 4 jours ouvrables.

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - CNPJ (Business Tax ID, format: 00.000.000/0000-00) or CPF (Individual Tax ID, format: 000.000.000-00)
   - Address
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. Une fois que vous avez planifié la collecte de votre appareil, vous pouvez afficher le code d’autorisation de votre appareil dans le volet **Planifier la collecte pour Azure**.

   ![Affichage du code d’autorisation de votre appareil](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Notez ce **code d’autorisation**. La personne chargée de l’enlèvement de l’appareil devra le fournir.

   Conformément aux exigences de sécurité, au moment de la planification de l’enlèvement, il est nécessaire de fournir le nom et les détails de la personne qui sera présente lors de l’enlèvement. Vous ou le point de contact devez avoir une pièce d’identité avec photo approuvée par le gouvernement qui sera validée par le centre de données.

7. Procédez à l’enlèvement de la Data Box dans le centre de données à l’heure planifiée.

   La personne chargée de l’enlèvement de l’appareil doit fournir ce qui suit :

   * Une copie de la confirmation par e-mail fournie par Microsoft Operations autorisant la visite du centre de données.

   * Le code d’autorisation. Le numéro de référence pour un enlèvement ou un dépôt est unique et validé dans le centre de données.

   * Une photo d’identité approuvée par le gouvernement. L’identité sera validée au centre de données, et le nom et les coordonnées de la personne chargée de l’enlèvement de l’appareil doivent être fournis lors de la planification de l’enlèvement.

   > [!NOTE]
   > Si un rendez-vous planifié est manqué, vous devez planifier un nouveau rendez-vous.

8. Votre commande passe automatiquement à l’état **Récupérée** une fois l’appareil enlevé au centre de données.

    ![Commande dans l’état Collecté](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

9. Après avoir enlevé l’appareil, copiez les données vers le ou les Data Box sur votre site. Une fois la copie des données terminée, vous pouvez préparer l’expédition du Data Box. Pour plus d’informations, consultez [Préparer l’expédition](data-box-deploy-picked-up.md#prepare-to-ship).

   L’étape **Préparer l’expédition** doit s’effectuer sans erreur critique. Dans le cas contraire, vous devrez réexécuter cette étape après avoir appliqué les corrections nécessaires. Une fois l’étape **Préparer l’expédition** terminée, vous pouvez afficher le code d’autorisation pour le dépôt dans l’interface utilisateur locale de l’appareil.

   > [!NOTE]
   > Ne partagez pas le code d’autorisation par e-mail. Il doit uniquement être vérifié au centre de données lors du dépôt.

   **Instructions pour le Brésil :** pour planifier un retour de l’appareil au Brésil, envoyez un e-mail à [adbops@microsoft.com](mailto:adbops@microsoft.com) avec les informations suivantes :

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. Si vous avez reçu une date de rendez-vous pour le dépôt, l’état de la commande doit être **Prêt pour la réception au centre de données Azure** dans le portail Azure. Suivez les instructions sous **Planifier le dépôt** pour renvoyer l’appareil.

    ![Instructions pour le dépôt d’appareil](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

11. Une fois que votre identité et votre code d’autorisation ont été vérifiés et que vous avez déposé l’appareil au centre de données, l’état de la commande passe à **Reçu**.

    ![Commande dans l’état Reçu](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

12. Une fois l’appareil reçu, la copie des données se poursuit. Lorsque la copie est finie, la commande est terminée.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Data Box](data-box-quickstart-portal.md)
