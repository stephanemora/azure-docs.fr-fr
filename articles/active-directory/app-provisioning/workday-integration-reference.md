---
title: Référence sur l’intégration d’Azure Active Directory et de Workday
description: Présentation technique approfondie de l’approvisionnement piloté par Workday RH
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104329"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Comment de l’approvisionnement Azure Active Directory s’intègre avec Workday

Le [service d’approvisionnement d’utilisateurs Azure Active Directory](../app-provisioning/user-provisioning.md) s’intègre avec [Workday HCM](https://www.workday.com) pour gérer le cycle de vie des identités des utilisateurs. Azure Active Directory propose trois intégrations prédéfinies : 

* [Approvisionnement d’utilisateurs de Workday vers Active Directory local](../saas-apps/workday-inbound-tutorial.md)
* [Approvisionnement d’utilisateurs de Workday vers Azure Active Directory](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

Cet article explique le fonctionnement de l’intégration et comment vous pouvez personnaliser le comportement d’approvisionnement pour différents scénarios de RH. 

## <a name="establishing-connectivity"></a>Établir une connectivité 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Restriction de l’accès de l’API Workday aux points de terminaison Azure AD
Le service d’approvisionnement Azure AD utilise l’authentification de base pour se connecter aux points de terminaison de l’API de services web Workday.  

Pour sécuriser davantage la connectivité entre le service d’approvisionnement Azure AD et Workday, vous pouvez restreindre l’accès afin que l’utilisateur du système d’intégration désigné accède uniquement aux API Workday à partir de plages d’adresses IP Azure AD autorisées. Demandez à votre administrateur Workday d’effectuer la configuration suivante dans votre locataire Workday. 

1. Téléchargez les [dernières plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=56519) pour le cloud public Azure. 
1. Ouvrez le fichier et recherchez la balise **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Plage d’adresses IP Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copiez toutes les plages d’adresses IP répertoriées dans l’élément *addressPrefixes*, et utilisez la plage pour créer votre liste d’adresses IP.
1. Connectez-vous au portail d’administration de Workday. 
1. Accédez à la tâche **Maintain IP Ranges** (conserver les plages d’adresses IP) pour créer une plage d’adresses IP pour les centres de données Azure. Spécifiez les plages d’adresses IP (à l’aide d’une notation CIDR) sous la forme d’une liste séparée par des virgules.  
1. Accédez à la tâche **Manage Authentication Policies** (Gérer les stratégies d’authentification) pour créer une stratégie d’authentification. Dans la stratégie d’authentification, utilisez une liste d’autorisation d’authentification pour spécifier la plage d’adresses IP Azure AD et le groupe de sécurité qui seront autorisés à accéder à partir de cette plage d’adresses IP. Enregistrez les modifications. 
1. Accédez à la tâche **Activate All Pending Authentication Policy Changes** (Activer toutes les modifications de stratégie d'authentification en attente) pour confirmer les modifications.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Limitation de l’accès aux données des employés dans Workday à l’aide de groupes de sécurité avec contraintes

La procédure par défaut pour [configurer l’utilisateur du système d’intégration Workday](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) accorde l’accès pour récupérer tous les utilisateurs dans votre locataire Workday. Dans certains scénarios d’intégration, vous pouvez limiter l’accès de sorte que seuls les utilisateurs appartenant à certaines organisations de supervision soient retournés par l’appel d’API Get_Workers et traités par le connecteur Azure AD Workday. 

Vous pouvez satisfaire à cette exigence en travaillant avec votre administrateur Workday et en configurant des groupes de sécurité du système d’intégration avec contraintes. Pour plus d’informations sur la procédure à suivre, consultez [cet article de la communauté Workday](https://community.workday.com/forums/customer-questions/620393) (*les informations d’identification de la communauté Workday sont requises pour accéder à cet article*)

Cette stratégie de limitation de l’accès à l’aide de groupes de sécurité du système d’intégration (ISSG) avec contraintes est particulièrement utile dans les scénarios suivants : 
* **Scénario de déploiement progressif** : vous avez un locataire Workday de grande taille et vous envisagez d’effectuer un déploiement progressif de Workday vers l’approvisionnement automatique Azure AD. Dans ce scénario, au lieu d’exclure les utilisateurs qui ne figurent pas dans l’étendue de la phase actuelle avec des filtres d’étendue Azure AD, nous vous recommandons de configurer des ISSG avec contraintes de façon à ce que seuls les employés figurant dans l’étendue soient visibles pour Azure AD.
* **Scénarios de travaux d’approvisionnement multiples** : vous avez un locataire Workday de grande taille et plusieurs domaines AD prenant chacun en charge une unité commerciale, division ou société distincte. Pour prendre en charge cette topologie, vous pouvez exécuter plusieurs travaux d’approvisionnement de Workday vers Azure AD, chaque travail approvisionnant un ensemble spécifique d’employés. Dans ce scénario, au lieu d’utiliser des filtres d’étendue Azure AD pour exclure des données d’employés, nous vous recommandons de configurer des ISSG avec contraintes de façon à ce que seules les données d’employés pertinentes soient visibles pour Azure AD. 

### <a name="workday-test-connection-query"></a>Requête de test de connexion à Workday

Pour tester la connectivité à Workday, Azure AD envoie la demande de services web Workday *Get_Workers* suivante. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Fonctionnement de la synchronisation complète

La **synchronisation complète** dans le contexte de l’approvisionnement piloté par Workday fait référence au processus d’extraction de toutes les identités de Workday et de détermination des règles d’approvisionnement à appliquer à chaque objet employé. La synchronisation complète se produit lors de la première activation de l’approvisionnement et lors du *redémarrage de l’approvisionnement* à partir du portail Azure ou à l’aide d’API Graph. 

Azure AD envoie la demande de services web Workday *Get_Workers* suivante pour récupérer les données d’employés. La requête recherche dans le journal des transactions de Workday toutes les entrées d’employés avec date d’effet à compter de l’heure correspondant à l’exécution de la synchronisation complète. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Le nœud *Response_Group* est utilisé pour spécifier les attributs d’employé à extraire de Workday. Pour obtenir une description de chaque indicateur dans le nœud *Response_Group*, reportez-vous à la [documentation de l’API Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType) de Workday. 

Certaines valeurs d’indicateur spécifiées dans le nœud *Response_Group* sont calculées en fonction des attributs configurés dans l’application d’approvisionnement Azure AD Workday. Reportez-vous à la section *Entités prises en charge* pour les critères utilisés pour définir les valeurs d’indicateur. 

La réponse de Workday à la demande *Get_Workers* pour la requête ci-dessus inclut le nombre d’enregistrements d’employés et le nombre de pages.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Pour récupérer la page suivante du jeu de résultats, la requête *Get_Workers* suivante spécifie le numéro de page en tant que paramètre dans le *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Le service d’approvisionnement Azure AD traite chaque page et itère dans l’ensemble des employés effectifs pendant la synchronisation complète. Pour chaque entrée d’employé importée à partir de Workday :
* L’[expression XPATH](workday-attribute-reference.md) est appliquée pour récupérer les valeurs d’attribut de Workday.
* Les règles de mappage d’attributs et de correspondance sont appliquées. 
* Le service détermine l’opération à effectuer dans la cible (Azure AD/AD). 

Une fois le traitement terminé, l’horodatage associé au début de la synchronisation complète est enregistré en tant que filigrane. Ce filigrane sert de point de départ pour le cycle de synchronisation incrémentielle. 

## <a name="how-incremental-sync-works"></a>Synchronisation incrémentielle

Après une synchronisation complète, le service d’approvisionnement Azure AD conserve la valeur `LastExecutionTimestamp` et l’utilise pour créer des requêtes delta afin de récupérer les modifications incrémentielles. Au cours d’une synchronisation incrémentielle, Azure AD envoie les types suivants de requêtes à Workday : 

* [requête relative aux mises à jour manuelles](#query-for-manual-updates) ;
* [requête relative aux mises à jour et arrêts avec date d’effet](#query-for-effective-dated-updates-and-terminations) ;
* [requête relative aux embauches futures](#query-for-future-dated-hires).

### <a name="query-for-manual-updates"></a>Requête relative aux mises à jour manuelles

La demande *Get_Workers* suivante interroge les mises à jour manuelles qui se sont produites entre la dernière exécution et l’exécution actuelle. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Requête relative aux mises à jour et arrêts avec date d’effet

La demande *Get_Workers* suivante interroge les mises à jour avec date d’effet qui se sont produites entre la dernière exécution et l’exécution actuelle. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Requête relative aux embauches futures

Si l’une des requêtes ci-dessus retourne une embauche future, la demande *Get_Workers* suivante est utilisée pour extraire des informations sur une nouvelle embauche future. L’attribut *WID* de la nouvelle embauche est utilisé pour effectuer la recherche, et la date d’effet est définie sur la date et l’heure de l’embauche. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Récupération des attributs de données d’employé

L’API *Get_Workers* peut retourner différents jeux de données associés à un employé. En fonction des [expressions d’API XPATH](workday-attribute-reference.md) configurées dans le schéma d’approvisionnement, le service d’approvisionnement Azure AD détermine les jeux de données à récupérer à partir de Workday. En conséquence, les indicateurs *Response_Group* sont définis dans la demande *Get_Workers*. 

Le tableau ci-dessous fournit des conseils sur la configuration de mappage à utiliser pour récupérer un jeu de données spécifique. 

| \# | Entité Workday                       | Incluse par défaut | Modèle XPATH à spécifier dans le mappage pour extraire des entités autres que par défaut             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Données à caractère personnel                        | Oui                 | wd:Worker\_Data/wd:Personal\_Data                                             |
| 2  | Données d’emploi                      | Oui                 | wd:Worker\_Data/wd:Employment\_Data                                           |
| 3  | Données supplémentaires sur le travail                  | Oui                 | wd:Worker\_Data/wd:Employment\_Data/wd:Worker\_Job\_Data\[@wd:Primary\_Job=0\]|
| 4  | Données sur l’organisation                    | Oui                 | wd:Worker\_Data/wd:Organization\_Data                                         |
| 5  | Données sur la chaîne de gestion                | Oui                 | wd:Worker\_Data/wd:Management\_Chain\_Data                                    |
| 6  | Organisation de supervision             | Oui                 | 'SUPERVISORY'                                                                 |
| 7  | Company                              | Oui                 | 'COMPANY'                                                                     |
| 8  | Unité commerciale                        | Non                  | 'BUSINESS\_UNIT'                                                              |
| 9  | Hiérarchie de l’unité commerciale              | Non                  | 'BUSINESS\_UNIT\_HIERARCHY'                                                   |
| 10 | Hiérarchie de la société                    | Non                  | 'COMPANY\_HIERARCHY'                                                          |
| 11 | Cost Center                          | Non                  | 'COST\_CENTER'                                                                |
| 12 | Hiérarchie du centre de coûts                | Non                  | 'COST\_CENTER\_HIERARCHY'                                                     |
| 13 | Fonds                                 | Non                  | 'FUND'                                                                        |
| 14 | Hiérarchie des fonds                       | Non                  | 'FUND\_HIERARCHY'                                                             |
| 15 | Cadeau                                 | Non                  | 'GIFT'                                                                        |
| 16 | Hiérarchie des cadeaux                       | Non                  | 'GIFT\_HIERARCHY'                                                             |
| 17 | Accorder                                | Non                  | 'GRANT'                                                                       |
| 18 | Hiérarchie des octrois                      | Non                  | 'GRANT\_HIERARCHY'                                                            |
| 19 | Hiérarchie du site d’entreprise              | Non                  | 'BUSINESS\_SITE\_HIERARCHY'                                                   |
| 20 | Organisation de la matrice                  | Non                  | 'MATRIX'                                                                      |
| 21 | Groupe de paie                            | Non                  | 'PAY\_GROUP'                                                                  |
| 22 | Programmes                             | Non                  | 'PROGRAMS'                                                                    |
| 23 | Hiérarchie du programme                    | Non                  | 'PROGRAM\_HIERARCHY'                                                          |
| 24 | Région                               | Non                  | 'REGION\_HIERARCHY'                                                           |
| 25 | Hiérarchie de l’emplacement                   | Non                  | 'LOCATION\_HIERARCHY'                                                         |
| 26 | Données d’approvisionnement des comptes            | Non                  | wd:Worker\_Data/wd:Account\_Provisioning\_Data                                |
| 27 | Données de vérification en arrière-plan                | Non                  | wd:Worker\_Data/wd:Background\_Check\_Data                                    |
| 28 | Données sur le droit aux avantages             | Non                  | wd:Worker\_Data/wd:Benefit\_Eligibility\_Data                                 |
| 29 | Données sur l’inscription aux avantages              | Non                  | wd:Worker\_Data/wd:Benefit\_Enrollment\_Data                                  |
| 30 | Données sur la carrière                          | Non                  | wd:Worker\_Data/wd:Career\_Data                                               |
| 31 | Données sur la compensation                    | Non                  | wd:Worker\_Data/wd:Compensation\_Data                                         |
| 32 | Données sur l’administration fiscale d’employé contingent | Non                  | wd:Worker\_Data/wd:Contingent\_Worker\_Tax\_Authority\_Form\_Type\_Data       |
| 33 | Données sur l’élément de développement                | Non                  | wd:Worker\_Data/wd:Development\_Item\_Data                                    |
| 34 | Données sur les contrats d’employé              | Non                  | wd:Worker\_Data/wd:Employee\_Contracts\_Data                                  |
| 35 | Données d’évaluation d’employé                 | Non                  | wd:Worker\_Data/wd:Employee\_Review\_Data                                     |
| 36 | Données de commentaires reçus               | Non                  | wd:Worker\_Data/wd:Feedback\_Received\_Data                                   |
| 37 | Données sur l’objectif d’employé                     | Non                  | wd:Worker\_Data/wd:Worker\_Goal\_Data                                         |
| 38 | Données photo                           | Non                  | wd:Worker\_Data/wd:Photo\_Data                                                |
| 39 | données de qualification                   | Non                  | wd:Worker\_Data/wd:Qualification\_Data                                        |
| 40 | Données sur les personnes associées                 | Non                  | wd:Worker\_Data/wd:Related\_Persons\_Data                                     |
| 41 | Données de rôle                            | Non                  | wd:Worker\_Data/wd:Role\_Data                                                 |
| 42 | Données de compétence                           | Non                  | wd:Worker\_Data/wd:Skill\_Data                                                |
| 43 | Données sur le profil de succession              | Non                  | wd:Worker\_Data/wd:Succession\_Profile\_Data                                  |
| 44 | Données d’évaluation de talent               | Non                  | wd:Worker\_Data/wd:Talent\_Assessment\_Data                                   |
| 45 | Données de compte d'utilisateur                    | Non                  | wd:Worker\_Data/wd:User\_Account\_Data                                        |
| 46 | Données de document d’employé                 | Non                  | wd:Worker\_Data/wd:Worker\_Document\_Data                                     |

>[!NOTE]
>Chaque entité Workday figurant dans la table est protégée par une **stratégie de sécurité du domaine** dans Workday. Si vous ne parvenez pas à récupérer un attribut associé à l’entité après avoir défini le bon XPATH, contactez votre administrateur Workday pour vous assurer que la stratégie appropriée de sécurité du domaine est configurée pour l’utilisateur du système d’intégration associé à l’application de configuration. Par exemple, pour récupérer des *données de compétence*, un accès *Get* est requis sur le domaine Workday *Données Workday : Compétences et expérience*. 

Voici quelques exemples de la façon dont vous pouvez étendre l’intégration de Workday pour répondre à des exigences spécifiques. 

**Exemple 1**

Supposons que vous souhaitiez récupérer les jeux de données suivants à partir de Workday et les utiliser dans vos règles d’approvisionnement :

* Centre de coûts
* Hiérarchie du centre de coûts
* Groupe de paie

Les jeux de données ci-dessus ne sont pas inclus par défaut. Pour récupérer ces jeux de données :
1. Connectez-vous au portail Azure et ouvrez votre Workday sur l’application d’approvisionnement d’utilisateurs AD/Azure AD. 
1. Dans le panneau Approvisionnement, modifiez les mappages et ouvrez la liste des attributs Workday à partir de la section Avancé. 
1. Ajoutez les définitions d’attributs suivantes et marquez-les comme « Obligatoires ». Ces attributs ne sont mappés à aucun attribut dans AD ou Azure AD. Ils servent simplement de signaux adressés au connecteur pour récupérer les informations sur le centre de coût, la hiérarchie du centre de coûts et le groupe de paie. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nom de l'attribut | Expression d’API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER_HIERARCHY']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() |

1. Une fois que le jeu de données sur le centre de coûts et le groupe de paiement est disponible dans la réponse à la demande *Get_Workers*, vous pouvez utiliser les valeurs XPATH ci-dessous pour récupérer le nom du centre de coût, le code du centre de coûts et le groupe de paie. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nom de l'attribut | Expression d’API XPATH |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Pay Group']/wd:Organization_Name/text() |

**Exemple 2**

Supposons que vous souhaitiez récupérer des certifications associées à un utilisateur. Ces informations sont disponibles dans le jeu de *données de qualification*. Pour récupérer ce jeu de données dans la réponse *Get_Workers*, utilisez le XPATH suivant : 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Exemple 3**

Supposons que vous souhaitiez récupérer les *groupes d’approvisionnement* attribués à un employé. Ces informations sont disponibles dans le jeu de *données d’approvisionnement de compte*. Pour récupérer ce jeu de données dans la réponse *Get_Workers*, utilisez le XPATH suivant : 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>Gestion de différents scénarios RH

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>Récupération des affectations de travaux internationales et des détails sur les travaux secondaires

Par défaut, le connecteur Workday récupère les attributs associés au travail principal de l’employé. Le connecteur prend également en charge la récupération de *données de travail supplémentaires* associées à des affectations de travaux internationales ou à des travaux secondaires. 

Utilisez les étapes ci-dessous pour récupérer les attributs associés aux affectations de travaux internationales : 

1. Définissez l’URL de connexion de Workday de manière à utiliser l’API de service web Workday version 30.0 ou ultérieure. Définissez en conséquence les [valeurs XPATH adéquates](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) dans votre application de provisionnement Workday. 
1. Utilisez le sélecteur `@wd:Primary_Job=0` sur le nœud `Worker_Job_Data` pour récupérer l’attribut correct. 
   * **Exemple 1 :** Pour obtenir `SecondaryBusinessTitle`, utiliser le XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **Exemple 2 :** Pour obtenir `SecondaryBusinessLocation`, utiliser le XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment configurer l’approvisionnement de Workday vers Active Directory :](../saas-apps/workday-inbound-tutorial.md)
* [Découvrez comment configurer l’écriture différée dans Workday](../saas-apps/workday-writeback-tutorial.md)
* [En savoir plus sur les attributs Workday pris en charge pour l'approvisionnement entrant](workday-attribute-reference.md)

