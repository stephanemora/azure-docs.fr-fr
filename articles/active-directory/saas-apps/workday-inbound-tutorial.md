---
title: 'Tutoriel : Configurer Workday pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et retirer automatiquement des comptes d’utilisateur sur Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849851"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Tutoriel : Configurer Workday pour l’attribution automatique d’utilisateurs (préversion)

L’objectif de ce tutoriel est de vous présenter les étapes à suivre pour importer des profils d’employé depuis Workday dans Active Directory et dans Azure Active Directory, avec réécriture facultative de l’adresse e-mail dans Workday.

## <a name="overview"></a>Vue d’ensemble

Le [service d’approvisionnement utilisateur Azure Active Directory](../manage-apps/user-provisioning.md) s’intègre aux [API de ressources humaines Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) afin d’approvisionner des comptes d’utilisateur. Azure AD utilise cette connexion pour activer les flux de travail d’approvisionnement de l’utilisateur suivants :

* **Provisionnement d’utilisateurs sur Active Directory** : synchronisez des ensembles de d’utilisateurs sélectionnés depuis Workday dans un ou plusieurs domaines Active Directory.

* **Attribution d’utilisateurs cloud uniquement dans Azure Active Directory** : dans les scénarios où Active Directory local n’est pas utilisé, les utilisateurs peuvent être attribués directement à partir de Workday dans Azure Active Directory à l’aide du service d’attribution d’utilisateurs Azure AD. 

* **Réécriture des adresses e-mail dans Workday** : le service d’attribution d’utilisateurs Azure AD peut réécrire les adresses e-mail des utilisateurs Azure AD dans Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quels sont les scénarios de ressources humaines couverts ?

Les flux de travail d’approvisionnement de l’utilisateur Workday pris en charge par le service d’approvisionnement de l’utilisateur Azure AD autorisent l’automatisation des scénarios de gestion du cycle de vie des identités et des ressources humaines suivants :

* **Nouvelles embauches** : lorsqu’un nouvel employé est ajouté à Workday, un compte d’utilisateur est automatiquement créé dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](../manage-apps/user-provisioning.md), avec l’écriture différée de l’adresse e-mail pour Workday.

* **Mises à jour du profil et des attributs de l’employé** : lorsqu’un enregistrement d’employé est mis à jour dans Workday (par exemple, le nom, le titre ou le responsable), son compte d’utilisateur est automatiquement mis à jour dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](../manage-apps/user-provisioning.md).

* **Résiliations de contrats d’employé** : lorsque le contrat d’un employé est résilié dans Workday, le compte d’utilisateur est automatiquement désactivé dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](../manage-apps/user-provisioning.md).

* **Employé ré-embauché** : lorsqu’un employé est ré-embauché dans Workday, son ancien compte peut être automatiquement réactivé ou réapprovisionné (selon votre préférence) dans Active Directory, Azure Active Directory et, éventuellement, Office 365 et [d’autres applications SaaS prises en charge par Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>À qui cette solution d’attribution d’utilisateurs convient-elle le mieux ?

Cette solution d’attribution d’utilisateurs Workday, actuellement en préversion publique, est idéale pour :

* les organisations qui souhaitent une solution cloud prédéfinie pour l’attribution d’utilisateurs Workday ;

* les organisations qui ont besoin d’une attribution d’utilisateurs directe de Workday à Active Directory, ou Azure Active Directory ;

* les organisations qui imposent l’attribution d’utilisateurs à l’aide de données provenant du module HCM Workday (voir [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)) ;

* les organisations qui ont besoin de joindre, de déplacer et de laisser les utilisateurs à synchroniser avec une ou plusieurs unités d’organisation, domaines et forêts Active Directory seulement sur la base d’informations de modifications détectées dans le module Workday HCM (voir [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)) ;

* les organisations qui utilisent Office 365 pour la messagerie électronique.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>Architecture de la solution

Cette section décrit l’architecture de la solution de provisionnement des utilisateurs de bout en bout pour les environnements hybrides courants. Il existe deux flux connexes :

* **Flux de données RH faisant autorité – de Workday vers Active Directory en local :** Dans ce flux, les événements concernant les employés (comme les nouveaux recrutements, les transferts, les fins de contrat) se produisent d’abord dans le locataire Workday HR cloud, puis les données des événements transitent dans Active Directory local par le biais d’Azure AD et l’agent de provisionnement. Selon l’événement, cela peut provoquer des opérations de création/mise à jour/activation/désactivation dans AD.
* **Flux de réécriture des e-mails – d’Active Directory en local vers Workday :** Une fois la création du compte terminée dans Active Directory, il est synchronisé avec Azure AD par le biais d’Azure AD Connect et l’attribut d’e-mail provenant d’Active Directory peut être réécrit dans Workday.

![Vue d’ensemble](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Flux de données utilisateur de bout en bout

1. L’équipe RH effectue des transactions sur les employés (entrants/changements de poste/sortants ou recrutements/transferts/arrêts) dans Workday HCM
2. Le service Azure AD Provisioning effectue des synchronisations planifiées des identités à partir de Workday HR et identifie les modifications qui doivent être traitées pour la synchronisation avec Active Directory local.
3. Le service Azure AD Provisioning appelle l’agent de provisionnement AAD Connect local avec une charge utile de demandes contenant des opérations de création/mise à jour/activation/désactivation de compte AD.
4. L’agent de provisionnement Azure AD Connect utilise un compte de service pour ajouter/mettre à jour des données de compte AD.
5. Le moteur Azure AD Connect / AD Sync effectue exécute la synchronisation différentielle pour extraire les mises à jour dans AD.
6. Les mises à jour d’Active Directory sont synchronisées avec Azure Active Directory local.
7. Si le connecteur de réécriture Workday est configuré, il réécrit l’attribut d’e-mail dans Workday, en fonction de l’attribut de correspondance utilisé.


## <a name="planning-your-deployment"></a>Planification de votre déploiement

Avant de commencer l’intégration de Workday, vérifiez les prérequis ci-dessous et lisez les conseils suivants sur la façon de faire correspondre votre architecture Active Directory et vos exigences d’approvisionnement de l’utilisateur avec la ou les solutions fournies par Azure Active Directory.

### <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure AD Premium P1 valide avec un accès administrateur général
* Un client de mise en œuvre de Workday à des fins de test et d’intégration
* Autorisations d’administrateur dans Workday pour créer un utilisateur de l’intégration système et apporter des modifications afin de tester les informations de l’employé
* Pour le provisionnement des utilisateurs sur Active Directory, un serveur exécutant Windows Server 2012 ou ultérieur avec le runtime .NET 4.7+ est nécessaire pour héberger l’[agent de provisionnement local](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pour la synchronisation entre Active Directory et Azure AD

### <a name="planning-considerations"></a>Considérations en matière de planification

Azure AD propose un vaste éventail de connecteurs d’attribution pour vous aider à résoudre la gestion de l’attribution et du cycle de vie d’identité de Workday à Active Directory, à Azure AD, aux applications SaaS et au-delà. Les fonctionnalités que vous allez utiliser et la manière de configurer la solution varient selon l’environnement et les exigences de votre organisation. Dans un premier temps, examinez le nombre d’éléments suivants présents et déployés dans votre organisation :

* Combien de forêts Active Directory sont en cours d’utilisation ?
* Combien de domaines Active Directory sont en cours d’utilisation ?
* Combien d’unités d’organisation (UO) Active Directory sont en cours d’utilisation ?
* Combien de clients Azure Active Directory sont en cours d’utilisation ?
* Existe-t-il des utilisateurs qui doivent être approvisionnés dans Active Directory et Azure Active Directory (par exemple, les utilisateurs « hybrides ») ?
* Existe-t-il des utilisateurs qui doivent être approvisionnés dans Azure Active Directory, mais pas dans Active Directory (par exemple, les utilisateurs « cloud only ») ?
* Les adresses e-mail des utilisateurs doivent-elles être différées dans Workday ?

Une fois que vous disposez des réponses à ces questions, vous pouvez planifier le déploiement de l’approvisionnement de Workday en suivant les instructions ci-dessous.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Planification du déploiement de l’agent de provisionnement AAD Connect

La solution de provisionnement des utilisateurs de Workday sur AD nécessite le déploiement d’un ou plusieurs agents de provisionnement sur des serveurs exécutant Windows 2012 R2 ou ultérieur, avec au moins 4 Go de RAM et le runtime .NET 4.7+. Les considérations suivantes doivent être prises en compte avant d’installer l’agent de provisionnement :

* Vérifiez que le serveur hôte exécutant l’agent de provisionnement a accès au domaine AD cible
* L’Assistant Configuration de l’agent de provisionnement inscrit l’agent auprès de votre locataire Azure AD et le processus d’inscription nécessite l’accès à *.msappproxy.net sur le port 8082. Vérifiez que les règles de pare-feu de trafic sortant sont en place pour permettre cette communication.
* L’agent de provisionnement utilise un compte de service pour communiquer avec le ou les domaines Active Directory locaux. Avant l’installation de l’agent, il est recommandé de créer un compte de service avec les autorisations de lecture/écriture des propriétés utilisateur et un mot de passe qui n’expire pas.  
* Lors de la configuration de l’agent de provisionnement, vous pouvez sélectionner des contrôleurs de domaine qui doivent gérer les demandes de provisionnement. Si vous avez plusieurs contrôleurs de domaine géographiquement répartis, installez l’agent de provisionnement dans le même site que vos contrôleurs de domaine par défaut, de façon à améliorer la fiabilité et les performances de la solution de bout en bout
* Pour la haute disponibilité, vous pouvez déployer plusieurs agents de provisionnement et les inscrire pour gérer le même ensemble de domaines Active Directory locaux.

> [!IMPORTANT]
> Dans les environnements de production, Microsoft recommande un minimum de 3 agents de provisionnement configurés avec votre locataire Azure AD pour la haute disponibilité.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Sélection des applications du connecteur de provisionnement à déployer

Lors de l’intégration de Workday et Active Directory, vous devez considérer plusieurs systèmes sources et cibles :

| Système source | Système cible | Notes |
| ---------- | ---------- | ---------- |
| Workday | Domaine Active Directory | Chaque domaine est traité comme un système cible distinct |
| Workday | Client Azure AD | Selon les besoins pour les utilisateurs « cloud only » |
| Forêt Active Directory | Client Azure AD | Ce flux est maintenant géré par AAD Connect |
| Client Azure AD | Workday | Pour la réécriture des adresses e-mail |

Pour faciliter les workflows de provisionnement entre Workday et Active Directory, Azure AD fournit plusieurs applications de connecteur de provisionnement que vous pouvez ajouter à partir de la galerie d’applications Azure AD :

![Galerie d’applications AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Provisionnement de Workday sur Active Directory** : cette application facilite le provisionnement de comptes utilisateur depuis Workday sur un domaine Active Directory unique. Si vous avez de plusieurs domaines, vous pouvez ajouter une instance de cette application à partir de la galerie d’applications Azure AD pour chaque domaine Active Directory vers lequel vous devez provisionner.

* **Approvisionnement de Workday à Azure AD** : alors que AAD Connect est l’outil qui doit être utilisé pour synchroniser les utilisateurs Active Directory vers Azure Active Directory, cette application peut être utilisée pour faciliter l’approvisionnement d’utilisateurs « cloud only » à partir de Workday vers un client Azure Active Directory unique.

* **Réécriture Workday** : cette application facilite la réécriture des adresses e-mail de l’utilisateur depuis Azure Active Directory vers Workday.

> [!TIP]
> L’application « Workday » standard est utilisée pour configurer l’authentification unique entre Workday et Azure Active Directory. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Déterminer le mappage et les transformations des attributs utilisateur de Workday vers AD

Avant de configurer le provisionnement d’utilisateurs sur un domaine Active Directory, posez-vous les questions suivantes. Les réponses à ces questions détermineront la configuration requise des filtres d’étendue et des mappages d’attributs.

* **Quels sont les utilisateurs de Workday à attribuer à cette forêt Active Directory ?**

   * *Exemple : Les utilisateurs dont l’attribut « Company » de Workday comporte la valeur « Contoso » et dont l’attribut « Worker_Type » contient « Regular ».*

* **Comment les utilisateurs sont-ils acheminés dans différentes unités d’organisation (UO) ?**

   * *Exemple : Les utilisateurs sont acheminés vers les unités d’organisation qui correspondent au lieu de bureau défini dans les attributs « Municipality » et « Country_Region_Reference » de Workday.*

* **Comment les attributs suivants devront-ils être remplis dans Active Directory ?**

   * Nom commun (cn)
      * *Exemple : Utiliser la valeur User_ID de Workday définie par les ressources humaines*
      
   * ID employé (employeeId)
      * *Exemple : Utiliser la valeur Worker_ID de Workday*
      
   * Nom de compte SAM (sAMAccountName)
      * *Exemple : Utiliser la valeur User_ID de Workday, filtrée par une expression de configuration Azure AD qui supprime les caractères non autorisés*
      
   * Nom d’utilisateur principal (userPrincipalName)
      * *Exemple : Utiliser la valeur User_ID de Workday, avec une expression de configuration Azure AD qui ajoute un nom de domaine*

* **Comment les utilisateurs doivent-ils être appariés entre Workday et Active Directory ?**

  * *Exemple : Les utilisateurs ayant une certaine valeur « Worker_ID » Workday sont mis en correspondance avec les utilisateurs Active Directory pour lesquels « employeeID » a la même valeur. Si la valeur Worker_ID est absente dans Active Directory, créer un nouvel utilisateur.*
  
* **La forêt Active Directory contient-elle déjà les identifiants utilisateur requis pour que la logique de correspondance fonctionne ?**

  * *Exemple : S’il s’agit d’un nouveau déploiement Workday, il est vivement recommandé qu’Active Directory soit prérempli avec les bonnes valeurs Worker_ID de Workday (ou la valeur d’identifiant unique choisie) pour simplifier autant que possible la logique de correspondance.*



L’installation et la configuration de ces applications de connecteurs d’approvisionnement spéciales sont présentées dans les autres sections de ce didacticiel. Les applications que vous choisissez de configurer dépendent des systèmes à provisionner, du nombre de domaines Active Directory et de locataires Azure AD dans votre environnement.



## <a name="configure-integration-system-user-in-workday"></a>Configurer un utilisateur du système d’intégration dans Workday

En général, tous les connecteurs d’approvisionnement de Workday exigent des informations d’identification pour un compte d’intégration système Workday pour se connecter à l’API de ressources humaines Workday. Cette section décrit comment créer un compte de système d’intégration dans Workday.

> [!NOTE]
> Il est possible d’ignorer cette procédure et d’utiliser à la place un compte d’administrateur général Workday en tant que compte d’intégration système. Cette procédure, bien qu’adaptée aux démonstrations, n’est pas recommandée pour les déploiements de production.

### <a name="create-an-integration-system-user"></a>Créer un utilisateur de système d’intégration

**Pour créer un utilisateur de système d’intégration :**

1. Connectez-vous à votre client Workday à l’aide d’un compte d’administrateur. Dans **Workday Application** (Application Workday), entrez « create user » dans la zone de recherche, puis cliquez sur **Create Integration System User** (Créer un utilisateur du système d’intégration).

    ![Créer un utilisateur](./media/workday-inbound-tutorial/wd_isu_01.png "Créer un utilisateur")
2. Exécutez la tâche **Create Integration System User** en fournissant un nom d’utilisateur et un mot de passe pour un nouvel utilisateur du système d’intégration.  
 * Laissez l’option **Require New Password at Next Sign In** désactivée, étant donné que cet utilisateur se connectera par programmation.
 * Laissez la valeur par défaut de 0 pour l’option **Session Timeout Minutes** afin d’éviter que les sessions de l’utilisateur n’expirent prématurément.
 * Sélectionnez l’option **Do Not Allow UI Sessions** (Ne pas autoriser les sessions d’interface utilisateur), car elle fournit une couche de sécurité supplémentaire qui empêche un utilisateur ayant le mot de passe du système d’intégration de se connecter à Workday. 

    ![Créer un utilisateur de système d’intégration](./media/workday-inbound-tutorial/wd_isu_02.png "Créer un utilisateur de système d’intégration")

### <a name="create-a-security-group"></a>Créer un groupe de sécurité
Dans cette étape, vous créez un groupe de sécurité du système d’intégration sans contrainte dans Workday et vous affectez l’utilisateur du système d’intégration créé à l’étape précédente à ce groupe.

**Pour créer un groupe de sécurité :**

1. Entrez create security group dans la zone de recherche, puis cliquez sur **Create Security Group**.

    ![Créer un groupe de sécurité](./media/workday-inbound-tutorial/wd_isu_03.png "Créer un groupe de sécurité")
2. Exécutez la tâche **Create Security Group**.  
   * Sélectionnez **Integration System Security Group (Unconstrained)** dans la liste déroulante **Type of Tenanted Security Group**.

    ![Créer un groupe de sécurité](./media/workday-inbound-tutorial/wd_isu_04.png "Créer un groupe de sécurité")

3. Une fois le groupe de sécurité créé, vous voyez une page où vous pouvez lui affecter des membres. Ajouter le nouvel utilisateur du système d’intégration à ce groupe de sécurité et sélectionnez l’étendue de l’organisation appropriée.
![Modifier un groupe de sécurité](./media/workday-inbound-tutorial/wd_isu_05.png "Modifier un groupe de sécurité")
 
### <a name="configure-domain-security-policy-permissions"></a>Configurer des autorisations de stratégie de sécurité du domaine
Dans cette étape, vous accordez au groupe de sécurité des autorisations de stratégie de « sécurité du domaine » pour les données des employés.

**Pour configurer des autorisations de stratégie de sécurité du domaine :**

1. Entrez **Domain Security Configuration** (Configuration de la sécurité du domaine) dans la zone de recherche, puis cliquez sur le lien **Domain Security Configuration Report** (Rapport sur la configuration de la sécurité du domaine).  

    ![Stratégies de sécurité de domaine](./media/workday-inbound-tutorial/wd_isu_06.png "Stratégies de sécurité de domaine")  
2. Dans la zone de texte **Domain** (Domaine), recherchez les domaines suivants et ajoutez-les au filtre un par un.  
   * *External Account Provisioning*
   * *Worker Data: Public Worker Reports*
   * *Person Data: Work Contact Information*
   * *Worker Data: All Positions*
   * *Worker Data: Current Staffing Information*
   * *Worker Data: Business Title on Worker Profile*
 
    ![Stratégies de sécurité de domaine](./media/workday-inbound-tutorial/wd_isu_07.png "Stratégies de sécurité de domaine")  

    ![Stratégies de sécurité de domaine](./media/workday-inbound-tutorial/wd_isu_08.png "Stratégies de sécurité de domaine") 

    Cliquez sur **OK**.

3. Dans le rapport qui s’affiche, sélectionnez les points de suspension (...) qui apparaissent en regard de **External Account Provisioning** (Provisionnement de compte externe), puis cliquez sur l’option de menu **Domain -> Edit Security Policy Permissions** (Domaine -> Modifier les autorisations de la stratégie de sécurité)

    ![Stratégies de sécurité de domaine](./media/workday-inbound-tutorial/wd_isu_09.png "Stratégies de sécurité de domaine")  

4. Sur la page **Edit Domain Security Policy Permissions** (Modifier les autorisations de la stratégie de sécurité), faites défiler jusqu’à la section **Integration Permissions** (Autorisations de l’intégration). Cliquez sur le signe « + » pour ajouter le groupe du système d’intégration à la liste des groupes de sécurité avec les autorisations d’intégration **Get** (Obtenir) et **Put** (Placer).

    ![Modifier l’autorisation](./media/workday-inbound-tutorial/wd_isu_10.png "Modifier l’autorisation")  

5. Cliquez sur le signe « + » pour ajouter le groupe du système d’intégration à la liste des groupes de sécurité avec les autorisations d’intégration **Get** (Obtenir) et **Put** (Placer).

    ![Modifier l’autorisation](./media/workday-inbound-tutorial/wd_isu_11.png "Modifier l’autorisation")  

6. Répétez les étapes 3 à 5 ci-dessus pour chacune de ces stratégies de sécurité restantes :

   | Opération | Stratégie de sécurité du domaine |
   | ---------- | ---------- | 
   | Get et Put | Worker Data: Public Worker Reports |
   | Get et Put | Person Data: Work Contact Information |
   | Obtenir | Worker Data: All Positions |
   | Obtenir | Worker Data: Current Staffing Information |
   | Obtenir | Worker Data: Business Title on Worker Profile |

### <a name="configure-business-process-security-policy-permissions"></a>Configurer des autorisations de stratégie de sécurité de processus métier
Dans cette étape, vous accordez des autorisations de stratégies de sécurité « sécurité des processus métier » pour les données des employés au groupe de sécurité. Ceci est nécessaire pour configurer le connecteur d’application de réécriture Workday. 

**Pour configurer des autorisations de stratégie de sécurité de processus métier :**

1. Entrez **Business Process Policy** (Stratégie de processus métier) dans la zone de recherche, puis cliquez sur le lien vers la tâche **Edit Business Process Security Policy** (Modifier la stratégie de sécurité de processus métier).  

    ![Stratégies de sécurité des processus métier](./media/workday-inbound-tutorial/wd_isu_12.png "Stratégies de sécurité des processus métier")  

2. Dans la zone de texte **Business Process Type** (Type de processus métier), recherchez *Contact*, sélectionnez le processus métier **Contact change** (Modification du contact) et cliquez sur **OK**.

    ![Stratégies de sécurité des processus métier](./media/workday-inbound-tutorial/wd_isu_13.png "Stratégies de sécurité des processus métier")  

3. Sur la page **Edit Business Process Security Policy** (Modifier la stratégie de sécurité de processus métier), faites défiler jusqu’à la section **Maintain Contact Information (Web Service)** (Gérer les informations de contact (service web)).

    ![Stratégies de sécurité des processus métier](./media/workday-inbound-tutorial/wd_isu_14.png "Stratégies de sécurité des processus métier")  

4. Sélectionnez et ajoutez le nouveau groupe de sécurité du système d’intégration à la liste des groupes de sécurité qui peuvent lancer la demande de services web. Cliquez sur **Done** (Terminé). 

    ![Stratégies de sécurité des processus métier](./media/workday-inbound-tutorial/wd_isu_15.png "Stratégies de sécurité des processus métier")  

 
### <a name="activate-security-policy-changes"></a>Activer les modifications de la stratégie de sécurité

**Pour activer les modifications de la stratégie de sécurité :**

1. Entrez activate dans la zone de recherche, puis cliquez sur le lien **Activate Pending Security Policy Changes**.

    ![Activer](./media/workday-inbound-tutorial/wd_isu_16.png "Activer") 
2. Commencez la tâche Activate Pending Security Policy Changes en entrant un commentaire à des fins d’audit, puis cliquez sur **OK**. 

    ![Activer la sécurité en attente](./media/workday-inbound-tutorial/wd_isu_17.png "Activer la sécurité en attente")  
1. Terminez la tâche sur l’écran suivant en cochant la case **Confirmer**, puis cliquez sur **OK**.

    ![Activer la sécurité en attente](./media/workday-inbound-tutorial/wd_isu_18.png "Activer la sécurité en attente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configuration de l’approvisionnement de l’utilisateur de Workday à Active Directory

Suivez ces instructions pour configurer le provisionnement des comptes d’utilisateur depuis Workday sur chaque domaine Active Directory dans l’étendue de votre intégration.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Partie 1 : Installer et configurer des agents de provisionnement locaux

Pour provisionner sur Active Directory local, un agent doit être installé sur un serveur avec .NET Framework 4.7+ et l’accès aux domaines Active Directory souhaités.

> [!TIP]
> Vous pouvez vérifier la version du .NET Framework sur votre serveur en suivant les instructions fournies [ici](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Si .NET 4.7 ou ultérieur n’est pas installé sur le serveur, vous pouvez le télécharger [ici](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).  

Une fois que vous avez déployé .NET 4.7+, vous pouvez télécharger l’**[agent de provisionnement local ici](https://go.microsoft.com/fwlink/?linkid=847801)** et suivez les étapes ci-dessous pour effectuer la configuration de l’agent.

1. Connectez-vous au serveur Windows Server où vous voulez installer le nouvel agent.
2. Lancez le programme d’installation de l’agent de provisionnement, acceptez les conditions d’utilisation, puis cliquez sur le bouton **Install** (Installer).
![Écran d’installation](./media/workday-inbound-tutorial/pa_install_screen_1.png "Écran d’installation")

3. Une fois l’installation terminée, l’Assistant démarrer et vous voyez l’écran **Connect Azure AD** (Connecter Azure AD). Cliquez sur le bouton **Authenticate** (S’authentifier) pour vous connecter à votre instance Azure AD.
![Connecter Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connecter Azure AD")

4. Authentifiez-vous auprès de votre instance Azure AD avec les informations d’identification de l’administrateur général. 
![Authentification de l’administrateur](./media/workday-inbound-tutorial/pa_install_screen_3.png "Authentification de l’administrateur")

5. Après une authentification réussie auprès d’Azure AD, vous voyez l’écran **Connect Active Directory** (Connecter Active Directory). Dans cette étape, entrez votre nom de domaine AD, puis cliquez sur le bouton **Add Directory** (Ajouter un annuaire).
![Ajouter un annuaire](./media/workday-inbound-tutorial/pa_install_screen_4.png "Ajouter un annuaire")

6. Vous êtes maintenant invité à entrer les informations d’identification nécessaires pour vous connecter au domaine AD. Sur le même écran, vous pouvez utiliser **Select domain controller priority** (Sélectionner la priorité de contrôleur de domaine) pour spécifier les contrôleurs de domaine que l’agent doit utiliser pour l’envoi des demandes de provisionnement.
![Informations d’identification du domaine](./media/workday-inbound-tutorial/pa_install_screen_5.png "Informations d’identification du domaine")

7. Après avoir configuré le domaine, le programme d’installation affiche une liste des domaines configurés. Dans cet écran, vous pouvez répéter les étapes 5 et 6 pour ajouter d’autres domaines ou cliquer sur **Suivant** pour passer à l’inscription de l’agent. 
![Domaines configurés](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domaines configurés")

   > [!NOTE]
   > Si vous avez plusieurs domaines AD (par exemple na.contoso.com, emea.contoso.com), ajoutez individuellement chaque domaine à la liste. Ajoutez seulement le domaine parent (par exemple contoso.com) n’est pas suffisant, et il est recommandé d’inscrire chaque domaine enfant auprès de l’agent. 

8. Passez en revue les détails de la configuration, puis cliquez sur **Confirm** (Confirmer) pour inscrire l’agent. 
![Écran de confirmation](./media/workday-inbound-tutorial/pa_install_screen_7.png "Écran de confirmation")

9. L’Assistant Configuration affiche la progression de l’inscription de l’agent.
![Inscription de l’agent](./media/workday-inbound-tutorial/pa_install_screen_8.png "Inscription de l’agent")

10. Une fois l’inscription de l’agent réussie, vous pouvez cliquer sur **Exit** (Quitter) pour quitter l’Assistant. 
![Écran Quitter](./media/workday-inbound-tutorial/pa_install_screen_9.png "Écran Quitter")

11. Vérifier l’installation de l’agent, et qu’il est en cours d’exécution en ouvrant le composant logiciel enfichable « Services » et en recherchant le service nommé « Microsoft Azure AD Connect Provisioning Agent » ![Services](./media/workday-inbound-tutorial/services.png)  


**Résolution des problèmes d’agent**

Le [journal des événements Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) sur la machine Windows Server hébergeant l’agent contient des événements pour toutes les opérations effectuées par l’agent. Pour afficher ces événements :
    
1. Ouvrez **Eventvwr.msc**.
2. Sélectionnez **Journaux Windows > Application**.
3. Regardez tous les événements consignés sous la source **AAD.Connect.ProvisioningAgent**. 
4. Consultez les erreurs et avertissements.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 2 : Ajout de l’application de connecteur de provisionnement et création de la connexion à Workday

**Pour configurer l’approvisionnement Workday dans Active Directory :**

1.  Accédez à <https://portal.azure.com>

2.  Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3.  Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4.  Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5.  Recherchez **Approvisionnement Workday dans Active Directory** et ajoutez cette application à partir de la galerie.

6.  Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7.  Définissez le **Mode** **Approvisionnement** sur **Automatique**

8.  Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. **Le résultat doit être le suivant : username@contoso4**

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, où contoso4 est remplacé par le nom de votre locataire et wd3-impl est remplacé par la chaîne d’environnement.

   * **Forêt Active Directory :** le « nom » de votre domaine Active Directory, tel qu’il est inscrit auprès de l’agent. Il s’agit généralement d’une chaîne telle que : *contoso.com*

   * **Conteneur Active Directory :** entrez le nom unique du conteneur où l’agent doit créer des comptes d’utilisateur par défaut. 
        Exemple : *OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> Ce paramètre concerne seulement les créations de comptes d’utilisateur si l’attribut *parentDistinguishedName* attribut n’est pas configuré dans les mappages d’attributs. Ce paramètre n’est pas utilisé pour la recherche d’utilisateurs ni pour les opérations de mise à jour. Toute la sous-arborescence du domaine se trouve dans l’étendue de l’opération de recherche.
   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».
> [!NOTE]
> Le service Azure AD Provisioning envoie la notification par e-mail si le travail de provisionnement passe à l’état [Mise en quarantaine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine).

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que les informations d’identification de Workday et celles d’Active Directory configurées sur le programme d’installation de l’agent sont valides.

![Portail Azure](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : Configuration des mappages d’attributs 

Dans cette section, vous allez configurer le flux des données de l’utilisateur de Workday vers Active Directory.

1.  Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les employés Workday sur OnPremises**.

2.  Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs dans Workday qui doivent se trouver dans la portée pour l’approvisionnement AD, en définissant un ensemble de filtres basés sur l’attribut. La portée par défaut est « tous les utilisateurs dans Workday ». Exemples de filtres :

   * Exemple : Étendue pour les utilisateurs avec des ID d’employés entre 1000000 et 2000000

      * Attribut : WorkerID

      * Opérateur : REGEX Match

      * Valeur : (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemple : Uniquement les employés et non les employés occasionnels 

      * Attribut : EmployeeID

      * Opérateur : IS NOT NULL

3.  Dans le champ **Actions de l’objet cible**, vous pouvez filtrer globalement les actions qui peuvent être exécutées sur Active Directory. Les actions **Créer** et **Mettre à jour** sont les plus courantes.

4.  Dans la section **Mappages d’attributs**, vous pouvez définir comment les attributs Workday sont mappés aux attributs Active Directory.

5. Cliquez sur un mappage d’attributs existants à mettre à jour ou cliquez sur **Ajouter un nouveau mappage** en bas de l’écran pour ajouter de nouveaux mappages. Un mappage d’attribut individuel prend en charge les propriétés suivantes :

      * **Type de mappage**

         * **Direct** : écrit la valeur de l’attribut Workday dans l’attribut AD, sans aucune modification

         * **Constante** : écrivez une valeur de chaîne constante et statique dans l’attribut AD

         * **Expression** : vous permet d’écrire une valeur personnalisée dans l’attribut AD, basée sur un ou plusieurs attributs Workday. [Pour plus d’informations, consultez l’article sur les expressions](../manage-apps/functions-for-customizing-application-data.md).

      * **Attribut source** : l’attribut utilisateur dans Workday. Si l’attribut que vous recherchez n’est pas présent, consultez [Personnalisation de la liste des attributs d’utilisateur Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valeur par défaut** : facultatif. Si l’attribut source a une valeur vide, le mappage écrit cette valeur à la place.
            La configuration la plus courante consiste à laisser ce champ vide.

      * **Attribut cible** : l’attribut de l’utilisateur dans Active Directory.

      * **Faire correspondre des objets à l’aide de cet attribut** : indique ci ce mappage est utilisé ou pas pour identifier les utilisateurs de manière unique entre Workday et Active Directory. Cela est généralement défini dans le champ ID de l’employé pour Workday, qui est habituellement mappé sur un des attributs d’ID d’employé dans Active Directory.

      * **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.

      * **Appliquer ce mappage**
       
         * **Toujours** : appliquez ce mappage à la création de l’utilisateur et aux actions de mise à jour

         * **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création utilisateur

6. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la      section Mappage d’attributs.

![Portail Azure](./media/workday-inbound-tutorial/WD_2.PNG)

**Vous trouverez ci-dessous quelques exemples de mappages d’attributs entre Workday et Active Directory, avec certaines expressions communes**

-   L’expression mappée à l’attribut parentDistinguishedName permet d’attribuer les utilisateurs à différentes unités d’organisation en fonction d’un ou plusieurs attributs sources Workday. Cet exemple les place en fonction de la ville dans laquelle il se trouve.

-   L’attribut userPrincipalName dans Active Directory est généré en concaténant l’identifiant utilisateur Workday avec un suffixe de domaine.

-   Vous pouvez consulter la [documentation sur l’écriture d’expressions](../manage-apps/functions-for-customizing-application-data.md), qui comprend des exemples de suppression de caractères spéciaux.

  
| ATTRIBUT WORKDAY | ATTRIBUT ACTIVE DIRECTORY |  CORRESPONDANCE D’ID ? | CRÉER / METTRE À JOUR |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Oui** | Écrit lors de la création uniquement |
| **l'UserId**    |  cn    |   |   Écrit lors de la création uniquement |
| **Join("@", [UserID], "contoso.com")**   | userPrincipalName     |     | Écrit lors de la création uniquement 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Écrit lors de la création uniquement |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Créer + mettre à jour |
| **FirstName**   | givenName       |     |    Créer + mettre à jour |
| **LastName**   |   sn   |     |  Créer + mettre à jour |
| **PreferredNameData**  |  displayName |     |   Créer + mettre à jour |
| **Société**         | société   |     |  Créer + mettre à jour |
| **SupervisoryOrganization**  | department  |     |  Créer + mettre à jour |
| **ManagerReference**   | manager  |     |  Créer + mettre à jour |
| **BusinessTitle**   |  title     |     |  Créer + mettre à jour | 
| **AddressLineData**    |  streetAddress  |     |   Créer + mettre à jour |
| **Municipalité**   |   l   |     | Créer + mettre à jour |
| **CountryReferenceTwoLetter**      |   co |     |   Créer + mettre à jour |
| **CountryReferenceTwoLetter**    |  c  |     |         Créer + mettre à jour |
| **CountryRegionReference** |  st     |     | Créer + mettre à jour |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Créer + mettre à jour |
| **PostalCode**  |   postalCode  |     | Créer + mettre à jour |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Créer + mettre à jour |
| **Fax**      | facsimileTelephoneNumber     |     |    Créer + mettre à jour |
| **Mobile**  |    mobile       |     |       Créer + mettre à jour |
| **LocalReference** |  preferredLanguage  |     |  Créer + mettre à jour |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Créer + mettre à jour |
  


### <a name="part-4-start-the-service"></a>Partie 4 : Démarrer le service
Une fois les parties 1 à 3 terminées, vous pouvez redémarrer le service d’approvisionnement dans le portail Azure.

1.  Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. À tout moment, consultez l’onglet **Journaux d’audit** dans le portail Azure pour connaître les actions effectuées par le service d’approvisionnement. Les journaux d’audit répertorient tous les événements de synchronisation individuels effectués par le service d’approvisionnement, tels que les utilisateurs lus dans Workday et par la suite ajoutés ou mis à jour dans Active Directory. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](../manage-apps/check-status-user-account-provisioning.md)**

1.  Consultez le [journal des événements Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) sur la machine Windows Server hébergeant l’agent pour connaître les nouveaux avertissements et erreurs. Ces événements sont visibles en lançant **Eventvwr.msc** sur le serveur et en sélectionnant **Journaux Windows > Application**. Tous les messages liés à l’approvisionnement sont enregistrés sous **l’AADSyncAgent** source.

6. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.

![Portail Azure](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configuration de l’approvisionnement de l’utilisateur sur Azure Active Directory
La façon dont vous configurez l’approvisionnement vers Azure Active Directory dépend de vos exigences d’approvisionnement, comme indiqué dans le tableau ci-dessous.

| Scénario | Solution |
| -------- | -------- |
| **Les utilisateurs doivent être approvisionnés dans Active Directory et Azure AD** | Utilisez  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Les utilisateurs doivent être approvisionnés dans Active Directory uniquement** | Utilisez  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Les utilisateurs doivent être configurés dans Azure AD uniquement (« cloud only »)** | Utilisez l’application **Approvisionnement de Workday à Azure Active Directory** dans la galerie d’applications |

Pour obtenir des instructions sur la configuration Azure AD Connect, consultez la [documentation Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

Les sections suivantes décrivent la configuration d’une connexion entre Workday et Azure AD pour approvisionner les utilisateurs « cloud only ».

> [!IMPORTANT]
> Ne suivez la procédure ci-dessous que si vous avez des utilisateurs « cloud only » qui doivent être approvisionnés dans Azure AD et pas dans Active Directory local.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 1 : Ajout de l’application des connecteurs de provisionnement Azure AD et création de la connexion à Workday

**Pour configurer Workday sur l’approvisionnement Azure Active Directory pour les utilisateurs « cloud only » :**

1.  Accédez à <https://portal.azure.com>

2.  Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3.  Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4.  Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5.  Recherchez **Approvisionnement de Workday vers Azure AD** et ajoutez cette application à partir de la galerie.

6.  Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7.  Définissez le **Mode** **Approvisionnement** sur **Automatique**

8.  Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. Le résultat doit être le suivant : username@contoso4

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, où contoso4 est remplacé par le nom de votre locataire et wd3-impl est remplacé par la chaîne d’environnement. Si cette URL n’est pas connue, collaborez avec votre partenaire d’intégration Workday ou votre représentant du support technique pour déterminer l’URL appropriée à utiliser.

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».

   * Cliquez sur le bouton **Tester la connexion**.

   * Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que l’URL et les informations d’identification Workday sont valides dans Workday.

### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : Configuration des mappages d’attributs 

Dans cette section, vous allez configurer le flux des données de Workday vers Azure Active Directory pour l’utilisateur « cloud only ».

1. Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les Workers sur Azure AD**.

2. Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs dans Workday qui doivent se trouver dans la portée pour l’approvisionnement vers Azure AD, en définissant un ensemble de filtres basés sur l’attribut. La portée par défaut est « tous les utilisateurs dans Workday ». Exemples de filtres :

   * Exemple : Étendue pour les utilisateurs avec des ID d’employés entre 1000000 et 2000000

      * Attribut : WorkerID

      * Opérateur : REGEX Match

      * Valeur : (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemple : Uniquement les employés occasionnels et pas les employés réguliers

      * Attribut : ContingentID

      * Opérateur : IS NOT NULL

3. Dans le champ **Actions de l’objet cible**, vous pouvez filtrer globalement les actions qui peuvent être exécutées sur Azure AD. Les actions **Créer** et **Mettre à jour** sont les plus courantes.

4. Dans la section **Mappages d’attributs**, vous pouvez définir la manière dont les différents attributs Workday sont mappés aux attributs Active Directory.

5. Cliquez sur un mappage d’attributs existants à mettre à jour ou cliquez sur **Ajouter un nouveau mappage** en bas de l’écran pour ajouter de nouveaux mappages. Un mappage d’attribut individuel prend en charge les propriétés suivantes :

   * **Type de mappage**

      * **Direct** : écrit la valeur de l’attribut Workday dans l’attribut AD, sans aucune modification

      * **Constante** : écrivez une valeur de chaîne constante et statique dans l’attribut AD

      * **Expression** : vous permet d’écrire une valeur personnalisée dans l’attribut AD, basée sur un ou plusieurs attributs Workday. [Pour plus d’informations, consultez l’article sur les expressions](../manage-apps/functions-for-customizing-application-data.md).

   * **Attribut source** : l’attribut utilisateur dans Workday. Si l’attribut que vous recherchez n’est pas présent, consultez [Personnalisation de la liste des attributs d’utilisateur Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valeur par défaut** : facultatif. Si l’attribut source a une valeur vide, le mappage écrit cette valeur à la place.
            La configuration la plus courante consiste à laisser ce champ vide.

   * **Attribut cible** : l’attribut utilisateur dans Azure AD.

   * **Faire correspondre des objets à l’aide de cet attribut** : indique si ce mappage doit être utilisé pour identifier les utilisateurs de manière unique entre Workday et Azure AD. Cela est généralement défini dans le champ ID de l’employé pour Workday, habituellement mappé sur un des attributs d’ID de l’employé (nouveau) dans Azure AD.

   * **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.

   * **Appliquer ce mappage**

     * **Toujours** : appliquez ce mappage à la création de l’utilisateur et aux actions de mise à jour

     * **Lors de la création uniquement** : appliquez ce mappage uniquement aux actions de création utilisateur

6. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la      section Mappage d’attributs.

### <a name="part-3-start-the-service"></a>Partie 3 : Démarrer le service
Une fois les parties 1 à 2 terminées, vous pouvez démarrer le service d’approvisionnement.

1. Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. Les événements de synchronisation individuels peuvent être affichés dans l’onglet **Journaux d’audit**. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](../manage-apps/check-status-user-account-provisioning.md)**

5. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configuration de l’écriture différée des adresses de messagerie à Workday
Suivez ces instructions pour configurer l’écriture différée des adresses e-mail des utilisateurs d’Azure Active Directory vers Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Partie 1 : Ajout de l’application de connecteur de provisionnement et création de la connexion à Workday

**Pour configurer le connecteur de réécriture Workday :**

1. Accédez à <https://portal.azure.com>

2. Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3. Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4. Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5. Recherchez **Écriture différée Workday** et ajoutez cette application à partir de la galerie.

6. Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7. Définissez le **Mode** **Approvisionnement** sur **Automatique**

8. Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur du compte de système d’intégration Workday, avec le nom du domaine client ajouté. Le résultat doit être le suivant : username@contoso4

   * **Mot de passe administrateur :** entrez le mot de passe du compte système d’intégration Workday

   * **URL du client :**  entrez l’URL du point de terminaison des services web Workday pour votre client. Cela doit ressembler à https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, où contoso4 est remplacé par le nom de votre locataire et wd3-impl est remplacé par la chaîne d’environnement (si nécessaire).

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer par e-mail en cas de défaillance ».

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion réussit, cliquez sur le bouton **Enregistrer** en haut. En cas d’échec, vérifiez que l’URL et les informations d’identification Workday sont valides dans Workday.

### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : Configuration des mappages d’attributs 

Dans cette section, vous allez configurer le flux des données de l’utilisateur de Workday vers Active Directory.

1. Dans l’onglet Approvisionnement sous **Mappages**, cliquez sur **Synchroniser les utilisateurs Azure AD sur Workday**.

2. Dans le champ **Portée d’objet source**, vous pouvez éventuellement filtrer les ensembles d’utilisateurs dans Azure Active Directory dont les adresses e-mail sont différées dans Workday. La portée par défaut est « tous les utilisateurs dans Azure AD ». 

3. Dans la section **Mappages d’attributs**, mettez à jour l’ID correspondant pour indiquer l’attribut dans Azure Active Directory dans lequel est stocké l’ID d’employé Workday. Une méthode populaire de correspondance consiste à synchroniser l’ID de l’employé Workday avec extensionAttribute1-15 dans Azure AD puis, à utiliser cet attribut dans Azure AD pour faire à nouveau correspondre les utilisateurs dans Workday. 

4. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la section Mappage d’attributs.

### <a name="part-3-start-the-service"></a>Partie 3 : Démarrer le service
Une fois les parties 1 à 2 terminées, vous pouvez démarrer le service d’approvisionnement.

1. Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Ceci démarre la synchronisation initiale, qui peut prendre un nombre variable d’heures en fonction du nombre d’utilisateurs dans Workday.

4. Les événements de synchronisation individuels peuvent être affichés dans l’onglet **Journaux d’audit**. **[Consultez le guide de création de rapports d’approvisionnement pour obtenir des instructions détaillées sur la façon de lire les journaux d’audit](../manage-apps/check-status-user-account-provisioning.md)**

5. Une fois cette opération terminée, un rapport de synthèse de l’audit est écrit dans l’onglet **Approvisionnement**, comme indiqué ci-dessous.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Personnaliser la liste des attributs d’utilisateurs Workday
Les applications d’attribution Workday pour Active Directory et Azure AD contiennent tous les deux une liste par défaut des attributs d’utilisateurs Workday utilisables. Toutefois, ces listes ne sont pas complètes. Workday prend en charge plusieurs centaines d’attributs d’utilisateurs possibles, qui peuvent être standard ou propres au locataire Workday concerné. 

Le service d’attribution Azure AD prend en charge la possibilité de personnaliser la liste ou l’attribut Workday de façon à inclure tous les attributs exposés dans l’opération [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) de l’API Ressources humaines.

Il vous faut pour cela utiliser [Workday Studio](https://community.workday.com/studio-download) afin d’extraire les expressions XPath qui représentent les attributs à utiliser, puis les ajouter à votre configuration d’attribution à l’aide de l’éditeur d’attributs avancé du Portail Azure.

**Pour récupérer une expression XPath pour un attribut d’utilisateur Workday :**

1. Téléchargez et installez [Workday Studio](https://community.workday.com/studio-download). Vous aurez besoin d’un compte Communauté Workday pour accéder au programme d’installation.

2. Téléchargez le fichier WDSL Human_Resources Workday en accédant à l’URL suivante : https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Lancez Workday Studio.

4. Dans la barre de commandes, sélectionnez l’option **Workday > Tester le service web dans le testeur**.

5. Sélectionnez **Externe**, puis le fichier WSDL Human_Resources que vous avez téléchargé à l’étape 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Définissez le champ **Emplacement** sur `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mais en remplaçant « IMPL-CC » par le type réel de votre instance, et « TENANT » par le nom réel de votre locataire.

7. Définissez **Opération** sur **Get_Workers**.

8.  Cliquez sur le petit lien **Configurer** sous les volets Requête/Réponse pour définir vos informations d’identification Workday. Cochez **Authentification**, puis entrez le nom d’utilisateur et le mot de passe de votre compte système d’intégration Workday. Veillez à mettre le nom d’utilisateur au format name@tenant et à laisser l’option **WS-Security UsernameToken** sélectionnée.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Sélectionnez **OK**.

10. Dans le volet **Requête**, collez le code XML ci-dessous et définissez **Employee_ID** sur l’ID d’employé d’un utilisateur réel de votre locataire Workday. Sélectionnez un utilisateur dont l’attribut à extraire est rempli.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Cliquez sur la flèche verte **Envoyer la requête** pour exécuter la commande. En cas de réussite, la réponse apparaîtra dans le volet **Réponse**. Vérifiez que la réponse comporte les données de l’ID d’utilisateur que vous avez entré et non une erreur.

12. En cas de réussite, copiez le code XML du volet **Réponse** et enregistrez-le dans un fichier XML.

13. Dans la barre de commandes de Workday Studio, sélectionnez **Fichier > Ouvrir un fichier...**  et ouvrez le fichier XML que vous avez enregistré. Il s’ouvre dans l’éditeur XML de Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Dans l’arborescence des fichiers, accédez à **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** pour trouver les données de votre utilisateur. 

15. Sous **wd: Worker**, recherchez l’attribut à ajouter, puis sélectionnez-le.

16. Copiez l’expression XPath de l’attribut sélectionné hors du champ **Chemin d’accès du document**.

1. Supprimez le préfixe **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** dans l’expression copiée.

18. Si le dernier élément dans l’expression copiée est un nœud (exemple : « /wd: Birth_Date »), ajoutez **/text()** à la fin de l’expression. Ce n’est pas nécessaire si le dernier élément est un attribut (exemple : « type /@wd: »).

19. Le résultat doit se présenter ainsi : `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. C’est ce que vous allez copier sur le Portail Azure.


**Pour ajouter votre attribut d’utilisateur Workday personnalisé à votre configuration d’attribution :**

1. Lancez le [Portail Azure](https://portal.azure.com), accédez à la section Attribution de votre application d’attribution Workday, selon la procédure décrite plus haut dans ce didacticiel.

2. Définissez **État d’attribution** sur **Désactivé**, puis sélectionnez **Enregistrer**. Ainsi, les modifications ne prendront effet que lorsque vous le déciderez.

3. Sous **Mappages**, sélectionnez **Synchroniser les travailleurs en local** (ou **Synchroniser les travailleurs sur Azure AD**).

4. Faites défiler l’écran suivant jusqu’en bas, puis sélectionnez **Afficher les options avancées**.

5. Sélectionnez **Modifier la liste d’attributs de Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Faites défiler la liste d’attributs jusqu’en bas, où se trouvent les champs d’entrée.

7. Pour **Nom**, entrez le nom d’affichage de votre attribut.

8. Pour **Type**, sélectionnez le type correspondant à votre attribut (le plus courant est **Chaîne**).

9. Pour **Expression API**, entrez l’expression XPath que vous avez copiée dans Workday Studio. Exemple : `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Sélectionnez **Ajouter un attribut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Sélectionnez **Enregistrer** ci-dessus, puis **Oui** dans la boîte de dialogue. Fermez l’écran Mappage des attributs s’il est toujours ouvert.

12. Dans l’onglet **Attribution** principal, sélectionnez à nouveau **Synchroniser les travailleurs en local** (ou **Synchroniser les travailleurs sur Azure AD**).

13. Sélectionnez **Ajouter un mappage**.

14. Votre nouvel attribut devrait maintenant apparaître dans la liste **Attribut source**.

15. Ajoutez le mappage de votre choix pour votre nouvel attribut.

16. Ensuite, pensez à redéfinir **État d’attribution** sur **Activé** et à enregistrer.

## <a name="known-issues"></a>Problèmes connus

* L’écriture de données dans l’attribut utilisateur thumbnailPhoto dans Active Directory local n’est pas prise en charge.

* Le connecteur « Workday à Azure AD » n’est pas pris en charge sur les locataires Azure AD où AAD Connect est activé.  

## <a name="managing-personal-data"></a>Gestion des données personnelles

La solution de provisionnement Workday pour Active Directory nécessite l’installation d’un agent de synchronisation sur un serveur joint à un domaine. Cet agent crée des journaux dans le journal des événements Windows, qui peut contenir des informations d’identification personnelle.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
* [Découvrir comment configurer l’authentification unique entre Workday et Azure Active Directory](workday-tutorial.md)
* [Découvrir comment intégrer d’autres applications SaaS à Azure Active Directory](tutorial-list.md)

