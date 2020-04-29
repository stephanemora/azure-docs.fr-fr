---
title: Convertir des configurations en ressources composites pour la configuration d’état – Azure Automation
description: Découvrez comment convertir des configurations en ressources composites pour la configuration d’état dans Azure Automation.
keywords: dsc,powershell,configuration,installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585512"
---
# <a name="convert-configurations-to-composite-resources"></a>Convertir des configurations en ressources composites

> S’applique à : Windows PowerShell 5.1

Une fois que vous avez commencé à créer des configurations, vous pouvez créer rapidement des « scénarios » qui gèrent les groupes de paramètres.
Voici quelques exemples :

- création d’un serveur web
- création d’un serveur DNS
- création d’un serveur SharePoint
- configuration d’un cluster SQL
- gestion des paramètres de pare-feu
- gestion des paramètres de mot de passe

Si vous souhaitez partager ce travail avec d’autres personnes, la meilleure solution consiste à empaqueter la configuration sous forme de [ressource composite](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Il peut être déroutant de créer des ressources composites pour la première fois.

> [!NOTE]
> Cet article fait référence à une solution tenue à jour par la communauté Open Source.
> La prise en charge est disponible uniquement sous forme de collaboration GitHub, et non auprès de Microsoft.

## <a name="community-project-compositeresource"></a>Projet communautaire : CompositeResource

Une solution tenue à jour par la communauté nommée [CompositeResource](https://github.com/microsoft/compositeresource) a été créée pour relever ce défi.

CompositeResource automatise la création d’un module à partir de votre configuration.
Commencez par faire un appel de source de type [dot sourcing](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) pour le script de configuration sur votre station de travail (ou serveur de builds) afin de le charger en mémoire.
Ensuite, au lieu d’exécuter la configuration pour générer un fichier MOF, utilisez la fonction fournie par le module CompositeResource pour automatiser une conversion.
L’applet de commande charge le contenu de votre configuration, obtient la liste de paramètres et génère un nouveau module avec tout ce dont vous avez besoin.

Une fois que vous avez généré un module, vous pouvez incrémenter la version et ajouter des notes de publication chaque fois que vous apportez des modifications et que vous les publiez dans votre propre [référentiel PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Après avoir créé un module de ressources composites contenant votre configuration (ou plusieurs configurations), vous pouvez les utiliser dans l’[expérience de création composable](/azure/automation/compose-configurationwithcompositeresources) d’Azure ou les ajouter à des [scripts de configuration DSC](/powershell/scripting/dsc/configurations/configurations) pour générer des fichiers MOF et [charger ces mêmes fichiers MOF dans Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Inscrivez ensuite vos serveurs [localement](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [dans Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) pour extraire les configurations.
À l’occasion de la dernière mise à jour du projet, des [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) ont aussi été publiés pour Azure Automation afin d’automatiser l’importation de configurations à partir de PowerShell Gallery.

Pour tester la création automatique de ressources composites pour DSC, accédez à [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) et téléchargez la solution ou cliquez sur « Project Site » pour afficher la [documentation](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la configuration d’état souhaité Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ressources DSC](/powershell/scripting/dsc/resources/resources)
- [Configuration de Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
