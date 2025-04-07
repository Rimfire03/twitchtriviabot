# Twitch Trivia Bot [fr]

Le Twitch Trivia Bot (TTB) est un script Python conçu pour organiser une session de quiz dans le chat Twitch. Le bot charge un ensemble de questions, anime une session de quiz, désigne un gagnant et enregistre les scores de manière dynamique. 

![img](https://i.imgur.com/YYznPaN.png)

# Fonctionnalités :
+ Charge des fichiers CSV contenant des questions et réponses de quiz, et gère n'importe quelle quantité de questions (limitée uniquement par les contraintes des fichiers CSV)
+ Sélectionne dynamiquement un ensemble de questions aléatoires dans la réserve de questions.
+ Pose automatiquement des questions, génère des indices dynamiques et passe les questions en fonction de délais configurables
+ Mode BONUS, permettant d'attribuer des points supplémentaires.
+ Enregistre les performances des participants aux quiz sur plusieurs sessions, permettant à ces derniers de consulter leurs scores à tout moment.
+ Les sessions de quiz interrompues par une déconnexion du bot peuvent être rechargées grâce aux sauvegardes créées en temps réel.

# Configuration :
La dernière version de Python 3 est nécessaire pour ce bot. Vous devrez installer Python 3 afin de pouvoir l'exécuter. Vous pouvez soit utiliser la distribution standard de Python 3 avec IDLE, soit opter pour quelque chose comme la distribution Anaconda avec Spyder (ce que j'utilise).

Vous devrez probablement installer quelques dépendances, notamment  et . Vous pouvez les installer via la ligne de commande avec  ou  (par exemple, "pip install PyYaml"). Lors des premières exécutions, si le programme ou l'interface graphique (GUI) ne se charge pas, assurez-vous de vérifier  pour lire les codes d'erreur et identifier ce qui doit être installé.

Veuillez utiliser la Dernière Release pour l'installation initiale. 

+ Le fichier “twitchtriviabot.py” n'a pas besoin d'être reconfiguré. Il sera chargé via la console Python et se connectera au chat Twitch.
Un fichier de questions de quiz (CSV) doit être configuré (plus de détails ci-dessous). Par défaut, ce package inclut « triviaset.csv », avec les colonnes appropriées pré-identifiées
+ Les fichiers « auth_config.yml » et « trivia_config.yml » doivent être configurés. Toutes les entrées doivent être sans crochets ni signes dièse. Ouvrez ces fichiers dans un éditeur de texte.
  + Dans « trivia_config.yml », les deux premiers champs, filename et filetype, doivent correspondre au fichier de questions de quiz (par défaut, « triviaset.csv »). Tous les champs numériques représentent des durées en secondes.
    + question_count = # représente le nombre de questions à inclure dans la session de quiz. Ces questions seront sélectionnées aléatoirement à partir de la liste des questions du fichier CSV. Pour être clair, ce chiffre ne correspond pas au nombre total de questions dans votre ensemble de quiz, mais au nombre de questions que vous souhaitez avoir dans une session de quiz.
    + hint_time1 / hint_time2 = Délai avant que les indices 1 et 2 soient fournis. Les indices sont décrits ci-dessous.
    + skip_time = Délai avant que la question soit sautée. Ce délai est également utilisé en mode 'poll' pour déterminer combien de temps attendre avant de lancer le sondage.
    + question_delay = Délai après que la question a été répondue, avant que la question suivante soit posée.
    + question_bonusvalue = Valeur attribuée aux questions des manches BONUS. La manche BONUS est décrite ci-dessous.
    + mode = Il y a trois paramètres :
        + `single` - Quiz standard : le premier à répondre gagne le point.
        + `poll` - Dans ce mode, pendant toute la durée du skip_time, les questions sont ouvertes aux réponses des joueurs. À la fin de chaque question, les premier et deuxième joueurs à répondre gagnent plus de points, tandis que tous les autres reçoivent un nombre inférieur de points.
        + `poll2` - C'est similaire au mode précédent, mais chaque question dispose d'un pool de réponses distinct pour "answer" et "answer2". Cela signifie, par exemple, qu'une question peut avoir deux réponses distinctes, et les joueurs peuvent marquer des points séparément pour chaque réponse.
    + music_mode = `true` or `false`. Le mode Musique est un mode contrôlé manuellement où l'administrateur peut démarrer et arrêter les questions via les boutons "Start Q" et "End Q" sur l'interface graphique (GUI). Au lieu de charger le fichier CSV d'ensemble de quiz, chaque question est chargée depuis les fichiers  `/config/music/artist.txt` et `track.txt`. Les questions ne commencent pas automatiquement après le délai prévu pour y répondre ; à la place, l'administrateur choisira manuellement quand la prochaine chanson est prête à être jouée. 
        + Fonctionnellement, l'administrateur préparerait ces fichiers artiste/titre, commencerait à jouer la chanson pour son audience, puis appuierait sur "Start Q" lorsque tout est prêt. Les réponses arrivent et, une fois que la question est notée, le système attend que l'administrateur choisisse de lancer la chanson suivante.
        + Dans le fichier de configuration, les paramètres mode `poll2` et durée `infinite` doivent être sélectionnés pour que music_mode soit défini sur `true`
    + order = `random` ou `ordered`. Cela permet de répondre aux questions soit dans l'ordre de l'ensemble de quiz d'origine, soit de manière aléatoire. Si `ordered` est choisi, il est conseillé de définir `question_count` égal au nombre de questions dans l'ensemble de quiz, ce qui permettra de présenter toutes les questions dans l'ordre. 
    + category_distribution = `random` ou `distributed`. Une fois le fichier CSV complet du quiz chargé, cela déterminera comment réduire l'ensemble complet au nombre de questions défini par `question_count` . `random` sélectionnera les questions de manière aléatoire, sans tenir compte des catégories. `distributed` tentera de répartir les catégories de questions de manière semi-équilibrée, en utilisant une méthode d'échantillonnage pour réduire l'ensemble complet, puis en vérifiant les moyennes par catégorie afin de garantir une session finale de quiz raisonnablement distribuée.
    + length = `finite` ou `infinite`. Lors de l'utilisation de `infinite`, les questions continuent d'être posées indéfiniment jusqu'à ce que l'administrateur décide de terminer la session de quiz. Les questions ne sont pas réorganisées ; elles seront posées continuellement dans le même ordre. En revanche, `finite` met fin à la session de quiz après avoir atteint le nombre de questions défini par `question_count`.
    + admins - séparé par des virgules
        + E.g., admins = player
        + E.g., admins = player,respondent
  + “auth_config.yml” :
    + host = default irc.twitch.tv
    + port = default 6667 (for twitch)
    + nick = nom d'utilisateur pour le bot
    + pass = Mot de passe au format “oath:xxxxxx...”. À récupérer sur twitchapps.com/tmi pour le bot.
    + chan = Chaîne Twitch à laquelle connecter le bot, où le quiz aura lieu. Cela a changé dans la version 2, où aucun signe dièse (#) n'est nécessaire (par exemple, cleartonic). Cela doit correspondre exactement au nom de la chaîne avec les mêmes majuscules.
    + encoding = either `utf-8` ou `ISO-8859-1`.  Un choix quelque peu expérimental, par défaut à `utf-8` plus de sécurité.

Pour configurer correctement le fichier triviaset.csv, prenez en compte les éléments suivants :
5 en-têtes sont spécifiés dans cette version : ‘category’, ‘question’, ‘answer’, ‘answer2’, ‘creator’. Conservez cet ordre, mais votre ensemble de quiz n'a pas besoin de conserver l'en-tête des colonnes.
Remplissez ligne par ligne chaque question en renseignant le sujet/jeu, et au moins une colonne Answer. Les champs Creator et Answer2 ne sont pas obligatoires.

Un journal sera enregistré à chaque exécution dans . Celui-ci s'accumulera au fil du temps et peut être utile pour résoudre des problèmes. À tout moment, vous pouvez supprimer le fichier journal s'il devient trop volumineux 

# Lancer le bot

Avec une ligne de commande ou un terminal ouvert :

`python twitchtriviabot.py` ou `python3 twitchtriviabot.py`

À partir de là, vous pouvez surveiller l'activité du bot. Toutes les réponses des utilisateurs dans la chaîne Twitch apparaîtront dans la console. La réponse de chaque question apparaîtra également dans la console. 

Si vous devez fermer l'interface graphique et arrêter le programme, maintenir généralement Ctrl+C arrêtera le programme dans la console/terminal.

# Fonctionnalités diverses 

### Indices et durée des questions 

Tous les minutages peuvent être ajustés manuellement dans le fichier trivia_config.yml. Par défaut, après qu'une question a été posée, des indices générés automatiquement (spécifiés dans trivia_config.yml) seront activés. Ensuite, la question sera ignorée. Une fois la limite de questions définie pour la session atteinte, le jeu se terminera et un gagnant sera désigné. 
+ Le premier indice remplace 2 caractères sur 3 dans la réponse par des "_".
+ Le deuxième indice remplace toutes les voyelles par des "_".

### BONUS

Le bonus peut être activé par un administrateur via la commande !bonus. Le mode bonus augmente la valeur des questions, par défaut à 3 points (modifiable dans trivia_config.yml). Il peut être activé ou désactivé à tout moment 

# Commands
L'interface graphique (GUI) et les commandes ci-dessous produiront les mêmes résultats. Certaines commandes ne sont disponibles que lors des sessions de quiz actives.

#### Admin only:
+ !triviastart - Démarre un nouveau quiz avec les conditions définies dans le fichier ‘trivia_config.yml’
+ !triviaend - Met fin à la session de quiz et désigne un gagnant 
+ !next/!skip - Passe à la question suivante. Toutes les questions expirent automatiquement après le délai défini dans le fichier ‘trivia_config.yml’, mais cette commande permet de passer manuellement à la suivante.
+ !bonus - Active ou désactive le mode bonus pour les questions
+ !stopbot - Interrompt la connexion du bot. 

#### All users:
+ !score - Affiche le score de l'utilisateur (inclut le score de la session, le score total pour tous les quiz et le nombre total de victoires dans tous les quiz).

# Credits
Created by [@cleartonic](https://twitter.com/cleartonic)

Thanks to cormac-obrien’s publicly available instructions to navigate Twitch's IRC API via Python & socket, which was used in various forms within this release (http://www.instructables.com/id/Twitchtv-Moderator-Bot/)
