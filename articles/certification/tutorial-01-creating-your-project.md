---
title: Programme Azure Certified Device - Tutoriel - Création de votre projet
description: Guide de création d’un projet sur le portail Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 44c053eac2215354fbd43e4c0068f02419010a85
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108131852"
---
# <a name="tutorial-create-your-project"></a>Tutoriel : Créer votre projet

Félicitations, vous avez choisi de certifier votre appareil par le biais du programme Azure Certified Device ! Vous avez maintenant sélectionné le programme de certification approprié pour votre appareil et êtes prêt à commencer sur le portail.

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Vous connecter au [portail Azure Certified Device](https://certify.azure.com/)
> * Créer un projet de certification pour votre appareil
> * Spécifier les détails d’appareil de base de votre projet

## <a name="prerequisites"></a>Prérequis

- Vous aurez besoin d’un [compte Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) professionnel/scolaire valide.
- Vous aurez besoin d’un compte MPN (Microsoft Partner Network) vérifié. Si vous n’avez pas de compte MPN, [rejoignez le réseau de partenaires](https://partner.microsoft.com/) avant de commencer.

## <a name="signing-into-the-azure-certified-device-portal"></a>Connexion au portail Azure Certified Device

Pour commencer, vous devez vous connecter au portail, où vous fournirez les informations de votre appareil, effectuerez des tests de certification et gérerez vos publications d’appareils sur le catalogue Azure Certified Device.

1. Accédez au [portail Azure Certified Device](https://certify.azure.com).
1. Sélectionnez `Company profile` (Profil de l’entreprise) sur le côté gauche et mettez à jour les informations du fabricant.
   ![Section Profil de l’entreprise](./media/images/company-profile.png)
1. Acceptez le contrat du programme pour commencer votre projet.

## <a name="creating-your-project-on-the-portal"></a>Création de votre projet sur le portail

Tout étant désormais configuré dans le portail, vous pouvez commencer le processus de certification. Tout d’abord, vous devez créer un projet pour votre appareil.

1. Dans l’écran d’accueil, sélectionnez `Create new project` (Créer un projet). Une fenêtre s’ouvre, qui permet d’ajouter les informations de base sur l’appareil indiquées dans la prochaine section.

 ![Image du bouton de création de projet](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Identification des informations de base sur l’appareil

Ensuite, vous devez fournir des informations de base sur l’appareil. Vous pourrez modifier ces informations ultérieurement.

1. Complétez les champs demandés dans la section `Basics` (Informations de base). Pour plus d’explications sur les champs **obligatoires**, consultez le tableau ci-dessous :

    | Champs                  | Description                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Nom du projet           | Nom interne qui ne sera pas visible sur le catalogue Azure Certified Device                                                        |
    | Nom du périphérique            | Nom public pour votre appareil                                                                                                |
    | Type d’appareil            | Spécification de Produit fini ou de Kit de développement prêt pour la solution.     Pour plus d’informations sur la terminologie, consultez le [glossaire des certifications](./resources-glossary.md).                                                                     |
    | Classe d’appareil           | Passerelle, capteur ou autre.  Pour plus d’informations sur la terminologie, consultez le [glossaire des certifications](./resources-glossary.md).                                                                    |
    | URL du code source de l’appareil | Obligatoire si vous certifiez un kit de développement prêt pour la solution, facultatif dans le cas contraire. L’URL doit pointer vers un emplacement GitHub pour le code de votre appareil. |
1. Sélectionnez le bouton `Next` (Suivant) pour accéder à l’onglet `Certifications`.

    ![Image du formulaire de création de projet, onglet Certifications](./media/images/create-new-project-certificationswindow.png)

1. Spécifiez la ou les certifications que vous souhaitez obtenir pour votre appareil.
1. Sélectionnez `Create` (Créer) afin que le nouveau projet soit enregistré et visible dans la page d’accueil du portail.

    ![Image du tableau des projets](./media/images/project-table.png)

1. Sélectionnez le nom du projet dans le tableau. La page du récapitulatif du projet s’ouvre et vous permet d’ajouter et d’afficher d’autres informations sur votre appareil.

    ![Image de la page des détails du projet](./media/images/device-details-section.png)

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à ajouter les détails de l’appareil et à tester ce dernier avec notre service de certification. Passez à l’article suivant pour découvrir comment modifier les détails de votre appareil.
> [!div class="nextstepaction"]
> [Tutoriel : Ajout des détails de l’appareil](tutorial-02-adding-device-details.md)