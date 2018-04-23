---
title: Basculer entre le mode d’affichage et le mode d’édition des rapports dans les collections d’espaces de travail Power BI | Microsoft Docs
description: Découvrez comment basculer entre le mode d’affichage et le mode d’édition de vos rapports dans les collections d’espaces de travail Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 23a8c4f0dd626a623df56de9546258a23d549d1a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Basculer entre le mode d’affichage et le mode d’édition des rapports dans les collections d’espaces de travail Power BI

Découvrez comment basculer entre le mode d’affichage et le mode d’édition de vos rapports dans les collections d’espaces de travail Power BI.

> [!IMPORTANT]
> Les collections d’espaces de travail Power BI sont déconseillées et disponibles jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la migration de vos données vers Power BI Embedded, consultez l’article [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migration du contenu de collections d’espaces de travail Power BI vers Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Créer un jeton d’accès

Vous devez créer un jeton d’accès qui vous donne la possibilité d’afficher et de modifier un rapport. Pour modifier et enregistrer un rapport, vous devez disposer de l’autorisation du jeton **Report.ReadWrite**. Pour plus d’informations, consultez l’article [Authentification et autorisation dans les collections d’espaces de travail Power BI](app-token-flow.md).

> [!NOTE]
> Cela vous permet de modifier un rapport existant et d’enregistrer ces modifications. Si vous souhaitez également bénéficier de la prise en charge de l’opération **Enregistrer sous**, vous devez fournir des autorisations supplémentaires. Pour plus d’informations, consultez la page [Étendues](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuration d’incorporation

Vous devez fournir les autorisations et un mode d’affichage pour voir le bouton « enregistrer » en mode d’édition. Pour plus d’informations, consultez la page [Détails de la configuration d’incorporation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Par exemple, dans JavaScript :

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

La valeur **models.ViewMode.View** de **viewMode** indique que le rapport doit être incorporé en mode d’affichage.

## <a name="view-mode"></a>Mode Affichage

Vous pouvez utiliser le code JavaScript suivant pour basculer en mode Affichage, si vous êtes en mode Édition.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Mode Édition

Vous pouvez utiliser le code JavaScript suivant pour basculer en mode Édition, si vous êtes en mode Affichage.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](get-started-sample.md)  
[Embed a report](embed-report.md) (Intégrer un rapport)  
[Authentification et autorisation dans les collections d’espaces de travail Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Référentiel Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Référentiel Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)
