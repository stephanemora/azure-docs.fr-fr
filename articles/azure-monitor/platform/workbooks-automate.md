---
title: Classeurs Azure Monitor et modèles Azure Resource Manager
description: Créez des rapports complexes en toute simplicité grâce à des classeurs Azure Monitor paramétrables prédéfinis et personnalisés déployés au moyen de modèles Azure Resource Manager.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658402"
---
# <a name="programmatically-manage-workbooks"></a>Gérer programmatiquement des classeurs

Les propriétaires de ressources ont la possibilité de créer et de gérer programmatiquement leurs classeurs au moyen de modèles Resource Manager, 

ce qui peut être utile dans différents scénarios :
* le déploiement de rapports d’analytique propres à l’organisation ou au domaine, ainsi que le déploiement de ressources (par exemple, des classeurs de performances et de défaillances propres à l’organisation pour de nouvelles applications ou machines virtuelles) ;
* le déploiement de rapports ou de tableaux de bord standard à l’aide de classeurs pour des ressources existantes.

Le classeur sera créé dans le groupe ou sous-groupe de ressources souhaité et comportera le contenu spécifié dans les modèles Resource Manager.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modèle Resource Manager pour le déploiement de classeurs
1. Ouvrez le classeur à déployer programmatiquement.
2. Basculez le classeur en mode d’édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
3. Ouvrez _l’Éditeur avancé_ avec le bouton _</>_ de la barre d’outils.
4. Dans l’éditeur, basculez _Type de modèle_ sur _Modèle Resource Manager_.
5. Le modèle Resource Manager de création apparaît dans l’éditeur. Copiez le contenu et utilisez-le tel quel ou fusionnez-le avec un modèle plus vaste qui déploie également la ressource cible.

    ![Image montrant comment obtenir le modèle Resource Manager sur l’interface utilisateur du classeur](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Exemple de modèle Azure Resource Manager
Ce modèle montre comment déployer un classeur simple qui affiche « Hello World ! » :
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Paramètres de modèle

| Paramètre | Explication |
| :------------- |:-------------|
| `workbookDisplayName` | Nom convivial du classeur utilisé dans la galerie ou la liste enregistrée. Doit être unique dans l’étendue du groupe de ressources et de la source. |
| `workbookType` | Galerie dans laquelle le classeur s’affichera. Valeurs prises en charge : workbook, `tsg`, Azure Monitor, etc. |
| `workbookSourceId` | ID de l’instance de ressource à laquelle le classeur sera associé. Le nouveau classeur s’affichera en rapport avec cette instance de ressource, par exemple dans le sommaire de la ressource sous _Classeur_. Si vous souhaitez que votre classeur apparaisse dans la galerie de classeurs d’Azure Monitor, utilisez la chaîne _Azure Monitor_ au lieu d’un ID de ressource. |
| `workbookId` | GUID unique de cette instance de classeur. Utilisez _[newGuid()]_ pour créer automatiquement un nouveau GUID. |
| `kind` | Permet de spécifier si le classeur créé est partagé ou privé. Utilisez la valeur _shared_ pour les classeurs partagés et _user_ pour les classeurs privés. |
| `location` | Emplacement Azure où sera créé le classeur. Utilisez _[resourceGroup().location]_ pour le créer dans le même emplacement que le groupe de ressources. |
| `serializedData` | Contient le contenu ou la charge utile à utiliser dans le classeur. Utilisez le modèle Resource Manager de l’interface utilisateur des classeurs pour récupérer la valeur. |

### <a name="workbook-types"></a>Types de classeurs
Les types de classeurs spécifient dans quel type de galerie de classeurs la nouvelle instance de classeur s’affichera. Options disponibles :

| Type | Emplacement de la galerie |
| :------------- |:-------------|
| `workbook` | Valeur par défaut utilisée dans la plupart des rapports, qui comprend la galerie de classeurs d’Application Insights, d’Azure Monitor, etc.  |
| `tsg` | Galerie de guides de dépannage dans Application Insights |
| `usage` | Galerie _Plus_ sous _Utilisation_ dans Application Insights |

### <a name="limitations"></a>Limites
Pour une raison technique, ce mécanisme ne permet pas de créer des instances de classeur dans la galerie _Classeurs_ d’Application Insights. Nous mettons tout en œuvre pour corriger cette limitation. En attendant, nous vous recommandons d’utiliser la Galerie de guides de dépannage (workbookType : `tsg`) pour déployer des classeurs Application Insights.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment les classeurs sont utilisés dans la nouvelle [expérience Azure Monitor pour le stockage](../insights/storage-insights-overview.md).

