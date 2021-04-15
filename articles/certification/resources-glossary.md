---
title: Glossaire du programme Azure Certified Device
description: Liste des termes courants utilisés dans le programme Azure Certified Device
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: 7dfdd4a89535621e08db8a8924282febdafd1569
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975668"
---
# <a name="azure-certified-device-program-glossary"></a>Glossaire du programme Azure Certified Device

Ce guide fournit les définitions des termes couramment utilisés dans le programme et le portail Azure Certified Device. Reportez-vous à ce glossaire pour clarifier le processus de certification. Pour plus de commodité, ce glossaire est classé en fonction des principaux concepts de certification pour lesquels vous pouvez vous poser des questions.

## <a name="device-class"></a>Classe d’appareil

Lorsque vous créez votre projet de certification, vous êtes invité à spécifier une classe d’appareil. La classe d’appareil fait référence au facteur de forme ou à la classification qui correspond le mieux à votre appareil.

- **Passerelle**

    Appareil qui traite les données envoyées sur un réseau IoT.

- **Capteur**

    Appareil qui détecte les modifications apportées à un environnement et y répond, et qui se connecte aux passerelles pour traiter les modifications.

- **Autres**

    Si vous sélectionnez Autre, ajoutez une description de votre classe d’appareil dans vos propres mots. Au fil du temps, nous allons continuer à ajouter de nouvelles valeurs à cette liste, en particulier et consultant les commentaires de nos partenaires.

## <a name="device-type"></a>Type d’appareil

Vous êtes également invité à sélectionner l’un des deux types d’appareils pendant le processus de certification.

- **Produit fini**

    Appareil qui est prêt pour les solutions et prêt pour le déploiement en production. Généralement dans un facteur de forme fini avec un microprogramme et un système d’exploitation. Il peut s’agir d’appareils à usage général qui nécessitent une personnalisation supplémentaire ou des appareils spécialisés qui ne nécessitent aucune modification pour être utilisés.
- **Kit de développement prêt pour les solutions**

    Kit de développement contenant du matériel et des logiciels idéaux pour faciliter le prototypage, généralement pas dans un facteur de forme fini. Contient généralement des exemples de code et des didacticiels pour permettre un prototypage rapide.

## <a name="component-type"></a>Type de composant

Dans la section Détails de l’appareil, vous allez décrire votre appareil en répertoriant les composants par type de composant. Vous pouvez afficher plus d’informations sur les composants [ici](./how-to-using-the-components-feature.md).

- **Produit prêt pour le client**

    Représentation de composant de l’appareil global ou principal. Cela diffère d’un **Produit fini**, qui est une classification de l’appareil comme étant prêt pour une utilisation par le client sans développement supplémentaire. Un Produit fini contient un composant de produit prêt pour le client.
- **Tableau de développement**

    Tableau intégré ou détachable avec microprocesseur pour faciliter la personnalisation.
- **Périphérique**

    Ajout intégré ou détachable au produit (par exemple, un accessoire). Il s’agit généralement d’appareils qui se connectent à l’appareil principal, mais qui ne contribuent pas aux fonctions principales des appareils. Au lieu de cela, il fournit des fonctions supplémentaires. La mémoire, la RAM, le stockage, les disques durs et les processeurs ne sont pas considérés comme des périphériques (ils doivent à la place être répertoriés sous Spécifications supplémentaires du composant produit prêt pour le client).
- **Système-sur-module**  

    Circuit au niveau du tableau qui intègre une fonction système dans un module unique.

## <a name="component-attachment-method"></a>Méthode d’attachement de composant

La méthode d’attachement de composant est un autre détail de composant qui informe le client de la façon dont le composant est intégré à l’ensemble du produit.

- **Intégré**
 
    Fait référence au moment où un composant d’appareil fait partie du châssis principal du produit. Cela fait généralement référence à un type de composant périphérique qui ne peut pas être supprimé de l’appareil.  
    Exemple : Capteur de température intégré à l’intérieur d’un châssis de passerelle.

- **Discret**

    Fait référence au moment où un composant ne fait **pas** partie du châssis principal du produit.  
    Exemple : Capteur de température externe qui doit être attaché à l’appareil.


## <a name="next-steps"></a>Étapes suivantes

Ce glossaire vous guidera tout au long du processus de certification de votre projet sur le portail. Vous êtes maintenant prêt à démarrer votre projet !
- [Tutoriel : Création de votre projet](./tutorial-01-creating-your-project.md)
