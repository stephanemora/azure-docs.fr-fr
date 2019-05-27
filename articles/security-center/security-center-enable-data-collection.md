---
title: Collecte de données dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer la collecte des données dans Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 8fed3ce98b23c5ac1cc97b88a278c5946f06af8e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968727"
---
# <a name="data-collection-in-azure-security-center"></a>Collecte de données dans Azure Security Center
Security Center collecte les données à partir de vos machines virtuelles (VM) Azure, les machines virtuelles identiques, les conteneurs de IaaS et les ordinateurs non Azure (notamment en local) pour surveiller les menaces et vulnérabilités de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Il peut s’agir des données suivantes : type et version de système d’exploitation, journaux d’activité de système d’exploitation (journaux d’événements Windows), processus en cours d’exécution, nom de machine, adresses IP et utilisateur connecté. L’agent Microsoft Monitoring Agent copie également les fichiers de vidage sur incident dans votre espace de travail.

La collecte de données est requise pour fournir une visibilité des mises à jour manquantes, des paramètres de sécurité du système d’exploitation mal configurés, de l’activation du point de terminaison ainsi que de l’intégrité et des détections de menaces. 

Cet article fournit des conseils sur la façon d’installer un agent Microsoft Monitoring Agent et de définir un espace de travail Log Analytics dans lequel stocker les données collectées. Les deux opérations sont nécessaires pour activer la collecte de données. 

> [!NOTE]
> - Collecte de données est nécessaire uniquement pour les ressources de calcul (machines virtuelles, machines virtuelles identiques, les conteneurs de IaaS et ordinateurs autres qu’Azure). Vous pouvez bénéficier d’Azure Security Center même si vous ne configurez pas d’agents ; toutefois, vous disposerez d’une sécurité limitée et les fonctionnalités répertoriées ci-dessus ne sont pas pris en charge.  
> - Pour obtenir la liste des plateformes prises en charge, consultez [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md).
> - La collecte de données pour les groupes de machines virtuelles identiques n’est actuellement pas prise en charge.
> - Stockage des données dans le journal Analytique, que vous utilisiez un espace de travail nouveau ou existant, peut payer des frais supplémentaires pour le stockage de données, consultez la page de tarification pour plus d’informations.

## Activer l’approvisionnement automatique de Microsoft Monitoring Agent <a name="auto-provision-mma"></a>

Pour collecter les données à partir des machines, vous devez avoir installé Microsoft Monitoring Agent.  L’installation de l’agent peut être automatique (recommandé), ou vous pouvez choisir d’installer l’agent manuellement.  

>[!NOTE]
> L’approvisionnement automatique est désactivé par défaut. Pour définir que Security Center installe l’approvisionnement par défaut automatique, mettez-le sur **Activé**.
>

Lorsque l’approvisionnement automatique est activée, Security Center approvisionne Microsoft Monitoring agent sur tout pris en charge les machines virtuelles Azure et toutes celles nouvellement créées. Le provisionnement automatique est fortement recommandé. Toutefois, l’installation manuelle des agents est également disponible. [Découvrez comment installer l’extension Microsoft Monitoring Agent](#manualagent)



Pour activer l’approvisionnement automatique de Microsoft Monitoring Agent :
1. Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
2. Cliquez sur **Modifier les paramètres** dans la colonne Paramètres de l’abonnement souhaité dans la liste.

   ![Sélectionnez un abonnement][7]

3. Dans **Stratégie de sécurité**, sélectionnez **Collecte de données**.
4. Dans **Provisionnement automatique**, sélectionnez **Activé** pour activer le provisionnement automatique.
5. Sélectionnez **Enregistrer**.

   ![Activer l’approvisionnement automatique][1]

>[!NOTE]
> - Pour obtenir des instructions sur l’approvisionnement d’une installation déjà existante, consultez [Approvisionnement automatique en cas d’installation d’un agent préexistant](#preexisting).
> - Pour recevoir des instructions sur l’approvisionnement manuel, consultez [Installer manuellement l’extension de Microsoft Monitoring Agent](#manualagent).
> - Pour recevoir des instructions sur la désactivation de l’approvisionnement automatique, consultez [Désactiver l’approvisionnement automatique](#offprovisioning).
> - Pour obtenir des instructions sur la manière d’intégrer Security Center à l’aide de PowerShell, voir [Automatiser l’intégration d’Azure Security Center à l’aide de PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuration de l’espace de travail
Les données collectées par Security Center sont stockées dans les espaces de travail Log Analytics.  Vous pouvez choisir de stocker les données collectées à partir des machines virtuelles Azure dans des espaces de travail créés par Security Center ou dans un espace de travail existant que vous avez créé. 

La configuration de l’espace de travail est définie par abonnement, et plusieurs abonnements peuvent utiliser le même espace de travail.

### <a name="using-a-workspace-created-by-security-center"></a>Utilisation d’un espace de travail créé par Security Center

Security Center peut créer automatiquement un espace de travail par défaut dans lequel stocker les données. 

Pour sélectionner un espace de travail créé par Security Center :

1. Sous **Configuration de l’espace de travail par défaut**, sélectionnez Utiliser un ou des espaces de travail créés par Security Center.
   ![Sélectionnez le niveau tarifaire][10] 

1. Cliquez sur **Enregistrer**.<br>
    Security Center crée un groupe de ressources et un espace de travail par défaut dans cette zone géographique et connecte l’agent à cet espace de travail. La convention d’affectation de noms pour l’espace de travail et le groupe de ressources est la suivante :<br>
   **Espace de travail : DefaultWorkspace-[ID-abonnement]-[géolocalisation]<br> Groupe de ressources : DefaultResourceGroup-[géolocalisation]**

   Si un abonnement contient des machines virtuelles se trouvant dans plusieurs zones géographiques, Security Center crée plusieurs espaces de travail. Plusieurs espaces de travail sont créés pour tenir à jour les règles de confidentialité des données.
1. Security Center activera automatiquement une solution Security Center sur l’espace de travail, en fonction du niveau de tarification défini pour l’abonnement. 

> [!NOTE]
> Le niveau tarifaire Log Analytics des espaces de travail créés par Security Center n’affecte pas la facturation Security Center. La facturation Security Center est toujours basée sur votre stratégie de sécurité Security Center et sur les solutions installées sur un espace de travail. Pour le niveau Gratuit, Security Center active la solution *SecurityCenterFree* sur l’espace de travail par défaut. Pour le niveau Standard, Security Center active la solution *Security* sur l’espace de travail par défaut.
> Le stockage de données dans Log Analytics peut occasionner des frais supplémentaires de stockage de données. Pour plus d’informations, voir la page de tarification.

Pour plus d’informations sur la tarification, consultez la page de [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Pour plus d’informations sur les comptes d’analytique de journal existants, consultez [clients analytique de journal existants](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Utilisation d’un espace de travail existant

Si vous disposez déjà d’un espace de travail Log Analytics existant, vous souhaiterez peut-être utiliser le même espace de travail.

Pour utiliser votre espace de travail Log Analytics existant, vous devez avoir des autorisations de lecture et d’écriture sur l’espace de travail.

> [!NOTE]
> Les solutions activées sur l’espace de travail existant s’appliqueront aux machines virtuelles Azure qui y sont connectées. Pour les solutions payantes, cela peut entraîner des frais supplémentaires. Pour des raisons de confidentialité de données, assurez-vous que votre espace de travail sélectionné est dans la bonne région géographique.
> Stockage des données dans l’analytique de journal peut payer des frais supplémentaires pour le stockage de données, consultez la page de tarification pour plus d’informations.

Pour sélectionner un espace de travail Log Analytics existant :

1. Sous **Configuration de l’espace de travail par défaut**, sélectionnez **Utiliser un autre espace de travail**.

   ![Sélectionner un espace de travail existant][2]

2. Dans le menu déroulant, sélectionnez un espace de travail pour stocker les données collectées.

   > [!NOTE]
   > Dans le menu déroulant, tous les espaces de travail dans l’ensemble de vos abonnements sont disponibles. Pour plus d’informations, consultez [Sélection de l’espace de travail parmi les abonnements](security-center-enable-data-collection.md#cross-subscription-workspace-selection). Vous devez avoir l'autorisation d'accéder à l’espace de travail.
   >
   >

3. Sélectionnez **Enregistrer**.
4. Après avoir sélectionné **Enregistrer**, vous serez invité à reconfigurer les machines virtuelles surveillées précédemment connectées à un espace de travail par défaut.

   - Sélectionnez **Non** si vous souhaitez que les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles machines virtuelles. Les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles installations d’agent ; machines virtuelles nouvellement détectées qui n’ont pas Microsoft Monitoring Agent installé.
   - Sélectionnez **Oui** si vous souhaitez que les nouveaux paramètres de l’espace de travail s’appliquent à toutes les machines virtuelles. En outre, chaque machine virtuelle connectée à un espace de travail créé par Security Center est reconnectée au nouvel espace de travail cible.

   > [!NOTE]
   > Si vous sélectionnez Oui, vous ne devez pas supprimer les espaces de travail créés par Security Center tant que toutes les machines virtuelles n’ont pas été reconnectées au nouvel espace de travail cible. Cette opération échoue si un espace de travail est supprimé trop tôt.
   >
   >

   - Sélectionnez **Annuler** pour annuler l’opération.

     ![Sélectionner un espace de travail existant][3]

5. Sélectionnez le niveau tarifaire de l’espace de travail que vous souhaitez définir pour l’agent Microsoft Monitoring. <br>Pour utiliser un espace de travail existant, définissez le niveau tarifaire pour l’espace de travail. Une solution Security Center sera installée sur l’espace de travail si elle ne s’y trouve pas déjà.

    a.  Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
     
    b.  Sélectionnez l’espace de travail souhaité où vous avez l’intention de connecter l’agent en cliquant sur **Modifier les paramètres** dans la colonne Paramètres de l’abonnement souhaité dans la liste.
        ![Sélectionnez un espace de travail][8] c. Définir le niveau tarifaire.
        ![Sélectionnez le niveau tarifaire][9] 
   
   >[!NOTE]
   >Si l’espace de travail a déjà une solution **Security** ou **SecurityCenterFree** activée, la tarification sera définie automatiquement. 

## <a name="cross-subscription-workspace-selection"></a>Sélection de l’espace de travail parmi les abonnements
Quand vous sélectionnez un espace de travail dans lequel stocker vos données, tous les espaces de travail dans l’ensemble de vos abonnements sont disponibles. La sélection de l’espace de travail parmi les abonnements vous permet de collecter des données à partir de machines virtuelles exécutées dans différents abonnements, et de les stocker dans l’espace de travail de votre choix. Cette sélection est utile si vous utilisez un espace de travail centralisé de votre organisation et que vous souhaitez l’utiliser pour la collecte de données de sécurité. Pour plus d’informations sur la gestion des espaces de travail, consultez [Gérer l’accès à l’espace de travail](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Niveau de collecte des données
La sélection d’un niveau de collecte de données dans Azure Security Center n’affecte que le stockage d’événements de sécurité dans votre espace de travail Log Analytics. L’agent d’Analytique de journal est toujours collecter et analyser les événements de sécurité requis pour la détection des menaces Azure Security Center, quel que soit le niveau d’événements de sécurité que vous choisissez de stocker dans votre espace de travail Analytique de journal (le cas échéant). Le choix de stocker des événements de sécurité dans votre espace de travail permettra l’investigation, la recherche et l’audit de ces événements dans votre espace de travail. 
> [!NOTE]
> Stockage des données dans l’analytique de journal peut payer des frais supplémentaires pour le stockage de données, consultez la page de tarification pour plus d’informations.
> 
> Vous pouvez choisir la stratégie de filtrage adaptée à vos abonnements et espaces de travail à partir de quatre ensembles d’événements à stocker dans votre espace de travail : 

- **Aucun** : désactiver le stockage d’événements de sécurité. Il s’agit du paramètre par défaut.
- **Minimal** : un plus petit ensemble d’événements pour les clients qui souhaitent réduire le volume d’événements.
- **Commun** : il s’agit d’un ensemble d’événements qui répond aux besoins de la plupart des clients et leur permet d’avoir une piste d’audit complète.
- **Tous les événements** : pour les clients qui souhaitent s’assurer que tous les événements sont stockés.


> [!NOTE]
> Ces ensembles d’événements de sécurité sont disponibles uniquement avec le niveau Standard de Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
Ces ensembles ont été conçus pour des scénarios classiques. Veillez à évaluer celui qui correspond à vos besoins avant de l’implémenter.
>
>

Pour déterminer les événements qui appartiennent aux ensembles d’événements **Commun** et **Minimal**, nous avons travaillé avec les clients et les normes industrielles pour en savoir plus sur la fréquence non filtrée de chaque événement et leur utilisation. Nous avons utilisé les instructions suivantes dans ce processus :

- **Minimal** : vérifiez que cet ensemble couvre uniquement les événements qui peuvent indiquer une violation avérée et des événements importants qui ont un volume très faible. Par exemple, cet ensemble contient une connexion utilisateur ayant réussi et ayant échoué (ID d’événement4624, 4625), mais il ne contient pas de déconnexion, ce qui est important pour l’audit, mais pas pour la détection, et a un volume relativement élevé. La plupart du volume de données de cet ensemble est constituée d’événements de connexion et d’un événement de création de processus (ID d’événement 4688).
- **Commun** : fournissez une piste d’audit utilisateur complète dans cet ensemble. Par exemple, cet ensemble contient les connexions et déconnexions de l’utilisateur (ID d’événement 4634). Nous incluons les actions d’audit, telles que les modifications de groupe de sécurité, les opérations Kerberos du contrôleur de domaine clé et les autres événements recommandés par les organisations du secteur.

Les événements qui ont un volume très faible ont été inclus dans l’ensemble Commun, car la motivation principale à préférer cet ensemble à l’ensemble Tous les événements est de réduire le volume et de ne pas filtrer d’événements spécifiques.

Voici le détail complet des ID d’événement App Locker et de sécurité pour chaque ensemble :

| Couche Données | Indicateurs d’événements collectés |
| --- | --- |
| Minimales | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Courant | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Si vous utilisez l’objet de stratégie de groupe (GPO), il est recommandé d’activer les stratégies d’audit d’événement de création de processus 4688 et le champ *CommandLine* à l’intérieur de l’événement 4688. Pour plus d’informations sur l’événement de création de processus 4688, consultez la [FAQ](security-center-faq.md#what-happens-when-data-collection-is-enabled) de Security Center. Pour plus d’informations sur ces stratégies d’audit, consultez les [recommandations pour la stratégie d’audit](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Pour activer la collecte de données pour les [Contrôles d’application adaptatifs](security-center-adaptive-application.md), Security Center configure une stratégie AppLocker locale en mode Audit pour autoriser toutes les applications. Cela amène AppLocker à générer des événements qui sont ensuite recueillis et exploités par Security Center. Il est important de noter que cette stratégie ne sera configurée sur aucun ordinateur sur lequel une stratégie AppLocker est déjà configurée. 
> - Pour collecter la plateforme de filtrage Windows [ID d’événement 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), vous devez activer [Connexion de la plateforme de filtrage d’audits](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Pour choisir votre stratégie de filtrage :
1. Dans le panneau **Stratégie de sécurité : collecte de données**, sélectionnez votre stratégie de filtrage dans **Événements de sécurité**.
2. Sélectionnez **Enregistrer**.

   ![Choix de la stratégie de filtrage][5]

### Approvisionnement automatique en cas d’installation d’un agent préexistant <a name="preexisting"></a> 

Les cas d’usage suivants spécifient la manière dont l’approvisionnement automatique fonctionne lorsqu’un agent ou une extension sont déjà installés. 

- Microsoft Monitoring Agent est installé sur l’ordinateur, mais pas en tant qu’extension (agent Direct)<br>
Si l’agent Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas comme une extension Azure), Security Center installe l’extension Microsoft Monitoring Agent et peut mettre à niveau l’agent Microsoft Monitoring vers la dernière version.
L’agent installé continuera à signaler dans ses espaces de travail déjà configuré et signalera en outre à l’espace de travail configuré dans Security Center (multihébergement est pris en charge).
Si l’espace de travail configuré est un espace de travail utilisateur (pas une espace de travail du centre de sécurité par défaut), vous devez installer le « sécurité / solution « securityFree » dessus pour le centre de sécurité démarrer le traitement des événements à partir des machines virtuelles et ordinateurs reporting à cet espace de travail.<br>
<br>
Pour les ordinateurs existants sur intégré d’abonnements à Security Center avant 2019-03-17, lorsqu’un agent existant sera détecté, l’extension Microsoft Monitoring Agent ne sera pas installée et l’ordinateur n’est pas affectée. Pour ces machines, consultez la recommandation « Résoudre analyse des problèmes d’intégrité de l’agent sur vos machines » pour résoudre les problèmes d’installation de l’agent sur ces ordinateurs.

  
- Agent de System Center Operations Manager est installé sur l’ordinateur<br>
Centre de sécurité installera l’extension Microsoft Monitoring Agent côté à côte vers Operations Manager existant. L’agent Operations Manager existant continuera à enregistrer dans le serveur Operations Manager normalement. Notez que l’agent Operations Manager et Microsoft Monitoring Agent partagent des bibliothèques Runtime communs, qui seront mise à jour vers la dernière version lors de ce processus.
Remarque : si Operations Manager 2012 version de l’agent est installé, **ne le faites pas** activer la configuration sur automatique.<br>

- Une extension de machine virtuelle existante est présente<br>
    - Lorsque l’Agent de surveillance est installé en tant qu’extension, la configuration de l’extension permet de générer uniquement un espace de travail unique. Security Center n’écrase pas les connexions existantes des espaces de travail utilisateur. Security Center stocke les données de sécurité à partir de la machine virtuelle dans l’espace de travail déjà connecté, autant que le « sécurité » ou « securityFree » solution a été installée dessus. Security Center peut mettre à niveau la version d’extension vers la dernière version de ce processus.  
    - Pour voir à quel espace de travail l’extension existante envoie des données, exécutez le test pour [Valider la connectivité avec Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Vous pouvez également ouvrir les espaces de travail Analytique de journal, sélectionnez un espace de travail, sélectionnez la machine virtuelle et rechercher à la connexion de l’agent d’Analytique de journal. 
    - Si vous disposez d’un environnement où le journal Analytique agent est installé sur les stations de travail clientes et création de rapports à un espace de travail Analytique de journal, consultez la liste des [les systèmes d’exploitation pris en charge par Azure Security Center](security-center-os-coverage.md) pour vous assurer votre système d’exploitation est pris en charge et consultez [clients analytique de journal existants](security-center-faq.md#existingloganalyticscust) pour plus d’informations.
 
### Désactiver l’approvisionnement automatique<a name="offprovisioning"></a>
Vous pouvez désactiver l’approvisionnement automatique à partir des ressources à tout moment en désactivant ce paramètre dans la stratégie de sécurité. 


1. Retournez au menu principal de Security Center et sélectionnez Stratégie de sécurité.
2. Cliquez sur **modifier les paramètres** dans la ligne de l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique.
3. Dans le panneau **Stratégie de sécurité : collecte de données**, dans **Provisionnement automatique**, sélectionnez **Désactivé**.
4. Sélectionnez **Enregistrer**.

   ![Désactiver le provisionnement automatique][6]

Quand le provisionnement automatique est désactivé, la section de configuration de l’espace de travail par défaut n’est pas affichée.

Si vous désactivez l’approvisionnement automatique préalablement activé :
-   Des agents ne seront pas approvisionnés sur les nouvelles machines virtuelles.
-   Security Center arrête la collecte de données à partir de l’espace de travail par défaut.
 
> [!NOTE]
>  La désactivation de l’approvisionnement automatique ne supprime pas Microsoft Monitoring Agent des machines virtuelles Azure sur lesquelles l’agent était approvisionné. Pour plus d’informations sur la suppression de l’extension OMS, consultez [Comment supprimer des extensions OMS installées par Security Center](security-center-faq.md#remove-oms).
>
    
## Approvisionnement manuel d’un agent <a name="manualagent"></a>
 
Il existe plusieurs manières d’installer Microsoft Monitoring Agent manuellement. Lors de l’installation manuelle, veillez à désactiver l’approvisionnement automatique.

### <a name="operations-management-suite-vm-extension-deployment"></a>Déploiement de l’extension de machines virtuelles Operations Management Suite 

Vous pouvez installer manuellement Microsoft Monitoring Agent pour que Security Center puisse collecter des données de sécurité sur vos machines virtuelles et fournir des suggestions et des alertes.
1. Sélectionnez Approvisionnement automatique : OFF.
2. Créez un espace de travail et définissez le niveau tarifaire de l’espace de travail que vous souhaitez définir pour l’agent Microsoft Monitoring :

   a.  Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
     
   b.  Sélectionnez l’espace de travail dans lequel vous avez l’intention de connecter l’agent. Assurez-vous que l’espace de travail est dans le même abonnement que vous utilisez dans Security Center et que vous disposez d’autorisations en lecture/écriture sur l’espace de travail.
       ![Sélectionnez un espace de travail][8]
3. Définir le niveau tarifaire.
   ![Sélectionnez le niveau tarifaire][9] 
   >[!NOTE]
   >Si l’espace de travail a déjà une solution **Security** ou **SecurityCenterFree** activée, la tarification sera définie automatiquement. 
   > 

4. Si vous souhaitez déployer les agents sur de nouvelles machines virtuelles à l’aide d’un modèle Resource Manager, installez l’extension de machine virtuelle OMS :

   a.  [Installez l’extension de machine virtuelle OMS pour Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installez l’extension de machine virtuelle OMS pour Linux](../virtual-machines/extensions/oms-linux.md)
5. Pour déployer les extensions sur des machines virtuelles existantes, suivez les instructions de [Collecter des données sur les machines virtuelles Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > La section **Collecter les données d’événements et de performances** est facultative.
   >
6. Pour utiliser PowerShell afin de déployer l’extension, utilisez l’exemple PowerShell suivant :
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Accédez à **Log Analytics** et cliquez sur **Paramètres avancés**.
    
      ![Définir l’analytique des journaux d’activité][11]

   2. Copiez les valeurs hors **WorkspaceID** et **clé primaire**.
  
      ![Copier les valeurs][12]

   3. Remplir la configuration publique et la configuration privée avec ces valeurs :
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Lors de l’installation sur une machine virtuelle Windows :
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Lors de l’installation sur une machine virtuelle Linux :
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Pour obtenir des instructions sur la manière d’intégrer Security Center à l’aide de PowerShell, voir [Automatiser l’intégration d’Azure Security Center à l’aide de PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Résolution de problèmes

-   Pour identifier des problèmes d’approvisionnement automatique, consultez [Problèmes d’intégrité de l’agent de surveillance](security-center-troubleshooting-guide.md#mon-agent).

-  Pour identifier la configuration réseau requise pour l’agent de surveillance, consultez [Résolution des problèmes de configuration réseau requise de l’agent de surveillance](security-center-troubleshooting-guide.md#mon-network-req).
-   Pour identifier des problèmes d’intégration manuelle, voir [Comment faire pour résoudre les problèmes d’intégration de Microsoft Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Pour identifier les problèmes de machines virtuelles et d’ordinateurs non managés, voir [Machines virtuelles et ordinateurs non managés](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré le fonctionnement de la collecte de données et de l’approvisionnement automatique dans Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
