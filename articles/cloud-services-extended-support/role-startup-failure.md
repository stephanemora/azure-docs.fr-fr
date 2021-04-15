---
title: Échec du démarrage de l’instance de rôle pour Azure Cloud Services (support étendu)
description: Résoudre les problèmes de démarrage de l’instance de rôle pour Azure Cloud Services (support étendu)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382353"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Résoudre les problèmes liés aux rôles Azure Cloud Services (support étendu) qui ne parviennent pas à démarrer
Voici quelques problèmes et solutions fréquemment rencontrés pour les rôles Cloud Services (support étendu) qui ne démarrent pas.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Échec de l’opération de service cloud avec RoleInstanceStartupTimeoutError
Il arrive qu’une ou plusieurs instances de rôle de votre service cloud (support étendu) ne démarrent pas dans le délai imparti. Ces instances de rôle mettent parfois du temps à démarrer ou sont peut-être en cours de recyclage. Un échec d’instance de rôle se produit avec RoleInstanceStartupTimeoutError. Il s’agit d’une erreur d’application de rôle. L’application de rôle contient deux parties principales, les « tâches de démarrage » et le « code de rôle (implémentation de RoleEntryPoint) », qui peuvent entraîner le recyclage du rôle. Si le rôle plante, l’agent PaaS le relance toujours. 

Pour connaître l’état actuel et les détails des instances de rôle en cas d’erreur, utilisez l’une des méthodes suivantes :

* PowerShell : utilisez l’applet de commande [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) pour récupérer les informations sur l’état d’exécution d’une instance de rôle dans un service cloud.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Portail Azure : accédez à votre service cloud et sélectionnez l’onglet Rôles et instances. Cliquez sur l’instance de rôle appropriée pour obtenir des informations sur son état 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Image montrant l’échec du démarrage du rôle dans le portail.":::
   
Voici quelques problèmes et solutions fréquemment rencontrés pour les rôles Azure Cloud Services (support étendu) qui ne démarrent pas ou qui oscillent continuellement entre les états Initialisation, Occupé et Arrêt.

## <a name="missing-dlls-or-dependencies"></a>DLL ou dépendances manquantes
Les rôles qui ne répondent pas ou qui basculent sans cesse entre les états Initialisation, Occupé et Arrêt peuvent être affectés par l’absence de bibliothèques DLL ou d’assemblys.
Symptômes liés à l’absence de bibliothèques DLL ou d’assemblys :

* Votre instance de rôle alterne entre les états **Initialisation**, **Occupé** et **Arrêt**.
* Votre instance de rôle est passée à l’état **Prêt** , mais la page ne s’affiche pas lorsque vous accédez à votre application web.

Plusieurs méthodes sont recommandées pour rechercher une solution à ces problèmes.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostiquer les problèmes de DLL manquante dans un rôle web
Lorsque vous accédez à un site web déployé dans un rôle web et que le navigateur affiche une erreur de serveur semblable à ce qui suit, cela peut indiquer qu’une bibliothèque DLL est manquante.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Image montrant une erreur d’exécution au moment de l’échec de démarrage du rôle":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostiquer les problèmes en désactivant les erreurs personnalisées
Vous pouvez afficher des informations plus complètes sur les erreurs en configurant le fichier web.config du rôle web pour qu’il désactive le mode d’erreur personnalisée et en redéployant le service.
Pour afficher des erreurs plus détaillées sans utiliser le Bureau à distance :
1.  Ouvrez la solution dans Microsoft Visual Studio.
2.  Dans l'Explorateur de solutions, recherchez le fichier web.config et ouvrez-le.
3.  Dans le fichier web.config, recherchez la section system.web et ajoutez la ligne suivante :
 ```xml
<customErrors mode="Off" />
```
4.  Enregistrez le fichier .
5.  Recréez le package et redéployez le service.
Une fois le service redéployé, un message d’erreur s’affiche avec le nom de la DLL ou de l’assembly manquant.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostiquer les problèmes en affichant l’erreur à distance
Vous pouvez utiliser le Bureau à distance pour accéder au rôle et afficher des informations plus complètes sur l’erreur à distance. Pour afficher les erreurs à l’aide du Bureau à distance, procédez comme suit :
1.  Activez l’extension de Bureau à distance pour le service cloud (support étendu). Pour plus d’informations, consultez [Appliquer l’extension de Bureau à distance à Cloud Services (support étendu) à l’aide du portail Azure](enable-rdp.md)
2.  Dans le portail Azure, une fois que l’instance affiche l’état Prêt, accédez à distance à l’instance. Pour plus d’informations sur l’utilisation du Bureau à distance avec Cloud Services (support étendu), consultez [Se connecter à des instances de rôle avec le Bureau à distance](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Connectez-vous à la machine virtuelle à l’aide des informations d’identification spécifiées lors de la configuration du Bureau à distance.
4.  Ouvrez une fenêtre de commandes.
5.  Tapez Ipconfig.
6.  Notez la valeur de l’adresse IPv4.
7.  Ouvrez Internet Explorer.
8.  Entrez l’adresse et le nom de l’application Web. Par exemple http://<IPV4 Address>/default.aspx.
L’accès au site web renvoie maintenant des messages d’erreur plus explicites :
* Erreur de serveur dans l’application « / ».
* Description : une exception non gérée s’est produite lors de l’exécution de la requête Web en cours. Veuillez consulter l’arborescence des appels de procédure pour plus d’informations sur l’erreur et sa source dans le code.
* Détails de l’exception : System.IO.FIleNotFoundException : impossible de charger le fichier ou l’assembly « Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35 » ou l’une de ses dépendances. Le système ne peut pas trouver le fichier spécifié.
Par exemple :

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Image montrant une exception au moment de l’échec de démarrage du rôle":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostiquer les problèmes à l’aide de l’émulateur de calcul
Vous pouvez utiliser l’émulateur Azure Compute pour diagnostiquer et résoudre les problèmes de dépendances manquantes ainsi que les erreurs du fichier web.config.
Pour obtenir de meilleurs résultats à l’aide de cette méthode de diagnostic, vous devez utiliser un ordinateur ou une machine virtuelle disposant d’une nouvelle installation de Windows. 
1.  Installer le [kit SDK Azure](https://azure.microsoft.com/downloads/) 
2.  Sur l’ordinateur de développement, générez le projet de service cloud.
3.  Dans l’Explorateur Windows, accédez au dossier bin\debug du projet de service cloud.
4.  Copiez le dossier .csx et le fichier .cscfg sur l’ordinateur que vous utilisez pour déboguer les problèmes.
5.  Sur la machine propre, ouvrez une fenêtre d’invite de commandes du kit SDK Azure, puis tapez csrun.exe /devstore:start.
6.  À l’invite de commandes, tapez run csrun <chemin_du_dossier_.csx> <chemin_du_fichier_.cscfg> /launchBrowser.
7.  Au démarrage du rôle, le détail de l’erreur s’affiche dans Internet Explorer. Vous pouvez également utiliser les outils de dépannage Windows standard pour diagnostiquer le problème.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostiquer les problèmes à l’aide d’IntelliTrace
Pour les rôles de travail et les rôles web qui utilisent .NET Framework 4, vous pouvez utiliser l’utilitaire [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), qui est disponible dans Microsoft Visual Studio Enterprise.
Pour déployer le service avec la fonction IntelliTrace activée, procédez comme suit :
1.  Vérifiez que le Kit de développement logiciel (SDK) Azure 1.3 ou ultérieur est installé.
2.  Déployez la solution à l’aide de Visual Studio. Au cours du déploiement, cochez la case Activer IntelliTrace pour les rôles .NET 4 .
3.  Une fois l'instance démarrée, ouvrez l'Explorateur de serveurs.
4.  Développez le nœud Azure\Cloud Services, puis recherchez le déploiement.
5.  Développez le déploiement jusqu’à ce que les instances de rôle s’affichent. Cliquez avec le bouton droit sur l’une des instances.
6.  Choisissez Afficher les fichiers journaux IntelliTrace. La page Résumé IntelliTrace s'ouvre.
7.  Recherchez la section du résumé relative aux exceptions. S’il existe des exceptions, la section s’intitule Données d’exception.
8.  Développez Données d'exception et recherchez les erreurs System.IO.FileNotFoundException semblables à ce qui suit :

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Image montrant les données d’exception au moment de l’échec de démarrage du rôle" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Résoudre les problèmes de DLL et d’assemblys manquants
Pour résoudre les erreurs liées à des DLL et des assemblies manquants, procédez comme suit :
1.  Ouvrez la solution dans Visual Studio.
2.  Dans l’Explorateur de solutions, ouvrez le dossier Références.
3.  Cliquez sur l’assembly identifié dans l’erreur.
4.  Dans le volet Propriétés, recherchez la propriété Copy Local et définissez sa valeur sur True.
5.  Redéployez le service cloud.
Après avoir vérifié que toutes les erreurs ont été corrigées, vous pouvez déployer le service sans cocher la case Activer IntelliTrace pour les rôles .NET 4 .

## <a name="next-steps"></a>Étapes suivantes 
- Pour découvrir comment résoudre les problèmes liés aux rôles de service cloud à l’aide des données de diagnostic de calcul PaaS Azure, consultez la [série de blogs de Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
