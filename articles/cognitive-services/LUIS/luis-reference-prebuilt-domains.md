---
title: Référence de domaine prédéfini - LUIS
titleSuffix: Azure Cognitive Services
description: Référence pour les domaines prédéfinis, qui sont des collections prédéfinies d’intentions et d’entités de LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: f5810a813b6c54f190d95061e79914457f51d19c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067612"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Référence de domaine prédéfinie pour votre application LUIS
Cette référence fournit des informations sur les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md), qui sont des collections prédéfinies d’intentions et d’entités proposées par LUIS.

Les [domaines personnalisés](luis-how-to-start-new-app.md), en revanche, commencent sans intentions ni modèles. Vous pouvez ajouter des intentions et entités de domaine prédéfini à un modèle personnalisé.

# <a name="supported-domains-across-cultures"></a>Domaines pris en charge dans toutes les cultures

Le tableau ci-dessous résume les domaines actuellement pris en charge. La prise en charge de l’anglais est généralement plus complète que les autres langues. 

| Type d’entité       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Calendrier](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Communication](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [E-mail](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Remarques](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Places](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [ToDo](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Services](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Météo](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Les domaines prédéfinis ne sont pas **pris en charge** en :

* Français (Canada)
* Hindi
* Espagnol (Mexique)

# <a name="description-for-luis-prebuilt-domains"></a>Description des domaines prédéfinis LUIS
## <a name="calendar"></a>**Calendrier**
Le calendrier contient les réunions et les rendez-vous personnels, PAS les événements publics (tels que les planifications de coupe du monde, les calendriers des événements à Seattle) ou les calendriers génériques (qui indiquent quel jour on est, quand l’automne commence, ou quel jour tombe le Labor Day).
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------------
 AcceptEventEntry | Accepter un rendez-vous/une réunion/un événement dans le calendrier. | Accepter un rendez-vous. <br> Accepter l’événement <br> Accepter la réunion d’aujourd’hui.
 Annuler | Annule l’action en cours par l’assistant virtuel, par exemple l’annulation du processus de création d’une réunion. <br> _**Avis** : Cette intention comprend principalement l’action « Annuler » dans le scénario de calendrier. Si vous avez besoin d’une expression générale sur « Annuler », utilisez l’intention « Annuler » dans le domaine **Services**._ | Ça ira, je veux simplement annuler l’événement. <br> Non, j’annule simplement le rendez-vous.
 ChangeCalendarEntry | Modifier ou replanifier l’entrée du calendrier. | Replanifier mon 6 h. Rendez-vous demain à 14 h. <br> Replanifier le rendez-vous du docteur pour 17 h <br> Replanifier le déjeuner avec Jenny Olson pour vendredi. <br> Modifier l’heure de l’événement.
 CheckAvailability | Recherchez la disponibilité pour un rendez-vous ou une réunion dans le calendrier de l’utilisateur ou d’une autre personne. | Quand Jim est-il disponible ? <br> Montrer quand Carole est disponible demain. <br> Chris est-il libre samedi ?
 Confirmer | Confirmez s’il faut effectuer une opération/action en fonction de l’intention précédente. <br> _**Avis** : Cette intention comprend principalement l’action « Confirmer » pour le scénario de calendrier. Si vous avez besoin d’expressions plus générales sur « Confirmer », utilisez l’intention « Confirmer » dans le domaine **Services**._| C’est correct, créer la réunion <br> Oui, merci, connexion à la réunion.
 ConnectToMeeting | Joindre une réunion. | Me connecter à l’appel de conférence à 11 h avec Andy. <br> Accepter l’appel de réunion sur le budget.
 ContactMeetingAttendees | Contacter les participants à la réunion. | Dire à la réunion que je suis en retard pour la réunion de 15 h. <br> Informer les collègues de la réunion de 8 h qu’il faudra commencer à 8 h 30.
 CreateCalendarEntry | Ajoutez un nouvel élément à usage unique au calendrier. | Créer une réunion pour discuter des problèmes. <br> Créer une réunion avec abc@microsoft.com
 DeleteCalendarEntry | Requête pour supprimer une entrée de calendrier.| Annulez mon rendez-vous avec Carol. <br> Supprimer ma réunion de 9 h. <br> Supprimer mon événement.
  FindCalendarEntry | Ouvrir l’application de calendrier ou rechercher l’entrée de calendrier. | Recherchez le rendez-vous de contrôle chez le dentiste. <br> Afficher mon calendrier. <br> Qu’y a-t-il dans mon calendrier de jeudi ?
 FindCalendarWhen | Vérifier l’heure à laquelle la planification a lieu. | Quand dois-je rencontrer Amber et Susan ? <br> Quand un brunch est-il planifié ? 
 FindCalendarWhere | Vérifier l’emplacement où la planification a lieu. | Où sont mes rendez-vous demain ? <br>Où dois-je rencontrer Stacy et Michael demain pour le dîner ?
  FindCalendarWho | Vérifier le ou les participants qui participeront à la planification cible. | Je souhaite confirmer les participants à la réunion de demain à 14 h. <br> Jim sera-t-il à la prochaine réunion des infirmiers ?
 FindCalendarDetail | Vérifier et afficher les détails de la planification. | Je dois vous fournir les détails de la réunion que j’ai planifiée avec mon collègue Paul.
 FindDuration | Vérifiez la durée. | Combien de temps aurai-je pour faire les courses ? <br> Combien de temps ai-je pour déjeuner ?
 FindMeetingRoom | Rechercher les salles de réunion disponibles. | Quelles salles de réunion sont disponibles ? <br> Nouvel emplacement de réunion, en trouver un.
 GoBack | Revenir à la dernière étape ou au dernier élément.  <br> _**Avis** : Consultez le domaine **Services** pour plus d’énoncés GoBack généraux._ | Précédent <br> Retour au dernier e-mail.
 Rejeter | L’utilisateur refuse ce que l’assistant virtuel a proposé. <br> _**Avis** : Consultez le domaine **Services** pour plus d’énoncés Reject généraux._ | Vous n’avez pas besoin de définir l’événement. <br> J’ai d’autres choses à faire à ce moment-là.
ShowNext | Vérifier l’événement suivant. <br> _**Avis** : Consultez le domaine **Services** pour plus d’énoncés ShowNext généraux._ | M’envoyer mon prochain événement. <br> Qu’est-ce qui est ensuite dans le calendrier ?
 ShowPrevious | Vérifier l’événement précédent. <br> _**Avis** : Consultez le domaine **Services** pour plus d’énoncés ShowPrevious généraux._ | Qu’est-ce qui est prévu avant cela ?
 TimeRemaining | Vérifiez le temps restant jusqu’à l’événement suivant. | Afficher le temps que j’ai avant mes réunions. <br> Afficher le temps que j’ai avant le début de la prochaine réunion.
 
### <a name="entities"></a>**Entités**
Nom de l’entité | Type d’entité | Description | Exemples | Slots
-------|-----------------------|-------------|---------|--------
ContactName | personName | Nom d’un contact ou d’un participant à une réunion. | Rencontrer **Betsy**. <br>  Rencontrer **Aubrey** le 3 juillet à 19 h. | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | simple | Le nom du calendrier cible. | Déjeuner avec maman le mardi 12 **personnel** <br> Utiliser mon calendrier **Google** comme calendrier par défaut. <br> Mettre à jour les cours de yoga sur lundi et mercredi à 15 h. Liste dans le calendrier **personnel**. | Google <br> personnel <br> business <br> main
Duration | datetime | Durée d’une réunion/d’un rendez-vous ou temps restant. | Ajouter au calendrier une réunion avec Gary pour discuter des détails de la bourse demain à 11 h pendant **20 minutes**. <br> Ajouter au calendrier un événement au métro le vendredi, je vais manger avec Thomas pendant **une heure** à 21 h. | une heure <br> 2 jours <br> 20 minutes 
EndDate | datetime | Date de fin d’une réunion ou d’un rendez-vous. | Ajouter au calendrier un concert à Bass Hall du 3 au **5 mai** | 5 mai  
EndTime | datetime | Heure de fin d’une réunion ou d’un rendez-vous. | Pouvez-vous prendre rendez-vous de 14 h 30 à **15 h** | three 
Location | simple | Emplacement de l’élément, de la réunion ou du rendez-vous de calendrier.  Les adresses, les villes et les régions sont de bons exemples d’emplacements. | Placer une réunion à **Fremont** pour mettre la tablette à Burma <br> Réunion Pro Bono à **Edina** | 209 Nashville Gym <br> 897 Pancake house <br> Garage 
MeetingRoom | simple | Salle d’une réunion ou d’un rendez-vous. | Ajouter au calendrier une réunion de travail avec Julien à 14 h. Dans son **Bureau** ce vendredi | son bureau <br> salle de conférence <br> Salle 2
MoveEarlierTimeSpan | datetime | Heure à laquelle l’utilisateur souhaite avancer une réunion ou un rendez-vous. | Avancer mon déjeuner en de **30 minutes**. | 30 minutes <br> deux heures 
MoveLaterTimeSpan |  datetime | Heure à laquelle l’utilisateur souhaite repousser une réunion ou un rendez-vous. | Repousser ma réunion de **4 heures**. | 4 heures <br> 15 minutes 
OrderReference | simple | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer. | Quel est mon prochain rendez-vous pour demain ? | Suivant
OriginalEndDate | datetime | Date de fin d’origine d’une réunion ou d’un rendez-vous. | Modifier mes congés du **vendredi** au lundi | Vendredi 
OriginalEndTime | datetime | Heure de fin d’origine d’une réunion ou d’un rendez-vous. | La réunion qui se finit à **15 h** doit durer jusqu’à 16 h | 3
OriginalStartDate | datetime | Date de début d’origine d’une réunion ou d’un rendez-vous. | Modifier le rendez-vous de **demain** à 10 h à mercredi à 9 h.  | demain 
OriginalStartTime | datetime | Heure de début d’origine d’une réunion ou d’un rendez-vous. | Modifier le rendez-vous de demain à **10 h** à mercredi à 9 h. | 10 h
PositionReference | ordinal | Position absolue dans une liste, identifiant un élément à récupérer. | Le **second** | seconde <br> Non. 3 <br> numéro 5
RelationshipName | list | Nom de la relation d’un contact. | Ajouter déjeuner à 13 h avec ma **femme** | femme <br> mari <br> sœur 
SlotAttribute | simple | L’attribut que l’utilisateur souhaite interroger ou modifier. | changer **l’emplacement** de l’événement <br> Changer **l’heure** sur 7 heures | location <br> time 
StartDate | datetime | Date de début d’une réunion ou d’un rendez-vous. | Créer une réunion le **mercredi** à 16 h. | Mercredi 
StartTime | datetime | Heure de début d’une réunion ou d’un rendez-vous. | Créer une réunion le mercredi à **16 h.** | 16 h
Objet | simple, modèle. Tout | Objet, comme le titre d’une réunion ou d’un rendez-vous. | À quelle heure commence la réunion de **préparation de la fête** ? | Dentiste <br> Déjeuner avec Julia 
Message | simple, modèle. Tout | Le message à envoyer aux participants. | Alertez les participants du dîner que **je serai en retard**. | Je serai en retard

## <a name="communication"></a>**Communication**
Requêtes pour effectuer des appels, envoyer des SMS ou des messages instantanés, rechercher et ajouter des contacts, et autres demandes diverses liées à la communication (généralement sortante). Les énoncés exclusivement _limités au nom du contact_ ne font pas partie du domaine Communication.

### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
AddContact | Ajoutez un nouveau contact à la liste des contacts de l’utilisateur. | Ajouter un contact.  <br>   Enregistrer ce numéro et inscrire le nom Jane.
AddFlag | Ajouter un indicateur à un e-mail | Marquer cet e-mail <br> Ajouter un marqueur à cet e-mail.
AddMore | Ajoutez d’autres informations à un courrier électronique ou au texte, dans le cadre d’une composition de courrier électronique ou de texte par étapes. | Ajouter d’autres informations au texte.  <br>   Ajouter d’autres informations au corps du courrier électronique.
Réponse | Répondez à un appel téléphonique entrant. | Répondre à l’appel.  <br>   Décrocher.
AssignContactNickname | Attribuez un surnom à un contact. | Remplacer Isaac par papa.  <br>   Modifier le surnom de Jim. <br>   Ajouter un surnom à Patti Owens.
CallBack | Retourner un appel téléphonique. | Rappeler.
CallVoiceMail | Connectez-vous à la messagerie vocale de l’utilisateur. | Me connecter à ma boîte de messagerie vocale. <br>Messagerie vocale. <br>   Appeler la messagerie vocale.
Annuler | Annuler une opération. | Annuler. <br>   Terminer.
CheckIMStatus | Vérifier l’état d’un contact dans la messagerie instantanée. | L’état En ligne de Jim est-il défini sur Absent ? 
CheckMessages | Rechercher les nouveaux messages ou e-mails. | Rechercher dans ma boîte de réception <br>  Ai-je de nouveaux messages ?
Confirmer | Confirmez une action. | Oui, envoyer. <br> Oui, je confirme que je souhaite envoyer ce courrier électronique.
EndCall | Terminer un appel téléphonique. | Raccrocher l’appel. <br> Finaliser.
FindContact | Recherchez des informations de contact par nom. | Trouver le numéro de maman. <br>   Me montrer le numéro de Carol.
FinishTask | Terminer la tâche actuelle. | J'ai fini <br> C’est tout.
TurnForwardingOff | Désactivez le transfert d’appel. | Arrêter de transférer mes appels. <br> Désactiver le transfert d’appel.
TurnForwardingOn | Activer le transfert d’appel. | Transfert de mes appels au 3333 <br>  Activer le transfert d’appel au 3333.
GetForwardingsStatus | Obtenez l’état actuel du transfert d’appel. | Mon transfert d’appel est-il activé ? <br>   Me dire si l’état de mon appel est activé ou désactivé.
GetNotifications | Obtenir les notifications. | Ouvrir mes notifications <br>   Vérifier les notifications Facebook de mon téléphone
GoBack | Revenez à l’étape précédente. | Revenir à Twitter <br>   Revenir en arrière d’une étape <br>   Retour
Ignorer | Ignorez un appel entrant. | Ne pas répondre <br>   Ignorer l’appel
IgnoreWithMessage | Ignorez un appel entrant et répondez plutôt par SMS. | Ne répondez pas à cet appel mais envoyez un message à la place. <br>   Ignorez et renvoyez du texte.
Composer | Effectuez un appel téléphonique. | Appeler Jim <br>   Merci de composer le 311.
FindSpeedDial | Recherchez le numéro de numérotation rapide défini pour un numéro de téléphone et vice versa. | À quoi correspond le numéro 5 ? <br>   La numérotation rapide est-elle définie ? <br>   Quel est le numéro pour 941-5555-333 ?
Transférer | Transférer un e-mail | Transférer cet e-mail à Greg.
ReadAloud | Lisez un message ou un courrier électronique à l’utilisateur. | Lisez du texte. <br>   Qu’a-t-elle dit dans le message ?
PressKey | Appuyez sur un bouton ou un nombre du clavier. | Composez étoile. <br>   Appuyez sur 1 2 3.
QueryLastText | Interroger le dernier message ou SMS. | Qui m’a écrit ? <br>   Qui m’a récemment écrit ?
Recomposer | Recomposez ou rappelez un numéro. | Recomposez. <br>   Recomposez mon dernier appel.
Rejeter | Rejetez un appel entrant. | Rejeter l’appel <br>   Ne pas répondre maintenant <br>   Non disponible pour le moment et rappellera plus tard.
Répondre | Répondre à un message. | Répondre à Iore Hound <br>   Répondre en disant que je suis en route
SearchMessages | Rechercher les messages dans certaines conditions. | Pouvez-vous rechercher mes e-mails envoyés par Jane ?
SendEmail | Envoyez un courrier électronique. Cette intention s’applique aux courriers électroniques mais pas aux SMS. | E-mail pour Mike Waters : Mike, le dîner de la semaine dernière était exceptionnel. <br>   Envoyer un e-mail à Bob.
SendMessage | Envoyez un SMS ou un message instantané. | Envoyer du texte à Chris et Carol <br>   Écrire à Greg sur Facebook <br>   
SetSpeedDial | Définissez un raccourci de numérotation rapide pour le numéro de téléphone d’un contact. | Définissez la numérotation rapide un pour Carol. <br>   Configurez la numérotation rapide pour maman.
ShowCurrentNotification | Afficher les notifications actuelles. | Y a-t-il de nouvelles notifications ? <br> Afficher une notification.
ShowNext | Affichez l’élément suivant, par exemple, dans une liste de SMS ou de courriers électroniques. | Affichez le suivant. <br>   Allez à la page suivante.
ShowPrevious | Affichez l’élément précédent, par exemple, dans une liste de SMS ou de courriers électroniques. | Affichez le précédent. <br>   Précédent. <br>   Allez au précédent.
TurnSpeakerOff | Désactivez le haut-parleur du téléphone. | Arrêtez le haut-parleur. <br>   Désactivez le mains libres.
TurnSpeakerOn | Activez le haut-parleur du téléphone. | Mode Mains libres. <br>   Activez le mains libres.

### <a name="entities"></a>**Entités**
Nom de l’entité | Type d’entité | Description | Exemples | Slots
------|-------|----------|--------------|---------------
Pièce jointe | simple | La pièce jointe que l’utilisateur souhaite envoyer par SMS ou par e-mail. | Envoyer par e-mail un **fichier** à partir de OneNote. <br> Envoyer mon **document** de maintenance à Katie. | file <br> doc
AudioDeviceType | simple | Type d’appareil audio (haut-parleur, casque, microphone, etc.). | Répondre en mode **mains libres**. <br> Renuméroter sur le **haut-parleur**. | haut-parleur <br> mains libres <br> bluetooth
Category | simple | La catégorie d’un message ou d’un e-mail ; la catégorie doit avoir une définition claire dans le système de messagerie, comme « Non lu », « Indicateur ». Description sans définition claire, par exemple « Nouveau » et « Récent » ne sont pas des catégories. | Marquer tous les e-mails comme **lus**  <br> Nouvel e-mail **haute priorité** pour Paul | important <br> haute priorité <br> read
ContactAttribute | simple | Attribut du contact sur lequel l’utilisateur effectue une demande.| Quels sont les **anniversaires** du mois prochain que je dois connaître ? | anniversaires <br> address <br> numéro de téléphone
ContactName | personName  | Nom d’un contact ou destinataire de message. | Envoyer le message à **Stevens** | Stevens
Date/Heure | datetime | Date et heure de réception d’un e-mail. | Lire le courrier **d’aujourd’hui** <br> Qui m'a envoyé des e-mails **aujourd’hui** ? <br> Qui a effectué un appel à **19 h** ? | aujourd’hui <br> demain
DestinationPhone | simple | La cible que l’utilisateur souhaite appeler ou à qui il veut envoyer un message. | Passer un appel à la **maison** <br> Envoyer un message texte à la **maison** | maison <br> home
EmailAddress | email | L’adresse de messagerie que l’utilisateur souhaite envoyer ou interroger. | envoyer un e-mail à Megan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | simple, modèle. Tout | Texte utilisé comme ligne d’objet d’un courrier électronique. | Rédiger un e-mail à David avec l’objet **Bonjour**  | RE : histoire intéressante
Clé | simple | La touche sur laquelle l’utilisateur veut appuyer. | Appuyez sur la touche **espace**. <br> appuyer sur **9** | g <br> étoile <br> 8
Lignes | simple | La ligne que l’utilisateur souhaite utiliser pour envoyer un e-mail ou un SMS. | Lire mon dernier e-mail **hotmail**. <br> Appeler Peter par **mobile**. <br> Appelez papa à sur ma ligne de **travail**.| hotmail <br> Skype <br> Cellule britannique
SenderName | personName | Nom de l’expéditeur. | Lire l’e-mail de **David** <br> E-mails de Chanda | David <br> Chanda
FromRelationshipName | simple | Nom de la relation de l’expéditeur. | Lisez le message de **Papa**. <br> Pouvez-vous lire tous les messages texte de **maman** ? | Papa <br> Maman 
Message | simple, modèle. Tout |  Message à envoyer en tant que courrier électronique ou SMS.  | Envoyer un e-mail indiquant « **Je suis occupé** ». | Je suis occupé
OrderReference | simple | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer. | Quel était le **dernier** message que j’ai envoyé ? <br> Lire le **dernier** e-mail de mon Nokia. <br> Lire les **nouveaux** messages texte. | last <br> latest <br> récent <br> le plus récent
PositionReference | simple, ordinal | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer.| Quel était le **premier** message que j’ai envoyé ? <br> Le **3e**.| Premier <br> 3e
phoneNumber | phoneNumber | Le numéro de téléphone que l’utilisateur souhaite appeler ou auquel il veut envoyer un message. | Envoyer un SMS au **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | simple | Nom de relation d’un contact ou destinataire de message. | Envoyer un e-mail à ma **femme** | femme
SearchTexts | simple, modèle. Tout | Textes utilisés pour le filtrage des e-mails ou SMS | Rechercher tous les e-mails contenant « **Surface Pro** » | Surface Pro
SpeedDial | simple | La numérotation rapide. | Appeler le **3 4 5**. <br> Définir la numérotation rapide **un**. | 345 <br> 5\.

## <a name="email"></a>**E-mail**
L’e-mail est un sous-domaine du domaine de la *communication*. Il contient principalement des demandes d’envoi et de réception de messages par e-mail.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
AddMore | Ajoutez d’autres informations à un courrier électronique ou au texte, dans le cadre d’une composition de courrier électronique ou de texte par étapes. | Ajouter d’autres informations au corps du courrier électronique.
Annuler | Annuler une opération. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés Cancel généraux.* | Annuler. Ne pas l’envoyer. <br> Terminer.
CheckMessages | Rechercher les nouveaux messages/e-mails sans demande conditionnelle. S’il existe une condition, les énoncés appartiennent à l’intention *SearchMessage*. | Vérifier ma boîte de réception. <br> Ai-je de nouveaux messages ? 
Confirmer | Confirmer une action en cours relative au traitement des e-mails. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés Confirm généraux.* | Oui, envoyer. <br> Je confirme que je souhaite envoyer ce courrier électronique.
Supprimer | Supprimer un e-mail ou des e-mails filtrés selon certaines conditions. | Placer l’e-mail dans la corbeille <br> Vider ma boîte de réception. <br> Supprimer l’e-mail de Mary.
ReadAloud | Lisez un message ou un courrier électronique à l’utilisateur. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés ReadAloud généraux.*  | Lire l’e-mail envoyé par Mary.
Répondre | Répondre à l’e-mail actuel. | Créer une réponse à l’e-mail. <br> Répondre en tapant « Merci beaucoup, cordialement, John ».
SearchMessages | Rechercher les messages selon certaines conditions, comme le nom de l’expéditeur, l’heure et l’objet. | Afficher les messages de Nisheen. <br> Pouvez-vous chercher mes e-mails intitulés « ABC » ?
SendEmail | Envoyez un courrier électronique. | Écrire un e-mail à Mike : Mike, le dîner de la semaine dernière était exceptionnel. <br> Envoyer un e-mail à Bob.
ShowNext | Affichez le ou les éléments suivants dans une liste de SMS ou de courriers électroniques. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés ShowNext généraux.* | Affichez le suivant. <br> Allez à la page suivante.
ShowPrevious | Affichez le ou les éléments précédents dans une liste de SMS ou de courriers électroniques. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés ShowPrevious généraux.* | Affichez le précédent. <br> Précédent. <br> Allez au précédent.
Transférer | Transférez un e-mail. | Transférer cet e-mail à Greg.
AddFlag | Ajouter un indicateur à un e-mail. | Marquer cet e-mail <br> Ajouter un marqueur à cet e-mail.
QueryLastText | Interroger le dernier e-mail. | Qui m’a envoyé un e-mail ? <br> Qui m’a récemment envoyé un e-mail ?


### <a name="entities"></a>**Entités**
Nom de l’entité | Type d’entité | Description | Exemples | Slots
------|-------|----------|--------------|---------------
Pièce jointe | simple | La pièce jointe que l’utilisateur souhaite envoyer par SMS ou par e-mail. | Envoyer par e-mail un **fichier** à partir de OneNote. <br> Envoyer mon **document** de maintenance à Katie. | file <br> doc
ContactName | personName  | Nom d’un contact ou destinataire de message. | Envoyer le message à **Stevens** | Stevens
Date | datetime | Date de réception d’un e-mail. | Lire le courrier **d’aujourd’hui** <br> Qui m'a envoyé des e-mails **aujourd’hui** ? | aujourd’hui
EmailAddress | email | L’adresse de messagerie que l’utilisateur souhaite envoyer ou interroger. | envoyer un e-mail à Megan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | simple, modèle. Tout | Texte utilisé comme ligne d’objet d’un courrier électronique. | Rédiger un e-mail à David avec l’objet **Bonjour**  | RE : histoire intéressante
SenderName | personName | Nom de l’expéditeur. | Lire l’e-mail de **David** <br> E-mails de Chanda | David <br> Chanda
FromRelationshipName | simple | Nom de la relation de l’expéditeur. | Lisez le message de **Papa**. <br> Pouvez-vous lire tous les messages texte de **maman** ? | Papa <br> Maman 
Message | simple, modèle. Tout |  Message à envoyer en tant que courrier électronique ou SMS.  | Envoyer un e-mail indiquant « **Je suis occupé** ». | Je suis occupé
Category | simple | La catégorie d’un message ou d’un e-mail ; la catégorie doit avoir une définition claire dans le système de messagerie, comme « Non lu », « Indicateur ». Description sans définition claire, par exemple « Nouveau » et « Récent » ne sont pas des catégories. | Marquer tous les e-mails comme **lus**  <br> Nouvel e-mail **haute priorité** pour Paul | important <br> haute priorité <br> read
OrderReference | simple | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer. | Quel était le **dernier** message que j’ai envoyé ? <br> Lire le **dernier** e-mail de mon Nokia. <br> Lire les **nouveaux** messages texte. | last <br> latest <br> récent <br> le plus récent
PositionReference | simple, ordinal | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer.| Quel était le **premier** message que j’ai envoyé ? <br> Le **3e**.| Premier <br> 3e
RelationshipName | simple | Nom de relation d’un contact ou destinataire de message. | Envoyer un e-mail à ma **femme** | femme
Temps | datetime | Temps | Envoyer un e-mail **ce soir**. | ce soir
SearchTexts | simple, modèle. Tout | Textes utilisés pour le filtrage des e-mails ou SMS | Rechercher tous les e-mails contenant « **Surface Pro** » | Surface Pro 
Lignes | simple | L’utilisateur de la ligne souhaite l’utiliser pour envoyer un e-mail. | Lire mon dernier e-mail **hotmail**. <br> Envoyer un e-mail à partir de mon **compte professionnel**.| hotmail <br> compte professionnel 

## <a name="homeautomation"></a>**HomeAutomation**
Le domaine HomeAutomation fournit les intentions et entités liées au contrôle des appareils domestiques intelligents. Nous prenons principalement en charge les commandes de contrôle associées aux lampes et aux climatiseurs. Mais certaines fonctionnalités de généralisation sont disponibles pour d’autres appareils électriques.
### <a name="supported-devices-and-properties"></a>**Appareils et propriétés pris en charge**
Appareil | properties
-------|---------
Capteur de température | Température
Lumière, lampe | Allumer/éteindre, luminosité, couleur
TV, radio | Allumer/éteindre, volume
Climatiseur (A/C), thermostat | Allumer/éteindre, température, puissance
Ventilateur | Allumer/éteindre, puissance
Prises, lecteur DVD, machine à glace, etc. | Allumer/éteindre

### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
 TurnOff | L’utilisateur veut éteindre l’appareil ou désactiver les paramètres.  | Éteindre les lumières. <br> Éteindre quelque chose. <br> Éteindre une chose.
 TurnOn | Activez un appareil ou allumez l’appareil et mettez-le dans un paramètre ou mode particulier. | Allume la lumière à 50 %. <br> Allumer ma cafetière <br> Pouvez-vous allumer ma cafetière ?
 SetDevice | L’utilisateur souhaite définir l’appareil sur un paramètre ou un type particulier.  | Régler le climatiseur sur 26 degrés. <br> Faire passer les lumières au bleu. <br> Appeler cette lumière lumière nocturne.
 QueryState | L’utilisateur demande l’état d’un appareil ou d’un paramètre.  | Sur quoi le thermostat est-il réglé ? <br> Le climatiseur est-il en marche ? <br> Quelle est la température de la chambre ?
 TurnUp | Augmenter les paramètres ou la luminosité des appareils. | Augmenter les lumières de 75 %. <br> Augmenter luminosité ici de 10 pour cent.  <br> Rendre le salon plus chaud.
 TurnDown | Réduire sans mettre hors tension un appareil, qu’il s’agisse d’un estompage, d’une baisse de température ou de réduction de la luminosité des lumières. | Réduire la lumière de la bibliothèque de 44 %. <br> Réduire les lumières. <br> Rendre la chambre plus fraîche.
### <a name="entities"></a>**Entités**
Nom de l’entité | Type d’entité | Description | Exemples
-------|----------|--------------|---------------
DeviceName | List | Texte défini par l’utilisateur pour ses appareils. | Bleu<br> Noël <br> Bureau
DeviceType | List | Appareils pris en charge. | Lumières <br> Climatiseur <br> Lumière nocturne
Location | simple | L’emplacement ou la pièce où se trouve l’appareil. | Cuisine<br> En bas <br> Chambre
NumericalChange | simple | Quantité d’augmentation ou de réduction d’un paramètre. <br> <br> _L’emplacement s’affiche uniquement avec les intentions turn_up et turn_down._ | 3<br> 50%<br>
OrderReference | ordinal | L’objectif de cet emplacement est de capturer la sélection des éléments. Il indique la position de l’élément sur une liste. | Premier<br> Seconde
Quantificateur | List | Indique le nombre d’instances référencées d’une entité particulière. Par exemple, « Tout », « Chaque », etc. | Tous<br> Chaque<br> Tout
Paramètre | Simple | Le paramètre sur lequel l’utilisateur souhaite régler son appareil, par exemple la scène, le niveau, l’intensité, la couleur, le mode, la température ou l’état de l’appareil. | Bleu<br> 72 <br> Chauffage 
SettingType | List | Paramètre d’appareil qui intéresse l’utilisateur. | Température<br> 
Heure/date |  datetime | Heure et durée d’utilisation d’un appareil| 5 minutes <br> 15 h
Unité | List | Pour étiqueter des mots tels que « degrés », « pour cent », « Fahrenheit », « Celsius », chaque terme d’unité doit être étiqueté séparément. | Degrés<br> Pourcentage


## <a name="notes"></a>**Remarques**
Le domaine Note facilite la création de notes et l’écriture d’éléments pour les utilisateurs.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
AddToNote | Ajouter des informations à une note ouverte. | Ajouter à ma planification une note pour contacter mon patron au sujet du projet.
Effacer | Effacez tous les éléments d’une note préexistante. | Supprimer tous les éléments de ma note de congés. <br>Effacer tout dans ma note de lectures.
Confirmer | Confirmez une action liée à une note. <br> ***Avis** : Cette intention comprend principalement l’action « Confirmer » pour le scénario de note. Si vous avez besoin d’expressions plus générales sur « Confirmer », utilisez l’intention « Confirmer » dans le domaine **Services**. * | D’accord pour cette note. <br>Je confirme la conservation de tous les éléments de listes.
Créer | Créez une nouvelle note. | Créer une note. <br>Noter de me rappeler que Jason est en ville la première semaine de mai. 
Supprimer | Supprimez une note entière. | Supprimer ma note de congés. <br>Supprimer ma liste de courses.
ReadAloud | Lire une note à voix haute. | Me lire la première note. <br>Me lire les détails.
fermez | Fermez la note actuelle. | Fermer la note. <br>Fermez la note actuelle.
Ouverts | Ouvrir une note préexistante. | Ouvrir ma note de congés. <br>Ouvrir la note « Planning ».
RemoveSentence | Supprimer une phrase d’une note. | Supprimer la dernière phrase. <br>Supprimer les chips de ma note de courses. <br>Supprimer les crayons de ma note de fournitures scolaires
ChangeTitle | Modifier le titre de la note. | Nommer cette note « Planning ».

### <a name="entities"></a>**Entités**
Nom de l’entité | Type d’entité | Description | Exemples 
------- | ------- | ------- | -------
Texte | simple, modèle. Tout | Texte d’une note ou d’un rappel. | s’étirer avant la marche <br> longue course demain
Intitulé | simple, modèle. Tout | Titre d’une note. | épicerie <br> personne à appeler <br> à faire
CreationDate | datetimeV2 | Cet emplacement sert quand l’utilisateur demande des notes créées dans une certaine plage de dates/heures. | 
Quantificateur | List | Lorsqu’un utilisateur demande à exécuter une action sur tous les éléments, chaque élément ou n’importe quel élément du texte d’une note. | tout <br> any <br> chaque
OrderReference | ordinal | L’utilisateur souhaite effectuer des actions avec les éléments « Premier », « Dernier », « Suivant », etc. | first <br> last


## <a name="places"></a>**Places**
Lieux inclut des entreprises, des établissements, des restaurants, des espaces publics et des adresses. Le domaine prend en charge la recherche de lieux et les questions relatives à des informations sur un lieu public, par exemple un emplacement, les heures d’ouverture et la distance. 
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
MakeCall | Appelez un lieu. | Applebees sur 1000/200 Rojas St et appeler.
FindPlace | Recherchez un lieu (entreprise, établissement, restaurant, espace public, adresse). Mais pas : <li> Nom de l’entreprise uniquement : Starbucks <li> Nom de l’emplacement uniquement : Seattle/Norvège <li> Cuisine, nourriture ou produit uniquement : Pizza/guacamole/italien | Où se trouve la bibliothèque la plus proche ? <br> Starbucks à Seattle. <br> Où se trouve la bibliothèque la plus proche ? <br> 
GetAddress | Demandez l’adresse d’une lieu. | Montrez-moi l’adresse de Guu dans Robson Street. <br> Quelle est l’adresse du Starbucks le plus proche ?
GetDistance | Demander la distance entre l’emplacement actuel et un emplacement spécifique. | À quelle distance se trouve l’Holiday Inn ?<br> À quelle distance je me trouve du parc Bellevue ? <br> À quelle distance se trouve Tahoe ?
GetPhoneNumber | Demandez le numéro de téléphone d’un lieu. | Quel est le numéro de téléphone du Starbucks le plus proche ?<br> Donnez-moi le numéro de Home Depot. <br> Un numéro de téléphone pour Disneyland.
GetPriceRange | Demander la plage de prix par tête pour un lieu. | Prix moyen de J. Sushi à Seattle. <br> Le Cineplex fait-il moitié prix le mercredi ? <br> Combien coûte un dîner homard au Sizzler ?
GetStarRating | Demandez l’évaluation en étoile d’un lieu. | Comment Zucca est-il évalué ?<br> Combien d’étoiles a French Laundry ?<br> L’aquarium de Monterrey est-il bien ?
GetHours | Demandez les heures d’ouverture d’un lieu. | À quelle heure ferme Safeway ?<br> Quels sont les horaires du Home Depot ?<br> La Starbucks est-il toujours ouvert ?
GetReviews | Demandez les avis d’un lieu. | Me montrer les avis de Cheesecake Factory. <br> Lire les avis de Cineplex. <br> Y a-t-il des avis récents pour Humperdinks ?
GetMenu | Demandez les plats à la carte d’un restaurant. | Le Zucca sert-il des plats végan ? <br> Qu’y a-t-il au menu de Sizzler ? <br> Me montrer le menu de l’Applebee.
RatePlace | Évaluer un lieu. | 4 étoiles pour Maxi’s Pizza à Kimberly. <br> Donner 4 étoiles à Bonefish sur TripAdvisor. <br> Créer une évaluation 4 étoiles pour La Hacienda.
AddFavoritePlace | L’utilisateur souhaite ajouter un emplacement à sa liste de favoris ou de MVP. | Enregistrer cet emplacement dans mes favoris. <br> Ajouter Best Buy à mes favoris.

### <a name="entities"></a>**Entités**
Entités LUIS | Type d’entité | Description | Exemples | Exemples d’énoncés
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | simple | Emplacement ou adresse d’un lieu. | Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield Rd.** à **Palo Alto** <br> Magasins de semences d'oiseau à **Seattle** <br> Obtenir la distance entre ici et **300 112th Ave se**.
Équipements | simple | Caractéristiques objectives et avantages d’un lieu public. | front de mer <br> stationnement gratuit | Restaurants de fruits de mer sur les **quais** de Kirkland. <br> Y a-t-il un **parking gratuit** à proximité ?
Cuisine | simple | Type d’alimentation, cuisine ou nationalité. | Chinois <br> Italien <br> Sushi <br> Nouilles <br> | Aidez-moi à trouver un restaurant **chinois**. <br> Quelles sont les heures d’ouverture du restaurant à **Sushis** ? <br> Où se trouve le restaurant de **steaks** le plus proche ?
Date | datetime | Date/heure ou durée pour la date de l’emplacement ciblé. | demain <br> aujourd’hui <br> 18 h | À quelle heure l’aquarium ferme-t-il **demain** ? <br> La quincaillerie la plus proche ouverte après **18 h.**
Distance | dimension | Distance à un lieu public à partir de la position actuelle de l’utilisateur. | 15 miles <br> 10 miles | Magasin de vêtements à moins de **15 kilomètres** <br> Restaurant pour enfants à seulement **10 kilomètres**
MealType | List | Type de repas comme petit-déjeuner ou déjeuner. | petit-déjeuner <br> dîner | Rechercher **petit déjeuner** à Greenwood Seattle <br> Trouver un lieu pour **déjeuner**.
À proximité | List | Décrit un lieu proche sans emplacement ou adresse absolue. | le plus proche <br> Dans cette zone <br> Près de moi | Trouver le restaurant indien **le plus proche**. <br> Où se trouve le Wetherspoon **local** ? <br> Quels sont les bons restaurants **près de moi** ?
OpenStatus | List | Indique si un lieu est ouvert ou fermé. | ouvert <br> Fermé | À quelle heure Yogurt Land **ferme** aujourd’hui ? <br> Quelles sont les **heures d’ouverture** de Costco ?
PlaceName | simple | Nom d’une destination qui est une entreprise, un restaurant, une attraction publique ou un établissement. Le nom de l’emplacement peut contenir un PlaceType s’il est couramment utilisé. | Central Park <br> Voie protégée <br> Walmart| À quelle heure la pharmacie **Safeway** ouvre-t-elle ? <br> **Walmart** est-il ouvert ?
PlaceType | simple | Type d’une destination qui est une entreprise locale, un restaurant, une attraction publique ou un établissement. | restaurant <br> opéra <br> cinéma | **cinémas** à Cambridge <br> Y a-t-il un **restaurant** près de moi ?
PriceRange | simple | La fourchette de prix des produits ou services du lieu. | bon marché <br> rentable <br> cher | Trouver une quincaillerie **abordable** <br> Y a-t-il une pizzeria **bon marché** ouverte maintenant ?
Produit | simple | Produit proposé par un lieu. | vêtements <br> téléviseurs | Où se trouve le meilleur endroit pour **manger** ? <br> Trouver des **téléviseurs** à East Kilbride.
Rating | simple | Évaluation d’un lieu. | 5 étoiles <br> top <br> bien | Y a-t-il de **bons** endroits pour sortir et manger demain <br> **meilleurs** restaurants d’Amsterdam <br> Répertoriez les trois **meilleures** pizzerias.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Le domaine de réservation de restaurant prend en charge les intentions de gestion des réservations pour les restaurants.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
Réserver | Demandez une réservation pour un restaurant. | Réserver au Zucca pour deux pour ce soir. <br> Réserver une table pour demain. <br> Table pour 3 à Palo Alto à 19h. <br> Faire une réservation pour 3 au Homard Rouge.
DeleteReservation | Annuler ou supprimer une réservation pour un restaurant. | Annuler la réservation à Zucca demain soir. <br> Oublier ma réservation au Homard Rouge à 19 h vendredi prochain. <br> Je n’ai pas besoin de la réservation pour Zucca, annuler.
ChangeReservation | Modifier l’heure, le lieu ou le nombre de personnes pour une réservation de restaurant existante. | Modifier ma réservation sur 21 h <br> Remplacez ma réservation du Zucca au Homard Rouge. <br> 7 personnes au lieu de 6 pour la réservation au Zucca.
Confirmer | Confirmer une action liée à la réservation. <br> ***Avis** : Cette intention comprend principalement l’action « Confirmer » pour le scénario de réservation de restaurant. Si vous avez besoin d’expressions plus générales sur « Confirmer », utilisez l’intention « Confirmer » dans le domaine **Services**. * | Oui, j’ai effectué les réservations pour ce soir à 8 heures chez le chef spécialisé en pâtes. <br> Oui, réserver. <br> Confirmer la réservation au bar à sushis.
FindReservationDetail | Afficher les informations détaillées de la réservation existante. | Trouver ma réservation au Renaissance à San Diego <br> Afficher la réservation de demain. <br> Afficher les détails de ma réservation au Zucca.
FindReservationWhere | Vérifier l’emplacement absolu de la réservation. | Où se trouve l’emplacement de Zucca de ma réservation ? <br> Afficher les paramètres régionaux de ma réservation demain.
FindReservationWhen | Vérifier l’heure exacte de la réservation | À quelle heure la réservation au Zucca est-elle prévue demain ? <br> Heure de ma réservation au Homard Rouge.
Rejeter | L’utilisateur refuse ce que l’assistant virtuel a proposé pour la gestion d’une réservation. <br> ***Avis** : Consultez le domaine **Services** pour plus d’énoncés Reject généraux.* | Vous n’avez pas besoin de définir l’événement. | Non, je ne souhaite pas modifier la réservation. <br> Non, ne pas réserver, j’ai fait une erreur.

### <a name="entities"></a>**Entités**
Entité LUIS | Type d’entité | Description | Exemples
-------|------|---------|-------------------
Adresse | simple | Emplacement ou adresse d’événement pour une réservation. | Palo Alto<br>300 112th Ave SE<br>Seattle
Atmosphère | List | Description de l’atmosphère d’un restaurant. | romantique<br>décontracté<br>adapté aux groupes<br>cool
Cuisine | simple | Type d’alimentation, cuisine ou nationalité. | Chinois<br>Italien<br>Mexicain<br>Sushi<br>Nouilles<br>Steak
MealType | List | Type de repas associé à une réservation. | petit-déjeuner<br>dîner<br>déjeuner<br>souper
PlaceName | simple | Le nom d’un restaurant | Zucca<br>Cheesecake Factory<br>Homard Rouge
Rating | simple | Évaluation d’un lieu ou restaurant. | 5 étoiles<br>3 étoiles<br>4 étoiles
NumberPeople | simple | Le nombre de personnes pour la réservation | 3<br>six
Temps | datetime| Heure pour la réservation de restaurant | demain<br>ce soir<br>19 h<br>Réveillon de Noël


## <a name="todo"></a>**ToDo**
Le domaine _ToDo_ fournit des types de listes de tâches pour que les utilisateurs ajoutent, marquent et suppriment leurs éléments todo.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
AddToDo | L’utilisateur souhaite ajouter des éléments de tâche pour tous les types de liste. |  Me rappeler d’acheter du lait. <br> Ajouter un élément appelé « acheter du lait » à ma liste de tâches
Confirmer | L’utilisateur souhaite confirmer une action en cours. L’énoncé contient un signal explicite comme « Oui » pour confirmer une opération. <br> <br> ***Avis** : Cette intention comprend principalement l’action « Confirmer » pour le scénario de liste de tâches. Si vous avez besoin d’expressions plus générales sur « Confirmer », utilisez l’intention « Confirmer » dans le domaine **Services**. * | Supprimer la tâche. <br> Je suis sûr que je souhaite ajouter cette tâche. <br> Oui, je veux le faire.
Annuler | L’utilisateur souhaite annuler l’action en cours.  <br> <br> ***Avis** : Cette intention comprend principalement l’action « Confirmer » pour le scénario de réservation de restaurant. Si vous avez besoin d’expressions plus générales sur « Confirmer », utilisez l’intention « Confirmer » dans le domaine **Services**. * | Non, oublier. <br> Simplement annuler la tâche. <br> Non, ne pas l’ajouter.
DeleteToDo | Supprimer un élément de la liste de tâches en faisant référence à son ordre ou supprimer tous les éléments de la liste de tâches. | Supprimer toutes les tâches. <br> Supprimer la deuxième.
MarkToDo | Marquez une tâche comme terminée ou accomplie en faisant référence à son ordre ou son contenu. | Marquer la tâche « acheter du lait » comme terminée. <br> Terminer la lecture des tâches. <br> Terminer tout.
ShowNextPage | Une liste est fractionnée en plusieurs pages à afficher. Afficher les éléments de liste de la page suivante pour l’utilisateur. | Pouvez-vous afficher la page suivante dans la liste des courses ? <br> Qu’y a-t-il sur la page suivante ? <br> Et ensuite ?
ShowPreviousPage | Afficher les éléments de liste sur la page précédente de l’utilisateur. | afficher le précédent. <br> Je dois vérifier les tâches précédentes.
ShowToDo | Afficher tous les éléments de la liste des tâches. | Supprimer ma liste d’achats. <br> Afficher ma liste des courses.

### <a name="entities"></a>**Entités**
Entité LUIS | Type d’entité | Description | Exemples
-------|------|---------|-------------------
ContainsAll | simple | Représente tous ou n’importe lequel des éléments de la liste des tâches | m’aider à supprimer **toutes** les tâches. <br> Terminer **tout**.
ordinal | ordinal | Référence ordinale ou numérique à un élément. | Marquez la **troisième** comme terminée. <br> Supprimez la **première** tâche.
ListType | simple | Type de la liste de tâches.  | Ajouter des chaussures à ma **liste de courses**.
FoodOfGrocery | List | Détection d’une liste d’articles alimentaires | Me rappeler d’acheter du **lait**. <br> Ajouter du **bœuf** à ma liste de courses.
TaskContent | simple, modèle. Tout | Détecter le contenu d’une tâche. | Me rappeler **d’appeler ma mère**. <br> Ajouter **souhaiter bon anniversaire à John** à ma liste de tâches


## <a name="utilities"></a>**Services**
Le domaine _Services_ est un domaine général parmi tous les modèles _LUIS_ prédéfinis, qui contient des intentions et des énoncés courants dans des scénarios de différence.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
 Annuler | Annuler une action/une requête/une tâche/un service. | Annuler ça. <br> Non, laissez tomber.
 Confirmer | Confirmez une action. | Bien sûr <br> Oui, s’il vous plaît <br> Confirmez.
 Rejeter | L’utilisateur refuse ce que l’assistant virtuel a proposé. | Non
 FinishTask | Terminez une tâche que l’utilisateur a commencée. | J’ai terminé. <br> C'est tout. <br> Terminer.
 GoBack | Revenez en arrière d’une étape ou revenez à une étape précédente. | Revenir en arrière d’une étape. <br> Revenir en arrière.
 Aide | Demandez de l’aide. | De l’aide s’il vous plaît. <br> Ouvrir l’aide.
 Répéter | Répétez une action. | Répéter ça.
 ShowNext | Afficher ou indiquer le ou les éléments suivants. | Affichez le suivant.
 ShowPrevious | Affichez l’élément précédent d’une série. | Afficher le précédent.
 StartOver | Redémarrez l’application ou démarrez une nouvelle session. | Redémarrer.
 Arrêter | Demandez à l’assistant virtuel de cesser de parler.  | Arrêter de dire cela s’il vous plaît.
 SelectAny | L’utilisateur demande à sélectionner n’importe quel élément ou résultat affiché à l’écran. | N’importe lequel. <br> Sélectionnez n’importe lequel.
 SelectNone | L’utilisateur ne sélectionne aucun des éléments existants et demande plus d’options. | Donnez-moi d’autres suggestions. <br> Aucun.
  SelectItem | L’utilisateur demande à sélectionner un élément ou des résultats affichés à l’écran. | Sélectionner le troisième. <br> Sélectionner celui en haut à droite.
 Escalader | Demander à un service client de gérer les problèmes. | Puis-je parler à quelqu’un ?
 ReadAloud | Lire quelque chose à haute voix pour l’utilisateur. | Lire cette page. <br> Lire à haute voix.

### <a name="entities"></a>**Entités**
Entité LUIS | Type d’entité | Description | Exemples
------------|-------------|-------------|---------
ordinal | ordinal | Référence ordinale ou numérique à un élément. | Le **second**. <br> Le **suivant**.
number | number | Quantité d’articles que l’utilisateur souhaite | Les **3** éléments suivants
DirectionalReference | simple | Point de référence pour l’emplacement d’un élément à l’écran. | Celui à droite <br> upper

## <a name="weather"></a>**Météo**
Le domaine Météo se concentre sur la vérification des conditions météorologiques et les conseils avec indication de l’emplacement et de l’heure ou de l’heure de vérification des conditions météorologiques.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
 CheckWeatherValue | Demander la météo ou un facteur météorologique pour un emplacement avec les prévisions ou les informations passées. <br> Les facteurs liés à la météo sont les suivants : <li> température </li> <li> pluie/neige/précipitations </li> <li> sec/humide/humidité </li> <li> brouillard </li> <li> Indice UV </li> <li> pouces de pluie/neige </li> | Quel est l’indice de qualité de l’air à Pékin ? <br> Combien de pluie est attendue à Seattle en mars ? <br> Température la plus élevée jamais enregistrée à Hawaï.
 CheckWeatherTime | Demander l’heure de la prévision ou de l’historique des facteurs météorologiques pour un lieu. | Quand la pluie est-elle prévue ? <br> Un bon moment pour aller au Canada <br> Quand est le mois le plus chaud au Minnesota ?
 QueryWeather | Poser des questions sur la météo ou des facteurs météorologiques pour un emplacement spécifique pour lequel une réponse de type « oui, non ou peut-être » est attendue, ou demander des conseils d’activité en fonction des conditions météorologiques. | Est-ce qu'il va pleuvoir demain ? <br> Est-ce qu’il fera beau aujourd’hui ? <br> Est-ce que le mois de main est trop tôt pour aller en Alaska ?
 ChangeTemperatureUnit | Modifier l’unité de météo, entre degrés Celsius, Kelvin et Fahrenheit. | Convertir en degrés Celsius. <br> Puis-je avoir ça en Kelvin ?
 GetWeatherAdvisory | Obtenir les avis météo ou des alertes à partir pour un emplacement spécifique. | Y a-t-il des avis météorologiques à Memphis ? <br> Y a-t-il des alertes météorologiques pour ma zone ?

### <a name="entities"></a>**Entités**
Entité LUIS | Type d’entité | Description | Exemples
------------|-------------|-------------|---------
Location | Geography | Emplacement absolu ou implicite d’une requête météo. | Palo Alto<br>Shanghai<br>Seattle<br>Delvina<br>
Date/Heure   | datetime | Date et heure d’interrogation de la météo. | Novembre<br>Toutes les heures<br>matin<br>Ce weekend<br>10 jours<br>
AdditionalWeatherCondition | list | Texte de description supplémentaire pour la météo, comme la vitesse ou la direction du vent. | direction<br>Rapide<br>speed
Historique | simple | Mots de description de la condition météorologique historique, y compris la moyenne et les valeurs limitrophes au cours de la période précédente. | passé<br>historique/passé<br>saisonnier<br>meilleur moment<br>jamais enregistré
PrecipitationUnit | dimension | Précipitations de neige ou de pluie. | 5 pouces<br>6 cm
SuitableFor | simple | La description d’une activité humaine sous une condition météorologique courante lorsque les utilisateurs interrogent l’avis d’activité en fonction de la condition météorologique. | veste<br>parapluie<br>natation
TemperatureUnit |température | température | 18 Celsius<br>7 degrés Kelvin
WeatherRange | simple | La condition spécifique de température, vent et autres conditions climatiques sur une période donnée | maximum<br>élevée<br>basse<br>moyenne élevée<br>Le plus élevé
WeatherCondition | simple | Description de la condition météorologique | ensoleillé<br>pluie<br>pluie en cours<br>température<br>neige<br>chaleur
WindDirectionUnit | list | Les mots de direction du vent | Nord<br>sud<br>est<br>ouest<br>nord-est


## <a name="web"></a>**Web**
Le domaine Web fournit les intentions et les entités pour la recherche d’un site web.
### <a name="intents"></a>**Intentions**
Nom de l’intention | Description | Exemples
---------|----------|---------
 WebSearch | Requête pour accéder à un site web spécifié ou rechercher sur un moteur de recherche. | Rechercher Surface sur google.com. <br> Trouver le morceau Joyeux anniversaire sur le web <br> Aller à www.twitter.com.

### <a name="entities"></a>**Entités**
Entité LUIS | Type d’entité | Description | Exemples
------------|-------------|-------------|---------
SearchEngine | List | Le moteur de recherche que l’utilisateur souhaite utiliser. | Bing <br> Google
SearchText | simple, modèle. Tout | Le texte que l’utilisateur souhaite rechercher. <br> _Marquer « Amis sur Facebook » comme SearchText si le site web après « sur » n’est pas un moteur de recherche. L’URL doit également être marquée comme SearchText._ | Film <br> Apprentissage approfondi <br> Tom Cruise
Lien | url | Lien vers le site web. | [www.twitter.com](www.twitter.com)

