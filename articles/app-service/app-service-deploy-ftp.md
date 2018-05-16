---
title: Déployer votre application dans Azure App Service avec FTP/S | Microsoft Docs
description: Découvrez comment déployer votre application dans Azure App Service avec FTP ou FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Déployer votre application dans Azure App Service avec FTP/S

Cet article vous explique comment utiliser FTP ou FTPS pour déployer votre application web, votre backend d’applications mobiles ou votre application API dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Le point de terminaison FTP/S de votre application est déjà actif. Aucune configuration n’est nécessaire pour activer le déploiement FTP/S.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Étape 1 : définir les informations d’identification du déploiement

Pour accéder au serveur FTP de votre application, vous devez disposer des informations d’identification du déploiement. 

Pour définir ou réinitialiser vos informations d’identification du déploiement, consultez [Informations d’identification du déploiement d’Azure App Service](app-service-deployment-credentials.md). Ce didacticiel illustre l’utilisation des informations d’identification de niveau utilisateur.

## <a name="step-2-get-ftp-connection-information"></a>Étape 2 : obtenir les informations de connexion FTP

1. Dans le [portail Azure](https://portal.azure.com), accédez à la [page de ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de votre application.
2. Sélectionnez **Vue d’ensemble** dans la navigation de gauche, puis notez les valeurs **Utilisateur FTP/de déploiement**, **Nom de l’hôte FTP** et **Nom de l’hôte FTPS**. 

    ![Informations de connexion FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Pour fournir le contexte approprié au serveur FTP, la valeur **Utilisateur FTP/de déploiement** affichée par le portail Azure inclut le nom de l’application.
    > Vous pouvez obtenir les mêmes informations en sélectionnant **Propriétés** dans la navigation de gauche. 
    >
    > En outre, le mot de passe du déploiement n’est jamais affiché. Si vous oubliez le mot de passe de votre déploiement, revenez à l’[étape 1](#step1) et réinitialisez-le.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Étape 3 : déployer les fichiers sur Azure

1. À partir de votre client FTP (par exemple, [Visual Studio](https://www.visualstudio.com/vs/community/) ou [FileZilla](https://filezilla-project.org/download.php?type=client)), utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
3. Copiez vos fichiers et la structure de répertoire qui leur correspond dans le répertoire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou dans le répertoire **/site/wwwroot/App_Data/Jobs/** pour WebJobs).
4. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement. 

> [!NOTE] 
> Contrairement aux [déploiements Git](app-service-deploy-local-git.md), un déploiement FTP ne prend pas en charge les automatisations de déploiement suivantes : 
>
> - restauration de dépendances (par exemple, des automatisations NuGet, NPM, PIP et Composer)
> - compilation de fichiers binaires .NET
> - génération du fichier web.config (dont voici un [exemple Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Générez ces fichiers requis manuellement sur votre ordinateur local, puis déployez-les avec votre application.
>
>

## <a name="enforce-ftps"></a>Appliquer le protocole FTPS

Pour une sécurité améliorée, vous devez autoriser FTP sur SSL uniquement. Vous pouvez également désactiver FTP et FTPS si vous n’utilisez pas de déploiement FTP.

Dans la page des ressources de votre application dans le [portail Azure](https://portal.azure.com), sélectionnez **Paramètres de l’application** dans la navigation de gauche.

Pour désactiver le protocole FTP non chiffré, sélectionnez **FTPS Only** (FTPS uniquement). Pour désactiver complètement les protocoles FTP et FTPS, sélectionnez **Désactiver**. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

![Désactiver le protocole FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>Résoudre les problèmes d’un déploiement FTP

- [Comment puis-je résoudre les problèmes d’un déploiement FTP ?](#how-can-i-troubleshoot-ftp-deployment)
- [Je ne peux pas envoyer par FTP et publier mon code. Comment puis-je résoudre le problème ?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Comment puis-je me connecter à FTP dans Azure App Service via le mode passif ?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Comment puis-je résoudre les problèmes d’un déploiement FTP ?

La première étape de résolution des problèmes d’un déploiement FTP consiste à isoler un problème de déploiement d’un problème d’application à l’exécution.

Un problème de déploiement se produit généralement si aucun fichier ou des fichiers incorrects sont déployés sur votre application. Il peut être résolu en examinant votre déploiement FTP ou en sélectionnant un autre chemin d’accès de déploiement (comme un contrôle de code source).

Un problème d’application à l’exécution se produit généralement si l’ensemble de fichiers approprié est déployé sur votre application, mais que le comportement de l’application est incorrect. Il peut être résolu en se concentrant sur le comportement du code lors de l’exécution et en examinant des chemins d’accès d’échec spécifiques.

Pour déterminer un problème de déploiement ou d’exécution, consultez [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problèmes de déploiement et d’exécution).

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Je ne peux pas envoyer par FTP et publier mon code. Comment puis-je résoudre le problème ?
Vérifiez que vous avez entré le nom d’hôte et les [informations d’identification](#step-1--set-deployment-credentials) corrects. Vérifiez également que les ports FTP suivants sur votre machine ne sont pas bloqués par un pare-feu :

- Port de connexion de contrôle FTP : 21
- Port de connexion de contrôle FTP : 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Comment puis-je me connecter à FTP dans Azure App Service via le mode passif ?
Azure App Service prend en charge la connexion via les modes actif et passif. Le mode passif est recommandé, car vos machines de déploiement se trouvent généralement derrière un pare-feu (dans le système d’exploitation ou faisant partie d’un réseau domestique ou d’entreprise). Consultez un [exemple dans la documentation de WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Étapes suivantes

Pour des scénarios de déploiement plus avancés, consultez [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md). Le déploiement GIT vers Azure autorise le contrôle de version, la restauration du package, MSBuild et bien plus encore.

## <a name="more-resources"></a>Autres ressources

* [Informations d’identification du déploiement d’Azure App Service](app-service-deploy-ftp.md)
