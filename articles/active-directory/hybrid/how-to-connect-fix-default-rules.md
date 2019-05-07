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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067631"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Résoudre les règles par défaut modifiées dans Azure AD Connect

Azure Active Directory (Azure AD) Connect utilise par défaut des règles pour la synchronisation.  Malheureusement, ces règles ne s’appliquent pas universellement à toutes les organisations. Selon vos besoins, vous devrez peut-être les modifier. Cet article décrit deux exemples des personnalisations plus courants et explique la méthode correcte pour obtenir ces personnalisations.

>[!NOTE] 
> Modification des règles par défaut existantes pour atteindre une personnalisation nécessaire n’est pas prise en charge. Si vous procédez ainsi, il empêche la mise à jour ces règles vers la dernière version dans les futures versions. Vous n’obtiendrez pas les correctifs de bogues que vous avez besoin, ou de nouvelles fonctionnalités. Ce document explique comment obtenir le même résultat sans modifier les règles par défaut existant. 

## <a name="how-to-identify-modified-default-rules"></a>Comment identifier les règles par défaut modifié
Depuis la version 1.3.7.0 d’Azure AD Connect, il est facile d’identifier la règle par défaut modifié. Accédez à **applications bureau**, puis sélectionnez **Synchronization Rules Editor**.

![Azure AD Connect, avec l’éditeur de règles de synchronisation mis en surbrillance](media/how-to-connect-fix-default-rules/default1.png)

Dans l’éditeur, toutes les règles par défaut modifiés sont affichés avec une icône d’avertissement devant le nom.

![Icône Avertissement](media/how-to-connect-fix-default-rules/default2.png)

 Désactivé la règle avec le même nom comme il apparaît également (il s’agit de la règle par défaut standard).

![Éditeur de règles de synchronisation, montrant la règle par défaut standard et la règle par défaut modifié](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personnalisations courantes
Les personnalisations courantes pour les règles par défaut sont les suivantes :

- Flux du changement d’attribut
- Modifier le filtre d’étendue
- Condition de jointure de modification

Avant de modifier toutes les règles :

- Désactiver le Planificateur de synchronisation. Le planificateur s’exécute toutes les 30 minutes par défaut. Assurez-vous qu’il ne démarre pas lorsque vous apporter des modifications et résolution des problèmes de vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrez PowerShell et exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Commandes PowerShell pour désactiver le Planificateur de synchronisation](media/how-to-connect-fix-default-rules/default3.png)

- La modification de filtre d’étendue peut entraîner la suppression des objets dans le répertoire cible. Soyez prudent avant d’apporter des modifications dans la portée d’objets. Nous vous recommandons d’apporter des modifications à un serveur intermédiaire avant d’apporter des modifications sur le serveur actif.
- Exécuter un aperçu sur un seul objet, comme indiqué dans le [valider la règle de synchronisation](#validate-sync-rule) section, après avoir ajouté une nouvelle règle.
- Exécuter une synchronisation complète après avoir ajouté une nouvelle règle ou de modification d’une règle de synchronisation personnalisées. Cette synchronisation s’applique des règles à tous les objets.

## <a name="change-attribute-flow"></a>Flux du changement d’attribut
Il existe trois scénarios différents pour modifier le flux d’attributs :
- Ajout d’un nouvel attribut.
- Remplaçant la valeur d’un attribut existant.
- Choisissez de ne pas synchroniser un attribut existant.

Vous pouvez effectuer ces sans modifier les règles par défaut standard.

### <a name="add-a-new-attribute"></a>Ajouter un nouvel attribut
Si vous trouvez qu’un attribut n’est pas transmis à partir de votre répertoire source vers le répertoire cible, utilisez le [Azure AD Connect sync : Extensions d’annuaire](how-to-connect-sync-feature-directory-extensions.md) pour résoudre ce problème.

Si les extensions ne résout votre problème, essayez d’ajouter deux nouvelles règles de synchronisation, décrits dans les sections suivantes.


#### <a name="add-an-inbound-sync-rule"></a>Ajouter une règle de synchronisation entrante
Une règle de synchronisation entrante signifie que la source de l’attribut est un espace de connecteur et la cible est le métaverse. Par exemple, pour avoir un nouvel attribut de flux à partir sur Active Directory local à Azure Active Directory, créez une nouvelle règle de synchronisation entrante. Lancer le **Synchronization Rules Editor**, sélectionnez **entrant** en tant que la direction, puis sélectionnez **ajouter une nouvelle règle**. 

 ! Synchronisation règles Editor](media/how-to-connect-fix-default-rules/default3a.png)

Suivez votre propre convention d’affectation de noms pour le nom de la règle. Ici, nous utilisons **personnalisé à partir d’AD - utilisateur**. Cela signifie que la règle est une règle personnalisée et une règle de trafic entrant à partir de l’espace de connecteur Active Directory dans le métaverse.   

 ![Créer une règle de synchronisation de trafic entrant](media/how-to-connect-fix-default-rules/default3b.png)

Fournir votre propre description de la règle, afin qu’une maintenance ultérieure de la règle soit facile. Par exemple, la description peut être basée sur l’objectif de la règle, et pourquoi il est nécessaire.

Effectuez vos sélections pour le **système connecté**, **Type d’objet système connecté**, et **Type d’objet métaverse** champs.

Spécifiez la valeur de priorité comprise entre 0 et 99 (faible le nombre, plus la priorité). Pour le **balise**, **activer la synchronisation de mot de passe**, et **désactivé** champs, utilisez les sélections par défaut.

Conserver **filtre d’étendue** vide. Cela signifie que la règle s’applique à tous les objets joints entre le système Active Directory connectée et le métaverse.

Conserver **règles de jointure** vide. Cela signifie que cette règle utilise la condition de jointure définie dans la règle par défaut standard. Il s’agit d’une autre raison pour ne pas désactiver ou supprimer la règle par défaut standard. S’il n’existe aucune condition de jointure, ne sont pas de flux de l’attribut. 

Ajouter les transformations appropriées pour votre attribut. Vous pouvez affecter une constante, pour rendre une constante de valeur de flux à votre attribut cible. Vous pouvez utiliser un mappage direct entre l’attribut source ou cible. Ou bien, vous pouvez utiliser une expression pour l’attribut. Voici différents [fonctions d’expression](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) vous pouvez utiliser.

#### <a name="add-an-outbound-sync-rule"></a>Ajouter une règle de synchronisation sortante
Pour lier l’attribut dans le répertoire cible, vous devez créer une règle sortante. Cela signifie que la source est le métaverse et la cible est le système connecté. Pour créer une règle sortante, démarrez le **Synchronization Rules Editor**, modifier le **Direction** à **sortant**, puis sélectionnez **ajouter une nouvelle règle**. 

![Éditeur de règles de synchronisation](media/how-to-connect-fix-default-rules/default3c.png)

Comme avec la règle de trafic entrant, vous pouvez utiliser votre propre convention d’affectation de noms pour nommer la règle. Sélectionnez le **système connecté** en tant que le locataire Azure AD, puis sélectionnez le système connecté objet auquel vous souhaitez définie la valeur d’attribut. Définir la priorité comprise entre 0 et 99. 

![Créer une règle de synchronisation de trafic sortant](media/how-to-connect-fix-default-rules/default3d.png)

Conserver **filtre d’étendue** et **règles de jointure** vide. Renseignez la transformation en tant que constante, direct ou une expression. 

Vous savez maintenant comment rendre un nouvel attribut pour un flux d’objet utilisateur à partir d’Active Directory à Azure Active Directory. Vous pouvez utiliser ces étapes pour mapper tout attribut provenant de n’importe quel objet à la source et cible. Pour plus d’informations, consultez [création de règles de synchronisation personnalisées](how-to-connect-create-custom-sync-rule.md) et [préparer pour configurer des utilisateurs](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Remplacer la valeur d’un attribut existant
Vous souhaiterez peut-être substituer la valeur d’un attribut qui a déjà été mappée. Par exemple, si vous souhaitez toujours définir une valeur null à un attribut dans Azure AD, créez simplement une règle de trafic entrant. Vérifiez la valeur de constante, `AuthoritativeNull`, flux vers l’attribut cible. 

>[!NOTE] 
> Utilisez `AuthoritativeNull` au lieu de `Null` dans ce cas. Il s’agit, car la valeur non null remplace la valeur null, même si elle a une priorité inférieure (une valeur numérique plus élevée dans la règle). `AuthoritativeNull`, quant à eux, il n’est pas remplacé par une valeur non null par d’autres règles. 

### <a name="dont-sync-existing-attribute"></a>Ne pas synchroniser l’attribut existant
Si vous souhaitez exclure un attribut à partir de la synchronisation, utilisez l’attribut fournie dans Azure AD Connect de fonctionnalité de filtrage. Lancez **Azure AD Connect** dans l’icône du bureau, puis sélectionnez **personnaliser les options de synchronisation**.

![Options de tâches supplémentaires Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Assurez-vous que **application Azure AD et filtrage des attributs** est sélectionné, puis sélectionnez **suivant**.

![Fonctionnalités facultatives à Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Effacer les attributs que vous souhaitez exclure de la synchronisation.

![Attributs de Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Modifier le filtre d’étendue
Azure AD Sync s’occupe de la plupart des objets. Vous pouvez réduire l’étendue des objets et réduire le nombre d’objets à exporter, sans modifier les règles de synchronisation par défaut standard. 

Utilisez une des méthodes suivantes pour réduire l’étendue des objets que vous synchronisez :

- attribut cloudFiltered
- Unité d’organisation de filtrage

Si vous réduisez l’étendue des utilisateurs en cours de synchronisation, la synchronisation de hachage de mot de passe s’arrête également pour les utilisateurs de la sortie filtrée. Si les objets sont déjà la synchronisation, une fois que vous réduisez la portée, les objets hors filtrés sont supprimés à partir du répertoire cible. Pour cette raison, assurez-vous que vous définissez l’étendue très soigneusement.

>[!IMPORTANT] 
> Augmentation de l’étendue des objets configurés par Azure AD Connect n’est pas recommandé. Cela rend difficile pour l’équipe de support Microsoft comprendre les personnalisations. Si vous devez augmenter l’étendue des objets, modifiez la règle existante, cloner et désactiver la règle d’origine. 

### <a name="cloudfiltered-attribute"></a>attribut cloudFiltered
Vous ne pouvez pas définir cet attribut dans Active Directory. Définissez la valeur de cet attribut en ajoutant une nouvelle règle de trafic entrant. Vous pouvez ensuite utiliser **Transformation** et **Expression** pour définir cet attribut dans le métaverse. L’exemple suivant montre que vous ne souhaitez pas synchroniser tous les utilisateurs dont le nom service commence par **HRD** (non-respect de la casse) :

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Tout d’abord, nous avons converti le département à partir de la source (Active Directory) en minuscules. Ensuite, à l’aide de la `Left` (fonction), nous a fallu uniquement les trois premiers caractères et de la comparer avec `hrd`. Si elle correspond, la valeur est définie sur `True`, sinon `NULL`. Dans la définition de la valeur null, une autre règle avec une priorité inférieure (une valeur numérique plus élevée) peut y écrire avec une condition différente. Aperçu de l’exécution sur un seul objet à valider la règle de synchronisation, comme indiqué dans le [valider la règle synchronisation](#validate-sync-rule) section.

![Créer des options de règle de synchronisation entrante](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Unité d’organisation de filtrage
Vous pouvez créer un ou plusieurs unités d’organisation (UO) et déplacer les objets que vous ne souhaitez pas synchroniser à ces unités d’organisation. Ensuite, configurez l’unité d’organisation filtrage dans Azure AD Connect. Lancez **Azure AD Connect** à partir de l’icône du bureau, puis sélectionnez les options suivantes. Vous pouvez également configurer l’unité d’organisation filtrage au moment de l’installation d’Azure AD Connect. 

![Tâches supplémentaires de Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Suivez l’Assistant et désactivez les unités d’organisation que vous ne souhaitez pas synchroniser.

![Azure AD se connecter domaine et unité d’organisation options de filtrage](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Condition de jointure de modification
Utilisez les conditions de jointure par défaut configurées par Azure AD Connect. Modification des conditions de jointure par défaut rend difficile pour le support technique Microsoft pour comprendre les personnalisations et de support du produit.

## <a name="validate-sync-rule"></a>Valider la règle de synchronisation
Vous pouvez valider la règle de synchronisation qui vient d’être ajouté à l’aide de la fonctionnalité d’aperçu, sans exécuter le cycle de synchronisation complète. Dans Azure AD Connect, sélectionnez **Service de synchronisation**.

![Azure AD Connect, avec le Service de synchronisation mis en surbrillance](media/how-to-connect-fix-default-rules/default10.png)

Sélectionnez **recherche de métaverse**. Sélectionnez l’objet d’étendue en tant que **personne**, sélectionnez **ajouter une Clause**et indiquez vos critères de recherche. Ensuite, sélectionnez **recherche**, double-cliquez sur l’objet dans les résultats de recherche. Assurez-vous que vos données dans Azure AD Connect sont à jour pour cet objet, en exécutant importation et synchronisation sur la forêt avant d’exécuter cette étape.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Sur **propriétés de l’objet métaverse**, sélectionnez **connecteurs**, sélectionnez l’objet dans le connecteur correspondant (forêt), puis **propriétés...** .

![Propriétés de l’objet métaverse](media/how-to-connect-fix-default-rules/default12.png)

Sélectionnez **aperçu...**

![Propriétés de l’objet espace connecteur](media/how-to-connect-fix-default-rules/default13a.png)

Dans la fenêtre d’aperçu, sélectionnez **générer l’aperçu** et **flux des attributs d’importation** dans le volet gauche.

![VERSION PRÉLIMINAIRE](media/how-to-connect-fix-default-rules/default14.png)
 
Ici, notez que la règle nouvellement ajoutée est exécutée sur l’objet et qu’il a défini la `cloudFiltered` attribut sur true.

![VERSION PRÉLIMINAIRE](media/how-to-connect-fix-default-rules/default15a.png)
 
Pour comparer la règle modifiée avec la règle par défaut, exporter les deux règles séparément, en tant que fichiers texte. Ces règles sont exportés dans un fichier de script PowerShell. Vous pouvez les comparer à l’aide de n’importe quel outil de comparaison de fichier (par exemple, windiff) pour voir les modifications. 
 
Notez que dans la règle modifiée, le `msExchMailboxGuid` attribut est modifié sur le **Expression** type, au lieu de **Direct**. En outre, la valeur est modifiée pour **NULL** et **ExecuteOnce** option. Vous pouvez ignorer les différences d’identifiés et priorité. 

![sortie de l’outil Windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Pour résoudre vos règles pour modifier les paramètres par défaut, supprimez la règle modifiée et activer la règle par défaut. Assurez-vous que vous ne perdez pas la personnalisation que vous tentez d’atteindre. Lorsque vous êtes prêt, exécutez **synchronisation complète**.

## <a name="next-steps"></a>Étapes suivantes
- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Paramètres personnalisés](how-to-connect-install-custom.md)



