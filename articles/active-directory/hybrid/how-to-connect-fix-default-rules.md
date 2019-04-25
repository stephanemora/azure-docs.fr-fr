---
title: Comment résoudre les règles par défaut modifiées - Azure AD Connect | Microsoft Docs
description: Découvrez comment résoudre les règles par défaut modifiées qui sont fournis avec Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60352878"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Résoudre les règles par défaut modifiées dans Azure AD Connect

Azure AD Connect est livré avec les règles par défaut pour la synchronisation.  Malheureusement, ces règles ne s’appliquent pas universellement à toutes les organisations et il peut arriver, selon vos besoins, lorsque vous avez besoin de les modifier.

 Si vous avez modifié les règles par défaut ou que vous envisagez de les modifier, prenez un moment pour lire ce document.

Ce document présente des 2 exemples des personnalisations plus courantes effectuées par les utilisateurs et explique la méthode correcte pour obtenir ces personnalisations.

>[!NOTE] 
> Modifier les règles par défaut existantes pour atteindre une personnalisation nécessaire n'est pas pris en charge - cela permet d’éviter que la mise à jour de ces règles vers la dernière version dans les versions futures. Cela vous empêche l’obtention des correctifs de bogues nécessaires et les nouvelles fonctionnalités.  Ce document explique comment obtenir le même résultat sans modifier les règles par défaut existant. 

## <a name="how-to-identify-modified-default-rules"></a>Comment identifier les règles par défaut modifiées ?
Depuis la version 1.3.7.0 de **Azure AD Connect**, il est désormais facile d’identifier la règle par défaut modifié. Vous pouvez accéder aux applications sur le bureau et cliquez sur **Synchronization Rules Editor**.

![Éditeur](media/how-to-connect-fix-default-rules/default1.png)

Dans l’éditeur, toutes les règles par défaut modifiées seront affichera avec une icône devant le nom comme indiqué ci-dessous :

![icon](media/how-to-connect-fix-default-rules/default2.png)

 Vous verrez également une règle désactivée avec le même nom en regard de celle-ci, qui est la règle par défaut standard :

![règles par défaut](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personnalisations courantes
Les personnalisations courantes pour les règles par défaut sont les suivantes :

- [Modification de flux d’attributs](#changing-attribute-flow)
- [Modification de filtre d’étendue](#changing-scoping-filter)
- [Modifier la condition de jointure](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Avant de modifier toutes les règles
- Désactiver le Planificateur de synchronisation.  Le planificateur s’exécute toutes les 30 minutes par défaut. Faites attention à ce qu’il ne démarre pas pendant que vous effectuez les modifications et que vous résolvez les problèmes de vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrez PowerShell et exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![règles par défaut](media/how-to-connect-fix-default-rules/default3.png)

- La modification de filtre d’étendue peut entraîner la suppression d’objets dans le répertoire cible. Soyez prudent avant d’apporter des modifications dans la portée d’objets. Il est recommandé d’apporter des modifications à un serveur intermédiaire avant d’apporter des modifications sur le serveur actif.
- Veuillez exécuter un aperçu sur un seul objet, comme indiqué dans [valider la règle de synchronisation](#validate-sync-rule) section, après avoir ajouté une nouvelle règle.
- Exécutez une synchronisation complète après avoir ajouté une nouvelle règle ou de modification d’une règle de synchronisation personnalisées. Cette synchronisation s’applique des règles à tous les objets.

## <a name="changing-attribute-flow"></a>Modification de flux d’attributs
Il existe 3 différents scénarios pour le flux d’attributs, nous allons examiner comment y parvenir sans modifier les règles par défaut standard.
- Ajout du nouvel attribut
- Substitution de valeur d’attribut existant
- Ne pas synchroniser l’attribut existant

### <a name="adding-new-attribute"></a>Ajouter un nouvel attribut :
Si vous trouvez qu’un attribut ne circule pas à partir de votre répertoire source vers le répertoire cible, vous pouvez utiliser le [Azure AD Connect sync : Extensions d’annuaire](how-to-connect-sync-feature-directory-extensions.md) à circuler les nouveaux attributs.

Veuillez noter que votre premier choix doit être d’utiliser [Azure AD Connect sync : Extensions d’annuaire](how-to-connect-sync-feature-directory-extensions.md), une fonctionnalité de zone fournie par Azure AD Connect. Toutefois, si elle n’a pas fonctionné pour vous puis de comme suit pour transmettre un attribut sans modifier la règle de synchronisation par défaut standard existante, faire cela en ajoutant deux nouvelles règles de synchronisation.


#### <a name="add-an-inbound-sync-rule"></a>Ajouter une règle de synchronisation entrante :
Règle de synchronisation entrante signifie que la source de l’attribut est un espace de connecteur et la cible est le métaverse. Par exemple, pour transmettre un nouvel attribut à partir d’Active Directory local à Azure Active Directory, créer une nouvelle règle de synchronisation entrante en lançant le **éditeur de règles de synchronisation**, puis sélectionnez la Direction que **entrant** et cliquez sur **ajouter une nouvelle règle**. 

 ![règles par défaut](media/how-to-connect-fix-default-rules/default3a.png)

Suivez votre propre convention d’affectation de noms pour le nom de la règle, ici nous avons utilisé **personnalisé à partir d’AD - utilisateur**, cela signifie que la règle est une règle personnalisée et une règle de trafic entrant à partir de l’espace de connecteur AD dans le métaverse.   

 ![règles par défaut](media/how-to-connect-fix-default-rules/default3b.png)

Donnez votre propre description de la règle pour qu’une maintenance ultérieure de la règle est facile, quel est l’objectif de cette règle et pourquoi il a été nécessaire.
Sélectionnez un système connecté (forêt), la source de l’attribut. Ensuite, sélectionnez le Type d’objet système connecté et le Type d’objet métaverse.

Spécifiez la valeur de priorité compris entre 0 et 99 (le numéro inférieur, plu la priorité). Conservez les autres champs tels que « Tag », « Activer la synchronisation de mot de passe » et « Disabled » comme valeur par défaut.

Keep « filtre d’étendue » vide, cela signifie que la règle s’applique à tous les objets joints entre le système connecté et le métaverse.

Conserver le vide « Règles de jointure », ce qui signifie que cette règle sera une superposition sur la condition de jointure définie dans la règle par défaut standard. Il s’agit d’une autre raison pour ne pas désactiver/supprimer la règle par défaut standard, car s’il n’existe aucune condition de jointure à une superposition ensuite l’attribut ne circule pas. 

Ajoutez une transformation appropriée pour votre attribut, vous pouvez affecter la constante à transmettre une valeur constante à votre attribut cible, ou diriger le mappage entre l’attribut source ou cible, ou une expression pour l’attribut. Voici différents [fonctions d’expression](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) vous pouvez utiliser.

#### <a name="add-an-outbound-sync-rule"></a>Ajouter une règle de synchronisation sortante :
Jusqu'à présent, nous avons effectué la moitié du travail, en ajoutant simplement une règle de synchronisation entrante, car l’attribut n’est pas encore lié vers le répertoire cible. Pour lier l’attribut pour le directeur de la cible, vous devez créer une règle sortante, ce qui signifie que la source est le métaverse et la cible est le système connecté. Pour créer une règle sortante, démarrez **éditeur de règles de synchronisation**, modifier le **Direction** à **sortant** et cliquez sur **ajouter une nouvelle règle**. 

![règles par défaut](media/how-to-connect-fix-default-rules/default3c.png)

Comme dans la règle de trafic entrant, vous pouvez utiliser votre propre convention d’affectation de noms à **nom** la règle. Sélectionnez le **système connecté** en tant que client Azure AD, sélectionnez l’objet système connecté auquel vous souhaitez définir la valeur d’attribut. Définir la priorité comprise entre 0 - 99. 

![règles par défaut](media/how-to-connect-fix-default-rules/default3d.png)

Conserver **filtre d’étendue** keep vide, **règles de jointure** vide, renseignez la transformation en tant que constante, Direct ou une expression. 

Dans cet exemple, nous avons montré comment transmettre un nouvel attribut pour un objet utilisateur à partir d’Active Directory à Azure Active Directory. Vous pouvez utiliser ces étapes pour mapper tout attribut provenant de n’importe quel objet à la source et cible.  Pour plus d’informations, consultez [création de règles de synchronisation personnalisées](how-to-connect-create-custom-sync-rule.md) et [préparer pour configurer des utilisateurs](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Substitution de valeur d’attribut existant
Il est possible que vous souhaitez remplacer la valeur d’attribut déjà mappé, par exemple vous souhaitez toujours définir la valeur Null à un attribut dans Azure AD, ce faire, vous pouvez simplement créer uniquement une règle de trafic entrant comme indiqué dans l’étape précédente et flux  **AuthoritativeNull** valeur constante pour l’attribut cible. Veuillez noter que nous avons utilisé AuthoritativeNulll au lieu de Null dans ce cas. Il s’agit, car la valeur non null remplacera la valeur Null, même si elle a une priorité inférieure (valeur numérique plus élevée dans la règle). Toutefois, le AuthoritativeNull sera traitée comme une valeur Null et ne sera pas remplacé par valeur non null par d’autres règles. 

### <a name="dont-sync-existing-attribute"></a>Ne pas synchroniser l’attribut existant
Si vous souhaitez exclure un attribut à partir de la synchronisation, vous pouvez utiliser l’attribut fournie dans Azure AD Connect de fonctionnalité de filtrage. Lancez **Azure AD Connect** dans l’icône du bureau, puis sélectionnez **personnaliser les options de synchronisation**.

![règles par défaut](media/how-to-connect-fix-default-rules/default4.png)

 Assurez-vous que **application Azure AD et filtrage des attributs** est sélectionné, cliquez sur **suivant**.

![règles par défaut](media/how-to-connect-fix-default-rules/default5.png)

Désactivez les attributs que vous souhaitez exclure de la synchronisation.

![règles par défaut](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Modification de filtre d’étendue
Azure AD Sync s’occupe de la plupart des objets, vous pouvez réduire l’étendue des objets et réduire à moins d’objets à exporter de manière pris en charge sans modifier les règles de synchronisation par défaut standard. Au cas où vous souhaitez augmenter l’étendue des objets, vous pouvez **modifier** la règle existante, cloner et désactivez la règle d’origine. Microsoft vous recommande de ne pas à augmenter l’étendue configurée par Azure AD Connect. Augmentation de l’étendue des objets rend difficile pour l’équipe du support technique comprendre les personnalisations et de support du produit.

Voici comment vous pouvez réduire l’étendue des objets synchronisés avec Azure AD. Veuillez noter que si vous réduisez l’étendue de la **utilisateurs** synchronisés la synchronisation de hachage de mot de passe s’arrête également pour les utilisateurs de la sortie filtrée. Si les objets sont déjà la synchronisation, puis réduisez le nombre d’étendue, les objets filtrés à la sortie sera supprimé à partir du répertoire cible, collaborez sur portée très soigneusement.
Voici les méthodes prises en charge afin de réduire l’étendue des objets que vous synchronisez.

- [attribut cloudFiltered](#cloudfiltered-attribute)
- [Filtrage d’unité d’organisation](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>attribut cloudFiltered
Veuillez noter qu’il ne s’agit pas d’un attribut qui peut être défini dans Active Directory. Vous devez définir la valeur de cet attribut en ajoutant une nouvelle règle de trafic entrant, comme indiqué dans **remplace la valeur d’attribut existant** section. Vous pouvez ensuite utiliser le **Transformation** et utiliser **Expression** pour définir cet attribut dans le métaverse. Voici un exemple que vous ne souhaitez pas synchroniser tous le de l’utilisateur dont le nom service commence par la casse **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Nous avons d’abord converti le département à partir de la source (Active Directory) en minuscules. Puis à l’aide de la fonction Left, nous a fallu 3 premiers caractères et de la comparer avec hrd. Si elle correspond, puis définissez la valeur NULL sinon True. Veuillez noter que nous définissons la valeur NULL, afin qu’une autre règle avec une priorité inférieure (valeur numérique plus élevée) peut écrire dans celui-ci avec une condition différente. Veuillez exécuter preview sur un seul objet à valider la règle de synchronisation comme indiqué dans [valider la règle de synchronisation](#validate-sync-rule) section.

![règles par défaut](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtrage d’unité d’organisation
Vous pouvez créer un ou plusieurs unités d’organisation et déplacer les objets que vous ne souhaitez pas synchroniser à ces unités d’organisation. Puis configurez l’unité d’organisation filtrage dans Azure AD Connect en lançant **Azure AD Connect** à partir de l’icône de bureau, sélectionnez les options comme indiqué ci-dessous. Vous pouvez également configurer l’unité d’organisation filtrage au moment de l’installation d’Azure AD Connect. 

![règles par défaut](media/how-to-connect-fix-default-rules/default8.png)

Suivez l’Assistant et désélectionnez ensuite les unités d’organisation que vous ne souhaitez pas synchroniser.

![règles par défaut](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Modifier la condition de jointure
Microsoft vous recommande de vous que vous utilisez la valeur par défaut configurées par Azure AD Connect de conditions de jointure. Modification des conditions de jointure par défaut rend difficile pour l’équipe du support technique comprendre les personnalisations et de support du produit.

## <a name="validate-sync-rule"></a>Valider la règle de synchronisation
Vous pouvez valider la règle de synchronisation qui vient d’être ajouté à l’aide de la fonctionnalité d’aperçu sans exécuter le cycle de synchronisation complet. Lancez **Service de synchronisation** l’interface utilisateur.

![règles par défaut](media/how-to-connect-fix-default-rules/default10.png)

Cliquez sur le **recherche de métaverse**, sélectionnez objet de portée en tant que **personne**, **ajouter une Clause** et indiquez vos critères de recherche. Cliquez sur **recherche** bouton, puis double-cliquez sur l’objet dans le **résultats de la recherche** Veuillez noter que vous exécutez l’importation et la synchronisation sur la forêt avant d’exécuter cette étape, il s’agit de garantir que les données dans Azure AD Connect est à jour pour cet objet.

![règles par défaut](media/how-to-connect-fix-default-rules/default11.png)



Sélectionnez **connecteurs**, sélectionnez l’objet dans connector(forest) correspondante, cliquez sur **propriétés...** .

![règles par défaut](media/how-to-connect-fix-default-rules/default12.png)

Cliquez sur le **aperçu...**

![règles par défaut](media/how-to-connect-fix-default-rules/default13a.png)

Cliquez sur **générer l’aperçu** et **flux des attributs d’importation** dans le volet gauche.

![règles par défaut](media/how-to-connect-fix-default-rules/default14.png)
 
Ici, vous remarquerez que la règle nouvellement ajoutée est exécutée sur l’objet et qu’il a défini l’attribut cloudFiltered sur True.

![règles par défaut](media/how-to-connect-fix-default-rules/default15a.png)
 
Comment comparer les règles modifié avec la règle par défaut ?
Vous pouvez exporter à la fois les règles séparément en tant que fichiers texte. Ces règles seront exportées en tant que fichier de script powershell. Vous pouvez les comparer à l’aide de n’importe quel outil de comparaison de fichier pour voir quelles sont les modifications sont effectuées. Ici dans cet exemple, j’ai utilisé windiff pour comparer deux fichiers.
 
Vous pouvez remarquer que, dans utilisateur règle modifiée, attribut msExchMailboxGuid est remplacé par **Expression** au lieu du type **Direct** avec la valeur en tant que **NULL** et  **ExecuteOnce** option. Vous pouvez ignorer les différences d’identifiés et priorité. 

![règles par défaut](media/how-to-connect-fix-default-rules/default17.png)
 
Comment la corriger une règle par défaut modifié ?
Pour résoudre vos règles pour les paramètres par défaut, vous pouvez supprimer la règle modifiée et activer la règle par défaut comme indiqué ci-dessous et puis exécuter un **synchronisation complète**. Avant de procéder qui prenez les actions correctives comme indiqué ci-dessus, afin que vous ne perdiez pas la personnalisation que vous voulez atteindre ## prochaines étapes

## <a name="next-steps"></a>Étapes suivantes
- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Paramètres personnalisés](how-to-connect-install-custom.md)

