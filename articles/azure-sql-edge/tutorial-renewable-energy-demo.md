---
title: Déploiement d’Azure SQL Edge sur les éoliennes d’un parc éolien Contoso
description: Dans ce tutoriel, vous allez utiliser Azure SQL Edge pour détecter l’activation des éoliennes d’un parc éolien Contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723471"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Utilisation d’Azure SQL Edge pour créer des ressources renouvelables plus intelligentes

Cette démonstration Azure SQL Edge prend comme exemple Contoso Renewable Energy, un parc éolien qui utilise SQL DB Edge pour le traitement des données au sein du générateur. 

Cette démonstration vous montre comment résoudre une alerte déclenchée par la détection d’une turbulence au niveau de l’appareil. Vous allez entraîner un modèle et le déployer sur SQL DB Edge afin de résoudre le problème de turbulence détecté et d’optimiser la puissance électrique.

Azure SQL Edge - Vidéo de démonstration Renewable Energy sur Channel 9 :
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Configuration de la démonstration sur votre ordinateur local
Git sera utilisé pour copier tous les fichiers de la démonstration sur votre ordinateur local. 

1. Pour télécharger Git, [cliquez ici](https://git-scm.com/download).
2. Ouvrez une invite de commandes pour accéder au dossier dans lequel télécharger le dépôt. 
3. Émettez la commande https://github.com/microsoft/sql-server-samples.git.
4. Accédez à **« sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo »** à l’emplacement où le dépôt a été cloné.
5. Suivez les instructions du fichier README.md pour configurer l’environnement de démonstration et exécuter la démonstration.