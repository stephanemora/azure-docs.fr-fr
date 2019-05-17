---
title: Résoudre les problèmes de l’Extension volet d’accès Azure pour IE | Microsoft Docs
description: Comment utiliser la stratégie de groupe pour déployer le module complémentaire Internet Explorer du portail Mes applications.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: f08a02842c97b0f4076a1b311aa918df6d83c592
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824497"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Résoudre les problèmes de l’Extension volet d’accès pour Internet Explorer

Cet article vous aide à résoudre les problèmes suivants :

* Vous ne parvenez pas à accéder à vos applications par le biais du portail Mes applications quand vous utilisez Internet Explorer.
* Le message « Installer le logiciel » s’affiche même si vous avez déjà installé le logiciel.

Si vous êtes un administrateur, consultez [comment déployer l’Extension volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Exécutez l’outil de diagnostic

Vous pouvez diagnostiquer les problèmes d’installation de l’Extension volet d’accès en téléchargeant et en exécutant l’outil de diagnostic du volet d’accès. 

Pour télécharger et installer l’outil de diagnostic :

1. [Sélectionnez ce lien pour télécharger l’outil de diagnostic.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Ouvrez le fichier et extraire le contenu sur votre ordinateur.
   
3. Pour exécuter l’outil, cliquez sur le fichier nommé *AccessPanelExtensionDiagnosticTool.js* et sélectionnez **ouvrir avec** > **Microsoft Windows Based Script Host** .
   
    ![Ouvrir avec > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Passez en revue les résultats de diagnostics qui s’affichent et sélectionnent **Oui** de résoudre les problèmes. Le **Check Results** boîte de dialogue s’affiche avec des informations sur ce qu’il faut faire si l’extension ne fonctionne pas.  

5. Lire le message et sélectionnez **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Vérifiez que l’extension Volet d’accès est activée.

Pour vérifier que vous avez activé l’Extension volet d’accès dans Internet Explorer :

1. Dans Internet Explorer, sélectionnez le **icône d’engrenage** sur le coin supérieur droit de la fenêtre et sélectionnez **options Internet**.
   
2. Accédez à la **programmes** onglet et sélectionnez **gérer les modules complémentaires**.
   
3. Sélectionnez **Extension volet d’accès** dans le **Microsoft Corporation** section et sélectionnez **activer**.
   
4. Pour enregistrer les modifications, fermez toutes les fenêtres de navigateur Internet Explorer, vous avez ouvert. La modification prend effet la prochaine fois que vous ouvrez Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Activer les extensions pour la navigation InPrivate

Pour activer les extensions pour la navigation InPrivate :

1. Dans Internet Explorer, sélectionnez le **icône d’engrenage** sur le coin supérieur droit de la fenêtre et sélectionnez **options Internet**.
   
2. Accédez à la **confidentialité** onglet et vérifiez que le **désactiver les barres d’outils et extensions au démarrage de la navigation InPrivate** à cocher est désactivée.
   
3.  Pour enregistrer les modifications, fermez toutes les fenêtres de navigateur Internet Explorer, vous avez ouvert. La modification prend effet la prochaine fois que vous ouvrez Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Désinstallation de l’extension Volet d’accès

Pour désinstaller l’Extension volet d’accès à partir de votre ordinateur :

1. Dans le panneau de configuration, recherchez *désinstaller*. 

2. Dans les résultats de recherche, sélectionnez **désinstaller un programme**.
   
    ![Recherchez le programme de désinstallation.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. Dans la liste, sélectionnez **Extension volet d’accès** et sélectionnez **désinstallation**.

    ![Désinstaller l’Extension volet d’accès.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Essayez ensuite de réinstaller l’extension pour vérifier que le problème a été résolu.

Si vous rencontrez des problèmes pour désinstaller l’extension, vous pouvez également supprimer à l’aide de la [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) outil.

## <a name="related-articles"></a>Articles connexes
* [Accès aux applications et authentification unique avec Azure Active Directory](what-is-single-sign-on.md)
* [Comment déployer l’Extension volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](deploy-access-panel-browser-extension.md)

