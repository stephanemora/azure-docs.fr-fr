---
title: Propriétés marketing
description: Description des différents champs marketing collectés dans le portail et de leur mode d’affichage dans le catalogue Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 262616ca82e9c06baec0d7a1b81a0e3dff2ed8db
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304509"
---
# <a name="marketing-properties"></a>Propriétés marketing

Au cours du processus d’[ajout des détails de votre appareil](tutorial-02-adding-device-details.md), vous devrez fournir des informations marketing qui seront affichées dans le [catalogue Azure Certified Device](https://devicecatalog.azure.com). Ces informations sont collectées sur le portail Azure Certified Device pendant le processus de soumission de certification et seront utilisées comme paramètres de filtre sur le catalogue. Cet article fournit une correspondance entre les champs collectés dans le portail et leur mode d’affichage sur le catalogue. Après avoir lu cet article, les partenaires devraient mieux comprendre quelles informations fournir pendant le processus de certification pour représenter au mieux leur produit sur le catalogue.

![Vue d’ensemble PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Vignette produit du catalogue Azure Certified Device

Les visiteurs du catalogue interagissent d’abord avec votre appareil sous forme d’une vignette produit du catalogue sur la page de recherche. Ils auront ainsi une vue d’ensemble de l’appareil et des certifications qui lui ont été accordées.

![Modèle de vignette produit](./media/concepts-marketing/product-tile.png)

| Champ | Description                  | Où ajouter dans le portail                |
|---------------|-------------------------|----------------------------------|
| Nom de l’appareil | Nom public de votre appareil certifié         | Onglet Informations de base dans Détails de l’appareil|
| Nom de la société| Nom de votre entreprise.  | Non modifiable dans le portail. Extrait du nom de compte MPN |
| Photo du produit  | Image de votre appareil avec une résolution minimale de 200 x 200 pixels  | Détails marketing |
| Classification de la certification  | Étiquette de certification Azure Certified Device obligatoire et badges de certification facultatifs  | Onglet Informations de base dans Détails de l’appareil Doit passer les tests appropriés dans la section Connecter et tester. |

## <a name="product-description-page-information"></a>Informations sur la page de description du produit

Une fois qu’un client a cliqué sur la vignette de votre appareil à partir de la page de recherche du catalogue, il accède à la page de description du produit de votre appareil. C’est là que se trouve l’essentiel des informations fournies pendant le processus de certification.

La partie supérieure de la page de description du produit met en évidence les principales caractéristiques, dont certaines ont déjà été utilisées pour la vignette du produit.

![Barre supérieure PDP](./media/concepts-marketing/pdp-top.png)

| Champ | Description                  | Où ajouter dans le portail                |
|---------------|-------------------------|----------------------------------|
| Classe d’appareil | Classification du facteur de forme et de l’objectif principal de votre appareil ([En savoir plus](./resources-glossary.md))       | Onglet Informations de base dans Détails de l’appareil|
| Type d’appareil | Classification de l’appareil en fonction de l’état de préparation à l’implémentation ([En savoir plus](./resources-glossary.md)) | Onglet Informations de base dans Détails de l’appareil |
| Disponibilité géographique | Régions dans lesquelles votre appareil est disponible à l’achat  | Détails marketing |
| Systèmes d’exploitation  | Système(s) d’exploitation pris en charge par votre appareil  | Onglet Détails du produit dans Détails de l’appareil |
| Secteurs d’activité cibles  | Les trois principaux secteurs d’activité pour lesquels votre appareil est optimisé  | Détails marketing |
| Description de produit  | Champ de texte libre dans lequel vous pouvez rédiger la description marketing de votre produit. Il peut s’agir de détails non répertoriés dans le portail ou d’un contexte supplémentaire pour les avantages de l’utilisation de votre produit. | Détails marketing|

Le reste de la page est consacré à l’affichage des spécifications techniques de votre appareil sous forme de tableau qui aidera votre client à mieux comprendre votre produit. Pour plus de commodité, les informations affichées en haut de la page sont également répertoriées ici. Le reste du tableau est divisé en sections selon les composants spécifiés dans le portail.

![Bas de page PDP](./media/concepts-marketing/pdp-bottom.png)

| Champ | Description                  | Où ajouter dans le portail                |
|---------------|-------------------------|----------------------------------|
| Type de composant | Classification du facteur de forme et de l’objectif principal de votre appareil ([En savoir plus](./resources-glossary.md))       | Détails du produit dans Détails de l’appareil|
| Nom du composant| Nom du composant que vous décrivez | Détails du produit dans Détails de l’appareil |
| Informations supplémentaires sur les composants | Spécifications matérielles supplémentaires, telles que les capteurs inclus, la connectivité, les accélérateurs, etc.  | Informations supplémentaires sur les composants dans Détails de l’appareil ([En savoir plus](./how-to-using-the-components-feature.md))  |
| Texte de dépendance de l’appareil | Texte fourni par le partenaire décrivant les différentes dépendances requises par le produit pour se connecter à Azure ([En savoir plus](./how-to-indirectly-connected-devices.md))   | Section des commentaires côté client de l’onglet Dépendances dans Détails de l’appareil |
| Lien de dépendance de l’appareil  | Lien vers un appareil certifié dont votre produit actuel a besoin  | Onglet Dépendances dans Détails de l’appareil |

## <a name="shop-links"></a>Liens d’achat
Un bouton « Acheter » est disponible à la fois sur la vignette produit et sur la page de description du produit. Lorsque le client clique dessus, une fenêtre s’ouvre et lui permet de sélectionner un distributeur (vous êtes autorisé à répertorier jusqu’à cinq distributeurs). Une fois la sélection effectuée, le client est redirigé vers l’URL fournie par le partenaire.

![Image de la fenêtre contextuelle Acheter](./media/concepts-marketing/shop.png)

| Champ | Description                  | Où ajouter dans le portail                |
|---------------|-------------------------|----------------------------------|
| Nom du serveur de distribution | Nom du distributeur qui vend votre produit | Détails marketing|
| Obtenir un appareil| Lien vers un site Web externe permettant au client d’acheter l’appareil (ou de demander un devis au distributeur). Il peut s’agir de la même page que celle du fabricant si le distributeur est le fabricant de l’appareil. Si une page d’achat n’est pas disponible, elle redirige le client vers la page du distributeur afin que le client puisse le contacter directement.  | URL de la page produit du distributeur dans les détails marketing. Si aucune page d’achat n’est disponible, le lien sera par défaut l’URL du distributeur dans Détails marketing. |

## <a name="external-links"></a>Liens externes
Des liens sont également inclus dans la page Description du produit pour accéder à des sites ou des fichiers fournis par les partenaires qui aident le client à mieux comprendre le produit. Ils apparaissent en haut de la page, sous le texte de description du produit. Les liens affichés diffèrent selon les types d’appareils et les programmes de certification.

| Lien | Description                  | Où ajouter dans le portail                |
|---------------|-------------------------|----------------------------------|
| Guide de démarrage* | Fichier PDF contenant des instructions pour connecter et utiliser votre appareil avec les services Azure | Ajouter la section du guide de démarrage du portail|
| Page du fabricant*|Lien vers la page du fabricant. Cette page peut être la page produit spécifique de votre appareil ou la page d’accueil de l’entreprise si une page marketing n’est pas disponible. | Page marketing du fabricant dans Détails marketing |
| Modèle de l'appareil | Modèles DTDL publics pour les solutions IoT Plug-and-Play  | Non modifiable dans le portail. Le modèle d’appareil doit être chargé vers le [référentiel de modèles publics](https://aka.ms/modelrepo).  |
| Code source de l’appareil | URL du code source de l’appareil pour les types d’appareils Dev Kit| Onglet Informations de base dans Détails de l’appareil  |

 **Obligatoire pour tous les appareils publiés*

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez compris la façon dont nous utilisons les informations que vous fournissez pendant la certification, vous êtes prêt à certifier votre appareil. Commencez votre projet de certification, ou revenez à l’étape des détails de l’appareil pour ajouter vos propres informations marketing.

- [Commencer votre parcours de certification](./tutorial-00-selecting-your-certification.md)
- [Ajout de détails sur l’appareil](./tutorial-02-adding-device-details.md)
