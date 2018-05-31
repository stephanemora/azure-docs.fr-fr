---
title: 'Azure Active Directory Connect : Forum Aux Questions | Microsoft Docs'
description: Cette page comporte le Forum Aux Questions relatif à Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054092"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Forum Aux Questions sur Azure Active Directory Connect

## <a name="general-installation"></a>Installation générale
**Q : L’installation fonctionnera-t-elle si l’option 2FA est activée pour l’administrateur global AD Azure ?**  
Cela est pris en charge avec les builds de février 2016.

**Q : Existe-t-il une manière d’installer Azure AD Connect sans assistance ?**  
L’installation d’Azure AD Connect n’est possible qu’à partir de l’Assistant Installation. Une installation automatique et silencieuse n’est pas prise en charge.

**Q : Je possède une forêt dans laquelle un domaine ne peut pas être contacté. Comment installer Azure AD Connect ?**  
Cela est pris en charge avec les builds de février 2016.

**Q : L’agent AD DS Health fonctionne-t-il sur Server Core ?**  
Oui. Après avoir installé l’agent, vous pouvez terminer le processus d’inscription à l’aide de l’applet de commande PowerShell suivante : 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q : AADConnect prend-il en charge la synchronisation à partir de deux domaines vers Azure AD ?**</br>
Oui, cette méthode est prise en charge. Reportez-vous à [Plusieurs domaines](active-directory-aadconnect-multiple-domains.md).
 
**Q : Est-il possible d’avoir plusieurs connecteurs pour le même domaine Active Directory dans Azure AD Connect ?**</br> Non. Ce n’est pas possible. 

## <a name="network"></a>Réseau
**Q : J’ai un pare-feu, un périphérique réseau ou autre chose qui limite la durée maximale pendant laquelle les connexions peuvent rester ouvertes sur mon réseau. Quel doit être le seuil de délai côté client lors de l’utilisation d’Azure AD Connect ?**  
Tous les logiciels réseau, périphériques physiques ou autres limitant la durée maximale pendant laquelle les connexions peuvent rester ouvertes doivent utiliser un seuil d'au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où est installé le client Azure AD Connect et Azure Active Directory. Cela s'applique également à tous les outils de synchronisation de Microsoft Identity précédemment publiés.

**Q : Les domaines avec un nom en une seule partie sont-ils pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux utilisant des noms de domaine en une seule partie.

**Q : Les forêts comportant des domaines AD disjoints sont-elles prises en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts locales contenant des espaces de noms disjoints.

**Q : Les noms NetBios comportant un point sont-ils pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge les forêts/domaines locaux  dont le nom NetBios contient un point « . ».

**Q : L'environnement IPv6 pur est-il pris en charge ?**  
Non, Azure AD Connect ne prend pas en charge l'environnement IPv6 pur.

## <a name="federation"></a>Fédération
**Q : Que faire si je reçois un e-mail me demandant de renouveler mon certificat Office 365**  
Suivez les instructions décrites dans la rubrique [Renouveler les certificats](active-directory-aadconnect-o365-certs.md) concernant le renouvellement du certificat.

**Q : « Mettre à jour automatiquement la partie de confiance » est défini pour la partie de confiance Office 365. Dois-je effectuer une action lorsque mon certificat de signature de jetons bascule automatiquement ?**  
Utilisez les instructions décrites dans l’article [Renouveler les certificats](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environnement
**Q : Est-il possible de renommer le serveur une fois qu’Azure AD Connect est installé ?**  
Non. La modification du nom du serveur empêche le moteur de synchronisation de se connecter à la base de données SQL et le service de démarrer.

## <a name="identity-data"></a>Données d’identité
**Q : L’attribut UPN (userPrincipalName) dans Azure AD ne correspond pas à l’UPN local. Pourquoi ?**  
Reportez-vous aux articles suivants :

* [Les noms d’utilisateur dans Office 365, Azure ou Intune ne correspondent pas à l’UPN local ou à l’ID de connexion secondaire](https://support.microsoft.com/en-us/kb/2523192)
* [Les modifications ne sont pas synchronisées par l’outil de synchronisation Azure Active Directory une fois que vous avez modifié l’UPN d’un compte utilisateur afin qu’il utilise un autre domaine fédéré](https://support.microsoft.com/en-us/kb/2669550)

Vous pouvez également configurer Azure AD pour permettre au moteur de synchronisation de mettre à jour userPrincipalName comme décrit dans [fonctionnalités du service de synchronisation Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**Q : Est-ce que la correspondance approximative des objets Groupe/Contact AD locaux avec les objets Azure AD Groupe/Contact existants est prise en charge ?**  
Oui, et elle est basée sur l’adresse proxy.  La correspondance approximative n’est pas prise en charge pour les groupes qui ne sont pas à extension messagerie.

**Q : Est-il possible de définir manuellement l’attribut ImmutableId sur des objets Groupe/Contact Azure AD existants pour la mise en correspondance exacte avec les objets Groupe/Contact locaux ?**  
Non, cela n’est pas pris en charge actuellement.

## <a name="custom-configuration"></a>Configuration personnalisée
**Q : Où réside la documentation sur les applets de commande PowerShell pour Azure Active Directory ?**  
À l’exception des applets de commande décrites sur ce site, les autres applets de commande PowerShell disponibles dans Azure AD Connect ne sont pas prises en charge par le client.

**Q : Puis-je utiliser « Exportation serveur/importation serveur » dans *Synchronization Service Manager* pour déplacer la configuration entre des serveurs ?**  
Non. Cette option ne récupérera pas tous les paramètres de configuration et ne doit pas être utilisée. Vous devez plutôt utiliser l’Assistant pour créer la configuration de base sur le deuxième serveur et utiliser l’éditeur de règles de synchronisation pour générer des scripts PowerShell afin de déplacer une règle personnalisée entre les serveurs. Consultez la section [Migration « Swing »](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Q : Les mots de passe peuvent-ils être mis en cache pour la page de connexion Azure, et est-il possible d’empêcher cela, étant donné que la page contient un élément d’entrée de mot de passe avec l’attribut autocomplete = "false" ?**</br>
Pour l’instant, nous ne prenons pas en charge la modification des attributs HTML du champ d’entrée de mot de passe, et notamment de la balise « autocomplete ». Nous travaillons actuellement à l’élaboration d’une fonctionnalité d’autorisation d’un code Javascript personnalisé qui vous permettra d’ajouter n’importe quel attribut au champ du mot de passe. Cette fonctionnalité devrait être accessible courant 2017.

**Q : La page de connexion Azure affiche les noms d’utilisateur des utilisateurs qui se sont déjà connectés avec succès.  Est-il possible de désactiver ce comportement ?**</br>
Pour l’instant, nous ne prenons pas en charge la modification des attributs HTML de la page de connexion. Nous travaillons actuellement à l’élaboration d’une fonctionnalité d’autorisation d’un code Javascript personnalisé qui vous permettra d’ajouter n’importe quel attribut au champ du mot de passe. Cette fonctionnalité devrait être accessible courant 2017.

**Q : Existe-t-il un moyen d’empêcher les sessions simultanées ?**</br>
Non.

## <a name="auto-upgrade"></a>Mise à niveau automatique

**Q : quels sont les avantages et les conséquences de l’utilisation de la mise à niveau automatique ?**</br>
Nous conseillons à tous les clients d’activer la mise à niveau automatique pour leur installation d’Azure AD Connect. Parmi les avantages pour les clients : ils recevront toujours les derniers correctifs, y compris les mises à jour de sécurité pour les vulnérabilités que nous trouvons dans Azure AD Connect. Le processus de mise à niveau est simple et se fait automatiquement dès qu’une nouvelle version est disponible. Nous répondons aux besoins de plusieurs milliers de clients Azure AD Connect via la mise à niveau automatique avec chaque nouvelle version.

Le processus de mise à niveau automatique commence toujours par établir si une installation est éligible pour la mise à niveau automatique (cela inclut la recherche de modifications personnalisées apportées aux règles, les facteurs environnementaux spécifiques, etc.) et si c’est le cas, la mise à niveau est effectuée et testée. Si les tests indiquent qu’une mise à niveau a échoué, la version précédente est automatiquement restaurée.

Selon la taille de l’environnement, le processus peut durer quelques heures, et pendant la mise à niveau, aucune synchronisation entre Windows Server AD et Azure AD ne se produit.

**Q : j’ai reçu un e-mail m’indiquant que ma mise à niveau automatique ne fonctionnait plus et que je dois installer la nouvelle version. Pourquoi dois-je effectuer cette opération ?**</br>
L’année dernière, nous avons publié une version d’Azure AD Connect qui, dans certaines circonstances, désactivait la fonctionnalité de mise à niveau automatique sur votre serveur. Nous avons résolu ce problème dans la version 1.1.750.0 d’Azure AD Connect, qui a été publiée à la fin du mois dernier. Les clients susceptibles d’avoir été affectés par ce problème doivent effectuer une mise à niveau manuelle vers la version la plus récente d’Azure AD Connect pour résoudre le problème. Pour effectuer manuellement la mise à niveau, vous devez télécharger et exécuter la dernière version du fichier AADConnect.msi.
 
-  Si votre version actuelle est antérieure à la version 1.1.750.0, vous devez mettre à niveau vers la dernière version, [qui peut être téléchargée ici](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Si votre version d’Azure AD Connect est la version 1.1.750.0 ou ultérieure, vous n’avez rien à faire pour résoudre le problème lié à la mise à niveau automatique, dans la mesure où vous disposez déjà de la version possédant le correctif. 

**Q : j’ai reçu un e-mail me demandant de mettre à niveau vers la version la plus récente pour réactiver la mise à niveau automatique. Je dispose de la version 1.1.654.0, ai-je besoin d’effectuer la mise à niveau ?** </br>    
Oui, vous devez mettre à niveau vers la version 1.1.750 ou une version ultérieure pour réactiver la mise à niveau automatique. Voici le lien qui explique comment mettre à niveau vers une version plus récente.

**Q : j’ai reçu un e-mail me demandant de mettre à niveau vers la version la plus récente pour réactiver la mise à niveau automatique. J’ai utilisé PowerShell pour activer la mise à niveau automatique, dois-je installer la dernière version ?**</br>    
Oui, vous devez tout de même mettre à niveau vers la version 1.1.750.0 ou une version ultérieure. L’activation du service de mise à niveau automatique avec PowerShell ne résout pas le problème de mise à niveau automatique repéré dans les versions antérieures à la version 1.1.750.

**Q : je souhaite mettre à niveau vers une version plus récente, mais je ne sais pas qui a installé Azure AD Connect et nous n’avons pas le nom d’utilisateur et le mot de passe.  En avons-nous besoin ?**</br>
Vous n’avez pas besoin de connaître le nom d’utilisateur et le mot de passe utilisés pour mettre à niveau Azure AD Connect. N’importe quel compte Azure AD disposant du rôle Administrateur général peut être utilisé.

**Q : comment puis-je savoir de quelle version d’Azure AD Connect je dispose ?**</br>   
Pour vérifier la version d’Azure AD Connect installée sur votre serveur, accédez au Panneau de configuration et recherchez la version de Microsoft Azure AD Connect installée sous « Programmes > Programmes et fonctionnalités » :

![version](media/active-directory-aadconnect-faq/faq1.png)

**Q : comment effectuer une mise à niveau vers la dernière version de AADConnect ?**</br>    
Cet [article](active-directory-aadconnect-upgrade-previous-version.md) explique comment mettre à niveau vers une version plus récente. 

**Q : nous avons déjà effectué une mise à niveau vers la dernière version de AADConnect l’année dernière. Devons-nous procéder à une nouvelle mise à niveau ?**</br> Les équipes d’Azure AD Connect mettent régulièrement à jour le service, et il est important que votre serveur soit à jour avec la version la plus récente pour tirer parti des correctifs de bogues et des mises à jour de sécurité, ainsi que des nouvelles fonctionnalités. Si vous activez la mise à niveau automatique, votre version du logiciel sera automatiquement mise à jour. Pour rechercher l’historique des versions d’Azure AD Connect, veuillez suivre ce [lien](active-directory-aadconnect-version-history.md).

**Q : combien de temps faut-il pour effectuer la mise à niveau et quel est l’impact sur mes utilisateurs ?**</br>    
Le temps nécessaire à la mise à niveau dépend de la taille de votre locataire. Pour les organisations de grande taille, il peut être préférable de procéder à la mise à niveau dans la soirée ou le week-end. Notez que, lors de la mise à niveau, aucune activité de synchronisation n’a lieu.

**Q : je pense que j’ai effectué la mise à niveau vers AADConnect, mais dans le portail Office, il est toujours fait mention de DirSync.  Pourquoi ?**</br>    
L’équipe Office travaille actuellement pour que les mises à jour du portail Office reflètent le nom du produit actuel : il ne reflète pas l’outil de synchronisation que vous utilisez.

**Q : j’ai vérifié mon statut de mise à niveau automatique et il indique « Suspendu ». Pourquoi ? Dois-je l’activer ?**</br>     
Un bogue a été introduit dans une version précédente : dans certains cas, l’état de la mise à niveau automatique a pris la valeur « Suspendu ». Il est techniquement possible de l’activer manuellement, mais cela nécessite plusieurs étapes complexes. Par conséquent, la meilleure chose à faire est d’installer la version la plus récente d’Azure AD Connect.

**Q : mon entreprise a des exigences strictes en matière de gestion des changements et je souhaite contrôler à quel moment les mises à niveau ont lieu. Puis-je contrôler le lancement des mises à niveau automatiques ?**</br> Non, cette fonctionnalité n’existe pas à l’heure actuelle, mais il s’agit de quelque chose que nous envisageons pour une version ultérieure.

**Q : recevrai-je un e-mail en cas d’échec de la mise à niveau automatique ? Comment savoir si elle a réussi ?**</br>     
Vous ne serez pas informé du résultat de la mise à niveau. Il s’agit d’une fonctionnalité que nous envisageons pour une version ultérieure.

**Q : publiez-vous un calendrier des mises à niveau automatiques prévues ?**</br>    
La mise à niveau automatique est la première étape de notre processus de lancement d’une nouvelle version. Chaque fois qu’une nouvelle version est publiée, nous mettons en place une mise à jour automatique. Les nouvelles versions d’Azure AD Connect sont préalablement annoncées dans la [feuille de route Azure AD](../../active-directory/whats-new.md).

**Q : la mise à niveau automatique met-elle à niveau AAD Connect Health ?**</br>   Oui, la mise à niveau automatique met également à niveau AAD Connect Health.

**Q : voulez-vous également mettre à niveau automatiquement les serveurs AAD Connect en mode de préproduction ?**</br>   
Non, vous ne pouvez pas mettre à niveau automatiquement un serveur Azure AD Connect qui est en mode de préproduction.

**Q : si la mise à niveau automatique échoue et que mon serveur AAD Connect ne démarre pas, que dois-je faire ?**</br>   
Dans de rares cas, le service Azure AD Connect ne démarre pas après la mise à niveau. Dans ces cas, redémarrez le serveur, ce qui permet généralement de résoudre le problème. Si le service Azure AD Connect ne démarre toujours pas, ouvrez un ticket de support. Voici un [lien](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) qui explique comment procéder. 

**Q : je ne sais pas quels sont les risques liés à la mise à niveau vers une version plus récente d’Azure AD Connect. Pouvez-vous m’appeler pour m’aider à procéder à la mise à niveau ?**</br>
Si vous avez besoin d’aide pour la mise à niveau vers une version plus récente d’Azure AD Connect, ouvrez un ticket de support. Voici un [lien](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) qui explique comment procéder.

## <a name="troubleshooting"></a>Résolution de problèmes
**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Recherche dans la Base de connaissances Microsoft (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Recherchez dans la Base de connaissances Microsoft (KB) des solutions techniques aux problèmes courants couverts par la garantie de réparation et d’assistance, relatifs à la prise en charge d’Azure AD Connect.

[Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Vous pouvez naviguer et rechercher des questions et des réponses techniques de la communauté ou poser votre question en cliquant [ici](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Comment bénéficier du support pour Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Cliquez sur ce lien pour bénéficier du support par le biais du portail Azure.

