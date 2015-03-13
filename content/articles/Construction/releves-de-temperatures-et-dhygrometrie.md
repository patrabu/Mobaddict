Title: Relevés de températures et d'hygrométrie
Date: 2011-03-25 16:17:16
Author: Patrick
Category: Construction
Tags: Projet

Afin de partager le comportement de notre maison, j'ai décidé
d'enregistrer la température au RDC et à l'étage.

Deux appareils Voltcraft DL-120TH USB ont été utilisés. Ces appareils
permettent d'enregistrer jusqu'à 16000 couples de valeurs (température
et hygrométrie relative).

Un de ces enregistreurs a été placé au rez de chaussée à environ 3
mètres du poêle. L'autre a été placé dans une chambre à l'étage qui
était inoccupée ; elle a servi de lieu de stockage de matériaux.

Les appareils ont été programmé pour enregistrer un couple de valeurs
toutes les 30 minutes, donc 48 mesures par jour. Cela permet de tenir
environ 330 jours (16000 / 48).

Format des données
------------------

Pour récupérer les données sur Linux j'ai utilisé un script issu du
projet suivant : [vdl120][]

Les données sont récupérés dans le format suivant :

    # [2010-11-17 22:23:03] 5568 points @ 1800 sec1290032583 19.6 60.1

La première ligne donne la date de début d'enregistrement, le nombre de
couples valeurs enregistrées et la fréquence d'enregistrement (1800
secondes soit une 1/2 heure).

La seconde ligne est un relevé avec trois champs séparés par un espace :

1.  la date au format UNIX (nombre de secondes depuis le 1er janvier
    1970)
2.  la température en °C
3.  le pourcentage d'humidité relative

Graphiques
----------

Pour les graphiques, la résolution n'est que de 3 heures. Ils sont
générés avec [gnuplot][] .

Voici le script utilisé (adapté du script fourni avec le projet vdl120)
:

    # Création d'un fichier PNGdatafile = filename.".dat"plotfile = filename.".png"

    set terminal png nocrop enhanced font arial 10 size 600,400set output plotfileset xdata timeset timefmt "%s"set format x "%d/%m"# set xtics nomirror rotate by -60set gridplot datafile every 6 using 1:2 with line lt 1 title "Temp / °C", \     datafile every 6 using 1:3 with line lt 2 title "RLF / %"

Ceci permet de produire une image au format PNG de 600 par 400 pixels
avec les deux courbes sur le même graphique.

Évolutions
----------

J'ai aussi une station météo qui permet d'enregistrer les mêmes données
avec un capteur extérieur. Il reste à la relier à un ordinateur puis à
compiler les données fournies.

Ceci permettra de mettre en parallèle l'évolution de la température
extérieure et la température intérieure.

J'ai aussi commencé à relevé les valeurs des capteurs du CESI. Cela
donnera lieu à d'autres articles.

  [vdl120]: http://vdl120.sourceforge.net/
    "Projet VDL120 sur Sourceforge.net"
  [gnuplot]: http://gnuplot.sourceforge.net/ "Projet Gnuplot"
