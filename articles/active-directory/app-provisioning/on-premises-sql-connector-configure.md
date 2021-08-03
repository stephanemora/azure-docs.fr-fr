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
ms.openlocfilehash: 0748a1b3b326ce8c731d599471c3f96346a815c2
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570157"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Configuration du connecteur SQL générique avec l’hôte du connecteur Azure AD ECMA

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Vous pouvez demander l’accès à cette fonctionnalité [ici](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.


Ce document décrit comment créer un nouveau connecteur SQL avec l’hôte du connecteur Azure AD ECMA et comment le configurer.  Vous devrez effectuer cette opération après avoir installé l’hôte du connecteur Azure AD ECMA.  

>[!NOTE] 
> Ce document traite uniquement de la configuration du connecteur SQL générique.  Pour obtenir un exemple pas à pas de la configuration du connecteur SQL générique, consultez [Tutoriel : Connecteur SQL générique pour l’hôte du connecteur ECMA](tutorial-ecma-sql-connector.md).

L’installation et la configuration de l’hôte du connecteur Azure AD ECMA sont un processus. Utilisez le flux ci-dessous pour vous guider tout au long du processus.

 ![Flux d’installation](./media/on-premises-sql-connector-configure/flow-1.png)  

Pour plus d’informations sur l’installation et la configuration, consultez :
   - [Configurations requises pour l’hôte du connecteur Azure AD ECMA](on-premises-ecma-prerequisites.md)
   - [Installation de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-install.md)
   - [Configurer l’hôte du connecteur Azure AD ECMA et l’agent d’approvisionnement](on-premises-ecma-configure.md)

Selon les options que vous sélectionnez, certains écrans de l’Assistant peuvent ne pas être disponibles et les informations peuvent être légèrement différentes.  Pour les besoins de cette configuration, le type d’objet utilisé est « utilisateur ». Utilisez les informations ci-dessous pour vous guider dans votre configuration.


## <a name="create-a-generic-sql-connector"></a>Créer un connecteur SQL générique

Pour créer un connecteur SQL générique, procédez comme suit :

 1.  Cliquez sur le raccourci de l’hôte du connecteur ECMA sur le bureau.
 2.  Sélectionnez **Nouveau connecteur**.
     ![Choisir Nouveau connecteur](.\media\on-premises-sql-connector-configure\sql-1.png)

 3. Sur la page **Propriétés**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.
     ![Entrer les propriétés](.\media\on-premises-sql-connector-configure\sql-2.png)

     |Propriété|Description|
     |-----|-----|
     |Nom|Nom de ce connecteur|
     |Minuteur de synchronisation automatique (minutes)|La valeur minimale autorisée est de 120 minutes.|
     |Jeton secret|123456 (Il doit s’agir d’une chaîne de 10 à 20 lettres et/ou chiffres ASCII.)|
     |Description|Description du connecteur|
     |Extension DLL|Pour un connecteur SQL générique, sélectionnez Microsoft.IAM.Connector.GenericSql.dll.|
 4. Sur la page **Connectivité**, renseignez les zones, puis cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.
     ![Entrer la connectivité](.\media\on-premises-sql-connector-configure\sql-3.png)

     |Propriété|Description|
     |-----|-----|
     |Fichier DSN|Fichier de nom de source de données utilisé pour se connecter au serveur SQL|
     |User Name|Nom d’utilisateur d’une personne disposant de droits sur le serveur SQL.  Il doit se présenter sous la forme de hostname\sqladminaccount pour les serveurs autonomes ou domain\sqladminaccount pour les serveurs membres du domaine.|
     |Mot de passe|Mot de passe du nom d’utilisateur fourni ci-dessus.|
     |DN est Ancre|À moins que votre environnement ne soit connu pour exiger ces paramètres, laissez « DN est Ancre » et « Type d’exportation: Remplacement d’objet » désélectionnées.|
     |Type d’exportation : Remplacement d’objet||
 5. Sur la page **Schéma 1**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.
     ![Entrer le schéma 1](.\media\on-premises-sql-connector-configure\sql-4.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode de détection du type d’objet|Méthode utilisée pour détecter le type d’objet que le connecteur approvisionnera.|
     |Liste de valeurs fixes/Table/Vue/SP|Doit contenir « Utilisateur ».|
     |Nom de colonne de Table/Vue/SP||
     |Paramètres de procédure stockée||
     |Fournir une requête SQL pour la détection des types d’objets||
 6. Sur la page **Schéma 2**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.  Cet écran de schéma peut être légèrement différent ou avoir des informations supplémentaires en fonction des types d’objets sélectionnés à l’étape précédente.
     ![Entrer le schéma 2](.\media\on-premises-sql-connector-configure\sql-5.png)

     |Propriété|Description|
     |-----|-----|
     |Utilisateur : Détection d’attribut|Doit être défini sur Table.|
     |Utilisateur : Table/Vue/SP|Doit contenir « Employés ».|
     |Utilisateur : Nom de Table à valeurs multiples/Vues||
     |Utilisateur : Paramètres de procédure stockée||
     |Utilisateur : Fournir une requête SQL pour la détection des types d’objets||
 7. Sur la page **Schéma 3**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.  Les attributs que vous voyez dépendent des informations fournies à l’étape précédente.
     ![Entrer le schéma 3](.\media\on-premises-sql-connector-configure\sql-6.png)

     |Propriété|Description|
     |-----|-----|
     |Sélectionner l’attribut DN pour Utilisateur||
 8. Sur la page **Schéma 4**, examinez les attributs Type de données et Direction du flux pour le connecteur.  Vous pouvez les modifier si nécessaire, puis cliquez sur suivant.
     ![Entrer le schéma 4](.\media\on-premises-sql-connector-configure\sql-7.png)  
 9. Sur la page **Général**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir.
     ![Entrer les informations générales](.\media\on-premises-sql-connector-configure\sql-8.png)

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
 10. Sur la page **Sélectionner une partition**, assurez-vous que les partitions appropriées sont sélectionnées, puis cliquez sur Suivant.
     ![Entrer les informations sur la partition](.\media\on-premises-sql-connector-configure\sql-9.png)  

 11. Sur la page **Profils d’exécution**, sélectionnez les profils d’exécution que vous souhaitez utiliser, puis cliquez sur Suivant.
     ![Entrer les profils d’exécution](.\media\on-premises-sql-connector-configure\sql-10.png)

     |Propriété|Description|
     |-----|-----|
     |Exporter|Profil d’exécution qui exportera les données vers SQL.  Ce profil d’exécution est obligatoire.|
     |Importation intégrale|Profil d’exécution qui importera toutes les données des sources SQL spécifiées précédemment.|
     |Importation différentielle|Profil d’exécution qui importera uniquement les modifications apportées aux sources SQL depuis la dernière importation complète ou différentielle.|
 
 12. Sur la page **Profils d’exécution**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir. 
     ![Entrer les informations d’exportation](.\media\on-premises-sql-connector-configure\sql-11.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode d’opération||
     |Table/Vue/SP||
     |Nom du paramètre d’index de début||
     |Nom du paramètre d’index de fin||
     |Paramètres de procédure stockée||
 
 13. Sur la page **Types d’objet**, renseignez les zones et cliquez sur Suivant.  Utilisez le tableau sous l’image pour obtenir des conseils concernant les différentes zones à remplir. 
     ![Entrer les types d’objets](.\media\on-premises-sql-connector-configure\sql-12.png)

     |Propriété|Description|
     |-----|-----|
     |Objet cible|Objet que vous êtes en train de configurer.|
     |Ancre|Attribut qui sera utilisé comme ancre des objets.  Cet attribut doit être unique dans le système cible. Le service d’approvisionnement d’Azure AD interrogera l’hôte ECMA à l’aide de cet attribut après le cycle initial. Cette valeur d’ancre doit être la même que celle du schéma 3.|
     |Attribut de requête|Utilisé par l’hôte ECMA pour interroger le cache en mémoire. Cet attribut doit être unique.|
     |DN|Attribut utilisé pour le nom unique des objets cibles.  L’option de génération automatique doit être sélectionnée dans la plupart des cas. Si cette option est désélectionnée, assurez-vous que l’attribut DN est mappé à un attribut dans Azure AD qui stocke le DN au format suivant : CN = anchorValue, Object = objectType.|
 
 14. L’hôte ECMA découvre les attributs pris en charge par le système cible. Parmi eux, vous pouvez choisir ceux que vous souhaitez exposer à Azure AD. Ces attributs peuvent ensuite être configurés dans le portail Azure pour l’approvisionnement.  Sur la page **Sélectionner les attributs**, sélectionnez dans la liste déroulante les attributs à ajouter. 
     ![Entrer les attributs](.\media\on-premises-sql-connector-configure\sql-13.png)

15. Sur la page **Suppression des privilèges d’accès**, examinez les informations relatives à la suppression des privilèges d’accès et apportez les modifications nécessaires. Les attributs sélectionnés sur la page précédente ne pourront pas être sélectionnés sur la page de suppression des privilèges d’accès. Cliquez sur Finish.
     ![Entrer les informations relatives à la suppression des privilèges d’accès](.\media\on-premises-sql-connector-configure\sql-14.png)



## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
- [Installation de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-install.md)
- [Configuration de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-configure.md)
- [Didacticiel : Connecteur SQL générique pour l’hôte du connecteur ECMA](tutorial-ecma-sql-connector.md)
