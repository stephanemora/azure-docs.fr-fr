---
title: Exemple d’application pour l’accès aux données d’analytique de place de marché commerciale
description: Utilisez l’exemple d’application pour créer votre propre application d’analytique de place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583527"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Exemple d’application pour l’accès aux données d’analytique de place de marché commerciale

Des exemples d’applications sont créés en langage C# et JAVA, et sont disponibles sur [GitHub](https://github.com/partneranalytics).

- [Exemple d’application C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Exemple d’application JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Vous pouvez choisir de vous inspirer de l’exemple d’application et créer votre propre application dans n’importe quel langage.

L’exemple d’application atteint les objectifs suivants :

- Génération d’un jeton Azure Active Directory (Azure AD)
- Obtention des jeux de données disponibles
- Création de requêtes définies par l’utilisateur
- Obtention des requêtes système et définies par l’utilisateur
- Planification d’un rapport

L’exemple d’application ne couvre pas la méthode d’appel des API pour d’autres fonctionnalités. Toutefois, le processus d’appel d’autres API reste le même que celui décrit ci-dessus.

## <a name="how-to-run-the-application"></a>Exécution de l’application

1. Clonez le dépôt sur un système local à l’aide de la commande suivante :

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Pour plus d’instructions, consultez le fichier `ProgrammaticExportSampleAppISV/README.md` dans le [dépôt](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git) GitHub.

1. Pour exécuter rapidement l’application, mettez à jour l’ID client et la clé secrète client dans **appsettings.Development.json**.

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Présente un extrait du fichier appsettings.Development.json":::

L’exécution de l’application démarre un serveur web local et ouvre une page (`https://localhost:44365/ProgrammaticExportSampleApp/sample`).

[![Illustre la page de planification de rapport.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Cette page effectue des appels d’API au serveur web s’exécutant sur l’ordinateur local, qui à son tour effectue les appels d’API d’accès par programmation réels.

## <a name="code-snippets"></a>Extraits de code

La structure de base du code C# pour exécuter les appels d’API d’accès par programmation est la suivante :

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Capture d’écran des appels d’API.":::

## <a name="next-steps"></a>Étapes suivantes

- [API pour accéder aux données d’analytique de place de marché commerciale](analytics-available-apis.md)
