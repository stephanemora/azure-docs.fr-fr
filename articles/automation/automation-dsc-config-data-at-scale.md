---
title: Données de configuration à la bonne échelle – Azure Automation
description: Découvrez comment configurer des données à la bonne échelle pour la configuration d’état dans Azure Automation.
keywords: dsc,powershell,configuration,installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585571"
---
# <a name="configuration-data-at-scale"></a>Données de configuration à grande échelle

> S’applique à : Windows PowerShell 5.1

Il peut être très compliqué de gérer des centaines voire des milliers de serveurs.
Aux dires des clients, le plus difficile en réalité est de gérer les [données de configuration](/powershell/scripting/dsc/configurations/configdata).
Organiser les informations dans les différentes constructions logiques comme l’emplacement, le type et l’environnement.

> [!NOTE]
> Cet article fait référence à une solution tenue à jour par la communauté Open Source.
> La prise en charge est disponible uniquement sous forme de collaboration GitHub, et non auprès de Microsoft.

## <a name="community-project-datum"></a>Projet communautaire : Datum

Une solution tenue à jour par la communauté nommée [Datum](https://github.com/gaelcolas/Datum) a été créée pour relever ce défi.
Datum s’appuie sur les idées ingénieuses d’autres plateformes de gestion de configuration et implémente le même type de solution pour PowerShell DSC.
Les informations sont [organisées dans des fichiers texte](https://github.com/gaelcolas/Datum#3-intended-usage) selon des idées logiques.
Voici quelques exemples :

- Paramètres devant s’appliquer globalement
- Paramètres devant s’appliquer à tous les serveurs d’un site
- Paramètres devant s’appliquer à tous les serveurs de base de données
- Paramètres d’un serveur individuel

Ces informations sont organisées dans le format de fichier de votre choix (JSON, YAML ou PSD1).
Des applets de commande sont ensuite fournies pour générer des fichiers de données de configuration en [regroupant les informations](https://github.com/gaelcolas/Datum#datum-tree) de chaque fichier dans une même vue d’un serveur ou rôle de serveur.

Une fois que les fichiers de données ont été générés, vous pouvez les utiliser avec des [scripts de configuration DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) pour générer des fichiers MOF et [charger ces mêmes fichiers MOF dans Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Inscrivez ensuite vos serveurs [localement](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [dans Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) pour extraire les configurations.

Pour tester Datum, accédez à [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) et téléchargez la solution ou cliquez sur « Project Site » pour afficher la [documentation](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la configuration d’état souhaité Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ressources DSC](/powershell/scripting/dsc/resources/resources)
- [Configuration de Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
