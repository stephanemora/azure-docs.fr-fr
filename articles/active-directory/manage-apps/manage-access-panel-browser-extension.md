---
title: Résolution des problèmes liés à l’extension Volet d’accès Azure pour IE| Microsoft Docs
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
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723906"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Résolution des problèmes liés à l’extension Volet d’accès pour Internet Explorer

Cet article vous aide à résoudre les problèmes suivants :

* Vous ne parvenez pas à accéder à vos applications par le biais du portail Mes applications quand vous utilisez Internet Explorer.
* Le message « Installer le logiciel » s’affiche même si vous avez déjà installé le logiciel.

Si vous êtes administrateur, consultez [Déploiement de l’extension Volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Exécuter l’outil de diagnostic

Vous pouvez diagnostiquer les problèmes d’installation de l’extension Volet d’accès en téléchargeant et en exécutant l’outil de diagnostic Volet d’accès. 

Pour télécharger et installer l’outil de diagnostic :

1. [Sélectionnez ce lien pour télécharger l’outil de diagnostic.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Ouvrez le fichier et extrayez le contenu sur votre ordinateur.
1. Pour exécuter l’outil, cliquez avec le bouton droit sur le fichier nommé *AccessPanelExtensionDiagnosticTool.js*, puis sélectionnez **Ouvrir avec** > **Microsoft Windows Based Script Host**.

    ![Ouvrir avec > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Passez en revue les résultats de diagnostic qui s’affichent et sélectionnez **Oui** pour résoudre les problèmes. La boîte de dialogue **Résultats du contrôle** s’affiche et indique la marche à suivre si l’extension ne fonctionne pas.  
1. Lisez le message, puis sélectionnez **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Vérifiez que l’extension Volet d’accès est activée.

Pour vérifier que vous avez bien activé l’extension Volet d’accès dans Internet Explorer :

1. Dans Internet Explorer, sélectionnez l’**icône d’engrenage** dans le coin supérieur droit de la fenêtre et sélectionnez **Options Internet**.
1. Accédez à l’onglet **Programmes**, puis sélectionnez **Gérer les modules complémentaires**.
1. Sélectionnez **Access Panel Extension (Extension Volet d’accès)** dans la section **Microsoft Corporation**, puis sélectionnez **Activer**.
1. Pour enregistrer les modifications, fermez toutes les fenêtres du navigateur Internet Explorer que vous avez ouvertes. La modification prend effet à la prochaine ouverture d’Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Activer des extensions pour la navigation InPrivate

Pour activer des extensions pour la navigation InPrivate :

1. Dans Internet Explorer, sélectionnez l’**icône d’engrenage** dans le coin supérieur droit de la fenêtre et sélectionnez **Options Internet**.
1. Accédez à l’onglet **Confidentialité**, puis vérifiez que la case **Désactiver les barres d’outils et les extensions lors du démarrage de la navigation InPrivate** est décochée.
1. Pour enregistrer les modifications, fermez toutes les fenêtres du navigateur Internet Explorer que vous avez ouvertes. La modification prend effet à la prochaine ouverture d’Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Désinstallation de l’extension Volet d’accès

Pour désinstaller l’extension Volet d’accès de votre ordinateur :

1. Dans le Panneau de configuration, recherchez *Désinstaller*.
1. Dans les résultats de recherche, sélectionnez **Désinstaller un programme**.

    ![Sélectionner l’option désinstaller un programme dans le Panneau de configuration](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Dans la liste, sélectionnez **Access Panel Extension (Extension Volet d’accès)** , puis cliquez sur le bouton **Désinstaller**.

    ![Désinstallation de l’extension Volet d’accès](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Essayez ensuite de réinstaller l’extension pour vérifier que le problème a été résolu.

Si vous rencontrez des problèmes pour désinstaller l’extension, vous pouvez également la supprimer à l’aide de l’outil [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673).

## <a name="related-articles"></a>Articles connexes

* [Accès aux applications et authentification unique avec Azure Active Directory](what-is-single-sign-on.md)
* [Déploiement de l’extension Volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](deploy-access-panel-browser-extension.md)
