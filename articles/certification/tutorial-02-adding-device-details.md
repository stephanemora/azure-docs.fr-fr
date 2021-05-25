---
title: Programme Azure Certified Device - Tutoriel - Ajout des détails de l’appareil
description: Guide pas à pas pour ajouter les détails de l’appareil à votre projet sur le portail Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 05/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: be6bcf84ebb9c979cd60e6ca6803b7a24a0944a1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108736299"
---
# <a name="tutorial-add-device-details"></a>Tutoriel : Ajouter les détails de l’appareil

Votre projet pour votre appareil étant créé, vous êtes prêt à commencer le processus de certification. Tout d’abord, nous allons ajouter les détails de votre appareil. Ceux-ci incluent les spécifications techniques que vos clients pourront consulter dans le catalogue Azure Certified Device et les détails marketing qui guideront leur achat.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter les détails de l’appareil avec les fonctionnalités Composants et Dépendances
> * Charger un guide de prise en main pour votre appareil
> * Spécifier les détails marketing pour que les clients achètent votre appareil
> * Identifier éventuellement les certifications du secteur

## <a name="prerequisites"></a>Prérequis

* Vous devez être connecté et disposer d’un projet pour votre appareil sur le [portail Azure Certified Device](https://certify.azure.com). Pour plus d’informations, consultez ce [tutoriel](tutorial-01-creating-your-project.md).
* Vous devez disposer d’un guide de prise en main pour votre appareil au format PDF. Nous fournissons un grand nombre de modèles de prise en main que vous pouvez utiliser, en fonction du programme de certification et de la langue de votre choix. Les modèles sont disponibles à l’emplacement GitHub [Get_Started_Templates](https://aka.ms/GSTemplate "Modèles de démarrage").

## <a name="adding-technical-device-details"></a>Ajout des détails techniques de l’appareil

La première section de la page de votre projet, appelée « Entrer les détails de l’appareil », vous permet de fournir des informations sur les principales fonctionnalités matérielles de votre appareil, telles que le nom de l’appareil, la description, le processeur, le système d’exploitation, les options de connectivité, les interfaces matérielles, les protocoles du secteur d’activité et les dimensions physiques. Même si de nombreux champs sont facultatifs, la plupart de ces informations seront mises à la disposition des clients potentiels dans le catalogue Azure Certified Device si vous choisissez de publier votre appareil une fois qu’il a été certifié.

1. Cliquez sur `Add` (Ajouter) dans la section « Entrer les détails de l’appareil » de la page du récapitulatif de votre projet pour ouvrir la section des détails de l’appareil. Vous voyez six sections à compléter.

![Image de la page des détails du projet](./media/images/device-details-menu.png)

2. Sous l’onglet `Basics` (Informations de base), passez en revue les informations que vous avez fournies quand vous avez créé le projet.
1. Sous l’onglet `Certifications`, passez en revue les certifications pour lesquelles vous postulez avec votre appareil.
1. Ouvrez l’onglet `Hardware` et ajoutez **au moins** un composant discret qui décrit votre appareil. Vous pouvez aussi consulter nos instructions sur l’[utilisation des composants](how-to-using-the-components-feature.md).
1. Cliquez sur `Save`. Vous pourrez alors modifier les détails de votre composant et ajouter des détails plus avancés.
1. Ajoutez toutes les informations pertinentes concernant les conditions de fonctionnement (comme l’indice de protection, la température de fonctionnement ou la certification de sécurité).

![Image de la section du matériel](./media/images/hardware-section.png)

7. Sous `Additional product details` (Détails supplémentaires du produit), listez les détails supplémentaires sur l’appareil non capturés par les détails du composant.
1. Si vous avez sélectionné `Other` (Autre) dans l’un des champs de composant ou que vous souhaitez signaler une circonstance particulière à l’équipe de certification Azure, laissez un commentaire explicatif dans la section `Comments for reviewer` (Commentaires pour le réviseur).
1. Ouvrez l’onglet `Software` et sélectionnez **au moins** un système d’exploitation.
1. (**Obligatoire pour les appareils du kit de développement** et fortement recommandé pour tous les autres) Sélectionnez un niveau pour indiquer le processus de configuration attendu pour connecter votre appareil à Azure. Si vous sélectionnez Niveau 2, vous devez fournir un lien vers l’image logicielle disponible.

![Image de la section du logiciel](./media/images/software-section.png)

11. Utilisez l’onglet `Dependencies` pour lister toutes les dépendances si votre appareil nécessite du matériel ou des services supplémentaires pour envoyer des données à Azure. Vous pouvez aussi consulter nos instructions supplémentaires pour [lister les dépendances](how-to-indirectly-connected-devices.md).
1. Une fois que vous êtes satisfait des informations que vous avez fournies, vous pouvez utiliser l’onglet `Review` (Examen) pour obtenir une vue d’ensemble en lecture seule de tous les détails de l’appareil qui ont été entrés.
1. Cliquez sur `Project summary` (Récapitulatif du projet) en haut de la page pour revenir à la page du récapitulatif.

![Page permettant de passer en revue les détails du projet](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Chargement d’un guide de prise en main

Le guide de prise en main est un document PDF qui simplifie l’installation, la configuration et la gestion de votre produit. Son objectif est de permettre aux clients de se connecter et de prendre en charge des appareils sur Azure avec votre appareil en toute simplicité. Dans le cadre du processus de certification, nous exigeons de nos partenaires qu’ils fournissent **un** guide de prise en main pour le programme de certification le plus pertinent.

1. Vérifiez que vous avez fourni toutes les informations demandées dans le fichier PDF de votre guide de prise en main, en fonction des [modèles](https://aka.ms/GSTemplate) fournis. Le modèle que vous utilisez doit être déterminé par le badge de certification pour lequel vous postulez. (Par exemple, un appareil IoT Plug-and-Play utilise le modèle IoT Plug-and-Play. De même, les appareils postulant *uniquement* pour la certification de base Azure Certified Device utilisent le modèle Azure Certified Device.)
1. Cliquez sur `Add` (Ajouter) dans la section « Bien démarrer » de la page du récapitulatif du projet.

![Image du bouton d’ajout d’un guide de prise en main](./media/images/gsg-menu.png)

2. Cliquez sur « Choisir un fichier » pour charger votre fichier PDF.
1. Examinez le document dans l’aperçu pour avoir une idée de sa mise en forme.
1. Enregistrez votre chargement en cliquant sur le bouton « Enregistrer ».
1. Cliquez sur `Project summary` (Récapitulatif du projet) en haut de la page pour revenir à la page du récapitulatif.

## <a name="providing-marketing-details"></a>Fourniture des détails marketing

Dans cette zone, vous allez fournir des informations marketing adaptées aux clients pour votre appareil. Ces champs seront présentés dans le catalogue Azure Certified Device si vous choisissez de publier votre appareil certifié.

1. Cliquez sur `Add` (Ajouter) dans la section « Ajouter les détails marketing » pour ouvrir la page des détails marketing.

![Image de la section des détails marketing](./media/images/marketing-details.png)

1. Chargez une photo de produit au format JPEG ou PNG qui sera utilisée dans le catalogue.
1. Rédigez une brève description de votre appareil qui sera affichée dans la page de description du produit au sein du catalogue.
1. Indiquez la disponibilité géographique de votre appareil.
1. Fournissez un lien vers la page marketing du fabricant de cet appareil. Il doit s’agir d’un lien vers un site qui fournit des informations supplémentaires sur l’appareil.
    > [!Note]
    > Vérifiez que toutes les URL fournies sont valides ou seront actives au moment de la publication après approbation.

1. Indiquez jusqu’à 3 secteurs d’activité cibles pour lesquels votre appareil est optimisé.
1. Fournissez des informations pour jusqu’à 5 distributeurs de votre appareil. Ces dernières peuvent inclure le propre site du fabricant.

    > [!Note]
    > Si aucune URL de page de produit de distributeur n’est fournie, le bouton `Shop` (Acheter) du catalogue affiche par défaut le lien fourni pour `Distributor page` (Page du distributeur), qui n’est pas nécessairement propre à l’appareil. Dans l’idéal, l’URL du distributeur doit aboutir à une page spécifique où un client peut acheter un appareil, mais ce n’est pas obligatoire. Si le distributeur est le fabricant lui-même, cette URL peut être la même que la page marketing du fabricant.

1. Cliquez sur `Save` (Enregistrer) pour confirmer vos informations.
1. Cliquez sur `Project summary` (Récapitulatif du projet) en haut de la page pour revenir à la page du récapitulatif.

## <a name="declaring-additional-industry-certifications"></a>Déclaration de certifications du secteur supplémentaires

Vous pouvez également promouvoir des certifications du secteur supplémentaires que vous avez éventuellement reçues pour votre appareil. Ces certifications peuvent aider à améliorer la clarté de l’utilisation prévue de votre appareil et pourront faire l’objet d’une recherche dans le catalogue Azure Certified Device.

1. Cliquez sur `Add` (Ajouter) dans la section « Fournir des certifications du secteur ».
1. Cliquez sur `Add a certification` (Ajouter une certification) pour effectuer une sélection dans la liste des programmes de certification du secteur. Si votre produit a obtenu une certification qui ne se trouve pas dans notre liste, vous pouvez spécifier une valeur de chaîne personnalisée en sélectionnant `Other (please specify)` (Autre (spécifiez)).
1. Fournissez éventuellement une description ou des notes au réviseur. Toutefois, ces notes ne seront pas publiées dans le catalogue.
1. Cliquez sur `Save` (Enregistrer) pour confirmer vos informations.
1. Cliquez sur `Project summary` (Récapitulatif du projet) en haut de la page pour revenir à la page du récapitulatif.

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant terminé le processus de description de votre appareil. Cela aidera l’équipe de revue Azure Certified Device et votre client à mieux comprendre votre produit. Une fois que vous êtes satisfait des informations que vous avez fournies, vous pouvez passer à la phase de test du processus de certification.
> [!div class="nextstepaction"]
> [Tutoriel : Test de votre appareil](tutorial-03-testing-your-device.md)
