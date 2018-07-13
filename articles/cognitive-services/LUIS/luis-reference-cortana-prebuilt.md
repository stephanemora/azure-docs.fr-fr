---
title: Informations de référence sur l’application prédéfinie Cortana | Microsoft Docs
description: Informations de référence sur l’assistant personnel Cortana, une application prédéfinie de LUIS (Language Understanding Intelligent Services).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368204"
---
# <a name="cortana-prebuilt-app-reference"></a>Informations de référence sur l’application prédéfinie Cortana
Ces informations de référence listent les intentions et les entités reconnues par l’application prédéfinie Cortana.

## <a name="cortana-prebuilt-intents"></a>Intentions Cortana prédéfinies
L’application d’assistant personnel prédéfinie peut identifier les intentions suivantes :

| Intention | Exemples d’énoncés |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|mettre à jour mon alarme de 7:30 à huit heures <br/>modifier mon alarme de 8h00 à 9h00 |
| builtin.intent.alarm.delete_alarm|supprimer une alarme <br/>supprimer mon alarme « réveil »|
| builtin.intent.alarm.find_alarm|à quelle heure mon alarme de réveil est-elle réglée ? <br/> mon alarme de réveil est-elle activée ? |
| builtin.intent.alarm.set_alarm|activer mon alarme de réveil<br/>pouvez-vous régler une alarme à 12h00 pour ma prise d’antibiotiques ?|
| builtin.intent.alarm.snooze|répéter l’alarme dans cinq minutes<br/>répéter l’alarme|
| builtin.intent.alarm.time_remaining| combien de temps reste-t-il jusqu’au « réveil » ? <br/> combien de temps reste-t-il jusqu’à mon alarme suivante ?|
| builtin.intent.alarm.turn_off_alarm | désactiver mon alarme de sept heures <br/> désactiver mon alarme de réveil |
| builtin.intent.calendar.change_calendar_entry| changer un rendez-vous<br/>reporter ma réunion d’aujourd’hui à demain|
|builtin.intent.calendar.check_availability|tim est-il occupé vendredi ?<br/>brian est-il libre samedi ?|
|builtin.intent.calendar.connect_to_meeting|joindre une réunion<br/>joindre la réunion en ligne|
|builtin.intent.calendar.create_calendar_entry|j’ai besoin de planifier un rendez-vous avec tony vendredi<br/>créer un rendez-vous avec Lisa à 14h00 dimanche|
|builtin.intent.calendar.delete_calendar_entry|supprimer mon rendez-vous<br/>supprimer de mon calendrier ma réunion de 15h00 demain|
|builtin.intent.calendar.find_calendar_entry|afficher mon calendrier<br/>afficher mon calendrier hebdomadaire|
|builtin.intent.calendar.find_calendar_when|quand a lieu ma prochaine réunion avec jon ?<br/>à quelle heure est mon rendez-vous de lundi avec larry ?|
|builtin.intent.calendar.find_calendar_where|afficher l’emplacement de ma réunion de six heures<br/>où a lieu cette réunion avec jon ?|
|builtin.intent.calendar.find_calendar_who|avec qui est cette réunion ?<br/>qui d’autre est invité ?|
|builtin.intent.calendar.find_calendar_why|quels sont les détails de ma réunion de 11 heures ?<br/>sur quoi porte ma réunion avec jon ?|
|builtin.intent.calendar.find_duration|combien de temps durera ma prochaine réunion<br/>combien de minutes durera ma réunion de cet après-midi|
|builtin.intent.calendar.time_remaining|combien de temps jusqu’à mon rendez-vous suivant ?<br/>combien de temps me reste-il avant la prochaine réunion ?|
|builtin.intent.communication.add_contact|enregistrer ce numéro et inscrire le nom josé<br/>mettre jason dans ma liste de contacts|
|builtin.intent.communication.answer_phone|répondre<br/>répondre à l’appel|
|builtin.intent.communication.assign_nickname|modifier le surnom pour nicolas<br/>ajouter un surnom pour jean dupont|
|builtin.intent.communication.call_voice_mail|messagerie vocale<br/>appeler la messagerie vocale|
|builtin.intent.communication.find_contact|afficher mes contacts<br/>rechercher des contacts|
|builtin.intent.communication.forwarding_off|arrêter de transférer mes appels<br/>désactiver le transfert d’appel|
|builtin.intent.communication.forwarding_on|configurer le transfert d’appel pour renvoyer les appels vers mon numéro de téléphone personnel<br/>transférer les appels vers mon numéro de téléphone personnel|
|builtin.intent.communication.ignore_incoming|ne pas répondre à cet appel<br/>pas maintenant, je suis occupé|
|builtin.intent.communication.ignore_with_message|ne pas répondre à cet appel mais envoyer un message à la place<br/>ignorer et envoyer un message|
|builtin.intent.communication.make_call|appeler bob et john<br/>appeler maman et papa|
|builtin.intent.communication.press_key|composer étoile<br/>appuyer sur 1 2 3|
|builtin.intent.communication.read_aloud|lire du texte<br/>qu’a-t-elle dit dans le message|
|builtin.intent.communication.redial|recomposer le numéro de mon dernier appel<br/>recomposer|
|builtin.intent.communication.send_email|e-mail pour mike waters : mike, le dîner de la semaine dernière était exceptionnel<br/>envoyer un e-mail à bob|
|builtin.intent.communication.send_text|envoyer un message à bob et à john<br/>message|
|builtin.intent.communication.speakerphone_off|arrêter le haut-parleur<br/>désactiver le téléphone mains libres|
|builtin.intent.communication.speakerphone_on|mode téléphone mains libres<br/>activer le téléphone mains libres|
|builtin.intent.mystuff.find_attachment|rechercher le document que john m’a envoyé par e-mail hier <br/>rechercher le document de john|
|builtin.intent.mystuff.find_my_stuff|je souhaite modifier ma liste d’achat d’hier<br/>rechercher mes notes de chimie de septembre
|builtin.intent.mystuff.search_messages|ouvrir le message <br/>messages|
|builtin.intent.mystuff.transform_my_stuff|partager ma liste d’achat avec mon mari<br/>supprimer ma liste d’achat|
|builtin.intent.ondevice.open_setting|ouvrir les paramètres de cortana <br/>accéder aux notifications|
|builtin.intent.ondevice.pause|désactiver la musique<br/>musique éteinte|
|builtin.intent.ondevice.play_music|je souhaite écouter la chanson owner of a lonely heart<br/>lire de la musique gospel|
|builtin.intent.ondevice.recognize_song|dites-moi quel est ce morceau<br/>analyser et récupérer le titre du morceau|
|builtin.intent.ondevice.repeat|répéter cette piste<br/>relire cet morceau|
|builtin.intent.ondevice.resume|redémarrer la musique<br/>recommencer la musique|
|builtin.intent.ondevice.skip_back|une piste en arrière<br/>morceau précédent|
|builtin.intent.ondevice.skip_forward|morceau suivant<br/>aller à la piste suivante|
|builtin.intent.ondevice.turn_off_setting|wi-fi désactivé<br/>désactiver le mode avion|
|builtin.intent.ondevice.turn_on_setting|wi-fi activé<br/>activer bluetooth|
|builtin.intent.places.add_favorite_place|ajouter cette adresse à mes favoris<br/>enregistrer cet emplacement dans mes favoris|
|builtin.intent.places.book_public_transportation|acheter un billet de train<br/>réserver un pass de tramway|
|builtin.intent.places.book_taxi|pouvez-vous me trouver un taxi à cette heure-ci ?<br/>trouver un taxi|
|builtin.intent.places.check_area_traffic|comment est la circulation sur la 520<br/>comment est la circulation sur l’i-5|
|builtin.intent.places.check_into_place|s’enregistrer chez joya<br/>s’enregistrer ici|
|builtin.intent.places.check_route_traffic|afficher la circulation sur la route de kirkland<br/>comment est la circulation vers Seattle ?|
|builtin.intent.places.find_place|où est-ce que j’habite <br/>indiquez-moi les trois meilleurs restaurants japonais|
|builtin.intent.places.get_address|montrez-moi l’adresse de guu dans robson street<br/>quelle est l’adresse du starbucks le plus proche ?|
|builtin.intent.places.get_coupon|rechercher des offres sur des téléviseurs dans mon quartier<br/>rabais à mountain view|
|builtin.intent.places.get_distance|à quelle distance se trouve l’holiday inn ?<br/>à quelle distance se trouve Tahoe|
|builtin.intent.places.get_hours|quelles sont les heures d’ouverture de bar del corso le lundi ?<br/>quand la bibliothèque est-elle ouverte ?|
|builtin.intent.places.get_menu|montrez-moi le menu d’applebee<br/>qu’y-a-t-il au menu de sizzler|
|builtin.intent.places.get_phone_number|donnez-moi le numéro de home depot<br/>quel est le numéro de téléphone du starbucks le plus proche ?|
|builtin.intent.places.get_price_range|combien coûte un dîner chez red lobster<br/>est-ce cher chez purple cafe|
|builtin.intent.places.get_reviews|montrez-moi les commentaires pour les quincailleries locales<br/>je souhaite voir des commentaires de restaurants|
|builtin.intent.places.get_route|montrez-moi comment aller à|est-il possible d’aller chez pizza hut à pied|
|builtin.intent.places.get_star_rating|combien d’étoiles a French Laundry ?<br/>l’aquarium de monterrey est-il bien ?|
|builtin.intent.places.get_transportation_schedule|à quelle heure le ferry de san francisco part-il ?<br/>à quelle heure est le dernier train pour seattle ?|
|builtin.intent.places.get_travel_time|quel est le temps de conduite entre denver et SF<br/>combien de temps faut-il pour aller d’ici à san fransisco|
|builtin.intent.places.make_call|appeler le Dr. smith à bellevue<br/>appeler le centre de maintenance de la 1ère rue|
|builtin.intent.places.rate_place|donner une évaluation pour ce restaurant<br/>donner cinq étoiles à il fornaio sur yelp|
|builtin.intent.places.show_map|quel est mon emplacement actuel <br/>quel est mon emplacement|
|builtin.intent.places.takes_reservations|est-il possible d’effectuer une réservation à l’olive garden<br/>la galerie d’art accepte-t-elle les réservations|
|builtin.intent.reminder.change_reminder|changer un rappel<br/>déplacer vers le haut l’image de rappel du jour de 30 minutes|
|builtin.intent.reminder.create_single_reminder|me rappeler de me réveiller à sept heures du matin<br/>me rappeler d’aller fêter halloween avec luca à 16h40|
|builtin.intent.reminder.delete_reminder|supprimer ce rappel<br/>supprimer ma photo de rappel|
|builtin.intent.reminder.find_reminder|ai-je des rappels pour aujourd’hui<br/>ai-je défini un rappel pour la soirée de luca|
|builtin.intent.reminder.read_aloud|lire le rappel à haute voix<br/>lire ce rappel|
|builtin.intent.reminder.snooze|répéter ce rappel demain<br/>répéter ce rappel|
|builtin.intent.reminder.turn_off_reminder|désactiver le rappel<br/>annuler le rappel du rendez-vous à l’aéroport|
|builtin.intent.weather.change_temperature_unit|convertir de fahrenheit en kelvin<br/>convertir de fahrenheit en celsius|
|builtin.intent.weather.check_weather|afficher les prévisions pour mon prochain voyage à seattle<br/>quel temps fera-t-il ce week-end|
|builtin.intent.weather.check_weather_facts|quel temps fait-il à hawaii en décembre ?<br/>quel était la température à cette période de l’année il y a un an ?|
|builtin.intent.weather.compare_weather|donnez-moi une comparaison des hautes et basses températures de dallas et houston<br/>comment la météo est-elle par rapport à slc et nyc|
|builtin.intent.weather.get_frequent_locations|donnez-moi mon emplacement le plus fréquent<br/>afficher les arrêts les plus fréquents|
|builtin.intent.weather.get_weather_advisory|avertissements météo<br/>afficher les conseils météo pour sacramento|
|builtin.intent.weather.get_weather_maps|afficher une carte météo<br/>montrez-moi les cartes météorologiques de l’afrique|
|builtin.intent.weather.question_weather|y aura-t-il de la brume demain matin ?<br/>aurai-je besoin d’une pelle pour déblayer la neige ce week-end ?|
|builtin.intent.weather.show_weather_progression|afficher le radar météo local<br/>démarrer le radar|
|builtin.intent.none|quel âge avez-vous<br/>ouvrir l’appareil photo|

## <a name="prebuilt-entities"></a>Entités prédéfinies 

Voici quelques exemples d’entités que l’application d’assistant personnel prédéfinie peut identifier :

|Entité |Exemple d’entité dans l’énoncé |
|-------|------------------|
|builtin.alarm.alarm_state | régler mon alarme de réveil sur `off`    <br/> mon alarme de réveil est-elle sur `on`  | 
|builtin.alarm.duration |répéter dans `10 minutes`    <br/> répéter l’alarme dans `5 minutes`  |
|builtin.alarm.start_date | définir une alarme pour `monday` à 7h00   <br/> définir une alarme pour `tomorrow` à midi   |
|builtin.alarm.start_time | créer une alarme pour `30 minutes`    <br/> régler l’alarme pour qu’elle se déclenche `in 20 minutes`   |
|builtin.alarm.title | mon alarme `wake up` est-elle activée <br/> pouvez-vous définir une alarme pour 11h45 du lundi au vendredi, nommée `take antibiotics` |
|builtin.calendar.absolute_location | créer un rendez-vous pour demain à `123 main street`   <br/> la réunion aura lieu à `cincinnati` le 5 juin    |
|builtin.calendar.contact_name|placer une réunion marketing sur mon calendrier et vérifier que `joe` y est <br/>je souhaite planifier un déjeuner à il fornaio et inviter `paul` |   
|builtin.calendar.destination_calendar|ajouter cela à mon planning `work`   <br/>mettre cela sur mon calendrier `personal` |
|builtin.calendar.duration| fixer un rendez-vous pour `an hour` à 18h00 ce soir <br/>  planifier une réunion de `2 hour` avec joe |  
|builtin.calendar.end_date | créer une entrée de calendrier nommée vacances de demain jusqu’à `next monday` <br/>    me marquer comme étant indisponible jusqu’à `monday, october 5th` | 
|builtin.calendar.end_time | la réunion se termine à `5:30 PM` <br/> la planifier de 11h00 à `noon`  |   
|builtin.calendar.implicit_location | annuler le rendez-vous à dmv <br/> changer l’emplacement de l’anniversaire de miles à `poppy restaurant` |    
|builtin.calendar.move_earlier_time | avancer la réunion de `an hour` <br/> avancer le rendez-vous du dentiste de `30 minutes`       |
|builtin.calendar.move_later_time | déplacer mon rendez-vous chez le dentiste de `30 minutes`    <br/> repousser la réunion de `an hour` |  
|builtin.calendar.original_start_date | replanifier mon rendez-vous chez le coiffeur de mardi à mercredi <br/> déplacer ma réunion avec ken de `monday` à mardi |
|builtin.calendar.original_start_time | replanifier ma réunion de `2:00` à 15h00  <br/> changer mon rendez-vous chez le dentiste de `3:30` à 16h00 |  
|builtin.calendar.start_date | à quelle heure ma soirée commence-t-elle `flag day` <br/> planifier le déjeuner pour `friday after next` à midi 
|builtin.calendar.start_time | je souhaite le planifier pour `this morning` <br/> je souhaite planifier le `morning` |  
|builtin.calendar.title | `vet appointment`  <br/> `dentist` Mardi |
|builtin.communication.audio_device_type | effectuer l’appel à l’aide de `bluetooth`  <br/> appeler à l’aide de mon `headset` | 
|builtin.communication.contact_name | envoyer un message texte à `bob jones`  <br/> | appeler `sarah`|
|builtin.communication.destination_platform | appeler dave à `london` <br/> appeler son `work line` |    
|builtin.communication.from_relationship_name | afficher les appels de ma `daughter` <br/> lire le courrier électronique de `mom`   |   
|builtin.communication.key | composer `star` <br/>  appuyer sur la touche `hash`    |
|builtin.communication.message | envoyer un e-mail à carly pour dire `i'm running late` <br/> envoyer à angus smith le message texte `good luck on your exam` |    
|builtin.communication.message_category | nouvel e-mail marqué pour `follow up`  <br/> nouvel e-mail marqué `high priority` |    
|builtin.communication.message_type | envoyer un `email`   <br/> lire mes messages `text` à haute voix |
|builtin.communication.phone_number | je veux composer le `1-800-328-9459` <br/>     appeler le `555-555-5555` |   
|builtin.communication.relationship_name | envoyer un message texte à mon `husband` <br/>  envoyer un e-mail à `family`| 
|builtin.communication.slot_attribute | changer le `recipient` <br/>    changer le `text` | 
|builtin.comminication.source_platform | l’appeler avec `skype` <br/> l’appeler à partir de ma `personal line` |
|builtin.mystuff.attachment| avec des documents `attached` <br/> rechercher l’`attachment` envoyé par bob |
|builtin.mystuff.contact_name| rechercher la feuille de calcul envoyée par Lisa à `me` <br/> où est le document que j’ai envoyé à `susan` hier soir |
|builtin.mystuff.data_source | `c:\dev\` <br/> mon `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | rechercher le `document` sur lequel j’ai travaillé hier soir <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> afficher le `visio diagram` de mike  |
|builtin.mystuff.end_date| montrez-moi les documents sur lesquels j’ai travaillé `between yesterday and today`   <br/> rechercher le document sur lequel je travaillais `before thursday the 31st` |
|builtin.mystuff.end_time| rechercher les fichiers que j’ai enregistrés `before noon` <br/> rechercher le document sur lequel je travaillais `before 4pm`|      
|builtin.mystuff.file_action| ouvrir la feuille de calcul que j’ai `saved` hier <br/> rechercher la feuille de calcul que kevin a `created`| 
|builtin.mystuff.from_contact_name | rechercher la proposition que `jason` m’a envoyée <br/> ouvrir le dernier e-mail de `isaac` |
|builtin.mystuff.keyword | montrez-moi les fichiers `french conjugation` <br/> rechercher le brouillon de `marketing plan` que j’ai élaboré hier |
|builtin.mystuff.location | le document que j’ai modifié à `work` <br/> les photos que j’ai prises à `paris` |
|builtin.mystuff.message_category | rechercher mes `new` e-mails <br/> rechercher mes `high priority` e-mails |
|builtin.mystuff.message_type | vérifier mes `email` <br/>  montrez-moi mes messages `text`  |
|builtin.mystuff.source_platform | rechercher dans mon courrier `hotmail` les e-mails de john    <br/> rechercher le document que j’ai envoyé à partir de `work` |
|builtin.mystuff.start_date | rechercher les notes à partir de `january` <br/> rechercher les e-mails que j’ai envoyés à bob `after january 1st` |
|builtin.mystuff.start_time | rechercher l’e-mail que j’ai envoyé à bob avant 14h00 mais `after noon` ? <br/> rechercher la feuille de calcul que Kristin m’a envoyée et que j’ai modifié `last night` | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | `download` le fichier que john m’a envoyé <br/> `open` mon document de recommandations d’annotations |
|builtin.note.note_text | créer une liste de produits d’épicerie, y compris `pork chops, applesauce and milk` <br/> créer une note pour `buy milk` |
|builtin.note.title | créer une note nommée `grocery list` <br/> créer une note nommée `people to call` |
|builtin.ondevice.music_artist_name | lire tout de `rufus wainwright` <br/> lire la musique de `garth brooks` |   
|builtin.ondevice.music_genre | afficher les morceaux de `classic rock` <br/> lire ma musique `classical` de la période baroque |
|builtin.ondevice.music_playlist | lire de manière aléatoire tous les morceaux de britney spears de la playlist `workout` <br/> lire la playlist `breakup`
|builtin.ondevice.music_song_name | lire `summertime` <br/> lire `me and bobby mcgee` | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | menez-moi à l’intersection de `5th and pike` <br/> non, je veux savoir comment aller à `1 microsoft way redmond wa 98052` |
|builtin.places.atmosphere | rechercher des lieux `interesting` où sortir    <br/> où puis-je trouver un restaurant `casual` |  
|builtin.places.audio_device_type |appeler le bureau de poste avec `hands free` <br/> appeler john avec `speakerphone` |    
|builtin.places.avoid_route | éviter la `toll road` <br/> menez-moi à san francisco en évitant la `construction on 101` |  
|builtin.places.cuisine | épicerie `halal` près de mountain view   <br/> restaurants haut de gamme `kosher` sur la péninsule |
|builtin.places.date | effectuer une réservation pour `next friday the 12th` <br/>  mashiko est-il ouvert les `mondays` ? |
|builtin.places.discount_type | rechercher un `coupon` pour les magasins macy <br/> trouvez-moi un `coupon` |
|builtin.places.distance | y a-t-il un bon restaurant `within 5 miles` d’ici <br/> rechercher de bons restaurants `within 15 miles` |   
|builtin.places.from_absolute_location | instructions pour aller de `45 elm street` à mon domicile <br/> donnez-moi des instructions pour aller de `san francisco` à palo alto  |
|builtin.places.from_place_name | en voiture de `post office` à 56 center street <br/> donnez-moi des instructions pour aller de `home depot` à lowes |
|builtin.places.from_place_type | instructions pour aller au centre ville à partir de `work` <br/>  donnez-moi des instructions pour aller de `drug store` à mon domicile |
|builtin.places.meal_type | endroits proches pour `dinner` <br/> rechercher un bon endroit pour un `lunch` d’affaires. | 
|builtin.places.nearby | montrez-moi des magasins intéressants `near` moi  <br/> y a-t-il de bons restaurants libanais `around` ici ? |
|builtin.places.open_status | quand le centre commercial est-il `closed` <br/> donnez-moi les heures `opening` du magasin | 
|builtin.places.place_name | menez-moi à `central park` <br/> rechercher `eiffel tower` |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | afficher les instructions pour l’itinéraire `shortest` <br/> prendre l’itinéraire `fastest` | 
|builtin.places.price_range| m’indiquer des emplacements qui sont `moderately affordable` <br/>  j’en voudrais un `expensive`   |
|builtin.places.product | où puis-je obtenir `fresh fish` par ici  <br/> où peut-on trouver `bare minerals` par ici |
|builtin.places.public_transportation_route | horaires pour le bus `m2` <br/> ligne de bus `3x` |  
|builtin.places.rating | afficher les restaurants `3 star` <br/> afficher les résultats qui sont `3 stars or higher`  |
|builtin.places.reservation_number | réserver une table pour `seven` personnes <br/>  effectuer une réservation pour `two` à il fornaio |
|builtin.places.results_number | montrez-moi les `10` cafés les plus proches <br/> montrez-moi les `3` meilleurs aquariums  
|builtin.places.service_provided | où puis-je aller pour `whale watch` par bus ? <br/> j’ai besoin d’un mécanicien pour `fix my brakes` |    
|builtin.places.time | je souhaite voir les lieux qui sont ouverts le samedi à `8 am`| mashiko est-il ouvert `now` |
|builtin.places.transportation_company | horaires des trains pour `new jersey transit` <br/> puis-je y aller avec la compagnie `bart` | 
|builtin.places.transportation_type | y a-t-il un magasin de musique où je puisse aller `on foot` ? <br/>  montrez-moi comment aller à mashiko en `biking`|
|builtin.places.travel_time | je souhaite pouvoir conduire `less than 15 minutes` <br/>   je voudrais un endroit où je puisse aller en `under 15 minutes` |   
|builtin.reminder.absolute_location | rappelez-moi de téléphoner à mon père quand j’atterrirai à `chicago` <br/> quand je reviendrai à `seattle`, rappelez-moi d’acheter du gaz |
|builtin.reminder.contact_name | quand `bob` appellera, rappelez-moi de lui raconter la blague <br/> créer un rappel pour mentionner le bus scolaire quand je discuterai avec `arthur` |
|builtin.reminder.leaving_absolute_location | rappel pour récupérer craig quand je quitterai `1200 main` |
|builtin.reminder.leaving_implicit_location | rappelez-moi d’acheter du gaz quand je quitterai `work`  |
|builtin.reminder.original_start_date | changer le rappel concernant la pelouse de `saturday` à dimanche <br/> déplacer le rappel concernant l’école de `monday` à mardi   |
|builtin.reminder.relationship_name | Quand mon `husband` téléphonera, rappelez-moi de lui parler de la réunion pta <br/> afficher un rappel quand `mom` téléphonera|
|builtin.reminder.reminder_text | pouvez-vous me rappeler de `bring up my small spot of patchy skin` quand le dr smith téléphonera  <br/> rappelez-moi de `pick up dry cleaning` à 16:40   |
|builtin.reminder.start_date | afficher un rappel `thursday after next` à 20h00  <br/> afficher un rappel `next thursday the 18th` à 20h00    |
|builtin.reminder.start_time | créer un rappel `in 30 minutes` <br/> créer un rappel pour arroser les plantes `this evening at 7` |  
|builtin.weather.absolute_location | pleuvra-t-il à `boston` <br/> quelles sont les prévisions pour `seattle` ?  |
|builtin.weather.date_range | météo de nyc `this weekend` <br/>   rechercher les prévisions à `five day` à hollywood en floride |
|builtin.weather.suitable_for | puis-je aller `hiking` en short ce week-end ?   <br/> fera-t-il assez beau pour `walk` jusqu’au match aujourd’hui ? | 
|builtin.weather.temperature_unit | quelle est la température aujourd’hui en `kelvin`   <br/> montrez-moi les températures en `celsius` |  
|builtin.weather.time_range | va-t-il neiger `tonight` ? <br/> y a-t-il du vent `now`?  |
|builtin.weather.weather_condition | montrer `precipitation` <br/> quelle est l’épaisseur de la `snow` à lake tahoe maintenant ?  |

