---
title: FAQ sur la protection par mot de passe Azure AD en local
description: Consulter les questions fréquentes relatives à la protection par mot de passe Azure AD dans un environnement Active Directory Domain Services local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625125"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur la protection par mot de passe Azure AD en local

Cette section fournit des réponses à de nombreuses questions fréquemment posées sur la protection des mots de passe Azure AD.

## <a name="general-questions"></a>Questions générales

**Q : Quels conseils donner aux utilisateurs sur la façon de sélectionner un mot de passe sécurisé ?**

Les conseils de Microsoft sur ce sujet se trouvent sur le lien suivant :

[Conseils pour le choix du mot de passe Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Q : La protection par mot de passe Azure AD en local est-elle prise en charge sur les clouds non publics ?**

La protection par mot de passe Azure AD en local est prise en charge sur le cloud public et le cloud Arlington. Aucune date n’a été annoncée concernant la disponibilité sur d’autres clouds.

Le portail Azure AD autorise la modification de la configuration spécifique en local « Protection par mot de passe pour Windows Server Active Directory », même dans les clouds non pris en charge. Ces modifications sont rendues persistantes, sans quoi elles ne prennent pas effet. L’inscription d’agents proxys ou de forêts en local n’est pas prise en charge dans les clouds non pris en charge, ce qui entraîne l’échec de toute tentative d’inscription.

**Q : Comment puis-je appliquer des avantages de la protection par mot de passe Azure AD à un sous-ensemble de mes utilisateurs en local ?**

Non pris en charge. Une fois déployée et activée, la protection par mot de passe Azure AD ne fait pas de distinction : tous les utilisateurs reçoivent les mêmes avantages en matière de protection.

**Q : Quelle est la différence entre une modification de mot de passe et une définition (ou réinitialisation) de mot de passe ?**

Une modification de mot de passe survient lorsqu’un utilisateur choisit un nouveau mot de passe après avoir prouvé qu’il a connaissance de l’ancien mot de passe. Par exemple, un changement de mot de passe se produit quand un utilisateur se connecte à Windows et est invité à choisir un nouveau mot de passe.

Une définition de mot de passe (parfois appelée une réinitialisation de mot de passe) survient lorsqu’un administrateur remplace le mot de passe d’un compte par un nouveau mot de passe, par exemple à l’aide de l’outil de gestion Utilisateurs et ordinateurs Active Directory. Cette opération requiert un niveau élevé de privilèges (généralement, administrateur de domaine), et la personne qui effectue l’opération n’a généralement pas connaissance de l’ancien mot de passe. Les scénarios de support technique opèrent souvent des définitions de mot de passe, par exemple, pour aider un utilisateur qui a oublié son mot de passe. Vous rencontrez également des situations de définition de mot de passe lorsqu’un compte d’utilisateur est créé pour la première fois avec un mot de passe.

Le comportement de la stratégie de validation de mot de passe est toujours identique, qu’il s’agisse d’une modification de mot de passe ou d’une définition de mot de passe. Le service d’agent du contrôleur de domaine pour la protection par mot de passe Azure AD consigne différents événements afin de vous informer en cas de modification ou de définition de mot de passe.  Voir [Supervision et journalisation dans la protection par mot de passe Azure AD](./howto-password-ban-bad-on-premises-monitor.md).

**Q : La protection par mots de passe Azure AD valide-t-elle les mots de passe existants après installation ?**

Non. La protection par mot de passe Azure AD peut uniquement appliquer une stratégie de mot de passe à des mots de passe en texte clair pendant une opération de définition ou de modification de mot de passe. Une fois qu’Active Directory a accepté un mot de passe, seuls des hachages spécifiques du protocole d’authentification de ce mot de passe sont conservés. Le mot de passe en texte clair n’étant jamais conservé, la protection par mot de passe Azure AD ne peut pas valider des mots de passe existants.

Après le déploiement initial de la protection par mot de passe Azure AD, l’ensemble des utilisateurs et des comptes commencent à utiliser un mot de passe validé par la protection par mot de passe Azure AD, car leurs mots de passe existants expirent normalement au fil du temps. Si nécessaire, vous pouvez accélérer ce processus en raison d’une expiration manuelle ponctuelle des mots de passe de comptes d’utilisateur.

Les comptes configurés avec l’option « Le mot de passe n’expire jamais » ne sont jamais obligés de modifier leur mot de passe, sauf en cas d’expiration manuelle.

**Q : Pourquoi des événements de mot de passe rejetés sont-ils consignés en double lorsque je tente de définir un mot de passe faible à l’aide du composant logiciel enfichable Utilisateurs et ordinateurs Active Directory ?**

Le composant logiciel enfichable Utilisateurs et ordinateurs Active Directory essaie d’abord de définir le nouveau mot de passe à l’aide du protocole Kerberos. En cas d’échec, le composant logiciel enfichable fera une seconde tentative de définition du mot de passe à l’aide d’un protocole hérité (SAM RPC). Les protocoles spécifiques utilisés ne sont pas importants. Si le nouveau mot de passe est considéré comme faible par la Protection de mots de passe Azure AD, le comportement de ce logiciel enfichable engendre la journalisation de deux ensembles d’événements de rejet de réinitialisation de mot de passe.

**Q : Pourquoi les événements de validation de mot de passe Protection par mot de passe Azure AD sont-ils journalisés avec un nom d’utilisateur vide ?**

Active Directory prend en charge la possibilité de tester un mot de passe pour voir s’il satisfait aux exigences actuelles du domaine en matière de complexité des mots de passe, par exemple à l’aide de l’API [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy). Lorsqu’un mot de passe est validé de cette manière, les tests comprennent également une validation par des produits basés sur des DLL de filtre de mots de passe, tels que la Protection par mot de passe Azure AD, mais les noms d’utilisateur passés à une DLL de filtre de mot de passe donnée seront vides. Dans ce scénario, la Protection par mot de passe Azure AD validera toujours le mot de passe à l’aide de la stratégie de mot de passe actuellement appliquée et émettra un message de journal des événements pour capturer le résultat, mais le message du journal des événements contiendra des champs de nom d’utilisateur vides.

**Q : L’installation de la protection par mot de passe Azure AD simultanément avec d’autres produits basés sur le filtrage par mot de passe est-elle prise en charge ?**

Oui. La prise en charge de plusieurs DLL de filtrage de mots de passe enregistrés est une fonctionnalité Windows de base et n’est pas spécifique à la protection par mot de passe Azure AD. Toutes les DLL de filtrage de mots de passe enregistrés doivent valider un mot de passe avant de l’accepter.

**Q : Comment puis-je déployer et configurer la protection par mot de passe Azure AD dans mon environnement Active Directory sans utiliser Azure ?**

Non pris en charge. La protection par mot de passe Azure AD est une fonctionnalité Azure qui accepte d’être étendue à un environnement Active Directory en local.

**Q : Comment puis-je modifier le contenu de la stratégie au niveau d’Active Directory ?**

Non pris en charge. La stratégie ne peut être administrée que par le biais du portail Azure AD. Consultez également la question précédente.

**Q : Pourquoi la technologie DFSR est-elle nécessaire pour la réplication sysvol ?**

La technologie FRS (prédécesseur de la technologie DFSR) présente de nombreux problèmes connus et n’est pas du tout prise en charge par les versions plus récentes de Windows Server Active Directory. Aucun essai de la protection par mot de passe Azure AD ne sera effectué sur les domaines configurés en FRS.

Pour plus d’informations, consultez les articles suivants :

[Argumentaire en faveur de la migration de la réplication sysvol vers DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Si votre domaine n’utilise pas encore DFSR, vous DEVEZ le faire migrer pour l’utiliser avant d’installer la protection par mot de passe Azure Active Directory. Pour plus d’informations, consultez le lien suivant :

[Guide de migration de la réplication SYSVOL : Réplication FRS vers DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Le logiciel de l’agent DC de protection par mot de passe Azure AD s’installe actuellement sur des contrôleurs de domaine dans des domaines qui utilisent encore FRS pour la réplication sysvol, mais ce logiciel ne fonctionne pas correctement dans cet environnement. Des effets secondaires supplémentaires peuvent entraîner l’échec de la réplication des fichiers individuels, et le succès apparent des procédures de restauration sysvol qui ne parviennent pas à répliquer tous les fichiers en mode silencieux. Vous devez faire migrer votre domaine pour utiliser DFSR dès que possible, à la fois pour les avantages inhérents à DFSR et pour débloquer le déploiement de la protection par mot de passe Azure AD. Les versions ultérieures du logiciel seront automatiquement désactivées lors de l’exécution dans un domaine qui utilise encore FRS.

**Q : De quelle quantité d’espace disque la fonctionnalité a besoin sur le partage sysvol du domaine ?**

L’utilisation d’un espace précis varie en fonction de facteurs, par exemple la surcharge de chiffrement, le nombre et la longueur des jetons interdits dans la liste globale interdite de Microsoft et dans la liste personnalisée par locataire. Le contenu de ces listes est susceptible d’évoluer à l’avenir. Ainsi, la prévision pour cette fonctionnalité d’au moins cinq (5) mégaoctets d’espace sur le partage sysvol du domaine est une appréciation raisonnable.

**Q : Pourquoi un redémarrage est-il nécessaire pour installer ou mettre à niveau le logiciel de l’agent contrôleur de domaine ?**

Cette exigence est due à un comportement de base de Windows.

**Q : Existe-t-il un moyen de configurer un agent contrôleur de domaine pour utiliser un serveur proxy spécifique ?**

Non. Le serveur proxy étant sans état, n’importe quel serveur proxy en particulier est utilisé.

**Q : Est-il possible de déployer le service proxy de la protection par mot de passe Azure AD, côte à côte avec d’autres services, comme Azure AD Connect ?**

Oui. Azure AD Connect et le service proxy de la protection par mot de passe Azure AD n’entrent jamais en conflit directement.

Malheureusement, une incompatibilité a été détectée entre la version du service du programme de mise à jour de l’agent Microsoft Azure AD Connect installée par le logiciel Azure AD Password Protection Proxy et la version du service installée par le logiciel [Proxy d’application Azure Active Directory](../manage-apps/application-proxy.md). Cette incompatibilité peut empêcher le service de mise à jour de l’agent de contacter Azure pour effectuer les mises à jour logicielles. Il est déconseillé d’installer d’installer Azure AD Password Protection Proxy et Proxy d'application Azure Active Directory sur le même ordinateur.

**Q : Dans quel ordre faut-il installer et inscrire les agents de contrôleur de domaine et les proxys ?**

L’ordre n’a aucune importance pour l’installation de l’agent de proxy, l’installation de l’agent du contrôleur de domaine, l’inscription de la forêt et l’inscription du proxy.

**Q : Dois-je me préoccuper des performances obtenues sur mes contrôleurs de domaine avant de déployer cette fonctionnalité ?**

Le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD ne devrait pas affecter considérablement les performances du contrôleur de domaine dans un déploiement Active Directory sain existant.

Pour la plupart des déploiements Active Directory, les opérations de modification de mot de passe représentent une petite proportion de la charge de travail globale sur un contrôleur de domaine donné. Imaginez, par exemple, un domaine Active Directory avec 10 000 comptes d’utilisateur et une stratégie MaxPasswordAge définie sur 30 jours. En moyenne, ce domaine verra 10 000/30 = ~ 333 opérations de modification de mot de passe par jour, ce qui représente un nombre insignifiant d’opérations, même pour un seul contrôleur de domaine. Envisagez le pire scénario d’utilisation possible : supposez que ces quelque 333 modifications de mot de passe sur un seul contrôleur de domaine soient effectuées dans une même heure. Ce scénario peut notamment se produire lorsque beaucoup d’employés viennent tous travailler un lundi matin. Même dans ce cas, il est toujours question de ~333/60 minutes, soit six modifications de mot de passe par minute, ce qui encore une fois n’est pas une charge importante.

Par contre, si vos contrôleurs de domaine actuels s’exécutent déjà à des niveaux de performances limitées (par exemple, en limite maximale par rapport au processeur, à l’espace disque, aux e/s de disque, etc.), il est conseillé d’ajouter des contrôleurs de domaine supplémentaires, ou d’augmenter l’espace disque disponible avant le déploiement de cette fonctionnalité. Consultez également la question se rapportant à l’utilisation de l’espace disque sysvol, posée plus haut.

**Q : Je veux tester la protection par mot de passe Azure AD sur quelques contrôleurs de domaine seulement dans mon domaine. Est-il possible d’imposer des modifications de mot de passe utilisateur pour utiliser ces contrôleurs de domaine en particulier ?**

Non. Le système d’exploitation client Windows contrôle quel contrôleur de domaine est utilisé lorsqu’un utilisateur change son mot de passe. Le contrôleur de domaine est sélectionné en fonction de facteurs, tels que des attributions de site et de sous-réseau Active Directory, une configuration réseau spécifique à l’environnement, etc. La protection par mot de passe AD Azure ne contrôle pas ces facteurs, elle ne peut pas influer sur la sélection du contrôleur de domaine pour changer un mot de passe utilisateur.

Un moyen d’atteindre en partie cet objectif consisterait à déployer la protection par mot de passe Azure AD sur tous les contrôleurs de domaine dans un site Active Directory donné. Cette approche fournira une couverture raisonnable aux clients Windows qui sont attribués à ce site, et donc également aux utilisateurs qui se connectent à ces clients et changent leur mot de passe.

**Q : Si j’installe le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD sur le contrôleur de domaine principal seulement, est-ce que tous les autres contrôleurs de domaine dans le domaine sont aussi protégés ?**

Non. Quand un mot de passe utilisateur est changé sur un contrôleur de domaine donné qui n’est pas contrôleur de domaine principal, le mot de passe en texte clair n’est jamais envoyé au contrôleur de domaine principal (il s’agit d’une idée fausse répandue). Dès lors qu’un nouveau mot de passe est accepté sur un contrôleur de domaine donné, ce contrôleur de domaine utilise le mot de passe pour créer les différents hachages spécifiques au protocole d’authentification de ce mot de passe, puis conserve ces hachages dans le répertoire. Le mot de passe en texte clair n’est pas gardé. Les hachages mis à jour sont ensuite répliqués sur le contrôleur de domaine principal. Les mots de passe utilisateur peuvent, dans certains cas, être changés directement sur le contrôleur de domaine principal, encore une fois en fonction de divers facteurs, tels que la topologie du réseau et la conception du site Active Directory. (Consultez la question précédente.)

En résumé, le déploiement du service d’agent contrôleur de domaine de la protection par mot de passe Azure AD sur le contrôleur de domaine principal est indispensable pour obtenir 100 % de la couverture de sécurité sur le domaine pour la fonctionnalité. Le seul déploiement de la fonctionnalité sur le contrôleur de domaine principal ne fournit pas les avantages de la sécurité basée sur la protection par mot de passe Azure AD aux autres contrôleurs de domaine dans le domaine.

**Q : Pourquoi le verrouillage intelligent personnalisé ne fonctionne-t-il pas même après l’installation des agents dans mon environnement Active Directory local ?**

Le verrouillage intelligent personnalisé est pris en charge uniquement dans Azure AD. Les modifications apportées aux paramètres de verrouillage intelligent personnalisé dans le portail Azure n’ont aucune incidence sur l’environnement Active Directory local, même avec les agents installés.

**Q : Existe-il un pack d’administration System Center Operations Manager disponible pour la protection par mot de passe Azure AD ?**

Non.

**Q : Pourquoi Azure AD refuse-t-il toujours les mots de passe faibles, même si j’ai configuré la stratégie en mode Audit ?**

Le mode Audit est uniquement pris en charge dans l’environnement Active Directory local. Azure AD est implicitement toujours en mode « appliquer » lorsqu’il évalue les mots de passe.

**Q : Mes utilisateurs voient le message d’erreur Windows classique lorsqu’un mot de passe est rejeté par la protection par mot de passe Azure AD. Est-il possible de personnaliser ce message d’erreur afin que les utilisateurs sachent ce qui s’est réellement produit ?**

Non. Le message d’erreur, affiché par les utilisateurs lorsqu’un mot de passe est rejeté par un contrôleur de domaine, est contrôlé par la machine cliente, et non par le contrôleur de domaine. Ce comportement se manifeste si un mot de passe est rejeté par les stratégies de mot de passe Active Directory par défaut, ou par une solution basée sur un filtre de mot de passe, comme la protection par mot de passe Azure AD.

## <a name="password-testing-procedures"></a>Procédures de test de mot de passe

Vous pouvez effectuer quelques tests de base sur différents mots de passe afin de valider le bon fonctionnement du logiciel et de mieux comprendre l’[algorithme d’évaluation des mots de passe](concept-password-ban-bad.md#how-are-passwords-evaluated). Cette section décrit une méthode de test conçue pour produire des résultats reproductibles.

Pourquoi est-il nécessaire de suivre ces étapes ? Plusieurs facteurs font qu’il est difficile de procéder à des tests contrôlés et reproductibles des mots de passe dans l’environnement Active Directory local :

* La stratégie de mot de passe est configurée et conservée dans Azure, et les copies de la stratégie sont synchronisées périodiquement par les agents DC locaux à l’aide d’un mécanisme d’interrogation. La latence inhérente à ce cycle d’interrogation peut être source de confusion. Par exemple, si vous configurez la stratégie dans Azure, mais que vous oubliez de la synchroniser avec l’agent DC, vos tests risquent de ne pas produire les résultats escomptés. L’intervalle d’interrogation est actuellement codé en dur pour se produire une fois par heure, mais attendre une heure entre deux modifications de stratégie n’est pas idéal pour un scénario de test interactif.
* Une fois qu’une nouvelle stratégie de mot de passe est synchronisée avec un contrôleur de domaine, la latence sera plus importante lors de la réplication vers d’autres contrôleurs de domaine. Ces délais peuvent entraîner des résultats inattendus si vous testez une modification de mot de passe sur un contrôleur de domaine qui n’a pas encore reçu la dernière version de la stratégie.
* Le fait de tester des modifications de mot de passe via une interface utilisateur rend difficile la confiance dans vos résultats. Par exemple, il est facile de mal saisir un mot de passe non valide dans une interface utilisateur, en particulier dans la mesure où la plupart des interfaces utilisateur de mot de passe masquent les entrées utilisateur (par exemple, l’interface utilisateur Windows Ctrl+Alt+Supprimer -> Changer le mot de passe).
* Il n’est pas possible de contrôler strictement quel contrôleur de domaine est utilisé lors des tests de modification de mot de passe à partir de clients joints à un domaine. Le système d’exploitation client Windows sélectionne un contrôleur de domaine en fonction de facteurs tels que des attributions de site et de sous-réseau Active Directory, une configuration réseau spécifique à l’environnement, etc.

Afin d’éviter ces problèmes, les étapes ci-dessous sont basées sur le test de la réinitialisation de mot de passe par ligne de commande lors de la connexion à un contrôleur de domaine.

> [!WARNING]
> Ces procédures doivent être utilisées uniquement dans un environnement de test, car toutes les modifications et réinitialisations de mot de passe entrantes seront acceptées sans validation pendant l’arrêt du service de l’agent DC, ainsi que pour éviter les risques accrus inhérents à la connexion à un contrôleur de domaine.

Les étapes suivantes supposent que vous avez installé l’agent DC sur au moins un contrôleur de domaine, que vous avez installé au moins un proxy et que vous avez inscrit le proxy et la forêt.

1. À l’aide des informations d’identification d’administrateur de domaine (ou d’autres informations d’identification disposant de privilèges suffisants pour créer des comptes d’utilisateur de test et réinitialiser les mots de passe), connectez-vous au contrôleur de domaine sur lequel est installé le logiciel de l’agent DC et qui a été redémarré.
1. Ouvrez Observateur d’événements et accédez au [journal des événements d’administration de l’agent DC](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Ouvrez une fenêtre d’invite de commandes avec élévation de privilèges.
1. Créez un compte de test pour tester les mots de passe.

   Il existe de nombreuses façons de créer un compte d’utilisateur, mais une option de ligne de commande est proposée ici pour vous faciliter la tâche lors des cycles de tests répétitifs :

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Pour les besoins de la discussion ci-dessous, supposons que nous avons créé un compte de test nommé « ContosoUser », par exemple :

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Ouvrez un navigateur web (vous devrez peut-être utiliser un appareil distinct au lieu de votre contrôleur de domaine), connectez-vous au [portail Azure](https://portal.azure.com) et accédez à Azure Active Directory > Sécurité > Méthodes d’authentification > Protection par mot de passe.
1. Modifiez la stratégie de protection par mot de passe Azure AD selon les besoins des tests que vous souhaitez effectuer.  Par exemple, vous pouvez décider de configurer le mode Appliqué ou le mode Audit, ou vous pouvez décider de modifier la liste des termes interdits dans votre liste personnalisée des mots de passe interdits.
1. Synchronisez la nouvelle stratégie en arrêtant et en redémarrant le service de l’agent DC.

   Cette étape peut être réalisée de différentes façons. Une façon serait d’utiliser la console d’administration de management des services en cliquant avec le bouton droit sur le service Protection par mot de passe Azure AD de l’agent DC et en choisissant « Redémarrer ». Une autre méthode peut être utilisée à partir de la fenêtre d’invite de commandes comme suit :

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Consultez l’observateur d’événements pour vérifier qu’une nouvelle stratégie a été téléchargée.

   Chaque fois que le service de l’agent DC est arrêté et démarré, vous devez voir deux événements 30006 émis à la suite. Le premier événement 30006 reflète la mise en cache de la stratégie sur le disque, dans le partage sysvol. Le deuxième événement 30006 (le cas échéant) doit avoir une date de stratégie Locataire mise à jour et, si tel est le cas, il reflète le téléchargement de la stratégie à partir d’Azure. La valeur de date de la stratégie Locataire est actuellement codée pour afficher l’heure approximative à laquelle la stratégie a été téléchargée à partir d’Azure.
   
   Si le deuxième événement 30006 n’apparaît pas, vous devez résoudre le problème avant de continuer.
   
   Les événements 30006 se présentent comme suit :
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Par exemple, le passage du mode Appliqué au mode Audit entraîne la modification de l’indicateur AuditOnly (la stratégie ci-dessus avec AuditOnly=0 est en mode Appliqué) ; les modifications apportées à la liste personnalisée des mots de passe interdits ne sont pas reflétées directement dans l’événement 30006 ci-dessus (et ne sont pas consignées ailleurs pour des raisons de sécurité). Le téléchargement de la stratégie à partir d’Azure après une telle modification inclut également la liste personnalisée des mots de passe interdits modifiée.

1. Effectuez un test en tentant de réinitialiser un nouveau mot de passe sur le compte d’utilisateur de test.

   Cette étape peut être effectuée à partir de la fenêtre d’invite de commandes comme suit :

   ```text
   net.exe user ContosoUser <password>
   ```

   Après avoir exécuté la commande, vous pouvez obtenir plus d’informations sur le résultat de la commande en consultant l’observateur d’événements. Les résultats de la validation du mot de passe sont documentés dans la rubrique [Journal des événements d’administration de l’agent DC](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log). Vous utiliserez ces événements pour valider le résultat de votre test en plus de la sortie interactive des commandes net.exe.

   Essayons un exemple : tenter de définir un mot de passe qui est interdit par la liste globale de Microsoft (notez que cette liste [n’est pas documentée](concept-password-ban-bad.md#global-banned-password-list), mais nous pouvons ici faire le test par rapport à un terme interdit connu). Cet exemple suppose que vous avez configuré la stratégie en mode Appliqué et que vous avez ajouté zéro terme à la liste personnalisée des mots de passe interdits.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Conformément à la documentation, étant donné que notre test était une opération de réinitialisation de mot de passe, vous devriez voir un événement 10017 et un événement 30005 pour l’utilisateur ContosoUser.

   L’événement 10017 doit se présenter comme dans l’exemple suivant :

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   L’événement 30005 doit se présenter comme dans l’exemple suivant :

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Essayons un autre exemple ! Cette fois, nous allons tenter de définir un mot de passe qui est interdit par la liste personnalisée des mots de passe interdits alors que la stratégie est en mode Audit. Cet exemple suppose que vous avez effectué les étapes suivantes : vous avez configuré la stratégie en mode Audit, ajouté le terme « larmoyant » à la liste personnalisée des mots de passe interdits et synchronisé la nouvelle stratégie résultante avec le contrôleur de domaine en effectuant un cycle du service de l’agent DC comme décrit ci-dessus.

   Maintenant que tout est bon, définissez une variante du mot de passe interdit :

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   N’oubliez pas que, cette fois-ci, l’opération a réussi, car la stratégie est en mode Audit. Vous devez voir un événement 10025 et un événement 30007 pour l’utilisateur ContosoUser.

   L’événement 10025 doit se présenter comme dans l’exemple suivant :
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   L’événement 30007 doit se présenter comme dans l’exemple suivant :

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Continuez à tester les différents mots de passe de votre choix et vérifiez les résultats dans l’observateur d’événements à l’aide des procédures décrites dans les étapes précédentes. Si vous avez besoin de modifier la stratégie dans le portail Azure, n’oubliez pas de synchroniser la nouvelle stratégie avec l’agent DC comme décrit précédemment.

Nous avons traité des procédures qui vous permettent de tester de manière contrôlée le comportement de validation des mots de passe de la capacité Protection par mot de passe Azure AD. La réinitialisation des mots de passe utilisateur à partir de la ligne de commande directement sur un contrôleur de domaine peut paraître un moyen étrange d’effectuer de tels tests, mais, comme décrit précédemment, il est conçu pour produire des résultats reproductibles. Lorsque vous testez différents mots de passe, gardez l’[algorithme d’évaluation des mots de passe](concept-password-ban-bad.md#how-are-passwords-evaluated) à l’esprit, car cela peut aider à expliquer des résultats auxquels vous ne vous attendiez pas.

> [!WARNING]
> Une fois tous les tests terminés, n’oubliez pas de supprimer tous les comptes d’utilisateur créés à des fins de test !

## <a name="additional-content"></a>Contenu supplémentaire

Les liens suivants ne font pas partie de la documentation de base se rapportant à la protection par mot de passe Azure AD, mais peuvent être une source utile d’informations supplémentaires sur cette fonctionnalité.

[Protection par mot de passe Azure AD désormais disponible au grand public !](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guide de protection contre le hameçonnage par e-mail – Partie 15 : Implémenter le service de la protection par mot de passe de Microsoft Azure AD (en local également !)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[La protection par mot de passe Azure AD et le verrouillage intelligent sont désormais disponibles en préversion publique !](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Support de formation Premier\Unifié Microsoft disponible

Si la protection par mot de passe Azure AD et son déploiement dans votre environnement vous intéressent, vous pouvez profiter d’un service proactif de Microsoft, disponible pour les clients qui ont un contrat de support Premier ou Unifié. Le service s’appelle Azure Active Directory : Protection par mot de passe. Pour plus d’informations, contactez votre responsable technique de compte.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez une question sur la protection par mot de passe Azure AD en local qui serait restée sans réponse ici, adressez-nous votre question en soumettant votre commentaire ci-dessous. Merci !

[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)