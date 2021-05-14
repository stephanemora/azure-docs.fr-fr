---
title: 'Synchronisation d’objets uniques Azure AD Connect '
description: Découvrez comment synchroniser un objet d’Active Directory vers Azure AD afin de résoudre des problèmes.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee850765006af15d8e323831d70dfcd7849d1287
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145958"
---
# <a name="azure-ad-connect-single-object-sync"></a>Synchronisation d’objets uniques Azure AD Connect 

L’outil de synchronisation d’objets uniques Azure AD Connect est une cmdlet PowerShell qui peut être utilisée pour synchroniser un objet individuel d’Active Directory vers Azure Active Directory. Le rapport généré peut être utilisé pour étudier et résoudre les problèmes de synchronisation par objet. 

> [!NOTE]
> L’outil prend en charge la synchronisation d’Active Directory vers Azure Active Directory. Il ne prend pas en charge la synchronisation d’Azure Active Directory vers Active Directory. 
>
> L’outil prend en charge la synchronisation de l’ajout et de la mise à jour d’un objet. Il ne prend pas en charge la synchronisation de la suppression d’un objet. 

## <a name="how-it-works"></a>Fonctionnement
L’outil de synchronisation d’objets uniques requiert un nom unique Active Directory en entrée pour rechercher le connecteur et la partition sources à importer. Il exporte les modifications vers Azure Active Directory. L’outil génère une sortie JSON similaire au type de ressource **provisioningObjectSummary**. 

L’outil de synchronisation d’objets uniques effectue les étapes suivantes : 

 1. Déterminer si le domaine (source) de l’objet (connecteur et partition Active Directory) est synchronisé. 
 2. Déterminer si le domaine (cible) de l’objet (connecteur et partition Azure Active Directory) est synchronisé. 
 3. Déterminer si l’unité d’organisation de l’objet est synchronisée. 
 4. Déterminer si l’objet est accessible à l’aide des informations d’identification du compte du connecteur. 
 5. Déterminer si le type de l’objet est synchronisé. 
 6. Déterminer si l’objet est synchronisé si le filtrage de groupe est activé. 
 7. Importer un objet depuis Active Directory vers l’espace du connecteur Active Directory. 
 8. Importer un objet depuis Azure Active Directory vers l’espace du connecteur Azure Active Directory. 
 9. Synchroniser l’objet à partir de l’espace du connecteur Active Directory. 
 10. Exporter l’objet de l’espace du connecteur Azure Active Directory vers Azure Active Directory. 

En plus de la sortie JSON, l’outil génère un rapport HTML qui contient tous les détails de l’opération de synchronisation. Le rapport HTML se trouve dans **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult-<date>.htm**. Ce rapport HTML peut être partagé avec l’équipe du support technique afin d’approfondir le dépannage, si nécessaire. 

Le rapport HTML contient les éléments suivants : 

|Onglet|Description|
|-----|-----|
|Étapes|Décrit les étapes de la synchronisation d’un objet. Chaque étape contient des détails sur la résolution des problèmes. Les étapes d’importation, de synchronisation et d’exportation contiennent des informations d’attribut supplémentaires telles que le nom, les valeurs multiples, le type, la valeur, l’ajout de valeur, la suppression de valeur, l’opération, la règle de synchronisation, le type de mappage et la source de données.| 
|Résolution des problèmes et recommandations|code d’erreur et raison. Les informations sur l’erreur ne sont disponibles qu’en cas de défaillance.| 
|propriétés modifiées|ancienne valeur et nouvelle valeur. S’il n’y a pas d’ancienne valeur ou si la nouvelle valeur est supprimée, cette cellule est vide. Pour les attributs à valeurs multiples, elle affiche le nombre. Le nom d’attribut est un lien vers l’onglet Étapes : Exporter l’objet de l’espace du connecteur Azure Active Directory vers Azure Active Directory : Informations sur l’attribut, qui contient des détails supplémentaires sur l’attribut, notamment le nom, les valeurs multiples, le type, la valeur, l’ajout de valeur, la suppression de valeur, l’opération, la règle de synchronisation, le type de mappage et la source de données.| 
|Résumé|vue d’ensemble des événements et des identificateurs de l’objet dans les systèmes source et cible.| 

## <a name="prerequisites"></a>Prérequis 

Pour pouvoir utiliser l’outil de synchronisation d’objets uniques, vous devez utiliser la version de mars 2021 d’Azure AD Connect ou une version ultérieure. 

### <a name="run-the-single-object-sync-tool"></a>Exécuter l’outil de synchronisation d’objets uniques 

Pour exécuter l’outil de synchronisation d’objets uniques, procédez comme suit : 

 1. Ouvrez une nouvelle session Windows PowerShell sur votre serveur Azure AD Connect avec l’option Exécuter en tant qu’administrateur. 

 2. Définissez la [stratégie d’exécution](/powershell/module/microsoft.powershell.security/set-executionpolicy) sur RemoteSigned ou Unrestricted. 

 3. Désactivez le planificateur de synchronisation après avoir vérifié qu’aucune opération de synchronisation n’est en cours d’exécution. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importez le module Diagnostics AdSync. 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Appelez la cmdlet de synchronisation d’objets uniques. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Réactivez le planificateur de synchronisation. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Paramètres d’entrée de la synchronisation d’objets uniques|Description| 
|-----|----|
|DistinguishedName|Ce paramètre est une chaîne obligatoire. </br></br>Il s’agit du nom unique de l’objet Active Directory qui doit être synchronisé et nécessite une résolution des problèmes.| 
|StagingMode|Il s’agit d’un paramètre de commutation facultatif.</br></br>Ce paramètre peut être utilisé pour empêcher l’exportation des modifications vers Azure Active Directory.</br></br>**Remarque** : La cmdlet valide l’opération de synchronisation. </br></br>**Remarque** : Le serveur intermédiaire d’Azure AD Connect n’exportera pas les modifications vers Azure Active Directory.|
|NoHtmlReport|Il s’agit d’un paramètre de commutation facultatif.</br></br>Ce paramètre peut être utilisé pour empêcher la génération du rapport HTML. 

## <a name="single-object-sync-throttling"></a>Limitation de la synchronisation d’objets uniques 

L’outil de synchronisation d’objets uniques **est** destiné à étudier et à résoudre les problèmes de synchronisation par objet. Il n’est **pas** destiné à remplacer le cycle de synchronisation exécuté par le planificateur. L’importation à partir d’Azure Active Directory et l’exportation vers Azure Active Directory sont soumises à des limitations. Veuillez réessayer au bout de cinq minutes si vous atteignez une limite. 

## <a name="next-steps"></a>Étapes suivantes
- [Résoudre les problèmes de synchronisation d’objets](tshoot-connect-objectsync.md)
- [Résoudre les problèmes de non synchronisation des objets](tshoot-connect-object-not-syncing.md)
- [Résolution des problèmes de bout en bout des objets et attributs Azure AD Connect](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)