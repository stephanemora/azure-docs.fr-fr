---
title: Échec du démarrage de l’instance de rôle dans Azure Cloud Services (support étendu)
description: Résoudre les problèmes de démarrage de l’instance de rôle dans Azure Cloud Services (support étendu)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 9c6fdee8dbb28e86e5084c9c0cfca97f5ac5ea05
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287155"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Résoudre les problèmes liés aux rôles Azure Cloud Services (support étendu) qui ne parviennent pas à démarrer

Voici quelques problèmes fréquemment rencontrés et solutions pour les rôles Azure Cloud Services (support étendu) qui ne démarrent pas.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Échec de l’opération de service cloud avec RoleInstanceStartupTimeoutError

Une ou plusieurs de vos instances de rôle dans Azure Cloud Services (support étendu) peuvent être lentes à démarrer, ou elles peuvent être en cours de recyclage et l’instance de rôle échoue. L’erreur d’application de rôle `RoleInstanceStartupTimeoutError` s’affiche.

L’application de rôle contient deux parties qui peuvent entraîner le recyclage de rôle : les *tâches de démarrage* et le *code de rôle (implémentation de RoleEntryPoint)* . 

Si le rôle s’arrête, l’agent PaaS (Platform as a Service) redémarre le rôle.

Vous pouvez obtenir l’état actuel et les détails d’une instance de rôle afin de diagnostiquer les erreurs à l’aide de PowerShell ou du portail Azure :

* **PowerShell** : utilisez l’applet de commande [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) pour obtenir les informations sur l’état d’exécution de l’instance de rôle :

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Portail Azure** : dans le portail, accédez à l’instance de service cloud. Pour afficher les détails sur l’état, sélectionnez **Rôles et instances**, puis sélectionnez l’instance de rôle.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Capture d’écran montrant un échec de démarrage de rôle dans le portail Azure.":::

## <a name="missing-dlls-or-dependencies"></a>DLL ou dépendances manquantes

Les rôles qui ne répondent pas ou qui basculent sans cesse entre états peuvent être affectés par l’absence de bibliothèques DLL ou d’assemblys.

Voici quelques symptômes liés à l’absence de bibliothèques DLL ou d’assemblys :

* Votre instance de rôle alterne entre les états **Initialisation**, **Occupé** et **Arrêt**.
* Votre instance de rôle a basculé à l’état **Prêt**, mais la page n’est pas visible lorsque vous accédez à votre application web.


Un site web qui est déployé dans un rôle web et pour lequel il manque une DLL peut afficher cette erreur d’exécution du serveur :

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Capture d’écran montrant une erreur d’exécution après un échec de démarrage de rôle.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Résoudre les problèmes de DLL et d’assemblys manquants

Pour résoudre les erreurs de DLL et d’assemblys manquants

1. Dans Visual Studio, ouvrez la solution.
2. Dans l’Explorateur de solutions, ouvrez le dossier *Références*.
3. Sélectionnez l’assembly identifié dans l’erreur.
4. Dans **Propriétés**, affectez la valeur **True** à la propriété **Copie locale**.
5. Redéployez le service cloud.

Après avoir vérifié que les erreurs n’apparaissent plus, redéployez le service. Quand vous configurez le déploiement, ne cochez pas la case **Activer IntelliTrace pour les rôles .NET 4**.

## <a name="diagnose-role-instance-errors"></a>Diagnostiquer les erreurs d’instance de rôle

Choisissez parmi les options suivantes pour diagnostiquer les problèmes liés aux instances de rôle.

### <a name="turn-off-custom-errors"></a>Désactiver les erreurs personnalisées

Pour afficher des informations complètes sur l’erreur, dans le fichier *web.config* du rôle web, affectez la valeur `off` au mode d’erreur personnalisé, puis redéployez le service :

1. Dans Visual Studio, ouvrez la solution.
2. Dans l’Explorateur de solutions, ouvrez le fichier *web.config*.
3. Dans la section `system.web`, ajoutez le code suivant :

   ```xml
   <customErrors mode="Off" />
   ```

4. Enregistrez le fichier .
5. Recréez le package et redéployez le service.

Une fois le service redéployé, un message d’erreur mentionne le nom des assemblys ou DLL manquants.

### <a name="use-remote-desktop"></a>Utiliser le Bureau à distance

Utilisez Bureau à distance pour accéder au rôle et afficher des informations complètes sur l’erreur :

1. [Ajoutez l’extension Bureau à distance à Azure Cloud Services (support étendu)](enable-rdp.md).
2. Dans le portail Azure, lorsque l’instance de service cloud affiche un état **Prêt**, utilisez Bureau à distance pour vous connecter au service cloud. Pour plus d’informations, consultez [Se connecter à des instances de rôle avec Bureau à distance activé](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Connectez-vous à la machine virtuelle à l’aide des informations d’identification que vous avez utilisées pour configurer Bureau à distance.
4. Ouvrez une fenêtre d’invite de commandes.
5. À l’invite de commandes, tapez **ipconfig**. Notez la valeur retournée pour l’adresse IPv4.
6. Ouvrez Microsoft Internet Explorer.
7. Dans la barre d’adresse, entrez l’adresse IPv4 suivie d’une barre oblique et du nom du fichier par défaut de l’application web. Par exemple : `http://<IPv4 address>/default.aspx`.

Si vous accédez au site web maintenant, vous verrez des messages d’erreur contenant plus d’informations. Voici un exemple :

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Capture d’écran présentant un exemple de message d’erreur.":::
  
### <a name="use-the-compute-emulator"></a>Utiliser l’émulateur de calcul

Vous pouvez utiliser l’émulateur de calcul Azure pour diagnostiquer et résoudre les problèmes liés aux dépendances manquantes ainsi que les erreurs du fichier *web.config*. Lorsque vous appliquez cette méthode pour diagnostiquer les problèmes, utilisez un ordinateur ou une machine virtuelle disposant d’une nouvelle installation de Windows.

Pour diagnostiquer les problèmes à l’aide de l’émulateur de calcul Azure

1. Installez le [kit SDK Azure](https://azure.microsoft.com/downloads/).
2. Sur l’ordinateur de développement, générez le projet de service cloud.
3. Dans l’Explorateur de fichiers, dans le projet de service cloud, accédez au dossier *bin\debug*.
4. Copiez le dossier *.csx* et le fichier *.cscfg* sur l’ordinateur que vous utilisez pour déboguer les problèmes.
5. Sur le nouvel ordinateur, ouvrez une fenêtre d’invite de commandes du SDK Azure.
6. À l’invite de commandes, entrez **csrun.exe /devstore:start**.
7. Ensuite, entrez **run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**.
8. Lorsque le rôle démarre, Internet Explorer affiche des informations détaillées sur l’erreur.

Si vous avez besoin de diagnostics supplémentaires, vous pouvez utiliser les outils de résolution des problèmes Windows standard.

### <a name="use-intellitrace"></a>Utiliser IntelliTrace

Pour les rôles de travail et les rôles Web qui utilisent .NET Framework 4, vous pouvez utiliser [IntelliTrace](/visualstudio/debugger/intellitrace). IntelliTrace est disponible dans Visual Studio Enterprise.

Pour déployer votre service cloud avec IntelliTrace activé

1. Vérifiez que le Kit de développement logiciel (SDK) Azure 1.3 ou ultérieur est installé.
2. Dans Visual Studio, déployez la solution. Quand vous configurez le déploiement, cochez la case **Activer IntelliTrace pour les rôles .NET 4**.
3. Une fois l’instance de rôle démarrée, ouvrez l’Explorateur de serveurs.
4. Développez le nœud **Azure\Services cloud**.
5. Développez le déploiement pour lister les instances de rôle. Cliquez avec le bouton droit sur une instance de rôle.
6. Sélectionnez **Afficher les fichiers journaux IntelliTrace**.
7. Dans **Résumé IntelliTrace**, accédez à  **Données d’exception**.
8. Développez **Données d’exception** et recherchez une erreur `System.IO.FileNotFoundException` :

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Capture d’écran des données d’exception pour un échec de démarrage de rôle." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [résoudre les problèmes liés aux rôles de service cloud à l’aide des données de diagnostic de calcul PaaS Azure](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).