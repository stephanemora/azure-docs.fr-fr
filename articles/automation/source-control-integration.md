---
title: Intégration du contrôle de code source dans Azure Automation
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046251"
---
# <a name="source-control-integration-in-azure-automation"></a>Intégration du contrôle de code source dans Azure Automation

Le contrôle de code source vous permet de vous assurer que vos runbooks dans votre compte Automation restent à jour par rapport à vos scripts dans votre référentiel de contrôle de code source GitHub ou Azure DevOps. Le contrôle du code source vous permet de collaborer avec votre équipe en toute simplicité, de suivre les modifications et de restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches avec vos comptes de développement, de test ou de production Automation, et contribue à promouvoir le code qui a été testé dans votre environnement de développement vers votre compte Automation de production.

Azure Automation prend en charge 3 types de contrôles de code source :

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>Conditions préalables

* Un référentiel de contrôle de code source (GitHub ou Visual Studio Team Services)
* [Un compte d’identification et une connexion](manage-runas-account.md)

> [!NOTE]
> Les travaux de synchronisation de contrôle de code source s’exécutent sous le compte Automation des utilisateurs et sont facturés au même tarif que les autres tâches Automation.

## <a name="configure-source-control"></a>Configuration du contrôle de code source

Dans votre compte Automation, sélectionnez **Contrôle de code source (préversion)**, puis cliquez sur **+ Ajouter**.

![Sélection du contrôle de code source](./media/source-control-integration/select-source-control.png)

Choisissez **Type de contrôle de code source**, puis cliquez sur **Authentifier**.

Examinez la page des autorisations de demande d’application, puis cliquez sur **Accepter**.

Sur la page **Récapitulatif du contrôle de code source**, fournissez les informations requises, puis cliquez sur **Enregistrer**. Le tableau ci-après décrit les champs disponibles.

|Propriété  |Description  |
|---------|---------|
|Nom du contrôle de code source     | Nom convivial du contrôle de code source.        |
|Type de contrôle de code source     | Type de source de contrôle de code source. Options disponibles :</br> Github</br>Visual Studio Team Services (Git)</br>Visual Studio Team Services (TFVC)        |
|Référentiel     | Nom du référentiel ou du projet. Ce nom est extrait du référentiel de contrôle de code source. Exemple : $/ContosoFinanceTFVCExample         |
|Branche     | Branche de laquelle extraire les fichiers sources. Le ciblage de branche n’est pas disponible pour le type de contrôle de code source TFVC.          |
|Chemin d’accès du dossier     | Dossier contenant les runbooks à synchroniser. Exemple : /Runbooks         |
|Synchronisation automatique     | Active ou désactive la synchronisation automatique lorsqu’une validation est effectuée dans le référentiel de contrôle de code source.         |
|Publier un runbook     | Si ce champ est défini sur **Actif**, les runbooks sont automatiquement publiés une fois qu’ils ont été synchronisés à partir du contrôle de code source.         |
|Description     | Champ de texte permettant de fournir des détails supplémentaires.        |

![Récapitulatif du contrôle de code source](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Synchronisation

Si la synchronisation automatique a été définie lors de la configuration de l’intégration du contrôle de code source, la synchronisation initiale démarre automatiquement. Si la synchronisation automatique n’a pas été définie, sélectionnez la source dans le tableau de la page **Contrôle de code source (préversion)**. Cliquez sur **Démarrer la synchronisation** pour initialiser le processus de synchronisation.  

Vous pouvez visualiser l’état du travail de synchronisation actuel ou des travaux précédents en cliquant sur l’onglet **Travaux de synchronisation**. Dans la liste déroulante **Contrôle de code source**, sélectionnez un contrôle de code source.

![État de synchronisation](./media/source-control-integration/sync-status.png)

Vous pouvez cliquer sur un travail afin de visualiser la sortie correspondante. Voici un exemple de sortie d’un travail de synchronisation de contrôle de code source.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter d’un référentiel de contrôle de code source, ouvrez **Contrôle de code source (préversion)** sous **Paramètres du compte** dans votre compte Automation.

Sélectionnez le contrôle de code source que vous souhaitez supprimer. Sur la page **Récapitulatif du contrôle de code source**, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
