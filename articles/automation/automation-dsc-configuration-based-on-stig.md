---
title: Configuration basée sur STIG à utiliser dans la configuration d’état – Azure Automation
description: Découvrez les configurations basées sur STIG pour la configuration d’état dans Azure Automation.
keywords: dsc,powershell,configuration,installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028280"
---
# <a name="configuration-based-on-stig"></a>Configuration basée sur STIG

> S’applique à : Windows PowerShell 5.1

Créer du contenu de configuration pour la première fois peut s’avérer délicat.
Dans bien des cas, l’objectif est d’automatiser la configuration des serveurs selon une « ligne de base » qui, de préférence, est conforme aux recommandations du secteur.

> [!NOTE]
> Cet article fait référence à une solution tenue à jour par la communauté Open Source.
> La prise en charge est disponible uniquement sous forme de collaboration GitHub, et non auprès de Microsoft.

## <a name="community-project-powerstig"></a>Projet communautaire : PowerSTIG

Un projet communautaire nommé [PowerSTIG](https://github.com/microsoft/powerstig) a pour but de résoudre ce problème en générant du contenu DSC basé sur des [informations publiques](https://public.cyber.mil/stigs/) sur STIG (Security Technical Implementation Guide).

Utiliser des lignes de base est plus compliqué qu’il n’y paraît.
Nombreuses sont les organisations à devoir [documenter les exceptions](https://github.com/microsoft/powerstig#powerstigdata) aux règles et à gérer ces données à la bonne échelle.
PowerSTIG résout le problème en fournissant des [ressources composites](https://github.com/microsoft/powerstig#powerstigdsc) destinées à gérer chaque partie de la configuration au lieu de tenter de gérer l’ensemble des paramètres dans un même fichier volumineux.

Une fois que les configurations ont été générées, vous pouvez utiliser les [scripts de configuration DSC](/powershell/scripting/dsc/configurations/configurations) pour générer des fichiers MOF et [charger ces mêmes fichiers MOF dans Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Inscrivez ensuite vos serveurs [localement](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [dans Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) pour extraire les configurations.

Pour tester PowerSTIG, accédez à [PowerShell Gallery](https://www.powershellgallery.com) et téléchargez la solution ou cliquez sur « Project Site » pour afficher la [documentation](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la configuration d’état souhaité Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ressources DSC](/powershell/scripting/dsc/resources/resources)
- [Configuration de Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
