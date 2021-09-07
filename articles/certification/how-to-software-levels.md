---
title: Niveaux logiciels des appareils certifiés Azure
description: Répartition des différents niveaux logiciels sous lesquels un appareil certifié Azure peut être classé.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 06/22/2021
ms.custom: template-concept
ms.openlocfilehash: 5cd8a97604c12397291d7a130ff81b790c1857fd
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970308"
---
# <a name="software-levels-of-azure-certified-devices"></a>Niveaux logiciels des appareils certifiés Azure

Les niveaux logiciels sont une fonctionnalité définie par le programme Azure Certified Device pour aider les générateurs d’appareils à indiquer le niveau technique de difficulté qu’un client peut attendre lors de la connexion de l’appareil aux services Azure. Apparaissant dans le catalogue en tant que « configuration logicielle Azure », ces valeurs sont destinées à aider les utilisateurs à mieux comprendre le produit et sa connexion à Azure. La définition de chacun de ces niveaux est fournie ci-dessous.

## <a name="level-1"></a>Niveau 1

L’utilisateur peut immédiatement connecter l’appareil à Azure en ajoutant simplement les détails de l’approvisionnement. L’appareil IoT certifié contient déjà un logiciel préinstallé qui a été utilisé pour la certification lors de l’achat. Ce niveau ressemble le plus à une expérience de configuration prête à l’emploi pour les débutants d’IoT qui ne sont pas aussi à l’aise avec la compilation du code source.

## <a name="level-2"></a>Niveau 2

L’utilisateur doit flasher/appliquer l’image logicielle fournie par le fabricant sur l’appareil pour se connecter à Azure. Des outils/expériences logicielles supplémentaires peuvent être nécessaires. Le lien vers l’image logicielle est également fourni dans notre catalogue.

## <a name="level-3"></a>Niveau 3

L’utilisateur doit suivre un guide fourni par le fabricant pour préparer et installer les logiciels spécifiques à Azure. Aucune image logicielle spécifique à Azure n’étant fournie, une certaine personnalisation et une certaine compilation du code source fourni sont requises.

## <a name="level-4"></a>Niveau 4

L’utilisateur doit développer, personnaliser et recompiler le code de son propre appareil pour se connecter à Azure. Aucun code source pris en charge par le fabricant n’est disponible. Ce niveau convient particulièrement aux développeurs qui cherchent à créer des déploiements personnalisés pour leur appareil.

## <a name="next-steps"></a>Étapes suivantes

Ces niveaux visent à vous aider à commencer à créer des solutions IoT avec Azure. Prêt à vous lancer ? Visitez le [catalogue Azure Certified Device](https://devicecatalog.azure.com) pour vous mettre à rechercher des appareils !

Êtes-vous un générateur d’appareils qui souhaite ajouter ce niveau logiciel à votre appareil certifié ? Suivez les liens ci-dessous.
- [Modifier un appareil précédemment publié](how-to-edit-published-device.md)
- [Tutoriel : Ajout des détails de l’appareil](tutorial-02-adding-device-details.md)
