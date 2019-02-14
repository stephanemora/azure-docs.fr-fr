---
title: Résolution des problèmes pour un objet qui ne se synchronise pas avec Azure AD | Microsoft Docs'
description: Résolution des problèmes pour un objet qui ne se synchronise pas avec Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55668b8ef8019e1ee808bc0cba9d98c0db53c584
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198738"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Dépanner un objet qui bloque la synchronisation avec Azure AD

Si un objet ne se synchronise pas comme attendu avec Azure AD, cela peut être causé par plusieurs raisons. Si vous avez reçu un message d’erreur d’Azure AD ou que vous voyez l’erreur dans Azure AD Connect Health, lisez [Résolution des erreurs d’exportation](tshoot-connect-sync-errors.md) à la place. Mais si vous essayez de résoudre un problème pour un objet qui n’est pas dans Azure AD, cette rubrique est pour vous. Elle décrit comment rechercher des erreurs dans le composant de synchronisation Azure AD Connect local.

>[!IMPORTANT]
>Dans le cas d’un déploiement d’Azure Active Directory (Azure AD) Connect version 1.1.749.0 ou ultérieure, utilisez la [tâche de résolution des problèmes](tshoot-connect-objectsync.md) disponible dans l’Assistant pour résoudre les problèmes de synchronisation d’objets. 

## <a name="synchronization-process"></a>Processus de synchronisation

Avant d’examiner les problèmes de synchronisation, essayons de comprendre le processus de synchronisation **Azure AD Connect** :

  ![Processus de synchronisation Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS :** Connector Space (table de base de données).
* **MV :** métaverse (table de base de données).
* **AD :** Active Directory
* **AAD :** Azure Active Directory

### <a name="synchronization-steps"></a>**Étapes de la synchronisation**
Le processus de synchronisation implique les étapes suivantes :

1. **Importation à partir d’AD :** les objets **Active Directory** sont placés dans **AD CS**.

2. **Importation à partir d’AAD :** les objets **Azure Active Directory** sont placés dans **AAD CS**.

3. **Synchronisation :** les **règles de synchronisation entrante** et les **règles de synchronisation sortante** sont exécutées dans l’ordre de priorité des nombres du plus petit au plus grand. Pour afficher les règles de synchronisation, vous pouvez accéder à **Synchronization Rules Editor** (Éditeur des règles de synchronisation) à partir des applications de bureau. Les **règles de synchronisation entrante** placent les données de CS dans MV. Les **règles de synchronisation sortante** déplacent les données de MV dans CS.

4. **Exportation vers AD :** Une fois la synchronisation effectuée, les objets sont exportés depuis AD CS vers **Active Directory**.

5. **Exportation vers AAD :** Une fois la synchronisation effectuée, les objets sont exportés depuis AAD CS vers **Azure Active Directory**.

## <a name="troubleshooting"></a>Résolution de problèmes

Pour rechercher les erreurs, vous allez examiner différents endroits dans l’ordre suivant :

1. Les [journaux des opérations](#operations) pour rechercher les erreurs identifiées par le moteur de synchronisation pendant l’importation et la synchronisation.
2. [L’espace de connecteur](#connector-space-object-properties) pour rechercher des objets manquants et des erreurs de synchronisation.
3. Le [métaverse](#metaverse-object-properties) pour rechercher les problèmes liés aux données.

Démarrez [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) avant de commencer ces étapes.

## <a name="operations"></a>Opérations
L’onglet opérations dans le Synchronization Service Manager est là où vous devez commencer le dépannage. L’onglet des opérations affiche les résultats des opérations les plus récentes.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

La partie supérieure affiche toutes les exécutions dans un ordre chronologique. Par défaut, le journal des opérations conserve les informations des sept derniers jours, mais vous pouvez modifier ce paramètre à l’aide du [planificateur](how-to-connect-sync-feature-scheduler.md). Vous souhaitez rechercher toute exécution qui ne présente pas un état de réussite. Vous pouvez modifier le tri en cliquant sur les en-têtes.

La colonne **État** regroupe les informations les plus importantes et présente le problème le plus sévère pour une exécution. Voici un récapitulatif rapide des états les plus courants par ordre de priorité d’inspection (où * indique plusieurs chaînes d’erreur possibles).

| Statut | Commentaire |
| --- | --- |
| stopped-* |L’exécution n’a pas pu se terminer. Par exemple, si le système distant est arrêté et ne peut pas être contacté. |
| stopped-error-limit |Il existe plus de 5 000 erreurs. L’exécution a été automatiquement arrêtée en raison du grand nombre d’erreurs. |
| completed-\*-errors |L’exécution s’est terminée, mais il existe des erreurs (moins de 5 000) qui doivent être examinées. |
| completed-\*-warnings |L’exécution s’est terminée, mais des données ne sont pas dans l’état attendu. Si vous avez des erreurs, alors ce message n’est, en général, qu’un symptôme. N’examinez pas les avertissements avant d’avoir résolu les erreurs. |
| réussi |Aucun problème. |

Lorsque vous sélectionnez une ligne, la partie inférieure est mise à jour pour afficher les détails de cette exécution. À l’extrême gauche de la partie inférieure, une liste peut s’afficher indiquant **Step #**. Cette liste ne s’affiche que si votre forêt contient plusieurs domaines et que chaque domaine est représenté par une étape. Le nom de domaine se trouve sous le titre **Partition**. Sous **Statistiques de synchronisation**, vous trouverez plus d’informations sur le nombre de modifications qui ont été traitées. Vous pouvez cliquer sur les liens pour obtenir la liste des objets modifiés. Si vous avez des objets comportant des erreurs, celles-ci s’affichent sous **Erreurs de synchronisation**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Résoudre les erreurs dans l’onglet des opérations
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Quand vous avez des erreurs, l’objet comportant l’erreur et l’erreur elle-même s’affichent sous forme de liens qui fournissent davantage d’informations.

Cliquez sur la chaîne d’erreur (**sync-rule-error-function-triggered** dans l’image). Une présentation de l’objet s’affiche d’abord. Pour voir l’erreur, cliquez sur le bouton **Trace de pile**. Cette trace vous fournit des informations de débogage pour l’erreur.

Vous pouvez cliquer avec le bouton droit dans la zone **Informations sur la pile d’appels**, choisir **Sélectionner tout** et **Copier**. Vous pouvez ensuite copier la pile et examiner l’erreur dans votre éditeur favori, tel que le Bloc-notes.

* Si l’erreur provient de **SyncRulesEngine**, les informations sur la pile d’appels affichent d’abord une liste de tous les attributs sur l’objet. Faites défiler vers le bas jusqu’à l’en-tête **InnerException =>**.  
  ![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
  La ligne qui suit affiche l’erreur. Dans l’image ci-dessus, l’erreur provient d’une règle de synchronisation Fabrikam personnalisée créée.

Si l’erreur elle-même ne fournit pas suffisamment d’informations, il est temps d’examiner les données elles-mêmes. Vous pouvez cliquer sur le lien avec l’identificateur d’objet et poursuivre le dépannage de [l’objet importé d’espace de connecteur](#cs-import).

## <a name="connector-space-object-properties"></a>Propriétés de l’objet espace connecteur
Si vous ne trouvez aucune erreur dans l’onglet [Opérations](#operations), exécutez l’étape suivante pour suivre l’objet espace de connecteur d’Active Directory vers le métaverse et Azure AD. Dans ce chemin d’accès, vous devez rechercher la cause du problème.

### <a name="search-for-an-object-in-the-cs"></a>Recherche d’un objet dans le CS

Dans **Synchronization Service Manager**, cliquez sur **Connecteurs**, sélectionnez le connecteur Active Directory, et **Espace de connecteur de recherche**.

Dans **Étendue**, sélectionnez **RDN** (lorsque vous souhaitez effectuer une recherche sur l’attribut CN) ou **DN ou ancre** (lorsque vous souhaitez effectuer une recherche sur l’attribut distinguishedName). Entrez une valeur et cliquez sur **Rechercher**.  
![Espace de connecteur de recherche](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Si vous ne trouvez pas l’objet recherché, il peut avoir été filtré avec le [filtrage basé sur le domaine](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou le [filtrage basé sur l’unité d’organisation](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Lisez la rubrique [Configuration du filtrage](how-to-connect-sync-configure-filtering.md) pour vérifier que le filtrage est configuré comme prévu.

Une autre recherche utile consiste à sélectionner le connecteur Azure AD. Dans **Étendue**, sélectionnez **Importation en attente**, puis cochez **Ajouter**. Cette recherche vous propose tous les objets synchronisés dans Azure AD qui ne peuvent pas être associés à un objet local.  
![Recherche d’espace de connecteur orphelin](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Ces objets ont été créés par un autre moteur de synchronisation ou un moteur de synchronisation avec une autre configuration de filtrage. Cette vue est une liste d’objets **orphelins** qui ne sont plus gérés. Vous devez examiner cette liste et envisager de supprimer ces objets à l’aide des applets de commande [Azure AD PowerShell](https://aka.ms/aadposh).

### <a name="cs-import"></a>Importation de CS
Quand vous ouvrez un objet cs, plusieurs onglets sont affichés en haut. L’onglet **Importer** affiche les données mises en lot après une importation.  
![Objet CS](./media/tshoot-connect-object-not-syncing/csobject.png)    
**L’ancienne valeur** montre ce qui est actuellement stocké dans Connect, et la **nouvelle valeur** ce qui a été reçu à partir du système source et n’a pas encore été appliqué. Si une erreur est présente sur l’objet, les modifications ne sont pas traitées.

**Error**  
![Objet CS](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
L’onglet **Erreur de synchronisation** est visible uniquement s’il existe un problème au niveau de l’objet. Pour plus d’informations, consultez [Résolution des problèmes de synchronisation](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Lignage CS
: l’onglet Lignage montre de quelle manière l’objet espace connecteur est lié à l’objet métaverse. Vous pouvez voir à quel moment le connecteur a importé pour la dernière fois une modification dans le système connecté et connaître les règles appliquées pour remplir les données dans le métaverse.  
![Lignage CS](./media/tshoot-connect-object-not-syncing/cslineage.png)  
Dans la colonne **Action**, vous pouvez voir une règle de synchronisation **Entrante** avec l’action **Approvisionner**. Cela signifie que tant que cet objet espace connecteur est présent, l’objet métaverse est conservé. Si la liste des règles de synchronisation affiche à la place une règle de synchronisation avec la direction **Sortante** et **Approvisionner**, cela indique que cet objet est supprimé en même temps que l’objet métaverse.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Vous pouvez également voir dans la colonne **PasswordSync** que l’espace connecteur entrant peut modifier le mot de passe, car une règle de synchronisation a la valeur **True**. Ce mot de passe est ensuite envoyé à Azure AD au moyen de la règle sortante.

Sous l’onglet Lignage, vous pouvez accéder au métaverse en cliquant sur [Propriétés de l’objet métaverse](#mv-attributes).

Deux boutons se trouvent en bas de tous les onglets : **Aperçu** et **Journal**.

### <a name="preview"></a>VERSION PRÉLIMINAIRE
: la page d’aperçu sert à synchroniser un seul objet. Elle est utile si vous résolvez des problèmes liés aux règles de synchronisation personnalisées et que vous souhaitez voir l’effet d’une modification sur un seul objet. Vous pouvez choisir entre **Synchronisation complète** et **Synchronisation delta**. Vous pouvez également choisir entre **Générer l’aperçu**, qui permet de conserver uniquement la modification en mémoire, et **Valider l’aperçu**, qui met à jour le métaverse et implémente toutes les modifications dans les espaces connecteur cibles.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
Vous pouvez inspecter l’objet et la règle appliquée pour un flux d’attribut particulier.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Journal
La page du journal est utilisée pour afficher l’état de synchronisation et l’historique du mot de passe. Pour plus d’informations, voir [Résolution des problèmes de synchronisation de hachage du mot de passe](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriétés de l’objet métaverse
Il est généralement préférable de commencer la recherche à partir de l'espace connecteur Active Directory source. Mais vous pouvez également démarrer la recherche depuis le métaverse.

### <a name="search-for-an-object-in-the-mv"></a>Recherche d’un objet dans le MV
Dans **Synchronization Service Manager**, cliquez sur **Recherche de métaverse**. Créez une requête capable de trouver l’utilisateur. Vous pouvez rechercher des attributs communs, comme le nom de compte (sAMAccountName) et userPrincipalName. Pour plus d’informations, voir [Recherche de métaverses](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Dans la fenêtre **Résultats de la recherche**, cliquez sur l’objet.

Si vous ne trouvez pas l’objet, il n’a pas encore atteint le métaverse. Continuez à rechercher l’objet dans [l’espace connecteur](#connector-space-object-properties) **Active Directory**. Si vous le trouvez dans l’espace connecteur **Active Directory**, il se peut qu’une erreur de synchronisation bloque l’entrée de l’objet dans le métaverse ou qu’un filtre d’étendue de règles de synchronisation soit appliqué.

### <a name="object-not-found-in-the-mv"></a>Objet introuvable dans MV
Si l’objet se trouve dans l’espace connecteur **Active Directory**, mais pas dans MV, le filtre d’étendue est appliqué. 

* Pour examiner le filtre d’étendue, accédez au menu de l’application de bureau, puis cliquez sur **Synchronization Rules Editor (Éditeur des règles de synchronisation)**. Filtrez les règles applicables à l’objet en ajustant le filtre ci-dessous.

  ![Recherche des règles de synchronisation entrante](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* Affichez chaque règle de la liste ci-dessus et vérifiez le paramètre **Scoping filter (Filtre d’étendue)**. Dans le filtre d’étendue indiqué ci-dessous, si l’attribut **isCriticalSystemObject** a la valeur Null ou FALSE, ou s’il n’est pas défini, il est donc dans l’étendue.

  ![Recherche des règles de synchronisation entrante](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* Accédez à la liste d’attributs [CS Import (Importation de CS)](#cs-import), puis vérifiez quel filtre bloque l’objet à déplacer vers MV. Ce filtre distinct de la liste d’attributs **Connector Space** affiche uniquement les attributs renseignés ou dont la valeur n’est pas de type Null. Par exemple, si l’attribut **isCriticalSystemObject** n’apparaît pas dans la liste, cela signifie qu’il a la valeur Null ou qu’il n’est pas renseigné.

### <a name="object-not-found-in-the-aad-cs"></a>Objet introuvable dans AAD CS
Si l’objet n’est pas présent dans **l’espace connecteur** **d’Azure Active Directory**, mais qu’il existe dans MV, examinez le filtre d’étendue des règles de synchronisation **sortante** de **l’espace connecteur** correspondant, puis vérifiez si l’objet est filtré en raison des [attributs MV](#mv-attributes) qui ne répondent pas aux critères.

* Pour afficher le filtre d’étendue sortant, sélectionnez les règles applicables de l’objet en ajustant le filtre ci-dessous. Affichez chaque règle et examinez la valeur correspondante de [l’attribut MV](#mv-attributes).

  ![Recherche des règles de synchronisation sortante](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Attributs MV
: sous l’onglet Attributs, vous pouvez voir les valeurs et le connecteur qui y a contribué.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Si un objet n’est pas synchronisé, examinez les attributs suivants dans le métaverse :
- L’attribut **cloudFiltered** est-il présent et défini sur **true** ? Si c’est le cas, il a été filtré selon la procédure décrite dans [Filtrage par attribut](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- L’attribut **sourceAnchor** est-il présent ? Si ce n’est pas le cas, utilisez-vous une topologie de forêt compte-ressource ? Si un objet est identifié comme une boîte aux lettres liée (l’attribut **msExchRecipientTypeDetails** a la valeur 2), l’attribut sourceAnchor est fourni par la forêt avec un compte Active Directory activé. Assurez-vous que le compte principal a été importé et synchronisé correctement. Le compte principal doit être répertorié dans les [connecteurs](#mv-connectors) pour l’objet.

### <a name="mv-connectors"></a>Connecteurs MV
: l’onglet Connecteurs affiche tous les espaces connecteur qui ont une représentation de l’objet.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Vous devez disposer d’un connecteur pour :

- Chaque forêt Active Directory dans laquelle l’utilisateur est représenté. Cette représentation peut inclure foreignSecurityPrincipals et les objets contact.
- Un connecteur dans Azure AD.

Si le connecteur Azure AD est manquant, lisez [Attributs MV](#mv-attributes) pour vérifier les critères d’approvisionnement sur Azure AD.

Cet onglet permet également d’accéder à l’[objet CS (Connector Space)](#connector-space-object-properties). Sélectionnez une ligne et cliquez sur **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes
- [Synchronisation Azure AD Connect](how-to-connect-sync-whatis.md)
- [Qu’est-ce que l’identité hybride ?](whatis-hybrid-identity.md)
