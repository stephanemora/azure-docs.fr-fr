---
title: Préparation de la solution Dynamics 365
description: Framework de création de packages, d’installation et de désinstallation de composants
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 919feb39c9cd84f8da0ef89827ad3e83e0eb9bc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935247"
---
# <a name="dynamics-365-solution-preparation"></a>Préparation de la solution Dynamics 365

Le système de solutions Dynamics 365 est une infrastructure de création de packages, d’installation et de désinstallation de composants qui fournissent des fonctionnalités métier spécifiques. Les solutions sont utilisées par les éditeurs de logiciels et autres partenaires Microsoft Dynamics 365 pour distribuer les extensions qu’ils créent.

Si vous êtes un éditeur de logiciels Dynamics 365 (xRM), vous avez probablement déjà créé une solution managée et disposez d’un fichier solution.zip. Dans votre solution, vérifiez que les champs « Nom d’affichage » et « Description » correspondent à ce que les clients doivent voir. Ceux-ci sont affichés dans le Centre d’administration CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Remarque :** Dans l’exemple de package suivant, nous supposons que le nom de la solution est « SampleSolution.zip »_

Si vous êtes un nouvel éditeur de logiciels, vous pouvez obtenir plus d’informations sur la création d’une solution ici : [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Si votre solution doit prendre en charge des données :

* Créer les exemples de données dans votre environnement de test
* Utilisez l’outil de migration de la configuration pour créer un schéma avec des règles de comparaison de vos données.
* Enregistrez votre schéma de configuration avec vos fichiers de projet. Vous en aurez besoin plus tard si vous mettez à jour vos données de configuration.
* Exportez vos données de configuration. Veillez à donner au fichier d’exportation un nom indiquant explicitement votre exportation.
