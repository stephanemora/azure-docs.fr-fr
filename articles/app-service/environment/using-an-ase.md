---
title: Utiliser et gérer un App Service Environment
description: Apprenez à créer, publier et mettre à l’échelle des applications dans un environnement Azure App Service. Trouvez les tâches courantes dans un document.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565646"
---
# <a name="use-an-app-service-environment"></a>Utilisation d’un environnement App Service #

L’App Service Environment (ASE) est un déploiement d’Azure App Service dans un sous-réseau du réseau virtuel Azure d’un client. Un ASE se compose des éléments suivants :

- **Serveurs frontaux** : c’est aux serveurs frontaux qu’aboutissent les protocoles HTTP/HTTPS dans un environnement App Service (ASE).
- **Workers** : il s’agit des ressources qui hébergent vos applications.
- **Base de données** : celle-ci contient des informations qui définissent l’environnement.
- **Stockage** : le stockage est utilisé pour héberger les applications publiées client.

Vous pouvez déployer un ASE avec une adresse IP virtuelle externe ou interne pour l’accès des applications. Le déploiement avec une adresse IP virtuelle externe est communément appelé ASE externe. La version interne est appelée ASE d’équilibrage de charge interne, car elle utilise un équilibreur de charge interne (ILB). Pour plus d’informations sur l'environnement ASE d'équilibrage de charge interne, consultez [Créer et utiliser un équilibreur de charge interne avec un environnement App Service][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Créer une application dans un ASE ##

Pour créer une application dans un environnement ASE, vous utilisez le même processus que lorsque vous la créez normalement, mais avec quelques petites différences. Lorsque vous créez un plan App Service :

- Au lieu de choisir un emplacement géographique dans lequel déployer votre application, vous choisissez un environnement ASE comme emplacement.
- Tous les plans App Service créés dans ASE doivent être dans un niveau tarifaire Isolé.

Si vous n'avez pas d'environnement ASE, vous pouvez en créer un en suivant les instructions de l'article [Créer un environnement App Service][MakeExternalASE].

Pour créer une application dans un ASE :

1. Sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

2. Entrez un nom pour l’application. Si vous avez déjà sélectionné un plan App Service dans un environnement ASE, le nom de domaine pour l’application reflète le nom de domaine de l’environnement ASE.

    ![Sélection du nom de l’application][1]

1. Sélectionnez un abonnement.

1. Entrez un nom pour un nouveau groupe de ressources ou sélectionnez **Use existing** (Utiliser existant) et choisissez-en un dans la liste déroulante.

1. Sélectionnez votre OS. 

1. Sélectionnez un plan App Service existant dans votre environnement ASE ou créez-en un en procédant comme suit :

    a. Dans le menu de gauche du portail Azure, sélectionnez **Créer une ressource > Application web**.

    b. Sélectionnez l’abonnement.
    
    c. Sélectionnez ou créez le groupe de ressources.
    
    d. Indiquez le nom de votre application web.
    
    e. Sélectionnez Code ou DockerContainer.
    
    f. Sélectionnez une Pile d’exécution.
    
    g. Sélectionnez Linux ou Windows. 
    
    h. Sélectionnez votre ASE dans la liste déroulante **Région**. 
    
    i. Sélectionnez ou créez un plan App Service. Si vous créez un plan de App Service, sélectionnez la taille de référence SKU **Isolée** appropriée.
    
    ![Niveaux tarifaires isolés][2]

    > [!NOTE]
    > Les applications Linux et les applications Windows ne peuvent pas se trouver dans le même plan App Service, mais elles peuvent être dans le même environnement ASE. 
    >

2. Sélectionner **Vérifier + créer**, puis **Créer** si les informations sont correctes.

## <a name="how-scale-works"></a>Fonctionnement de la mise à l’échelle ##

Chaque application App Service s’exécute dans un plan App Service. Les environnements App Service contiennent les plans App Service, et les plans App Service contiennent les applications. Lorsque vous mettez à l’échelle une application, vous mettez à l’échelle le plan App Service, et donc toutes les applications contenues dans le même plan.

Lorsque vous mettez à l’échelle un plan App Service, l’infrastructure nécessaire est automatiquement ajoutée. Il existe un délai pour les opérations de mise à l’échelle lorsque l’infrastructure est ajoutée. Si vous effectuez plusieurs opérations de mise à l’échelle de façon séquentielle, la première demande de mise à l’échelle de l’infrastructure est traitée et les autres sont mises en file d’attente. Une fois la première opération de mise à l’échelle terminée, les autres demandes d’infrastructure opèrent ensemble. Une fois l’infrastructure ajoutée, les plans App Service sont affectés en fonction des besoins. La création d’un plan App Service est elle-même une opération de mise à l’échelle car elle demande du matériel supplémentaire. 

Dans l’App Service mutualisé, la mise à l’échelle est immédiate, car il existe un pool de ressources instantanément disponible pour la prendre en charge. Dans un environnement ASE, il n’existe pas de mémoire tampon et les ressources peuvent être allouées en fonction des besoins.

Dans un ASE, vous pouvez mettre à l’échelle un plan App Service jusqu’à 100 instances. Un ASE peut avoir jusqu’à 201 instances dans l’ensemble des plans App Service qu’il comprend. 

## <a name="ip-addresses"></a>Adresses IP ##

App Service a la possibilité d’allouer une adresse IP dédiée à une application. Cette capacité à allouer une adresse IP dédiée à une application est disponible après la configuration d’un protocole SSL basé sur IP, comme expliqué dans [Lier un certificat SSL personnalisé existant à Azure App Service][ConfigureSSL]. Dans un ASE d’équilibrage de charge interne (ILB), vous ne pouvez pas ajouter d’adresses IP à utiliser pour un protocole SSL basé sur IP.

Avec un ASE externe, vous pouvez configurer le protocole SSL basé sur IP pour votre application de la même manière que dans l’App Service mutualisé. Il existe toujours une adresse de secours dans l’ASE, dans la limite de 30 adresses IP. Chaque fois que vous en utilisez une, une autre est ajoutée afin qu’une adresse soit toujours disponible si besoin. Un délai est nécessaire pour allouer une autre adresse IP, ce qui empêche d’ajouter des adresses IP de façon rapprochée.

## <a name="front-end-scaling"></a>Mise à l’échelle du serveur frontal ##

Lorsque vous montez en charge vos plans App Service, les workers sont automatiquement ajoutés pour les prendre en charge. Chaque ASE est créé avec deux serveurs frontaux. Les serveurs frontaux montent en charge automatiquement à raison d’un serveur frontal pour chaque total de 15 instances du plan App Service. Si vous avez trois plans App Service comprenant cinq instances chacun, vous avez un total de 15 instances et de trois serveurs frontaux. Si vous effectuez une mise à l’échelle pour atteindre un total de 30 instances, vous avez quatre serveurs frontaux, et ainsi de suite. 

Le nombre de serveurs frontaux alloués par défaut est correct pour une charge modérée. Vous pouvez modifier le ratio en le réduisant à un serveur frontal pour cinq instances. Vous pouvez également modifier la taille des serveurs frontaux. Par défaut, ils n’ont qu’un seul cœur. Vous pouvez modifier la taille des serveurs frontaux dans le portail en choisissant des tailles de deux ou quatre cœurs à la place. La modification du ratio ou des tailles de serveurs frontaux occasionne des frais. Pour en savoir plus, consultez [Tarification d'Azure App Service][Pricing]. Si vous cherchez à améliorer la capacité de chargement de votre ASE, vous obtiendrez davantage d’amélioration en commençant par opérer une mise à l’échelle vers des serveurs frontaux à deux cœurs avant d’ajuster le ratio de mise à l’échelle. La modification de la taille en cœurs de vos serveurs frontaux entraîne une mise à niveau de votre ASE et doit être effectuée en dehors des heures de bureau normales.

Les ressources des serveurs frontaux sont le point de terminaison HTTP/HTTPS pour l’ASE. Avec la configuration de serveur frontal par défaut, l’utilisation de mémoire par serveur se situe en permanence autour de 60 %. La raison principale pour mettre à l’échelle vos serveurs frontaux est l’UC qui est principalement déterminée par le trafic HTTP.

## <a name="app-access"></a>Accès de l’application ##

Dans un ASE externe, le suffixe de domaine utilisé pour la création d’application est *.&lt;asename&gt;.p.azurewebsites.net*. Si votre ASE s’appelle _external-ase_ et que vous y hébergez une application appelée _contoso_, vous l’atteignez aux URL suivantes :

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Pour plus d’informations sur la création d’un ASE externe, voir [Créer un environnement App Service][MakeExternalASE].

Dans un ASE ILB, le suffixe de domaine utilisé pour la création d’application est *.&lt;asename&gt;.appserviceenvironment.net*. Si votre ASE s’appelle _ilb-ase_ et que vous y hébergez une application appelée _contoso_, vous l’atteignez aux URL suivantes :

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Pour plus d'informations sur la création d'un environnement ASE d'équilibrage de charge interne, consultez [Créer et utiliser un environnement ASE d'équilibrage de charge interne][MakeILBASE]. 

L’URL scm est utilisée pour accéder à la console Kudu ou pour publier votre application à l’aide de Web Deploy. Pour plus d'informations sur la console Kudu, consultez [Console Kudu pour Azure App Service][Kudu]. La console Kudu offre une interface utilisateur web pour le débogage, le chargement et la modification des fichiers, etc.

## <a name="publishing"></a>Publication ##

Tout comme vous le feriez avec l’App Service multilocataire, dans un environnement ASE, vous pouvez publier avec :

- Déploiement Web.
- FTP.
- Intégration continue.
- Opération de glisser-déplacer dans la console Kudu.
- Un IDE tel que Visual Studio, Eclipse ou Intellij IDEA.

Avec un environnement ASE externe, toutes ces options de publication ont le même comportement. Pour plus d'informations, consultez [Déploiement dans Azure App Service][AppDeploy]. 

La principale différence avec la publication concerne un environnement ASE d’équilibrage de charge interne. Avec un environnement ASE d’équilibrage de charge interne, les points de terminaison de publication sont tous disponibles uniquement via l’équilibrage de charge interne. L’équilibrage de charge interne se trouve sur une adresse IP privée dans le sous-réseau de l’environnement ASE du réseau virtuel. Si vous n’avez pas d’accès réseau à l’équilibrage de charge interne, vous ne pouvez pas publier d’applications dans cet environnement ASE. Comme indiqué dans [Créer et utiliser un environnement ASE d'équilibrage de charge interne][MakeILBASE], vous devez configurer le DNS pour les applications du système. Cela inclut le point de terminaison GCL. S’ils ne sont pas définis correctement, vous ne pouvez pas publier. Vos IDE doivent également avoir un accès réseau à l’équilibreur de charge interne pour publier directement dans celui-ci.

À la base, les systèmes d’intégration continue sur Internet, comme GitHub et Azure DevOps, ne fonctionnent pas avec un environnement ASE d’équilibreur de charge interne, car le point de terminaison de publication n’est pas accessible via Internet. Vous pouvez activer la publication sur un ASE ILB à partir d’Azure DevOps en installant un agent de mise en production auto-hébergé dans le réseau virtuel contenant l’ASE ILB. Vous pouvez aussi utiliser un système d’intégration continue qui s’appuie sur un modèle d’extraction, comme Dropbox.

Les points de terminaison de publication pour les applications d’un environnement ASE d’équilibreur de charge interne utilisent le domaine avec lequel l’environnement ASE d’équilibreur de charge interne a été créé. Vous pouvez le voir dans le profil de publication de l’application et dans le panneau du portail de l’application (dans **Vue d’ensemble** > **Bases** et également dans **Propriétés**). 

## <a name="storage"></a>Stockage

Un ASE dispose de 1 To de stockage pour toutes les applications qu’il contient. Par défaut, un plan App Service de référence SKU Isolée a une limite de 250 Go. Si vous avez au moins cinq plans App Service, vous devez veiller à ne pas dépasser la limite de 1 To de l’ASE. Si vous avez besoin de plus que la limite de 250 Go dans un plan App Service, contactez le support technique pour fixer la limite du plan App Service à un maximum de 1 To. Lorsque la limite du plan est ajustée, il existe toujours une limite de 1 To pour tous les plans App Service dans l’ASE. 

## <a name="logging"></a>Journalisation ##

Vous pouvez intégrer votre ASE avec Azure Monitor pour envoyer des journaux sur l’ASE à Storage, Event Hub ou Log Analytics. Les éléments journalisés aujourd’hui sont les suivants :

| Situation | Message |
|---------|----------|
| L’ASE est non sain | L’ASE spécifié n’est pas sain en raison d’une configuration de réseau virtuel non valide. L’ASE est suspendu si l’état non sain perdure. Vérifiez que les instructions définies ici sont respectées : https://docs.microsoft.com/azure/app-service/environment/network-info |
| Le sous-réseau de l’ASE n’a presque plus d’espace disponible | L’ASE spécifié se trouve dans un sous-réseau qui n’a presque plus d’espace disponible. Il y a {0} adresses restantes. Une fois ces adresses épuisées, l’ASE ne peut pas adapter son échelle  |
| L’ASE approche la limite d’instance totale | L’ASE spécifié approche la limite d’instance totale de l’ASE. Il contient actuellement {0} instances du plan App Service pour un maximum de 201 instances. |
| L’ASE ne peut pas atteindre une dépendance | L’ASE spécifié ne parvient pas à atteindre {0}.  Vérifiez que les instructions définies ici sont respectées : https://docs.microsoft.com/azure/app-service/environment/network-info |
| L’ASE est suspendu | L’ASE spécifié est suspendu. La suspension de l’ASE peut être due à un manque de compte ou à une configuration de réseau virtuel non valide. Résolvez la cause racine et reprenez l’ASE pour continuer à traiter le trafic |
| Une mise à niveau de l’ASE a commencé | Une mise à niveau de plateforme vers l’ASE spécifié a commencé. Attendez-vous à des retards dans les opérations de mise à l’échelle |
| Une mise à niveau de l’ASE est terminée | Une mise à niveau de plateforme pour l’ASE spécifié s’est terminée. |
| Des opérations de mise à l’échelle ont commencé | Une mise à l’échelle d’un plan App Service ({0}) a commencé. État souhaité : {1} I{2} workers 
| Les opérations de mise à l’échelle sont terminées | Une mise à l’échelle d’un plan App Service ({0}) est terminée. État actuel : {1} I{2} workers |
| Des opérations de mise à l’échelle ont échoué | Une mise à l’échelle d’un plan App Service ({0}) a échoué. État actuel : {1} I{2} workers |

Pour activer la journalisation sur votre ASE : 

1. Accédez aux paramètres de diagnostic dans le portail.  
1. Sélectionnez Ajouter un paramètre de diagnostic.
1. Fournir un nom pour l’intégration du journal
1. Vérifiez et configurez les destinations de journal souhaitées. 
1. Vérifier AppServiceEnvironmentPlatformLogs

![Paramètres du journal de diagnostic d’ASE][4]

Si vous effectuez une intégration avec Log Analytics, vous pouvez voir les journaux en sélectionnant Journaux dans le portail ASE et en créant une requête sur AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Préférence de mise à niveau ##

Si vous avez plusieurs ASE, vous pouvez en mettre certains à niveau avant d’autres. Dans l’objet du Gestionnaire des ressources HostingEnvironment d’ASE, vous pouvez définir une valeur pour UpgradePreference. Le paramètre upgradePreference peut être configuré via le modèle ARMClient ou https://resources.azure.com.  Les trois valeurs possibles sont les suivantes :

* Aucune : valeur par défaut qui signifie qu’Azure ne mettra à niveau votre ASE dans aucun lot particulier
* Tôt : signifie que votre ASE sera mis à niveau au cours de la première moitié des mises à niveau d’App Service
* Tard : signifie que votre ASE sera mis à niveau au cours de la deuxième moitié des mises à niveau d’App Service

Si vous utilisez https://resources.azure.com, vous pouvez définir la valeur upgradePreferences de la façon suivante :

1. En accédant à resources.azure.com et en vous connectant avec votre compte Azure
1. Naviguez dans abonnements\/\[nom d’abonnement\]\/resourceGroups\/\[nom de groupe de ressources\]\/fournisseurs\/Microsoft.Web\/hostingEnvironments\/\[noms de l’ASE\]
1. En sélectionnant Lecture/écriture en haut
1. Sélectionner Modifier
1. Remplacez la valeur de upgradePreference par une valeur souhaitée parmi les trois choix.
1. Sélectionner un correctif

![affichage des ressources azure com][5]

La fonctionnalité upgradePreferences est vraiment utile lorsque vous avez plusieurs ASE, car vos ASE paramétrés sur « Tôt » seront mis à niveau avant ceux paramétrés sur « Tard ». Lorsque vous avez plusieurs ASE, vos ASE de dev/test doivent être paramétrés sur « Tôt » et vos ASE de production sur « Tard ».

## <a name="pricing"></a>Tarifs ##

La référence SKU de tarification appelée **Isolée** est uniquement destinée à être utilisée avec un ASE. Tous les plans App Service hébergés dans l’ASE font partie de la référence SKU de tarification Isolée. Les tarifs du plan App Service Isolé peuvent varier selon la région. 

En plus de vos plans App Service, il existe un tarif fixe pour l’environnement ASE en lui-même. Le forfait ne change pas avec la taille de votre ASE et couvre l’infrastructure ASE au taux de mise à l’échelle par défaut de 1 serveur frontal supplémentaire pour 15 instances du plan App Service.  

Si le taux de mise à l’échelle par défaut de 1 serveur frontal pour 15 instances du plan App Service n’est pas assez rapide, vous pouvez ajuster le ratio auquel des serveurs frontaux sont ajoutés ou la taille des serveurs frontaux.  Lorsque vous ajustez le ratio ou la taille, vous payez pour les cœurs frontaux qui ne seraient pas ajoutés par défaut.  

Par exemple, si vous ajustez le ratio de mise à l’échelle sur 10, un serveur frontal est ajouté toutes les 10 instances dans vos plans App Service. Le prix forfaitaire couvre un taux de mise à l’échelle d’un serveur frontal 15 instances. Avec un ratio de mise à l’échelle de 10, vous payez pour le troisième serveur frontal qui est ajouté pour les 10 instances du plan App Service. Vous n’avez pas besoin de payer lorsque vous atteignez 15 instances, car il a été ajouté automatiquement.

Si vous avez ajusté la taille des frontaux à deux cœurs, mais n’avez pas modifié le ratio, vous payez pour les cœurs supplémentaires.  Un ASE étant créé avec deux serveurs frontaux, même en dessous du seuil de mise à l’échelle automatique, vous payeriez pour deux cœurs supplémentaires si vous avez porté la taille à des serveurs frontaux de deux cœurs.

Pour en savoir plus, consultez [Tarification d'Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Supprimer un environnement ASE ##

Pour supprimer un environnement ASE : 

1. Utilisez **Supprimer** en haut du panneau **Environnement App Service**. 

1. Entrez le nom de votre environnement ASE pour confirmer que vous souhaitez le supprimer. Lorsque vous supprimez un environnement ASE, vous supprimez également l’ensemble de son contenu. 

    ![Suppression de ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
