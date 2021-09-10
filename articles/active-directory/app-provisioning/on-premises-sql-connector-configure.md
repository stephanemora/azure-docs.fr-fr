---
title: Configuration du connecteur SQL générique avec l’hôte du connecteur Azure AD ECMA
description: Ce document décrit comment configurer le connecteur SQL générique avec l’hôte du connecteur Azure AD ECMA.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437363"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Configuration du connecteur SQL générique avec l’hôte du connecteur Azure AD ECMA

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Cet article décrit comment créer un nouveau connecteur SQL avec l’hôte du connecteur Azure Active Directory (Azure AD) ECMA et comment le configurer. Vous devez effectuer cette tâche une fois que vous avez correctement installé l’hôte du connecteur Azure AD ECMA.

>[!NOTE] 
> Cet article traite uniquement de la configuration du connecteur SQL générique. Pour obtenir un exemple pas à pas de la configuration du connecteur SQL générique, consultez [Tutoriel : Connecteur SQL générique pour l’hôte du connecteur ECMA](tutorial-ecma-sql-connector.md)

 Ce flux vous guide tout au long du processus d’installation et de configuration de l’Hôte connecteur ECMA Azure AD.

 ![Diagramme montrant le flux d’installation.](./media/on-premises-sql-connector-configure/flow-1.png)

Pour plus d’informations sur l’installation et la configuration, consultez :
   - [Configurations requises pour l’hôte du connecteur Azure AD ECMA](on-premises-ecma-prerequisites.md)
   - [Installation de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-install.md)
   - [Configurer l’hôte du connecteur Azure AD ECMA et l’agent d’approvisionnement](on-premises-ecma-configure.md)

Selon les options que vous sélectionnez, certains écrans de l’Assistant peuvent ne pas être disponibles et les informations peuvent être légèrement différentes. Pour les besoins de cette configuration, le type d’objet utilisé est « utilisateur ». Utilisez les informations ci-dessous pour vous guider dans votre configuration. 

#### <a name="supported-systems"></a>Systèmes pris en charge
* Microsoft SQL Server et Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 et 11g
* Oracle 12c et 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>Créer un connecteur SQL générique

Pour créer un connecteur SQL générique :

 1. Sélectionnez le raccourci de l’hôte du connecteur ECMA sur le Bureau.
 1. Sélectionnez **Nouveau connecteur**.
 
     ![Capture d’écran montrant Choisir le nouveau connecteur.](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. Sur la page **Propriétés**, renseignez les zones et sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
 
     ![Capture d’écran montrant Entrer les propriétés.](.\media\on-premises-sql-connector-configure\sql-2.png)

     |Propriété|Description|
     |-----|-----|
     |Name|Nom de ce connecteur.|
     |Minuteur de synchronisation automatique (minutes)|La valeur minimale autorisée est de 120 minutes.|
     |Jeton secret|123456 (Ce jeton doit être une chaîne de 10 à 20 lettres et/ou chiffres ASCII.)|
     |Description|Description du connecteur.|
     |Extension DLL|Pour un connecteur SQL générique, sélectionnez **Microsoft.IAM.Connector.GenericSql.dll**.|
 1. Sur la page **Connectivité**, renseignez les zones, puis sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
 
     ![Capture d’écran montrant Entrer la connectivité.](.\media\on-premises-sql-connector-configure\sql-3.png)

     |Propriété|Description|
     |-----|-----|
     |Fichier DSN|Fichier de nom de source de données utilisé pour se connecter à l’instance SQL Server.|
     |User Name|Nom d’utilisateur d’une personne disposant de droits sur l’instance SQL Server. Il doit se présenter sous la forme de hostname\sqladminaccount pour les serveurs autonomes ou domain\sqladminaccount pour les serveurs membres du domaine.|
     |Mot de passe|Mot de passe du nom d’utilisateur venant d’être communiqués.|
     |DN est Ancre|À moins que votre environnement ne soit connu pour exiger ces paramètres, ne sélectionnez pas les cases **DN est Ancre** ni **Type d’exportation : remplacement de l’objet**.|
     |Type d’exportation : remplacement de l’objet||
 1. Sur la page **Schéma 1**, renseignez les zones et sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
 
     ![Capture d’écran montrant la page Schema 1.](.\media\on-premises-sql-connector-configure\sql-4.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode de détection du type d’objet|Méthode utilisée pour détecter le type d’objet que le connecteur approvisionnera.|
     |Liste de valeurs fixes/Table/Vue/SP|Cette zone doit contenir **Utilisateur**.|
     |Nom de colonne de Table/Vue/SP||
     |Paramètres de procédure stockée||
     |Fournir une requête SQL pour la détection des types d’objets||
 1. Sur la page **Schéma 2**, renseignez les zones et sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner. Cet écran de schéma peut être légèrement différent ou avoir des informations supplémentaires en fonction des types d’objets que vous avez sélectionnés à l’étape précédente.
 
     ![Capture d’écran montrant la page Schema 2.](.\media\on-premises-sql-connector-configure\sql-5.png)

     |Propriété|Description|
     |-----|-----|
     |Utilisateur : Détection d’attribut|Cette propriété de type doit être définie sur **Table**.|
     |Utilisateur : Table/Vue/SP|Cette zone doit contenir **Employés**.|
     |Utilisateur : Nom de Table à valeurs multiples/Vues||
     |Utilisateur : Paramètres de procédure stockée||
     |Utilisateur : Fournir une requête SQL pour la détection des attributs||
 1. Sur la page **Schéma 3**, renseignez les zones et sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner. Les attributs que vous voyez dépendent des informations fournies par vos soins à l’étape précédente.
 
     ![Capture d’écran montrant la page Schema 3.](.\media\on-premises-sql-connector-configure\sql-6.png)

     |Propriété|Description|
     |-----|-----|
     |Sélectionner l’attribut DN pour Utilisateur||
 1. Sur la page **Schéma 4**, examinez l’attribut **DataType** et la direction du flux pour le connecteur. Vous pouvez les modifier si nécessaire, puis sélectionner **Suivant**.
 
     ![Capture d’écran montrant la page Schema 4.](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. Dans la page **General**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
 
     ![Capture d’écran qui montre la page Global.](.\media\on-premises-sql-connector-configure\sql-8.png)

     |Propriété|Description|
     |-----|-----|
     |Requête de filigrane||
     |Fuseau horaire de la source de données|Sélectionnez le fuseau horaire dans lequel se trouve la source de données.|
     |Format date/heure de la source de données|Spécifiez le format de la source de données.|
     |Utiliser des paramètres nommés pour exécuter une procédure stockée||
     |Méthodes d’opération||
     |Nom de l’extension||
     |Définir le nom du SP de mot de passe||
     |Définir les paramètres du SP de mot de passe||
 1. Sur la page **Sélectionner une partition**, assurez-vous que les partitions appropriées sont sélectionnées, puis sélectionnez **Suivant**.
 
     ![Capture d’écran montrant la page Sélectionner une partition.](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. Sur la page **Profils d’exécution**, sélectionnez les profils d’exécution que vous souhaitez utiliser, puis sélectionnez **Suivant**.
 
     ![Capture d’écran montrant la page Run Profiles.](.\media\on-premises-sql-connector-configure\sql-10.png)

     |Propriété|Description|
     |-----|-----|
     |Exporter|Profil d’exécution qui exportera les données vers SQL. Ce profil d’exécution est obligatoire.|
     |Importation intégrale|Profil d’exécution qui importera toutes les données des sources SQL spécifiées précédemment.|
     |Importation différentielle|Profil d’exécution qui importera uniquement les modifications apportées aux sources SQL depuis la dernière importation complète ou différentielle.|
 
 1. Sur la page **Profils d’exécution**, renseignez les zones et sélectionnez **Suivant**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
  
     ![Capture d’écran montrant Entrer les informations d’exportation.](.\media\on-premises-sql-connector-configure\sql-11.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode d’opération||
     |Table/Vue/SP||
     |Nom du paramètre d’index de début||
     |Nom du paramètre d’index de fin||
     |Paramètres de procédure stockée||
 
 1. Dans la page **Object Types**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner. 
 
     ![Capture d’écran montrant la page Object Types.](.\media\on-premises-sql-connector-configure\sql-12.png)

     |Propriété|Description|
     |-----|-----|
     |Objet cible|Objet que vous êtes en train de configurer.|
     |Ancre|Attribut qui sera utilisé comme ancre des objets. Cet attribut doit être unique dans le système cible. Le service de provisionnement Azure AD interrogera l’hôte ECMA à l’aide de cet attribut après le cycle initial. Cette valeur d’ancre doit être la même que celle du schéma 3.|
     |Query Attribute|Utilisé par l’hôte ECMA pour interroger le cache en mémoire. Cet attribut doit être unique.|
     |DN|Attribut utilisé pour le nom unique des objets cibles. La case **Généré automatiquement** doit être cochée dans la plupart des cas. Si elle ne l’est pas, vérifiez que l’attribut DN est mappé à un attribut dans Azure AD qui stocke le DN au format suivant : CN = anchorValue, Object = objectType.|
 
 1. L’hôte ECMA découvre les attributs pris en charge par le système cible. Parmi eux, vous pouvez choisir ceux que vous souhaitez exposer à Azure AD. Ces attributs peuvent ensuite être configurés dans le portail Azure pour l’approvisionnement. Sur la page **Sélectionner les attributs**, sélectionnez dans la liste déroulante les attributs à ajouter.
 
     ![Capture d’écran montrant la page Select Attributes.](.\media\on-premises-sql-connector-configure\sql-13.png)

1. Sur la page **Suppression des privilèges d’accès**, examinez les informations relatives à la suppression des privilèges d’accès et apportez les modifications nécessaires. Les attributs sélectionnés sur la page précédente ne pourront pas être sélectionnés sur la page **Suppression les privilèges d’accès**. Sélectionnez **Terminer**.

     ![Capture d’écran montrant la page Deprovisioning.](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
- [Installation de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-install.md)
- [Configuration de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-configure.md)
- [Tutoriel : Connecteur SQL générique pour l’Hôte de connecteur ECMA](tutorial-ecma-sql-connector.md)
