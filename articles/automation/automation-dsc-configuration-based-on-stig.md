---
title: Configurer des données basées sur STIG pour Azure Automation State Configuration
description: Cet article explique comment configurer des données basées sur STIG pour Azure Automation State Configuration.
keywords: dsc,powershell,configuration,installation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d1b05f9e77d3530f3e883aa3f9d98de09c8f54c2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836989"
---
# <a name="configure-data-based-on-stig"></a>Configurer des données basées sur STIG

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
Inscrivez ensuite vos serveurs [localement](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [dans Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) pour extraire les configurations.

Pour tester PowerSTIG, accédez à [PowerShell Gallery](https://www.powershellgallery.com) et téléchargez la solution ou cliquez sur « Project Site » pour afficher la [documentation](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre DSC PowerShell, consultez [Vue d’ensemble de la fonctionnalité Desired State Configuration de Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Découvrez les ressources DSC PowerShell dans [Ressources DSC](/powershell/scripting/dsc/resources/resources).
- Pour plus d’informations sur la configuration du Configuration Manager local, consultez [configuration du Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig).
