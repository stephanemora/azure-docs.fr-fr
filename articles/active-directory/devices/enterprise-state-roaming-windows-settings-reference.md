---
title: Informations de référence sur les paramètres d’itinérance de Windows 10 - Azure Active Directory
description: Paramètres pour l’itinérance ou la sauvegarde dans Windows 10 avec Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a75b662fabcce8ffa64f1b705e86a1c1dde625
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194328"
---
# <a name="windows-10-roaming-settings-reference"></a>Référence des paramètres d’itinérance Windows 10

Voici une liste des paramètres destinés à l’itinérance ou à la sauvegarde dans Windows 10. 

## <a name="devices-and-endpoints"></a>Appareils et points de terminaison

Consultez le tableau suivant pour obtenir un récapitulatif des périphériques et des types de comptes pris en charge par la structure de synchronisation, de sauvegarde et de restauration framework dans Windows 10.

| Type de compte et fonctionnement | Bureau | Mobile |
| --- | --- | --- |
| Azure Active Directory : synchronisation |Oui |Non |
| Azure Active Directory : sauvegarde/restauration |Non |Non |
| Compte Microsoft : synchronisation |Oui |Oui |
| Compte Microsoft : sauvegarde/restauration |Non |Oui |

## <a name="what-is-backup"></a>Qu’est-ce qu’une sauvegarde ?

En règle générale, les paramètres Windows se synchronisent par défaut, cependant, certains paramètres sont simplement sauvegardés, notamment la liste des applications installées sur un périphérique. La sauvegarde est destinée uniquement aux appareils mobiles et n’est pas disponible pour les utilisateurs d’Enterprise State Roaming. La sauvegarde utilise un compte Microsoft et stocke les paramètres et données d’application dans OneDrive. Si, sur le périphérique , un utilisateur désactive la synchronisation avec l’application Settings, les données de l’application normalement synchronisées se transforment en simple sauvegarde. Les données de sauvegarde sont uniquement accessibles via l’opération de restauration lors de la première utilisation d’un nouvel appareil. Les sauvegardes peuvent être désactivées via les paramètres du périphérique et peuvent être gérées et supprimées via le compte OneDrive de l’utilisateur.

## <a name="windows-settings-overview"></a>Vue d’ensemble des paramètres Windows

Les groupes de paramètres suivants sont disponibles pour les utilisateurs finaux afin d’activer/désactiver la synchronisation des paramètres sur les appareils Windows 10.

* Thème : Arrière-plan du Bureau, Vignette de l’utilisateur, Position de la barre des tâches, etc. 
* Paramètres Internet Explorer : Historique de navigation, URL saisies, Favoris, etc. 
* Mots de passe : Gestionnaire d'informations d'identification Windows, incluant le profils Wi-Fi 
* Préférences linguistiques : Dictionnaire, Paramètres de langue système 
* Options d’ergonomie : Narrateur, Loupe, Clavier visuel 
* Autres paramètres Windows : consultez les détails de Paramètres Windows
* Paramétrage du navigateur Microsoft Edge : Favoris Microsoft Edge, liste de lecture et autres paramètres

![Synchroniser vos paramètres](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Cet article s’applique au précédent navigateur HTML Microsoft Edge qui a été lancé avec Windows 10 en juillet 2015. L’article ne s’applique pas au nouveau navigateur Microsoft Edge basé sur Chromium publié le 15 janvier 2020. Pour plus d’informations sur le comportement de synchronisation pour le nouveau navigateur Microsoft Edge, consultez l’article [Synchronisation de Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-enterprise-sync).

La synchronisation de groupes de paramètres pour le navigateur Microsoft Edge (favoris, liste de lecture) peut être activée ou désactivée par les utilisateurs finaux dans le menu Paramètres du navigateur Microsoft Edge.

![Compte](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Pour Windows 10 version 1803 ou ultérieure, la synchronisation de groupe de paramètres Internet Explorer (Favoris, URL saisies) peut être activée ou désactivée par les utilisateurs finaux via l’option de menu Paramètres d’Internet Explorer. 

![Paramètres](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Détails des Paramètres Windows

Dans le tableau qui suit, les autres entrées de la colonne Groupe de paramètres font référence aux paramètres pouvant être désactivés en accédant à Paramètres > Comptes > Synchroniser vos paramètres > Autres paramètres Windows. 

Les entrées internes dans la colonne Groupe de paramètres font référence aux paramètres et aux applications qui ne peuvent être désactivées de la synchronisation qu’au sein de l’application elle-même ou par la désactivation de l’ensemble du périphérique à l’aide des paramètres Gestion des périphériques mobiles ou des paramètres de stratégie de groupe.
Les paramètres non itinérants ou la synchronisation n’appartiennent pas à un groupe.

| Paramètres | Bureau | Mobile | Groupe |
| --- | --- | --- | --- |
| **Comptes**: avatar du compte |sync |X |Thème |
| **Comptes**: autres paramètres de compte |X |X | |
| **Bande passante mobile avancée** : Nom du réseau de partage de connexion Internet (active la découverte automatique des points d’accès Wi-Fi mobiles via Bluetooth) |X |X |Mots de passe |
| **Données d’application**: les applications individuelles peuvent synchroniser des données |sauvegarde de synchronisation |sauvegarde de synchronisation |interne |
| **Liste d’applications**: liste des applications installées |X |sauvegarde |Autres |
| **Bluetooth**: tous les paramètres Bluetooth |X |X | |
| **Invite de commandes** : Paramètres « Defaults » de l’invite de commandes |sync |X |interne |
| **Informations d’identification** : Stockage sécurisé des informations d’identification |sync |sync |password |
| **Date, heure et région**: horaire automatique (synchronisation Internet) |sync |sync |langage |
| **Date, heure et région** : horloge de 24 heures |sync |X |langage |
| **Date, heure et région**: date et heure |sync |X |langage |
| **Date, heure et région**: fuseau horaire | |X |langage |
| **Date, heure et région**: heure d’été |sync |X |langage |
| **Date, heure et région**: pays/région |sync |X |langage |
| **Date, heure et région**: premier jour de la semaine |sync |X |langage |
| **Date, heure et région**: format régional (paramètres régionaux) |sync |X |langage |
| **Date, heure et région**: date courte |sync |X |langage |
| **Date, heure et région**: date longue |sync |X |langage |
| **Date, heure et région**: heure courte |sync |X |langage |
| **Date, heure et région**: heure complète |sync |X |langage |
| **Personnalisation du bureau**: thème du bureau (arrière-plan, couleur système, sons système par défaut, écran de veille) |sync |X |Thème |
| **Personnalisation du bureau**: papier peint en diaporama |sync |X |Thème |
| **Personnalisation du bureau**: paramètres de la barre des tâches (position, masquage automatique, etc.) |sync |X |Thème |
| **Personnalisation du bureau**: disposition de l’écran d’accueil |X |sauvegarde | |
| **Appareils**: imprimantes partagées auxquelles vous êtes connectées |X |X |Autres |
| **Navigateur Microsoft Edge** : liste de lecture |sync |sync |interne |
| **Navigateur Microsoft Edge** : favoris |sync |sync |interne |
| **Navigateur Microsoft Edge** : sites les plus consultés <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : sites les plus consultés <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : paramètres de la barre des favoris <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : afficher le bouton Accueil <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : bloquer les fenêtres publicitaires <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : me demander ce qu’il faut faire avec chaque téléchargement <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : proposer d’enregistrer les mots de passe <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : envoyer les demandes Ne pas me suivre <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : enregistrer les entrées des formulaires <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : afficher les suggestions de recherche et de site au fil de la frappe <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : préférences pour les cookies <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : permettre aux sites d’enregistrer les licences des médias protégés sur mon appareil <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Navigateur Microsoft Edge** : paramètre du lecteur d’écran <sup>[[1]](#footnote-1)</sup> |sync |sync |interne |
| **Contraste élevé** : Activé ou désactivé |sync |X |options d’ergonomie |
| **Contraste élevé** : Paramètres du thème |sync |X |options d’ergonomie |
| **Internet Explorer**: ouvrir les onglets (URL et titre) |sync |sync |Internet Explorer |
| **Internet Explorer**: liste de lecture |sync |sync |Internet Explorer |
| **Internet Explorer**: URL saisies |sync |sync |Internet Explorer |
| **Internet Explorer**: historique de navigation |sync |sync |Internet Explorer |
| **Internet Explorer**: favoris |sync |sync |Internet Explorer |
| **Internet Explorer**: URL exclues |sync |sync |Internet Explorer |
| **Internet Explorer**: pages d’accueil |sync |sync |Internet Explorer |
| **Internet Explorer**: suggestions de domaine |sync |sync |Internet Explorer |
| **Clavier**: les utilisateurs peuvent activer/désactiver le clavier visuel |sync |X |options d’ergonomie |
| **Clavier**: activer un oui rémanent (désactivé par défaut) |sync |X |options d’ergonomie |
| **Clavier**: activer les clés de filtre (désactivé par défaut) |sync |X |options d’ergonomie |
| **Clavier**: activer les touches bascules (désactivé par défaut) |sync |X |options d’ergonomie |
| **Internet Explorer** : langue du domaine : QWERTY chinois (CHS) - activer l’apprentissage automatique |sync |X |Langage |
| **Langue** : QWERTY CHS - activer le classement de candidats dynamique |sync |X |Langage |
| **Langue** : QWERTY CHS - jeu de caractères chinois simplifié |sync |X |Langage |
| **Langue** : QWERTY CHS - jeu de caractères chinois traditionnel |sync |X |Langage |
| **Langue** : QWERTY CHS - pinyin approximatif |sync |sauvegarde |Langage |
| **Langue** : QWERTY CHS - paires approximatives |sync |sauvegarde |Langage |
| **Langue** : QWERTY CHS - pinyin complet |sync |X |Langage |
| **Langue** : QWERTY CHS - pinyin double |sync |X |Langage |
| **Langue** : QWERTY CHS - lecture de correction automatique |sync |X |Langage |
| **Langue** : QWERTY CHS - touche bascule C/E, MAJ |sync |X |Langage |
| **Langue** : QWERTY CHS - touche bascule C/E, Ctrl |sync |X |Langage |
| **Langue** : WUBI CHS - mode de saisie de caractère unique |sync |X |Langage |
| **Langue** : WUBI CHS - afficher le codage de candidats restant |sync |X |Langage |
| **Langue** : WUBI CHS - émission d’un bip sonore lorsque le codage des 4 coins est non valide |sync |X |Langage |
| **Langue** : Bopomofo CHT - inclure CJK Ext-A |sync |X |Langage |
| **Langue** : IME japonais - frappe prédictive et mots personnalisés |sync |sync |Langage |
| **Langue** : IME Coréen (KOR) |X |X |Langage |
| **Langue**: reconnaissance d’écriture manuscrite |X |X |Langage |
| **Langue**: profil de langue |sync |sauvegarde |Langage |
| **Langue**: vérification orthographique et mise en évidence des fautes d’orthographe |sync |sauvegarde |Langage |
| **Langue**: liste de claviers |sync |sauvegarde |Langage |
| **Écran de verrouillage**: tous les paramètres de verrouillage |X |X | |
| **Loupe**: activer ou désactiver (bascule maître) |X |X |options d’ergonomie |
| **Loupe**: activer ou désactiver les couleurs d’inversion (désactivé par défaut) |sync |X |options d’ergonomie |
| **Loupe**: suivre les éléments sélectionnés au clavier |sync |X |options d’ergonomie |
| **Loupe**: suivre le curseur de la souris |sync |X |options d’ergonomie |
| **Loupe**: démarrer lorsque les utilisateurs se connectent (désactivé par défaut) |sync |X |options d’ergonomie |
| **Souris**: modifier la taille du curseur de la souris |sync |X |Autres |
| **Souris**: modifier la couleur du curseur de la souris |sync |X |Autres |
| **Souris**: tous les autres paramètres |X |X | |
| **Narrateur**: lancement rapide |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent modifier la tonalité du narrateur |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent activer ou désactiver les conseils de lecture du narrateur pour les éléments communs (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent l’activer ou le désactiver s’ils peuvent entendre des caractères tapés (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: les utilisateurs peuvent décider d’activer ou de désactiver le narrateur s’ils entendent les mots (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: insérer le curseur après le Narrateur (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: activer la mise en évidence visuelle du curseur du Narrateur (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: lire les pistes audio (activé par défaut) |sync |X |options d’ergonomie |
| **Narrateur**: activer les touches du clavier tactile lorsque vous levez le doigt (désactivé par défaut) |sync |X |options d’ergonomie |
| **Options d’ergonomie**: définir l’épaisseur du curseur clignotant |sync |X |options d’ergonomie |
| **Options d’ergonomie**: supprimer des images d’arrière-plan (désactivé par défaut) |sync |X |options d’ergonomie |
| **Alimentation et mise en veille**: tous les paramètres |X |X | |
| **Personnalisation de l’écran d’accueil** : couleur d’accentuation (téléphone uniquement) |X |sync |Thème |
| **Frappe**: dictionnaire |sync |sauvegarde |Langage |
| **Frappe**: correction automatique du mot mal orthographié |sync |sauvegarde |Langage |
| **Frappe**: mettre en surbrillance les mots mal orthographiés |sync |sauvegarde |Langage |
| **Frappe**: afficher les suggestions de texte en cours de frappe |sync |sauvegarde |Langage |
| **Frappe**: ajouter un espace après la sélection d’une suggestion de texte |sync |sauvegarde |Langage |
| **Frappe**: ajouter un point après un double-clic sur la barre d’espace |sync |sauvegarde |Langage |
| **Frappe**: mettre une majuscule sur la première lettre de chaque phrase |sync |sauvegarde |Langage |
| **Frappe**: utiliser des lettres majuscules lorsque je double-clique sur la touche Maj |sync |sauvegarde |Langage |
| **Frappe**: les touches émettent des sons pendant la frappe |sync |sauvegarde |Langage |
| **Frappe**: données de personnalisation pour le clavier tactile |sync |sauvegarde |Langage |
| **Wi-Fi** : Profils Wi-Fi (WPA uniquement) |sync |sync |Mots de passe |

###### <a name="footnote-1"></a>Note 1

Version minimale du système d’exploitation prise en charge : Windows Creators Update (Build 15063). 

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble, consultez [Vue d’ensemble d’Enterprise State Roaming](enterprise-state-roaming-overview.md).
