---
title: Référence de domaine prédéfini - Azure | Microsoft Docs
titleSuffix: Azure
description: Référence pour les domaines prédéfinis, qui sont des collections prédéfinies d’intentions et d’entités de LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 14c53bd25913922a0cd7cc438ad0fbe8b4663dd1
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061962"
---
# <a name="prebuilt-domain-reference"></a>Référence de domaine prédéfini
Cette référence fournit des informations sur les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md), qui sont des collections prédéfinies d’intentions et d’entités proposées par LUIS.

Les [domaines personnalisés](create-new-app.md), en revanche, commencent sans intentions ni modèles. Vous pouvez ajouter des intentions et entités de domaine prédéfini à un modèle personnalisé.

## <a name="list-of-prebuilt-domains"></a>Liste des domaines prédéfinis
LUIS propose 20 domaines prédéfinis. 

| Domaine prédéfini | Description | Langues prises en charge |
| ---------------- |-----------------------|:------:|
| Calendrier | Le domaine Calendrier fournit des intentions et entités pour ajouter, supprimer ou modifier un rendez-vous, vérifier la disponibilité des participants et rechercher des informations sur un événement de calendrier.| fr-FR<br/> zh-CN |
| Caméra | Le domaine Caméra fournit des intentions et entités pour prendre des photos, enregistrer des vidéos et diffuser une vidéo sur une application.| fr-FR |
| Communication | Envoi de messages et appels téléphoniques.| fr-FR <br/> zh-CN |
| Divertissement  | Gestion des requêtes liées à la musique, aux films et à la TV.| fr-FR |
| Événements | Réservation des tickets de concerts, festivals, rencontres sportives et spectacles de comédie.| fr-FR |
| Fitness | Gestion des requêtes liées au suivi des activités de fitness.| fr-FR |
| Jeux | Gestion des requêtes liées à une partie d’un jeu multijoueurs.| fr-FR |
| HomeAutomation | Contrôle des appareils domestiques intelligents tels que les lumières et les appliances.| fr-FR<br/> zh-CN |
| MovieTickets | Réservation de tickets de films au cinéma.| fr-FR |
| Musique | Lecture de musique sur un lecteur de musique.| fr-FR<br/> zh-CN |
| Remarque | Le domaine Note fournit des intentions et entités liées à la création, la modification et la recherche de notes.| fr-FR<br/> zh-CN |
| OnDevice | Le domaine OnDevice fournit des intentions et entités liées au contrôle de l’appareil.| fr-FR<br/> zh-CN |
| Lieux  | Gestion des requêtes liées aux lieux tels que les entreprises, établissements, restaurants, espaces publics et adresses.| fr-FR<br/> zh-CN |
| Rappel | Gestion des requêtes liées à la création, la modification et la recherche de rappels.| fr-FR<br/> zh-CN |
| RestaurantReservation | Gestion des requêtes pour gérer les réservations de restaurant.| fr-FR<br/> zh-CN |
| Taxi | Gestion des réservations de taxi.| fr-FR<br/> zh-CN |
| Traduire | Traduction de texte dans une langue cible.| fr-FR<br/> zh-CN |
| TV | Contrôle des téléviseurs.| fr-FR |
| Services  | Gestion des requêtes communes à de nombreux domaines telles que « aide », « répéter », « recommencer ».| fr-FR |
| Météo | Obtention de rapports et de prévisions météo.| fr-FR<br/> zh-CN |
| Web | Navigation vers un site web.| fr-FR<br/> zh-CN |

Pour plus de détails sur chaque domaine, consultez les sections qui suivent.

## <a name="calendar"></a>Calendrier 

Le domaine Calendrier fournit les intentions et entités liées aux entrées de calendrier. Les intentions de Calendrier incluent l’ajout, la suppression ou la modification d’un rendez-vous, la vérification de la disponibilité des participants et la recherche d’informations sur une entrée de calendrier ou un rendez-vous.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Ajouter | Ajoutez un nouvel élément à usage unique au calendrier.| Créer un rendez-vous avec Lisa à 14 h 00 dimanche <br/><br/>Je souhaite planifier une réunion<br/><br/>Je dois configurer une réunion|
| CheckAvailability | Recherchez la disponibilité pour un rendez-vous ou une réunion dans le calendrier de l’utilisateur ou d’une autre personne.| Quand Jim est-il disponible ? <br/><br/>Montrer quand Carole est disponible demain<br/><br/>Chris est-il libre samedi ?|
| Supprimer | Requête pour supprimer une entrée de calendrier.| Annulez mon rendez-vous avec Carol. <br/><br/>Supprimer ma réunion de 9 h 00<br/>|
| Modifier | Requête pour modifier une réunion ou une entrée de calendrier existante.| Déplacez ma réunion de 9 h 00 à 10 h 00.<br/><br/>Je souhaite mettre à jour mon calendrier.<br/><br/>Replanifiez ma réunion avec Ryan.|
| Rechercher | Affichez mon calendrier hebdomadaire.| Recherchez le rendez-vous de contrôle chez le dentiste. <br/><br/>Afficher mon calendrier<br/>|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Lieu | Emplacement de l’élément, de la réunion ou du rendez-vous de calendrier. Les adresses, les villes et les régions sont de bons exemples d’emplacements.| 209 Nashville Gym <br/><br/>897 Pancake house<br/><br/>Garage|
| Objet | Titre d’une réunion ou d’un rendez-vous.| Rendez-vous chez le dentiste <br/><br/>Déjeuner avec Julia<br/><br/>Rendez-vous chez le docteur|

## <a name="camera"></a>Caméra 
Le domaine Caméra fournit les intentions et entités liées à l’utilisation d’une caméra. Les intentions couvrent la capture une photo, d’un selfie, d’une capture d’écran ou d’une vidéo et la diffusion d’une vidéo sur une application.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| CapturePhoto| Capturez une photo.| Prendre une photo<br/><br/>capture|
| CaptureScreenshot | Effectuez une capture d’écran.| Prenez une capture d’écran.<br/><br/>capturez l’écran.|
| CaptureSelfie | Effectuer un selfie.| Prendre un selfie <br/><br/>prendre une photo de moi |
| CaptureVideo | Démarrez l’enregistrement vidéo.| Démarrer l’enregistrement <br/><br/>Commencer l’enregistrement|
| StartBroadcasting| Commencez la diffusion vidéo.| Commencer la diffusion sur Facebook|
| StartBroadcasting| Arrêtez la diffusion vidéo.| Arrêter la diffusion|
| StopVideoRecording| Arrêtez l’enregistrement d’une vidéo.| Ça suffit<br/><br/>arrêter l’enregistrement|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| AppName | Nom d’une application sur laquelle diffuser la vidéo.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Communication 
Le domaine Communication fournit des intentions et entités liées à la messagerie, aux messages et aux appels téléphoniques.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| AddContact| Ajoutez un nouveau contact à la liste des contacts de l’utilisateur.|Ajouter un contact <br/><br/>Enregistrer ce numéro et insérer le nom Carol|
| AddMore| Ajoutez d’autres informations à un courrier électronique ou au texte, dans le cadre d’une composition de courrier électronique ou de texte par étapes.|Ajouter d’autres informations au texte <br/><br/>Ajouter d’autres informations au corps du courrier électronique|
| Réponse| Répondez à un appel téléphonique entrant.|Répondre à l’appel <br/><br/>Décrocher|
| AssignContactNickname| Attribuez un surnom à un contact.|Remplacer Isaac par papa <br/>Modifier le surnom de Jim<br/>Ajouter un surnom à Patti Owens|
| CallVoiceMail| Connectez-vous à la messagerie vocale de l’utilisateur.|Me connecter à ma boîte de messagerie vocale <br/>Messagerie vocale<br/>Appeler la messagerie vocale|
| CheckIMStatus| Vérifiez l’état d’un contact dans Skype.|L’état En ligne de Jim est-il défini sur Absent ? <br/>Carol est-elle disponible pour une conversation ?|
| Confirmer| Confirmez une action.|OUI<br/>OK<br/>D’accord<br/>Je confirme que je souhaite envoyer ce courrier électronique.<br/>|
| Composer| Effectuez un appel téléphonique.|Appeler Jim<br/>Merci de composer 311<br/>|
| FindContact| Recherchez des informations de contact par nom.|Rechercher le numéro de Carol<br/>Me montrer le numéro de Carol<br/>|
| FindSpeedDial| Recherchez le numéro de numérotation rapide défini pour un numéro de téléphone et vice versa.|À quoi correspond le numéro 5 ?<br/>La numérotation rapide est-elle définie ?<br/>Quel est le numéro pour 941-5555-333 ?|
| GetForwardingsStatus| Obtenez l’état actuel du transfert d’appel.|Mon transfert d’appel est-il activé ?<br/>Me dire si l’état de mon appel est activé ou désactivé<br/>|
| GoBack| Revenez à l’étape précédente.|Revenir à Twitter<br/>Revenir en arrière d’une étape<br/>Retour|
| Ignorer| Ignorez un appel entrant.|Ne pas répondre<br/>Ignorer l’appel|
| IgnoreWithMessage| Ignorez un appel entrant et répondez plutôt par SMS.|Ne répondez pas à cet appel mais envoyez un message à la place.<br/>Ignorez et renvoyez du texte.|
| PressKey| Appuyez sur un bouton ou un nombre du clavier.|Composez étoile.<br/>Appuyez sur 1 2 3.|
| ReadAloud| Lisez un message ou un courrier électronique à l’utilisateur.|Lisez du texte.<br/>Qu’a-t-elle dit dans le message ?|
| TurnForwardingOff| Effectuez un appel téléphonique.|<br/><br/>|
| Recomposer| Recomposez ou rappelez un numéro.|Recomposez.<br/>Recomposez mon dernier appel.|
| Rejeter| Rejetez un appel entrant.|Rejeter l’appel<br/>Ne pas répondre maintenant<br/>Non disponible pour le moment et rappellera plus tard.|
| SendEmail| Envoyez un courrier électronique. Cette intention s’applique aux courriers électroniques mais pas aux SMS.|Courrier électronique à Mike Waters : Mike, le dîner de la semaine dernière était exceptionnel.<br/>Envoyer un courrier électronique à Bob<br/>|
| SendMessage| Envoyez un SMS ou un message instantané.|Envoyer du texte à Chris et Carol|
| SetSpeedDial| Définissez un raccourci de numérotation rapide pour le numéro de téléphone d’un contact.|Définissez la numérotation rapide un pour Carol.<br/>Configurez la numérotation rapide pour maman.|
| ShowNext| Affichez l’élément suivant, par exemple, dans une liste de SMS ou de courriers électroniques.|Affichez le suivant.<br/>Allez à la page suivante.|
| ShowPrevious| Affichez l’élément précédent, par exemple, dans une liste de SMS ou de courriers électroniques.|Affichez le précédent.<br/>Précédent<br/>Allez au précédent.|
| StartOver| Démarrez le système ou démarrez une nouvelle session.|Démarrer<br/>Nouvelle session<br/>restart|
| TurnForwardingOff| Désactivez le transfert d’appel.|Arrêter le transfert de mes appels<br/>Désactiver le transfert d’appel|
| TurnForwardingOn| Désactivez le haut-parleur du téléphone.|Transfert de mes appels au 3333<br/>Activer le transfert d’appel au 3333|
| TurnSpeakerOff| Désactivez le haut-parleur du téléphone.|Arrêtez le haut-parleur.<br/>Désactivez le mains libres.<br/>|
| TurnSpeakerOn| Activez le haut-parleur du téléphone.|Mode Mains libres.<br/>Activez le mains libres.<br/>|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| AudioDeviceType | Type d’appareil audio (haut-parleur, casque, microphone, etc.).| Intervenant<br/>Mains libres<br/>Bluetooth|
| Catégorie | Catégorie d’un message ou courrier électronique.| Important<br/>Priorité élevée|
| ContactAttribute | Attribut du contact sur lequel l’utilisateur effectue une demande.| Anniversaires<br/>Adresse<br/>Numéro de téléphone|
| ContactName | Nom d’un contact ou destinataire de message.| Carol<br/>Jim<br/>Chris|
| EmailSubject | Texte utilisé comme ligne d’objet d’un courrier électronique.| RE : histoire intéressante|
| Lignes | Ligne que l’utilisateur souhaite utiliser pour effectuer un appel ou à partir de laquelle envoyer en SMS/courrier électronique.| Ligne professionnelle<br/>Cellule britannique<br/>Skype|
| Message | Message à envoyer en tant que courrier électronique ou SMS.| Cela a été un plaisir de te voir aujourdh’ui. À bientôt !|
| MessageType | Nom d’un contact ou destinataire de message.| Texte<br/>Email|
| OrderReference | Position ordinale ou relative dans une liste, identifiant l’élément à récupérer. Par exemple, « dernier » ou « récent» dans « Quel est le dernier message que j’ai envoyé ? »| Dernier<br/>Récent|
| SenderName | Nom de l’expéditeur.| Patti Owens|

## <a name="entertainment"></a>Divertissement  
Le domaine Divertissement fournit les intentions et entités liées à la recherche de films, musiques, jeux et programmes télévisés.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Recherche| Recherchez des films, musiques, applications, jeux et programmes télévisés.|Recherchez la boutique Halo.<br/>Recherchez Avatar.|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| ContentRating | Évaluation de contenu multimédia telle que G ou R pour les films.|Vidéo pour enfants.<br/>Évalué PG.|
| Genre | Genre d’un film, d’un jeu, d’une application ou d’une chanson.|Comédies<br/>Dramas<br/>Amusant|
| Mot clé| Mot-clé de recherche générique spécifiant un attribut qui n’existe pas dans les emplacements multimédias plus spécifiques.|Bandes son<br/>Moon River<br/>Amelia Earhart|
| Langage | Évaluation de contenu multimédia telle que G ou R pour les films.|Français<br/>Français<br/>Coréen|
| MediaFormat | Type technique spécial supplémentaire dans lequel le média est formaté.|Films HD<br/>Films 3D<br/>Téléchargeable|
| MediaSource | Boutique ou Place de marché pour acquérir le média.|Netflix<br/>Prime|
| MediaSubTypes| Types de médias plus petits que les films et jeux.|Démonstrations<br/>Dlc<br/>Bandes-annonce|
| Nationalité| Pays dans lequel un film, un programme ou une chanson a été créé.|Français<br/>Allemand<br/>Coréen|
| Personne| Acteur, directeur, producteur, musicien ou artiste associé à un film, une application, un jeu ou un programme télévisé.|Madonna<br/>Stanley Kubrick|
| Rôle| Rôle joué par une personne dans la création d’un média.|Chante<br/>Dirigé par<br/>par|
| Intitulé| Nom d’un film, d’une application, d’un jeu, d’un programme télévisé ou d’une chanson.|Friends<br/>Minecraft|
| type| Type ou format de média d’un film, d’une application, d’un jeu, d’un programme télévisé ou d’une chanson.|Musique<br/>MovieTV <br/>programmes|
| UserRating| Évaluation utilisateur en étoile ou pouces.|5 étoiles<br/>3 étoiles<br/>4 étoiles|

## <a name="events"></a>Événements 
Le domaine Événements fournit les intentions et entités liées à la réservation de tickets pour des événements tels que des concerts, festivals, rencontres sportives et spectacles de comédie.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Réserver| Achetez des tickets pour un événement.|Je souhaiterai acheter un ticket pour la symphonie ce week-end.|


### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Adresse | Emplacement ou adresse de l’événement. |Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| NOM | Nom d’un événement.|Shakespeare in the Park|
| PlaceName| Nom d’emplacement de l’événement.|Louvre<br/>Opera House<br/>Broadway|
| PlaceType | Type de l’emplacement où l’événement se déroulera.|Café<br/>Théâtre<br/>Bibliothèque|
| type | Type d’un événement.|Concert<br/>Rencontre sportive|

## <a name="fitness"></a>Fitness 
Le domaine Fitness fournit les intentions et entités liées au suivi d’activités de fitness. Les intentions incluent l’enregistrement de notes, la durée ou distance restante ou l’enregistrement des résultats d’activité.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| AddNote| Ajoute des notes supplémentaires à une activité suivie.|La difficulté de cette course était de 6/10<br/>Le terrain sur lequel je cours est de l’asphalte<br/>J’utilise un vélo à 3 vitesses|
|GetRemaining| Obtient la durée ou distance restante d’une activité.|Combien de temps jusqu’au prochain tour ?<br/>Combien de kilomètres restants dans ma course ? Combien de temps avant le changement ?|
| LogActivity| Enregistrez ou consignez les résultats d’activité terminée.|Enregistrer ma dernière course<br/>Consigner ma marche de samedi matin<br/>stocker ma précédente nage|
| LogWeight| Enregistrez ou consignez le poids actuel de l’utilisateur.|Enregistrer mon poids actuel<br/>consigner mon poids maintenant<br/>stocker mon poids de corps actuel|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| ActivityType | Type d’activité à suivre. |Exécuter<br/>Marche<br/>Natation<br/>Cycle |
| Aliment | Type d’aliment à suivre dans une application de fitness. |Banane<br/>Saumon<br/>Boisson protéinée|
| MealType| Type de repas à suivre dans une application de santé ou de fitness.|Petit-déjeuner<br/>Dîner<br/>Déjeuner<br/>Souper|
| Mesure| Type de mesures de temps, distance ou poids à utiliser dans une application de fitness ou de santé.|Kilomètres<br/>Miles<br/>Minutes<br/>Kilogrammes|
| Number | Quantité numérique à utiliser dans une application de fitness ou de santé.|19<br/>three<br/>200<br/>one|
| StatType | Type statistique sur des données agrégées, à utiliser dans une application de fitness ou de santé.|Somme<br/>Moyenne<br/>Maximale<br/>Minimale|

## <a name="gaming"></a>Jeux 
Le domaine Jeux fournit des intentions et entités liées à la gestion d’une partie dans un jeu multijoueurs.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| InviteParty| Invitez un contact à rejoindre une partie.|Inviter ce joueur dans ma partie<br/>Venir dans ma partie<br/>Rejoindre mon clan|
|LeaveParty| Obtient la durée ou distance restante d’une activité.|Je suis parti<br/>Je quitte cette partie pour une autre<br/>Je quitte|
| StartParty| Commencez une partie d’un multijoueurs.|Dude commençons une partie<br/>commencer une partie<br/>nous devons commencer un clan ce soir|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Contact| Nom de contact à utiliser dans un jeu multijoueurs.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
Le domaine HomeAutomation fournit les intentions et entités liées au contrôle des appareils domestiques intelligents tels que les lumières et les appliances.

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| TurnOff| Désactivez, fermez ou déverrouillez un appareil.|Éteindre les lumières<br/>Arrêter la cafetière<br/>Fermer la porte du garage|
|TurnOn| Activez un appareil ou mettez l’appareil dans un paramètre ou mode particulier.|allumer ma cafetière<br/>pouvez-vous allumer ma cafetière ?<br/>Réglez le thermostat sur 72 degrés.|


### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Appareil | Type d’appareil qui peut être activé ou désactivé.|cafetière<br/>thermostat<br/>lumières|
| Opération | État pour définir l’appareil.|lock<br/>ouvert<br/>sur<br/>arrêt|
| Salle | Emplacement ou pièce où se trouve l’appareil.|salon<br/>chambre<br/>cuisine|

## <a name="movietickets"></a>MovieTickets 
Le domaine MovieTickets fournit les intentions et entités liées à la réservation de tickets de films au cinéma.

### <a name="examples"></a>Exemples
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Réserver | Achetez des tickets de films.|M’acheter deux tickets pour Captain Omar and the two musketeers<br/>Je souhaite acheter un ticket pour le film de demain<br/>Je souhaite un ticket pour Captian Omar Part 2 mercredi prochain|
|GetShowTime| Obtenez la présentation d’un film.|De quoi parle Captain Omar ?|


### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Adresse | Adresse d’un cinéma.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | Titre d’un film.|Life of Pi<br/>Hunger Games<br/>Inception|
| PlaceName | Nom d’un cinéma.|Cinema Amir<br/>Alexandria Theatre<br/>New York Theater|
| PlaceType | Type d’emplacement dans lequel un film est présenté.|cinéma<br/>salle de conférence<br/>Cinéma IMAX|

## <a name="music"></a>Musique 
Le domaine Musique fournit les intentions et entités liées à la lecture de musique sur un lecteur de musique.

### <a name="examples"></a>Exemples
```
play Beethoven
Increase track volume
Skip to the next song
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| DecreaseVolume | Diminuez le volume de l’appareil.|diminuer le volume de la piste<br/>volume bas|
| IncreaseVolume | Augmentez le volume de l’appareil.|augmenter le volume de la piste<br/>volume haut|
| Muet |Coupe le son de la lecture de musique.|Couper le son de la chanson<br/>Mettre la piste sur volume muet<br/>Couper la musique |
| Suspendre | Suspendez la lecture de la musique.|Suspendre<br/>Suspendre la musique<br/>Suspendre la piste|
| PlayMusic | Lisez de la musique sur un appareil.|lire Kevin Durant<br/>lire Paradise de Coldplay<br/>lire Hello d’Adele|
| Répéter |Répétez la musique en cours de lecture.|Répéter la chanson<br/>Relire la piste<br/>Répéter la musique|
| Reprendre | Reprenez la musique en cours de lecture.|Reprendre la chanson<br/>Recommencer la musique<br/>Annuler la suspension|
| SkipBack | Passez une piste précédente.|Passer à la chanson suivante<br/>Lire la chanson suivante|
| SkipForward |Passez une piste suivante.|Lire la chanson précédente<br/>Revenir à la piste précédente |
| Arrêter | Arrêtez une action liée à la lecture de musique. |Arrêtez la lecture de cet album.|
| Réactiver le son | Réactivez le son d’un appareil de lecture de musique.| Réactivez le son.|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| ArtistName | Acteur, directeur, producteur, compositeur, musicien ou artiste associé au média à lire sur un appareil.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | Genre de la musique demandée.|Country<br/>Broadway<br/>Lire ma musique classique de la période Baroque|

## <a name="note"></a>Remarque 
Le domaine Note fournit des intentions et entités liées à la création, la modification et la recherche de notes.

### <a name="examples"></a>Exemples
```
Add to my groceries note lettuce tomato bread coffee
Check off bananas from my grocery list
Remove all items from my vacation list
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| AddToNote | Ajoutez des informations à une note.|Ajouter à ma liste de course laitue tomate pain café<br/>Ajouter à ma liste des tâches<br/>ajouter cupcakes à ma Wunderlist|
| CheckOffItem | Retirez des éléments d’une note préexistante.|Retirer les bananes de ma liste de courses<br/>Marquer cheese cake comme fait sur ma liste de courses de vacances|
| Effacer | Effacez tous les éléments d’une note préexistante.|Supprimer tous les éléments de ma liste de congés<br/>Effacer tout dans ma liste de lectures|
| Confirmer | Confirmez une action liée à une note.|C’est d’accord pour moi<br/>Oui<br/>Je confirme la conservation de tous les éléments de listes|
| Créer | Créez une nouvelle note. | Créer une liste<br/>Noter de me rappeler que Jason est en ville la première semaine de mai|
| Supprimer | Supprimez une note entière. |Supprimer ma liste de congés <br/>supprimer ma liste de courses|
| DeleteNoteItem | Supprimez des éléments d’une note préexistante.| Supprimer les chips de ma liste de courses<br/>Supprimer les crayons de ma liste de fournitures scolaires|
| ReadAloud | Lisez à haute voix une liste.|Me lire la première<br/>Me lire les détails|
| ShowNext | Affichez l’élément suivant dans une liste de notes.|Afficher le suivant<br/>Page suivante<br/>Suivant|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| AppName | Nom de l’application de prise de notes.|Wunderlist<br/>OneNote|
| ContactName | Nom d’un contact dans une note.|Carol<br/>Jim<br/>Chris|
| DataSource | Emplacement de notes.|OneDrive<br/>Google docs<br/>mon ordinateur|
| DataType | Type de fichier ou de document, généralement associé à des programmes logiciels particuliers.|Diapositives<br/>Feuille de calcul<br/>Feuille de calcul|
| Texte | Texte d’une note ou d’un rappel.|s’étirer avant la marche<br/>longue course demain|
| Intitulé | Titre d’une note.|épicerie<br/>personne à appeler<br/>à faire|

## <a name="ondevice"></a>OnDevice 
Le domaine OnDevice fournit des intentions et entités liées au contrôle de l’appareil.

### <a name="examples"></a>Exemples
```
Close video player
Cancel playback
Can you make the screen brighter?
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| AreYouListening | Demandez si l’appareil écoute.|activé ?<br/>vous écoutez ?|
|CloseApplication|Fermez l’application de l’appareil.|fermer lecteur de vidéo|
|FileBug|Signalez un bogue sur l’appareil.|merci de signaler un bogue<br/>Pouvez-vous signaler un bogue pour moi ?<br/>Me laisser signaler ce bogue|
|GoBack|Demandez à revenir en arrière d’une étape ou de revenir à l’étape précédente.|Merci de revenir en arrière<br/>Aller à l’écran précédent<br/>Revenir en arrière arrêter l’écoute|
|Aide| Demandez de l’aide.|De l’aide s’il vous plaît<br/>Hello<br/>Que pouvez-vous faire ?<br/>J’ai besoin d’aide| 
|LocateDevice|Localisez l’appareil.|Pouvez-vous localiser mon téléphone<br/>Rechercher l’iphone de Tom<br/>Rechercher mon téléphone|
|LogIn|Connectez-vous à un service à l’aide de l’appareil.|Merci de me connecter<br/>Connexion Facebook<br/>Se connecter à LinkedIn|
|LogOut|Déconnectez-vous d’un service à l’aide de l’appareil.|Déconnecter mon téléphone<br/>Se connecter à Twitter<br/>Se déconnecter|
|MainMenu|Affichez le menu principal d’un appareil.|Affichez le menu.|
|OpenApplication|Ouvrez une application sur l’appareil.|Merci d’ouvrir l’alarme<br/>Activer la caméra<br/>Lancer le calendrier|
|OpenSetting|Ouvrez un paramètre sur l’appareil.|Ouvrez les paramètres réseau.|
|PairDevice|Associez l’appareil.|Pouvez-vous m’aider à associer le signal Bluetooth au téléphone<br/>Activer le bluetooth et l’associer à l’ordinateur portable<br/>Associer le signal Bluetooth à mon ordinateur portable|
|PowerOff | Désactivez l’appareil.|Pouvez-vous arrêter mon ordinateur<br/>Shutdown<br/>Désactiver mon mobile|
|QueryBattery|Obtenez des informations sur l’autonomie de la batterie.|Montrez-moi l’autonomie de la batterie.<br/>Quel est l’état de ma batterie<br/>Quelle quantité de batterie reste-t-il ?<br/>Me montrer la batterie|
|QueryWifi|Obtenez des informations sur le Wi-Fi.|Obtenez des infos de Wi-Fi.|
|Redémarrer|Redémarrez l’appareil.|Veuillez redémarrer.|
|RingDevice| Demandez à l’appareil de sonner pour pouvoir le retrouver lorsqu’il est perdu.|Faites sonner mon téléphone.| 
|SetBrightness|Définissez la luminosité de l’appareil.|Définir la luminosité sur moyenne<br/>Définir la luminosité sur élevée<br/>Définir la luminosité sur faible|
|SetupDevice|Commencez la configuration de l’appareil.|Je souhaite installer le programme d’installation du système d’exploitation<br/>Configurer s’il vous plaît<br/>Faire la configuration pour moi|
|ShowAppBar|Affichez la barre d’une application.|Me montrer la barre d’application<br/>Barre d’application s’il vous plaît<br/>Me laisser voir la barre d’application|
|ShowContextMenu|Affichez un menu contextuel.|Me laisser voir le menu contextuel<br/>Menu contextuel s’il vous plaît<br/>Pouvez-vous me montrer le menu contextuel|
|Veille|Mettez l’appareil en veille.|Passer en veille<br/>Veille<br/>Veille de mon ordinateur|
|SwitchApplication|Changez l’application à utiliser sur l’appareil.|Basculez sur mon lecteur multimédia.|
|TurnDownBrightness|Diminuez la luminosité de l’appareil.|Atténuez l’écran.|
|TurnOffSetting|Désactivez un paramètre d’appareil.|Désactiver Bluetooth<br/>Désactiver données<br/>Déconnecter bluetooth|
|TurnOnSetting|Activez un paramètre d’appareil.|Il en va <br/> Off|
|TurnUpBrightness|Augmentez la luminosité de l’appareil.|Pouvez-vous éclaircir l’écran ?|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| AppName | Nom d’une application sur l’appareil.|SoundCloud<br/>YouTube|
| BrightnessLevel | Définissez le niveau de luminosité sur l’appareil.|Cent pourcent<br/>Cinquante<br/>40%|
| ContactName | Nom d’un contact sur l’appareil.|Paul<br/>Marlen Max|
| DeviceType | Type d’appareil. |Téléphone<br/>Kindle<br/>Ordinateur portable|
| MediaType | Type de média géré par l’appareil.|Musique<br/>Film<br/>Programmes télévisés|
| SettingType | Type de paramètres ou panneau de paramètres que l’utilisateur souhaite modifier.|WiFi<br/>Réseau sans fil<br/>Schéma de couleur<br/>Centre de notifications|

## <a name="places"></a>Lieux  
Le domaine Lieux fournit les intentions pour la gestion des requêtes liées aux lieux tels que les entreprises, établissements, restaurants, espaces publics et adresses.

### <a name="examples"></a>Exemples
```
Save this location to my favorites
How far away is Holiday Inn?
At what time does Safeway close?
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Ajoutez un emplacement à la liste des favoris de l’utilisateur.|Enregistrer cet emplacement dans mes favoris<br/>Ajouter cette adresse à mes favoris|
|CheckAccident|Demandez s’il y a un accident sur une route donnée.|Y-a-til un accident sur la 880 ?<br/>Me montrer les informations de l’accident|
|CheckAreaTraffic|Vérifiez le trafic dans une zone ou sur autoroute, et non sur un itinéraire donné.|Trafic à Seattle<br/>Quel est le trafic à Seattle ?|
|CheckIntoPlace|Archivez dans un lieu à l’aide de réseaux sociaux.|M’archiver sur Foursquare<br/>Archiver ici|
|CheckRouteTraffic| Vérifiez le trafic d’un itinéraire spécifique indiqué par l’utilisateur.|Quel est le trafic vers Mashiko ?<br/>Me montrer le trafic vers Kirkland<br/>Comment est le trafic vers Seattle ?| 
|Confirmer|Confirmez une action liée à un lieu.|Confirmez ma réservation de restaurant.|
|Quitter|Action pour quitter une tâche liée à un lieu.|Merci de quitter<br/>Quitter en me donnant des directions|
|FindPlace|Recherchez un lieu (entreprise, établissement, restaurant, espace public, adresse).|Où se trouve la bibliothèque la plus proche ?<br/>Me trouver un bon restaurant italien à Mountain View|
|GetAddress| Demandez l’adresse d’une lieu.|Me montrer l’adresse de Guu dans la rue Robson<br/>Quelle est l’adresse du Starbucks le plus proche ?| 
|GetDistance|Demandez la distance par rapport à un lieu spécifique.|À quelle distance se trouve l’Holiday Inn ?<br/>à quelle distance je me trouve du parc Bellevue<br/>quelle est la distance de Tahoe|
|GetHours|Demandez les heures d’ouverture d’un lieu.|À quelle heure ferme Safeway ?<br/>Quels sont les horaires du Home Depot ?<br/>La Starbucks est-il toujours ouvert ?|
|GetMenu|Demandez les plats à la carte d’un restaurant.|Le Zucca sert-il des plats végan ?<br/>Qu’y-a-t-il au menu du Sizzler<br/>Me montrer le menu de l’Applebee|
|GetPhoneNumber| Demandez le numéro de téléphone d’un lieu.|Quel est le numéro de téléphone du Starbucks le plus proche ?<br/>Donner le numéro de Home Depot| 
|GetPriceRange| Demande la gamme de prix d’un lieu.|Le Zucca est-il bon marché ?<br/>Le Cineplex fait-il moitié prix le mercredi ?<br/>Combien coûte un dîner homard au Sizzler ?|
|GetReviews|Demandez les avis d’un lieu.|Me montrer les avis de Cheesecase Factory<br/>Lire les avis de Cineplex dans Yelp|
|GetRoute|Demandez les directions vers un lieu.|Comment aller à pied au parc Bellevue<br/>Me montrer le chemin le plus court vers la 8ème et la 59ème à partir d’ici<br/>Me donner des directions pour Mountain View CA|
|GetStarRating|Demandez l’évaluation en étoile d’un lieu.|Quelle est l’évaluation de Zucca dans Yelp ?<br/>Combien d’étoiles a French Laundry ?<br/>L’aquarium de Monterrey est-il bien ?|
|GetTransportationSchedule|Obtenez les horaires de bus d’un lieu.|À quelle heure est le prochain bus pour le centre-ville ?<br/>Me montrer les bus de King County|
|GetTravelTime|Demandez le temps de trajet pour une destination donnée.|Combien de temps entre ici et San Francisco<br/>Quel est le temps de conduite entre Denver et SF|
|MakeCall|Appelez un lieu.|Appeler maman<br/>Je souhaite appeler Anne sur Skype<br/>Appeler Jim|
|MakeReservation|Demandez une réservation pour un restaurant ou autre entreprise.|Réserver au Zucca pour deux pour ce soir<br/>Réserver une table pour demain<br/>Table pour 3 à Palo Alto à 8|
|MapQuestions|Demandez des informations sur des directions ou si une route donnée mène à une destination.|La 13 traverse-t-elle le centre-ville ?<br/>Puis-je prendre la 880 pour Oakland ?|
|Rating|Obtenez la description de l’évaluation d’un restaurant ou lieu.|Combien d’étoiles le Contoso Inn a-t-il ?|
|ReadAloud|Lisez à haute voix une liste de lieux.|Me lire la première<br/>Me lire les détails|
|SelectItem|Choisissez un élément dans une liste de choix liés à un ou des lieux.|Sélectionner le second<br/>Sélectionner le premier|
|ShowMap|Affichez une carte d’une zone.|Afficher une carte pour la seconde<br/>Afficher carte<br/>Rechercher San Francisco sur la carte|
|ShowNext|Affichez l’élément suivant d’une série.|Afficher le suivant<br/>aller à la page suivante|
|ShowPrevious|Affichez l’élément précédent d’une série.|afficher le précédent<br/>previous<br/>aller au précédent|
|StartOver|Redémarrez l’application ou démarrez une nouvelle session.|Démarrer<br/>Nouvelle session<br/>
restart|
|TakesReservations|Demandez si un lieu accepte les réservations.|La galerie d’art accepte-t-elle les réservations<br/>Est-il possible de faire une réservation à l’Olive Garden

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Emplacement ou adresse d’un lieu.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Équipements | Caractéristiques/avantages d’un lieu.|repas enfant gratuit<br/>front de mer<br/>stationnement gratuit|
| Atmosphère | Atmosphère d’un lieu.|pour enfants<br/>restaurant familial<br/>sportif|
| Cuisine | Cuisine d’un lieu. |Méditerranéenne<br/>Italien<br/>Indienne|
| DestinationAddress| Emplacement ou adresse de destination.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| Nom d’une destination qui est une entreprise, un restaurant, une attraction publique ou un établissement.|central park<br/>voie protégée<br/>walmart|
| DestinationPlaceType | Type d’une destination qui est une entreprise locale, un restaurant, une attraction publique ou un établissement. |Restaurant<br/>Opera<br/>Cinéma|
| Distance | Distance par rapport à un lieu.|15 miles<br/>5 miles<br/>10 miles|
| MealType | Type de repas comme petit-déjeuner ou déjeuner. |petit-déjeuner<br/>dîner<br/>déjeuner<br/>souper|
| OpenStatus | Indique si un lieu est ouvert ou fermé.|Ouverts<br/>Fermé<br/>ouverture|
| PlaceName | Nom d’un lieu.|Cheesecake Factory|
| PlaceType | Type d’un lieu.|Café<br/>Théâtre<br/>Bibliothèque|
| PreferredRoute | Itinéraire préféré spécifié par l’utilisateur. | 101 <br/>202 <br/>Itinéraire 401|
| Produit | Produit proposé par un lieu. | Vêtements<br/>Appareils photo ASR numériques<br/>Poisson frais | 
| PublicTransportationRoute | Nom de l’itinéraire de transport public que l’utilisateur recherche. | Voie ferrée du nord-est<br/>Ligne de bus 3X |
| Rating | Évaluation d’un lieu. | 5 étoiles<br/>3 étoiles<br/>4 étoiles|
| RouteAvoidanceCriteria | Critères pour éviter des itinéraires spécifiques comme pour éviter des accidents, travaux ou péages | Péages <br/>Travaux<br/>Itinéraire 11|
| ServiceProvided | Il s’agit du service proposé par une entreprise ou un lieu comme la coupe de cheveux, le déneigement, l’aménagement paysager. | coupe de cheveux<br/>mécanique<br/>plombier|
| TransportationCompany | Nom d’un prestataire de transport.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Type de transport.|Bus<br/>Former<br/>Conduite|

## <a name="reminder"></a>Rappel 
Le domaine Rappel fournit les intentions et entités pour créer, modifier et rechercher des rappels.

### <a name="examples"></a>Exemples
```
Change my interview to 9 am tomorrow
Remind me to buy milk on my way back home
Can you check if I have a reminder about Christine's birthday?
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Modifier| Modifiez un rappel.|Changer mon rendez-vous à 9 h 00 demain<br/>Déplacer mon rappel de mission à demain|
| Créer| Créez un nouveau rappel.|Créer un rappel<br/>Me rappeler d’acheter du lait<br/>Je souhaite me rappeler d’appeler Rebecca lorsque je rentre à la maison|
| Supprimer | Supprimez un rappel.|Supprimer mon rappel de photo<br/>Supprimer ce rappel|
| Rechercher | Recherchez un rappel.|Ai-je un rappel sur mon anniversaire ?<br/>Pouvez-vous vérifier si j’ai un rappel sur l’anniversaire de Christine ?|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Texte | Description d’un rappel.|aller chercher au pressing<br/>déposer ma voiture au service après-vente|

## <a name="restaurantreservation"></a>RestaurantReservation 
Le domaine RestaurantReservation fournit les intentions et entités liées à la gestion des réservations de restaurant.

### <a name="examples"></a>Exemples
```
Reserve at Zucca for two for tonight
Book a table at BJ's for tomorrow
Table for 3 in Palo Alto at 7
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Réserver | Demandez une réservation pour un restaurant. |Réserver au Zucca pour deux pour ce soir<br/>Réserver une table pour demain<br/>Table pour 3 à Palo Alto à 7|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Adresse| Emplacement ou adresse d’événement pour une réservation.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Équipements | Attribut décrivant les équipements d’un lieu.|vue sur l’océan<br/>interdiction de fumer|
| AppName | Nom d’une application pour faire des réservations.|OpenTable<br/>Yelp<br/>TripAdvisor|
| Atmosphère | Description de l’atmosphère d’un restaurant ou autre lieu.|romantique<br/>décontracté<br/>adapté aux groupes|
| Cuisine | Type d’alimentation, cuisine ou nationalité. |Chinois<br/>Italien<br/>Mexicain|
| MealType | Type de repas associé à une réservation.|petit-déjeuner<br/>dîner<br/>déjeuner<br/>souper|
| PlaceName | Nom d’une entreprise locale, restaurant, attraction publique ou établissement.|IHOP<br/>Cheesecake Factory<br/>Louvre|
| PlaceType | Type d’une entreprise locale, restaurant, attraction publique ou établissement.|restaurant<br/>opéra<br/>cinéma|
| Rating | Évaluation d’un lieu ou restaurant.|5 étoiles<br/>3 étoiles<br/>4 étoiles|

## <a name="taxi"></a>Taxi 
 
Le domaine Taxi fournit les intentions et entités pour créer et gérer les réservations de taxi.

### <a name="examples"></a>Exemples
```
Get me a cab at 3 pm
How much longer do I have to wait for my taxi?
Cancel my Uber
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Réserver | Appelez un taxi. |M’obtenir un taxi<br/>Trouver un taxi<br/>Me réserver un uber x|
| Annuler | Annulez une action liée à la réservation d’un taxi.|Annuler mon taxi<br/>Annuler mon Uber|
| Suivre | Suivez un itinéraire de taxi.|Combien de temps dois-je attendre mon taxi ?<br/>Où est mon Uber ?|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Adresse| Adresse associée à la réservation d’un taxi. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| Emplacement ou adresse de destination. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | Nom d’une destination qui est une entreprise locale, un restaurant, une attraction publique ou un établissement. |Central Park<br/>Voie protégée<br/>Walmart|
| DestinationPlaceType | Type d’une destination qui est une entreprise locale, un restaurant, une attraction publique ou un établissement. |Restaurant<br/>Opera<br/>Cinéma|
| PlaceName | Nom d’entreprise locale, restaurant, attraction publique ou établissement. |Central Park<br/>Voie protégée<br/>Walmart|
| PlaceType| Type de lieu dans une demande pour réserver un taxi.|Restaurant<br/>Opera<br/>Cinéma|
| TransportationCompany | Nom d’un prestataire de transport.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Type de transport.|Bus<br/>Former<br/>Conduite|

## <a name="translate"></a>Traduire 
Le domaine Traduire fournit les intentions et entités liées à la traduction de texte dans une langue cible.

### <a name="examples"></a>Exemples
```
Translate to French
Translate hello to German
Translate this sentence to English
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Traduire| Traduisez du texte dans une autre langue.|Traduire en français<br/>Traduire hello en allemand|


### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| TargetLanguage | Langue cible d’une traduction.|Français<br/>Allemand<br/>Coréen|
| Texte | Texte à traduire.|Hello World<br/>Good morning<br/>Good evening|

## <a name="tv"></a>TV 
 
Le domaine TV fournit les intentions et entités pour contrôler les téléviseurs.

### <a name="examples"></a>Exemples
```
Switch channel to BBC
Show TV guide
Watch National Geographic
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| ChangeChannel| Changez une chaîne sur un téléviseur.|Changer la chaîne sur CNN<br/>Basculer la chaîne sur BBC<br/>Aller à la chaîne 4|
| ShowGuide| Affichez le guide télé.|Afficher le guide télé<br/>qu’y-a-t-il maintenant sur la chaîne cinéma ?<br/>afficher ma liste de programmes|
| WatchTV| Demandez à voir une chaîne télé.|Je souhaite voir Disney channel<br/>aller à TV s’il vous plaît<br/>Voir National Geographic|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| ChannelName | Nom d’une chaîne télé.|CNN<br/>BBC<br/>Chaîne cinéma|

## <a name="utilities"></a>Services  
Le domaine Services fournit les intentions pour des tâches qui sont communes à de nombreuses tâches, comme les mots de bienvenue, l’annulation, la confirmation, l’aide, la répétition, la navigation, le démarrage et l’arrêt.

### <a name="examples"></a>Exemples
```
Go back to Twitter
Please help
Repeat last question please
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Annuler | Annulez une action.|Annuler le message<br/>Je ne souhaite plus envoyer le courrier électronique|
| Confirmer | Confirmez une action.|Ouais je confirme<br/>Bien, je confirme<br/>D’accord, je confirme|
| FinishTask | Terminez une tâche que l’utilisateur a commencée.|J’ai terminé<br/>J’ai fini<br/>C’est terminé|
| GoBack | Revenez en arrière d’une étape ou revenez à une étape précédente.|Revenir à Twitter<br/>Revenir en arrière d’une étape<br/>Retour|
| Aide | Demandez de l’aide.|De l’aide s’il vous plaît<br/>ouvrir l’aide<br/>help|
| Répéter | Répétez une action.|Répéter la dernière question s’il vous plaît<br/>répéter la dernière chanson|
| ShowNext | Affichez l’élément suivant d’une série. |Afficher le suivant<br/>aller à la page suivante|
| ShowPrevious | Affichez l’élément précédent d’une série.|afficher le précédent|
| StartOver | Redémarrez l’application ou démarrez une nouvelle session.|Démarrer<br/>Nouvelle session<br/>restart|
| Arrêter | Arrêtez une action.| Arrêter de dire cela s’il vous plaît<br/>Taisez-vous<br/>Arrêter s’il vous plaît|

## <a name="weather"></a>Météo 
Le domaine Météo fournit les intentions et entités pour l’obtention de rapports et de prévisions météo.

### <a name="examples"></a>Exemples
```
weather in London in september
What?s the 10 day forecast?
What's the average temperature in India in september?
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| GetCondition | Obtenez les faits historiques liés à la météo. |météo à Londres en septembre<br/>Quelle est la température moyenne en Inde en septembre ?|
| GetForecast | Obtenez la météo actuelle et la prévision pour les prochains jours. |Quel temps fait-il aujourd’hui ?<br/>Quelle est la prévision à 10 jours ?<br/>Quel temps fera-t-il ce week-end ?|

### <a name="entities"></a>Entités
| Nom de l’entité | Description | Exemples |
| ---------------- |-----------------------|----|
| Lieu| Emplacement absolu d’une requête météo.|Seattle<br/>Paris<br/>Palo Alto|

## <a name="web"></a>Web 
Le domaine Web fournit une intention pour la navigation vers un site web.

### <a name="examples"></a>Exemples
```
Navigate to facebook.com
Go to www.twitter.com
Navigate to www.bing.com
```

### <a name="intents"></a>Intentions
| Nom de l’intention | Description | Exemples |
| ---------------- |-----------------------|----|
| Naviguer | Requête pour accéder à un site web spécifié. |Accéder à facebook.com<br/>Aller à www.twitter.com|

