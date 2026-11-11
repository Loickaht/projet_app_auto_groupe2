Séance : 01
Nom : ZOGO James
Porteur : P4

Étapes assignées :
1.7 : trous
1.11 : cardinalité
1.12 : cibles

CONTEXTE GENERAL DU SUJET :

Une banque a 200 000 clients. Pour 160 000 d’entre eux (train.csv), elle sait si le client a fait une transaction ou non. Pour 40 000 autres (a_decider.csv), elle ne le sait pas. Le projet consiste à deviner, pour ces 40 000 clients, lesquels feront une transaction.

On devine en étudiant les 160 000 cas connus. Un modèle est un programme qui cherche des régularités du type « les clients qui ont cette caractéristique transactent plus souvent ». Il applique ensuite ces régularités aux clients inconnus. Chaque client est décrit par 200 nombres, var_0 à var_199, dont on ignore la signification.
Pour la réalisation de l'étape 1.7, il s'agira de compter les lignes strictement identiques en décidant de si on les garde ou si on les retire. Pour cela, on devra afficher le nombre de ces lignes, la décision et la raison de cette décision. 

RAPPEL :

Variable (ou feature) --> Une colonne du tableau, une information sur le client.
Cible (target) --> Ce qu’on veut prédire : 1 = a fait une transaction, 0 = non.
Entraînement --> Le moment où le modèle étudie les cas connus.
Classification binaire --> Prédire une réponse à deux possibilités (oui/non).
Positif --> Un client pour qui la réponse est « oui » (1). Ici, 10 % des clients.

LES 12 PROCESS UNE PAR UNE

On nous a confié un énorme classeur de fiches clients. Avant de l’utiliser, nous le vérifiond de fond en comble. C’est ce que font ces étapes.

1. Inventorier les tables

Quoi : lister les fichiers dont on dispose.
Pourquoi : savoir ce qu’on a avant de commencer.
Trouvé : deux fichiers, train.csv (les clients dont on connaît la réponse) et a_decider.csv (ceux à deviner). 

2. Charger et compter

Quoi : ouvrir les fichiers et compter lignes et colonnes.
Pourquoi : vérifier que rien n’est tronqué.
Trouvé : train fait 160 000 lignes sur 202 colonnes (identifiant + cible + 200 variables), a_decider fait 40 000 lignes sur 201 colonnes. Il lui manque seulement target, ce qui est normal puisque c’est ce qu’on doit deviner.

3. Typer

Quoi : vérifier la nature de chaque colonne (nombre à virgule, entier, texte).
Pourquoi : un ordinateur ne traite pas un texte comme un nombre. Une colonne de texte cachée parmi des nombres ferait planter le calcul.
Trouvé : les 200 variables sont des nombres décimaux, l’identifiant est du texte, la cible est 0 ou 1. Rien d’inattendu.

4. Déterminer les cardinalités

Quoi : compter, pour chaque colonne, le nombre de valeurs différentes.
Pourquoi : cela révèle la nature de la colonne. Une colonne « sexe » a 2 valeurs. Une colonne « montant exact » en a presque autant que de lignes.
Trouvé : les variables ont entre 447 et 140 250 valeurs différentes. Ce sont donc de vraies mesures continues, pas des catégories déguisées. Seule var_68, avec 447 valeurs, est nettement plus « grossière » que les autres.

5. Déterminer les valeurs possibles

Quoi : regarder les minimums et maximums, et les valeurs de la cible.
Pourquoi : repérer des valeurs absurdes (âge de −500) ou des erreurs de saisie.
Trouvé : la cible ne prend que 0 ou 1. Les variables vont de −90 à +74, avec 131 variables qui prennent des valeurs négatives. Cela montre que ce ne sont pas des montants mais des valeurs centrées autour de zéro. L’énoncé annonçait −73,8 à +65,4 et 124 variables négatives. Notez l’écart dans votre journal.

6. Déterminer les trous

Quoi : compter les cases vides.
Pourquoi : un modèle ne sait pas gérer une case vide. Il faut décider quoi en faire (la remplir, supprimer la ligne…).
Trouvé : zéro case vide. C’est le cas simple.

7. Déterminer les doublons

Quoi : chercher les lignes ou identifiants qui apparaissent deux fois.
Pourquoi : si un même client est dans l’entraînement et dans le test, le modèle a déjà vu la réponse. Son score serait trop beau et trompeur (voir « fuite » plus bas).
Trouvé : aucun doublon, et aucun client commun aux deux fichiers. C’est propre.

8. Déterminer les fuites

C’est la notion la plus importante du projet.
Une fuite est une information qui n’existerait pas au moment de la vraie décision, mais qui s’est glissée dans les données d’entraînement.
Analogie : vous préparez un examen avec les réponses imprimées au dos de la feuille. Vous aurez 20/20, mais vous n’aurez rien appris. En vrai, sans les réponses, vous échouerez.
Ce qu’on a vérifié :

la cible n’est pas dans a_decider ;
l’identifiant ne donne aucun indice sur la réponse ;
l’ordre des lignes ne donne aucun indice ;
aucune variable ne prédit « trop bien » à elle seule ;
train et a_decider se ressemblent : un modèle chargé de les distinguer n’y arrive pas mieux qu’un tirage à pile ou face.
Trouvé : aucune fuite détectée.
9. Déterminer la cible

Quoi : regarder la répartition des 0 et des 1.
Trouvé : 16 078 clients sur 160 000 (10 %) ont transacté. C’est le déséquilibre. Un programme qui répondrait toujours « non » aurait raison dans 90 % des cas, tout en étant parfaitement inutile puisqu’il ne trouve jamais personne. C’est pourquoi on n’utilise pas le simple pourcentage de bonnes réponses (l’exactitude).

10. Déterminer le plancher

Quoi : mesurer ce que donne une méthode très simple, sans modèle sophistiqué.
Pourquoi : c’est le niveau à battre. Annoncer « mon modèle a 0,85 » ne veut rien dire tant qu’on ignore ce que donne une méthode naïve. Le plancher sert de point de comparaison.
Trouvé : les règles naïves donnent entre 0,50 (hasard) et 0,57 (meilleure variable seule). Votre plancher est donc environ 0,57, proche des 0,5639 de l’énoncé.

11. Déterminer la métrique

Quoi : choisir la mesure qui note votre travail.
Ici, c’est l’AUC.
Explication simple : prenez au hasard un client qui a transacté et un qui n’a pas transacté. Le modèle donne à chacun un score. L’AUC est la probabilité que le client qui a transacté ait reçu le score le plus haut.

0,5 : le modèle ne fait pas mieux que le hasard.
1,0 : le modèle classe parfaitement.
Entre les deux : plus c’est haut, mieux c’est.
Pourquoi c’est adapté : l’AUC juge la qualité du classement et reste valable même quand les données sont déséquilibrées 10/90.
12. Déterminer le coût des erreurs

Il existe deux façons de se tromper :

Vente manquée : le client aurait transacté, mais on ne l’a pas sollicité.
Sollicitation inutile : on a contacté un client qui n’aurait rien fait.
Ces deux erreurs ne coûtent pas pareil. Rater une vente fait perdre de l’argent, alors que contacter quelqu’un pour rien coûte un appel ou un SMS.
L’énoncé ne donne pas les montants. J’ai posé une hypothèse (10 pour une vente manquée, 1 pour une sollicitation inutile), à remplacer par les vrais chiffres. Ils déterminent à partir de quel score on décide de contacter un client : le seuil.
