---
title: Solution SAP Azure Sentinel - Informations de référence relatives au contenu de sécurité | Microsoft Docs
description: En savoir plus sur le contenu de sécurité intégré fourni par la solution SAP Azure Sentinel.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 05/12/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: dc5aa5da39e25a8db899b84a0410f07c480ee3e1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815172"
---
# <a name="azure-sentinel-sap-solution-security-content-reference-public-preview"></a>Solution SAP Azure Sentinel : Informations de référence relatives au contenu de sécurité (préversion publique)

Cet article détaille le contenu de sécurité disponible pour la [solution SAP Azure Sentinel](sap-deploy-solution.md#deploy-sap-security-content).

Le contenu de sécurité disponible comprend un classeur intégré et des règles analytiques intégrées. Vous pouvez également ajouter des [watchlists](watchlists.md) liées à SAP à utiliser dans votre recherche, vos règles de détection, votre chasse des menaces et vos playbooks de réponse.

> [!IMPORTANT]
> La solution SAP Azure Sentinel est actuellement en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>


## <a name="sap---system-applications-and-products-workbook"></a>Classeur SAP - Applications et produits système

Utilisez le classeur [SAP - Applications et produits système](sap-deploy-solution.md#deploy-sap-security-content) pour visualiser et surveiller les données ingérées via le connecteur de données SAP.

Exemple :

:::image type="content" source="media/sap/sap-workbook.png" alt-text="Classeur SAP - Applications et produits système.":::

Pour plus d’informations, consultez [Tutoriel : Visualisation et supervision des données](tutorial-monitor-your-data.md).

## <a name="built-in-analytics-rules"></a>Règles analytiques intégrées

Les tableaux suivants répertorient les [règles analytiques](sap-deploy-solution.md#deploy-sap-security-content) intégrées incluses dans la solution SAP Azure Sentinel, déployées à partir de la place de marché Solution Azure Sentinel.

### <a name="high-level-built-in-sap-solution-analytics-rules"></a>Règles analytiques de solutions SAP intégrées de haut niveau

|Nom de la règle  |Description  |Action source  |Tactique  |
|---------|---------|---------|---------|
|**SAP - Haut niveau - Modification d’un utilisateur sensible disposant de privilèges**     |  Identifie les modifications d’utilisateurs sensibles disposant de privilèges.    <br> <br>Gérez les utilisateurs disposant de privilèges dans la watchlist [SAP - Utilisateurs disposant de privilèges](#users). |   Modifiez les informations d’utilisateur et les autorisations à l’aide de `SU01`. <br><br>**Source de données** : SAPcon - Journal d'audit     |  Réaffectation de privilèges, accès aux informations d’identification       |
|**SAP - Haut niveau - Modification de la configuration du client**     |     Identifie les modifications apportées à la configuration du client, notamment le rôle du client ou le mode d’enregistrement des modifications.    |  Modifiez la configuration du client à l’aide du code de transaction `SCC4`. <br><br>**Source de données** : SAPcon - Journal d'audit  |    Évasion de défense, exfiltration, persistance     |
|**SAP - Haut niveau - Données modifiées durant l’activité de débogage**     | Identifie les modifications des données du runtime pendant une activité de débogage.         | 1. Activez le débogage (« /h »). <br>2. Sélectionnez un champ à modifier et mettre à jour sa valeur.<br><br>**Source de données** : SAPcon - Journal d'audit        |  Mouvement latéral, exécution       |
|**SAP - Haut niveau - Désactivation du journal d’audit de sécurité**     | Identifie la désactivation du journal d’audit de sécurité.        |  Désactivez le journal d’audit de sécurité à l’aide de `SM19/RSAU_CONFIG`. <br><br>**Source de données** : SAPcon - Journal d'audit |   Exfiltration, évasion de défense, persistance      |
|**SAP - Haut niveau - Exécution d’un programme ABAP sensible**     |Identifie l’exécution directe d’un programme ABAP sensible. <br><br>Gérez les programmes ABAP dans le watchlist [SAP - Programmes ABAP sensibles](#programs).        | Exécutez un programme directement à l’aide de `SE38`/`SA38`/`SE80`. <br> <br>**Source de données** : SAPcon - Journal d'audit      |     Exfiltration, mouvement latéral, exécution    |
|**SAP - Haut niveau - Exécution d’un code de transaction sensible**     | Identifie l’exécution d’un code de transaction sensible. <br><br>Gérez les codes de transaction dans la watchlist [SAP - Codes de transactions sensibles](#transactions).       |  Exécutez un code de transaction sensible. <br><br>**Source de données** : SAPcon - Journal d'audit      |    Découverte, exécution     |
|**SAP - Haut niveau - Module de fonction testé**     |  Identifie le test d’un module de fonction.       |   Testez un module de fonction à l’aide de `SE37` / `SE80`.  <br><br>**Source de données** : SAPcon - Journal d'audit    |   Collection, évasion de défense, mouvement latéral      |
|**SAP - Haut niveau - HANA DB - Attribuer des autorisations d’administrateur**     |   Identifie des privilèges d’administrateur ou une attribution de rôle.      |  Affectez à un utilisateur un rôle d’administrateur ou des privilèges.  <br><br>**Sources de données** : Agent Linux - Syslog     | Réaffectation de privilèges        |
|**SAP - Haut niveau - HANA DB - Modifications de la stratégie de piste d’audit**     |  Identifie les modifications pour les stratégies de piste d’audit HANA DB.       |     Créez ou mettez à jour la stratégie d’audit existante dans les définitions de sécurité. <br> <br>**Sources de données** : Agent Linux - Syslog    |  Mouvement latéral, évasion de la défense, persistance       |
|**SAP - Haut niveau - HANA DB - Désactivation de la piste d’audit**     |    Identifie la désactivation du journal d’audit HANA DB.     |    Désactivez le journal d’audit dans la définition de sécurité HANA DB. <br><br>**Sources de données** : Agent Linux - Syslog     |  Persistance, mouvement latéral, évasion de défense       |
|  **SAP - Haut niveau - HANA DB-Actions d’administration de l’utilisateur**   | Identifie les actions d’administration de l’utilisateur.        |  Créez, mettez à jour ou supprimez un utilisateur de base de données. <br><br>**Sources de données** : Agent Linux - Syslog*       |Réaffectation de privilèges         |
|**SAP - Haut niveau - Connexion à partir d’un réseau inattendu**     |   Identifie une connexion à partir d’un réseau inattendu. <br><br>Gérez les réseaux dans la watchlist [SAP - Réseaux](#networks).    |    Connectez-vous au système principal à partir d’une adresse IP qui n’est pas affectée à l’un des réseaux. <br><br>**Source de données** : SAPcon - Journal d'audit    |   Accès initial      |
|**SAP - Haut niveau - Exécution RFC d’un module de fonction sensible**     | Les modèles de fonction sensibles à utiliser dans les détections appropriées.    <br><br>Gérez les modules de fonction dans la watchlist [Modules de fonction SAP sensibles](#module).   |      Exécutez un module de fonction à l’aide de RFC.  <br><br>**Source de données** : SAPcon - Journal d'audit |  Exécution, mouvement latéral, découverte       |
|**SAP - Haut niveau - Utilisateur sensible disposant de privilèges et connecté**     |    Identifie la connexion d’un utilisateur sensible disposant de privilèges. <br><br>Gérez les utilisateurs disposant de privilèges dans la watchlist [SAP - Utilisateurs disposant de privilèges](#users).    |  Connectez-vous au système principal à l’aide de `SAP*` ou d’un autre utilisateur privilégié.  <br><br>**Source de données** : SAPcon - Journal d'audit     |   Accès initial, accès aux informations d’identification      |
|  **SAP - Haut niveau - Un utilisateur sensible disposant de privilèges apporte une modification à un autre utilisateur**   |   Identifie les modifications d’utilisateurs sensibles disposant de privilèges dans d’autres utilisateurs.       | Modifiez les informations d’utilisateur/autorisations à l’aide de SU01.  <br><br>**Source de données** : SAPcon - Journal d'audit     |   Réaffectation de privilèges, accès aux informations d’identification       |
|**SAP - Haut niveau - Modification de la configuration système**     | Identifie les modifications apportées à la configuration système.        |   Adaptez les options de modification du système ou la modification des composants logiciels à l’aide du code de transaction `SE06`.<br><br>**Source de données** : SAPcon - Journal d'audit |Exfiltration, évasion de défense, persistance   |
|     |         |         |         |

### <a name="medium-level-built-in-sap-solution-analytics-rules"></a>Règles analytiques de solutions SAP intégrées de niveau moyen

|Nom de la règle  |Description  |Action source  |Tactique  |
|---------|---------|---------|---------|
|**SAP - Niveau moyen - Attribution d’un profil sensible**     |  Identifie les nouvelles affectations d’un profil sensible à un utilisateur. <br><br>Gérez les profils sensibles dans la watchlist [SAP - Profils sensibles](#profiles).      |    Affectez un profil à un utilisateur à l’aide de `SU01`. <br><br>**Sources de données** : SAPcon - Journal des documents de modification    |  Réaffectation de privilèges       |
|**SAP - Niveau moyen - Attribution d’un rôle sensible**     |    Identifie les nouvelles affectations pour un rôle sensible à un utilisateur.     <br><br>Gérez les rôles sensibles dans la watchlist [SAP - Rôles sensibles](#roles).|  Affectez un rôle à un utilisateur à l’aide de `SU01` / `PFCG`. <br><br>**Sources de données** : SAPcon - Journal des documents de modification, journal d'audit     |   Réaffectation de privilèges      |
|**SAP - Niveau moyen - Attaques par force brute**     |     Identifie les attaques par force brute sur le système SAP en fonction des tentatives de connexion ayant échoué pour le système principal.    |   Essayez de vous connecter à partir de la même adresse IP à plusieurs systèmes/clients dans l’intervalle de temps planifié. <br><br>**Source de données** : SAPcon - Journal d'audit      | Accès aux informations d’identification        |
|**SAP - Niveau moyen - Affectation d’autorisations critiques - Nouvelle valeur d’autorisation**     | Identifie l’affectation d’une valeur d’objet d’autorisation critique à un nouvel utilisateur.  <br><br>Gérez les objets d’autorisation critiques dans la watchlist [SAP - Objets d’autorisation critiques](#objects).      |  Affectez un nouvel objet d’autorisation ou mettez à jour un objet existant dans un rôle à l’aide de `PFCG`. <br><br>**Sources de données** : SAPcon - Journal des documents de modification      |     Réaffectation de privilèges    |
|**SAP - Niveau moyen - Affectation d’autorisations critiques - Nouvelle affectation d’utilisateur**     |  Identifie l’affectation d’une valeur d’objet d’autorisation critique à un nouvel utilisateur. <br><br>Gérez les objets d’autorisation critiques dans la watchlist [SAP - Objets d’autorisation critiques](#objects).    |    Affectez un nouvel utilisateur à un rôle qui contient des valeurs d’autorisation critiques, à l’aide de `SU01`/`PFCG`. <br><br>**Sources de données** : SAPcon - Journal des documents de modification    |  Réaffectation de privilèges       |
|**SAP - Niveau moyen - Activités de débogage**     |  Identifie toutes les activités associées au débogage.       |Activez le débogage (« /h ») dans le système, déboguez un processus actif, ajoutez un point d’arrêt au code source, etc. <br><br>**Source de données** : SAPcon - Journal d'audit |   Découverte      |
|**SAP - Niveau moyen - Plusieurs connexions par adresse IP**     |  Identifie la connexion de plusieurs utilisateurs à partir d’une même adresse IP dans un intervalle de temps planifié.       |    Connectez-vous à l’aide de plusieurs utilisateurs via la même adresse IP. <br><br>**Source de données** : SAPcon - Journal d'audit | Accès initial        |
|**SAP - Niveau moyen - Plusieurs connexions par utilisateur**     | Identifie les connexions du même utilisateur à partir de plusieurs terminaux dans un intervalle de temps planifié.  <br><br>Disponible uniquement via la méthode Audit SAL, pour les versions SAP 7.5 et ultérieures.      |   Connectez-vous à l’aide du même utilisateur, en utilisant des adresses IP différentes.   <br><br>**Source de données** : SAPcon - Journal d'audit   |  Pré-attaque, accès aux informations d’identification, accès initial, collection       |
|**SAP - Niveau moyen - Modification de la configuration du journal d'audit de sécurité**     |  Identifie les modifications apportées à la configuration du journal d’audit de sécurité       |   Modifiez la configuration du journal d’audit de sécurité à l’aide de `SM19`/`RSAU_CONFIG`, comme les filtres, l’état, le mode d’enregistrement, etc. <br><br>**Source de données** : SAPcon - Journal d'audit      |    Persistence, exfiltration, évasion de défense     |
|**SAP - Niveau moyen - La transaction est déverrouillée**     |Identifie le déverrouillage d’une transaction.         |     Déverrouillez un code de transaction à l’aide de `SM01`/`SM01_DEV`/`SM01_CUS`. <br><br>**Source de données** : SAPcon - Journal d'audit   |  Persistance, exécution       |
|     |         |         |         |

### <a name="low-level-built-in-sap-solution-analytics-rules"></a>Règles analytiques de solutions SAP intégrées de bas niveau

|Nom de la règle  |Description  |Action source  |Tactique  |
|---------|---------|---------|---------|
|**SAP - Bas niveau - Plusieurs modifications de mot de passe par l’utilisateur**     |   Identifie plusieurs modifications de mot de passe par l’utilisateur.      |   Modifier le mot de passe de l'utilisateur <br><br>**Source de données** : SAPcon - Journal d'audit      |    Accès aux informations d’identification     |
|**SAP - Bas niveau - Accès direct aux tables sensibles avec connexion par boîte de dialogue**     |   Identifie l’accès à une table générique via une connexion par boîte de dialogue.      |  Ouvrez le contenu de la table à l’aide de `SE11`/`SE16`/`SE16N`. <br><br>**Source de données** : SAPcon - Journal d'audit      |    Découverte     |
|     |         |         |         |

## <a name="available-watchlists"></a>Watchlists disponibles

Le tableau suivant répertorie les [watchlists](sap-deploy-solution.md#deploy-sap-security-content) disponibles pour la solution SAP Azure Sentinel et les champs de chaque watchlist.

Ces watchlists fournissent la configuration pour la solution Monitoring continu des menaces pour SAP Azure Sentinel, et sont accessibles dans le référentiel GitHub Azure Sentinel à l’adresse https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists.


|Nom de la watchlist  |Description et champs  |
|---------|---------|
|<a name="objects"></a>**SAP - Objets d’autorisation critiques**     |  Objet d’autorisation critique pour lequel les affectations doivent être régies.     <br><br>- **AuthorizationObject** : objet d’autorisation SAP, par exemple `S_DEVELOP`, `S_TCODE` ou `Table TOBJ` <br>- **AuthorizationField** : champ d’autorisation SAP, par exemple `OBJTYP` ou `TCD`    <br>- **AuthorizationValue** : valeur de champ d’autorisation SAP, par exemple `DEBUG`       <br>- **ActivityField** : champ d’activité SAP. Dans la plupart des cas, cette valeur est `ACTVT`. Pour les objets d’autorisation sans **activité**, ou avec un seul champ d’**activité** rempli avec `NOT_IN_USE`.        <br>- **Activity** : activité SAP, selon l’objet d’autorisation, par exemple : `01`: Créer ; `02` : Modifier ; `03`: Afficher, etc.      <br>- **Description** : description explicite d’un objet d’autorisation critique. |
|**SAP - Réseaux exclus** | Pour la maintenance interne de réseaux exclus, par exemple pour ignorer les répartiteurs Web, les serveurs Terminal Server, etc. <br><br>-**Network** : adresse ou plage d’adresses IP de réseau, par exemple `111.68.128.0/17`. <br>-**Description** : description explicite du réseau.|
|**Utilisateurs SAP exclus** |Utilisateurs système connectés au système et qui doivent être ignorés. Par exemple, les alertes pour plusieurs connexions par le même utilisateur. <br><br>- **User** : utilisateur SAP <br>-**Description** : description explicite de l’utilisateur. |
|<a name="networks"></a>**SAP - Réseaux**     |  Réseaux internes et de maintenance pour l’identification des connexions non autorisées.      <br><br>- **Network** : adresse ou plage d’adresses IP de réseau, par exemple `111.68.128.0/17`     <br>- **Description** : description explicite du réseau.|
|<a name="users"></a>**SAP - Utilisateurs disposant de privilèges**     |   Utilisateurs disposant de privilèges et soumis à des restrictions supplémentaires.  <br><br>- **User** : l’utilisateur ABAP, par exemple `DDIC` ou `SAP` <br>- **Description** : description explicite de l’utilisateur. |
|<a name= "programs"></a>**SAP - Programmes ABAP sensibles**     |      Programmes ABAP sensibles (rapports) pour lesquels l’exécution doit être régie.   <br><br>- **ABAPProgram** : programme ou rapport ABAP, par exemple `RSPFLDOC`     <br>- **Description** :  description explicite du programme.|
|<a name="module"></a>**SAP - Module de fonction sensible**     |   Réseaux internes et de maintenance pour l’identification des connexions non autorisées.      <br><br>- **FunctionModule** : module de fonction ABAP, tel que `RSAU_CLEAR_AUDIT_LOG`       <br>- **Description** : description explicite du module.     |
|<a name="profiles"></a>**SAP - Profils sensibles**     |  Profils sensibles pour lesquels les affectations doivent être régies.     <br><br>- **Profile** : profil d’autorisation SAP, par exemple `SAP_ALL` ou `SAP_NEW`      <br>- **Description** :  description explicite du profil.|
|<a name="tables"></a>**SAP - Tables sensibles**     |  Tables sensibles, où l’accès doit être régi.  <br><br>- **Table** : table de dictionnaires ABAP, par exemple `USR02` ou `PA008` <br>- **Description** : description explicite de la table. |
|<a name="roles"></a>**SAP - Rôles sensibles**     |  Rôles sensibles pour lesquels l’affectation doit être régie.    <br><br>- **Role** : rôle d’autorisation SAP, par exemple `SAP_BC_BASIS_ADMIN`  <br>- **Description** : description explicite du rôle. |
|<a name="transactions"></a>**SAP - Transactions sensibles**     |     Transactions sensibles pour lesquelles l’exécution doit être régie.  <br><br>- **TransactionCode** : code de transaction SAP, par exemple `RZ11` <br>- **Description** : description explicite du code. |
|<a name="systems"></a>**SAP - Systèmes**     |    Décrit le paysage des systèmes SAP en fonction du rôle et de l’utilisation.<br><br>- **SystemID** : ID du système SAP (SYSID) <br>- **SystemRole** : rôle du système SAP, indiqué par l’une des valeurs suivantes : `Sandbox`, `Development`, `Quality Assurance`, `Training`, `Production` <br>- **SystemUsage** : utilisation du système SAP, indiquée par l’une des valeurs suivantes : `ERP`, `BW`, `Solman`, `Gateway`, `Enterprise Portal`        |
| | |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Tutoriel : Déployer la solution Azure Sentinel pour SAP](sap-deploy-solution.md)
- [Informations de référence sur les journaux de la solution SAP Azure Sentinel](sap-solution-log-reference.md)
- [Déployer le connecteur de données SAP Azure Sentinel localement](sap-solution-deploy-alternate.md)
- [Exigences de SAP détaillées pour la solution SAP Azure Sentinel](sap-solution-detailed-requirements.md)