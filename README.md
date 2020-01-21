# Laboratoire_1_Algo_Compression
École de technologie supérieure LOG320 – Structures de données et algorithmes
Laboratoire #1
Algorithmes de compression
Patrick Cardinal Durée : 4 semaines
Introduction
Il y a de plus de données disponibles et plusieurs de ces types de données occupent beaucoup d’espace (ou doivent
utiliser beaucoup de bande passante). La vidéo est un exemple commun de données qui utilise beaucoup de bande
passante. Les fichiers vidéo sont généralement compressés avant d’être transférés ou sauvegardés. Les algorithmes
de transmission de données sont donc de plus en plus importants. Il existe deux types compression : avec et sans
perte. Les algorithmes sans perte permettent de décompresser le fichier et de retrouver exactement le fichier original.
Dans les compressions avec perte, ce n’est pas le cas. Le fichier décompressé peut être légèrement différent. C’est
le cas pour la compréhension vidéo pour laquelle la perte de qualité produite par la décompression peut ne pas être
visible à l’œil. Dans le cas de ce laboratoire, nous allons nous concentrer sur deux algorithmes sans perte : LZW et
Huffman.
Les objectifs de ce laboratoire sont :
• Comprendre les algorithmes proposés et les implémenter
• Implémenter efficacement les algorithmes
• Proposer des optimisations
• Analyser asymptotiquement l’algorithme
• Être en mesure de justifier les choix de conception
Algorithme LZW
L’algorithme LZW est algorithme de compression de type dictionnaire. Cet algorithme a été publié en 1984 par
Welch mais il est basé sur l’algorithme LZ77 publié 1977 par Ziv et Lempel. Le principe de l’algorithme de
remplacer des séquences de symboles par un nouveau symbole de taille fixe.
Algorithme de Compression
Comme mentionné auparavant, l’algorithme construit un dictionnaire qui contient des séquences des symboles qui
ont déjà été vus depuis le début du processus de compression. Au départ, le tableau est initialisé avec tous les
symboles uniques (un seul symbole). Dans notre cas, nous allons utiliser des symboles de 8 bits, ce qui veut dire que
le dictionnaire sera initialisé avec 256 éléments (les codes allant de 0 à 255). Il faut ensuite déterminer la taille
maximale du dictionnaire qui déterminera aussi la taille des nouveaux codes dans le fichier compressé. Par exemple,
si on choisit des codes de 9 bits, la taille du dictionnaire sera 512, donc il restera de la place que pour 256 nouveaux
symboles. Par contre, si on choisit des codes de 16 bits, nous allons avoir énormément d’espace pour les nouveaux
codes. Par contre, comme les nouveaux codes seront deux fois plus longs que les originaux, le taux de compression
pourrait être affecté. Sur certains types de fichiers, le fichier compressé pourrait être plus gros que l’original.
L’algorithme ci-dessous décrit l’algorithme de compression en supposant que le dictionnaire est déjà initialisé avec
les symboles uniques.
CompressionLZW(T[1.N])
1. s = T[1]
2. pour i = 2 à N
3. c = T[i]
4. si s+c Î dict
5. s = s+c
6. sinon
7. sortir le code de s
8. dict = dict+{s+c}
9. s = c
10. sortir le code de s
2
Afin de comprendre l’algorithme, supposons que nous voulons compresser la chaîne de caractère
« ababcbababaaaaaaa ». Afin de simplifier l’explication, on suppose que le dictionnaire est initialisé avec les 3
symboles de la chaîne :
a b c
0 1 2
Dans votre cas, le dictionnaire sera toujours initialisé avec les 256 codes de 8 bits.
Au départ, la variable s contiendra la première variable de la chaîne, donc s= « a ». Dans la boucle, un autre
caractère est lu, dans notre exemple, ce sera c= « b ». À la ligne 4, l’algorithme vérifie si la chaîne « ab » est déjà
dans notre dictionnaire. Lorsque c’est le cas, l’algorithme va essayer de trouver une séquence plus longue qui n’est
pas encore dans le dictionnaire. Dans notre cas, la chaîne n’est pas dans le dictionnaire. La chaîne est donc ajoutée :
a b c ab
0 1 2 3
Le code s est ensuite produit (écrit dans le fichier compressé par exemple) et la variable s est modifiée et prend la
valeur de c (« b » dans notre cas.
À l’itération suivante, la valeur « a » est lue. Comme « ba » n’est pas dans le dictionnaire, la séquence est ajoutée :
a b c ab ba
0 1 2 3 4
La nouvelle valeur de s deviendra « a ». Le prochain caractère « b » est ensuite traité. La chaîne « ab » est dans le
dictionnaire. Dans cette situation, la chaîne s devient « ab » et l’algorithme passe au prochain caractère « c ». La
chaîne « abc » n’est pas dans le dictionnaire. Le code de « ab » est donc écrit dans le fichier compressé et la chaîne
« abc » est ajoutée au dictionnaire :
a b c ab ba abc cb
0 1 2 3 4 5 6
À ce moment, le fichier compressé contient les codes 0,1,3. L’algorithme continu à traiter les éléments un par un. À
la fin de l’exécution, le dictionnaire sera :
a b c ab ba abc cb bab baba aa aaa aaaa
0 1 2 3 4 5 6 7 8 9 10 11
La version compressée de la chaîne sera 0,1,3,2,4,7,0,9,10,0. Dans cet exemple, la chaîne originale contenait 17
symboles et elle a été encodée avec seulement 10 symboles. Le taux de compression va cependant dépendre du
nombre de bits utilisés pour représenter les nouveaux codes (rappelons que la taille des nouveaux codes dépend de la
taille du dictionnaire).
Vous n’avez pas à sauvegarder le dictionnaire. Avec cet algorithme, le dictionnaire va être automatiquement recréé
lors de la décompression.
3
Algorithme de décompression
Lors de la décompression, le dictionnaire doit être initialisé de la même façon que lors de la phase de compression.
La taille du dictionnaire doit aussi être la même sinon la longueur des nouveaux codes sera différente. L’algorithme
ci-dessous décrit le processus de décompression.
Au départ, la chaîne s est vide. Le premier code k lu est « 0 ». L’algorithme vérifie si le code est dans le
dictionnaire. C’est le cas, le code 0 correspond au « a ». Le « a » est donc écrit dans le fichier décompressé. Les
lignes 8 et 9 ne sont pas exécutées puisque la chaîne s est toujours vide. À la fin de l’algorithme, la chaîne de
caractères associée au code k est assignée à s. Donc, après le première itération, s= « a ».
À la seconde itération, le code lu du fichier compressé est « 1 ». Le code « 1 » est dans le dictionnaire et représente
la chaîne « b ». Donc, la chaîne est écrite dans le fichier décompressé qui contient maintenant « ab ». Ensuite, à la
ligne 9, le code « ab » est ajouté au dictionnaire :
a b c ab
0 1 2 3
La chaîne s devient ensuite « b ». Le code suivant est 3. Le code 3 est dans le dictionnaire représente la chaîne
« ab ». Cette chaîne est donc ajoutée au fichier décompressé qui contient maintenant « abab » et la chaîne « ba » sera
ajoutée au dictionnaire :
a b c ab ba
0 1 2 3 4
L’algorithme continue de la même façon avec tous les codes. La seule particularité est lorsque le code lu n’est pas
dans le dictionnaire. Ce phénomène n’arrive pas souvent et se produit parce que l’encodeur est toujours un peu en
avance par rapport au décodeur. Heureusement, la solution est simple. Il suffit de recréer la chaîne en concaténant le
contenu de s et le premier caractère de s. Par exemple, si s = « ab », la séquence à écrire dans le fichier décompressé
sera « aba ». Cette exception est gérée par les lignes 5 et 6 de l’algorithme.
Gestion du dictionnaire
Le nombre de symboles créé lors du processus de compression augmente généralement assez rapidement. Même
avec un gros dictionnaire, un gros fichier à compresser va rapidement remplir le dictionnaire. La stratégie la plus
simple dans ce cas est de simplement réinitialiser le dictionnaire à l’état et initiale. Il existe d’autres stratégies
possibles qui pourraient être intéressantes d’explorer. Il ne faut pas oublier que l’encodeur et le décodeur doivent
partager la même stratégie de gestion du dictionnaire.
DecompressionLZW(T[1.N])
1. s = NULL
2. pour i = 1 à N
3. k = T[i]
4. seq = dict[k]
5. si seq == NULL
6. seq = s + s[0]
7. sortir seq
8. si s != NULL
9. dict = dict+{s+seq[0]}
10. s ← seq
4
Algorithme de Huffman
L’algorithme de Huffman est une méthode statistique de compression de données. Un fichier texte (ou binaire) est
représenté par une série d’octets (séquence de 8 bits). Le principe de l’algorithme est de transformer chacun de ces
octets en une séquence de bits dont la longueur est variable. La longueur de la nouvelle séquence qui représente un
octet spécifique est calculée en fonction de sa fréquence dans le fichier. L’algorithme va faire en sorte que les octets
plus rares vont être codés par une séquence plus longue (et possiblement de plus de 8 bits) tandis que les octets plus
fréquents seront codés par une séquence plus courte et inférieure à 8 bits. Il a été démontré que les gains obtenus par
les octets les plus fréquents surpassent les pertes causées par les codes plus longs des éléments les moins fréquents.
Le résultat net est donc un fichier dont la taille est inférieure à sa taille originale.
Une propriété importante de l’algorithme est qu’aucun code n’est le préfix d’un autre code. Ceci veut dire que si par
exemple un octet est codé par la séquence ’001’, aucun autre code ne va commencer par cette séquence. Cette
propriété assure qu’il n’y aura pas de confusion lors du décodage.
L’algorithme de compression se décompose en trois phases qui sont décrites ci-dessous.
Table de Fréquence
La première phase consiste à lire le fichier à compresser et de construire une table de fréquences. Cette table contient
le nombre d’occurrences de chacun des caractères dans le fichier à compresser. L’algorithme ci-dessous décrit cette
opération.
La table de fréquences doit ensuite être triée selon la fréquence des caractères comme le montre la figure ci-dessous :
Figure 1 : Table de fréquences
Vous remarquerez que les éléments de la table sont en fait des feuilles de l’arbre binaire qui sera créé lors de la
deuxième phase de l’algorithme. Cette technique permettra de faciliter la construction de l’arbre binaire qui est
appelé « arbre de Huffman ».
Construction de l’arbre de Huffman
La seconde phase consiste à créer l’arbre de Huffman (on rappel qui s’agit d’un arbre binaire) en fonction de la table
de fréquences. L’algorithme effectue des réductions successives de la table de fréquences. L’opération de réduction
consiste à extraire de la table les deux éléments ayant les fréquences les plus faibles et à les combiner. La
combinaison amène à la création d’un nouveau noeud de l’arbre dont les enfants seront les deux éléments extraits de
la table. La fréquence associée à ce nouveau nœud se trouve à être la somme des fréquences de ses deux enfants.
1
b
2
a
5
c
CréerTableFréquences(Fichier)
1. T = {}
2. pour tous les octets dans Fichier
3. si c ∈ T
4. T[c] = T[c] + 1
5. sinon
6. T = T ∪ c
7. T[c] = 1
8. retourner T
5
La figure 2 montre comment les deux éléments de la table représentée à la figure 1 ayant les fréquences les plus
faibles sont combinés afin de former un nouvel élément de l’arbre.
Figure 2 : Combinaison de deux éléments de la table de fréquences
Le nouvel élément ainsi créé est replacé dans la table à la position correspondant à la fréquence combinée des deux
nœuds enfants. Il est important que la table demeure toujours triée. L’opération est ainsi répétée jusqu’à ce que la
table ne contienne qu’un seul élément qui sera la racine de notre arbre de Huffman.
La figure 3 montre l’arbre de Huffman créé à partir de la table de fréquence de la figure 1.
Figure 3 : Arbre de Huffman construit à partir de la table de fréquence de la figure 1.
Encoder le fichier source
La troisième phase de la compression consiste à encoder chacun des caractères du fichier source à l’aide des codes
obtenus en traversant l’arbre de Hufman. Vous remarquerez que chacune des branches de l’arbre de la figure 3
correspond à une valeur binaire (0 ou 1). Le code d’un caractère est la suite de bits obtenus en traversant l’arbre à
partir de la racine jusqu’au caractère à encoder. Par exemple, le caractère ‘a’ sera codé « 10 » tandis que le caractère
‘c’ sera codé « 0 ».
1
b
2
a
2
a
1
b
3
∅
2
a
1
b
3
∅
5
c
8
∅
0
0 1
1
6
Compresser le fichier consiste donc à remplacer les octets du fichier source par les codes déterminés à partir de
l’arbre de Huffman. Dans notre petit exemple, les 8 octets (5 ‘c’, 2 ‘a’ et 1 ‘b’) utilisaient 64 bits. La version
encodée utilisera seulement 11 bits.
Il ne faut cependant pas oublier qu’il faut aussi sauvegarder les informations nécessaires pour être en mesure de
décompresser le fichier encodé. Il est clair que dans le cas de petits fichiers, le fichier compressé pourrait être plus
gros que le fichier original! La méthode la plus simple pour conserver les informations nécessaires pour la
décompression est de simplement sauvegarder la table de fréquences dans l’en-tête du fichier compressé.
Décompresser un fichier
La décompression d’un fichier encodé est relativement simple. Premièrement, il faut récupérer les informations
nécessaires à la construction de l’arbre de Huffman. Typiquement, ces informations sont sauvegardées dans l’en-tête
du fichier compressé. Ensuite, il suffit de traverser l’arbre de Huffman en considérant les bits du fichier compressé.
À chaque fois qu’une feuille de l’arbre est atteinte, un caractère a été décodé. On continue ensuite la lecture du
fichier encodé en traversant de nouveau l’arbre à partir de la racine.
Spécification du programme à remettre
Le programme doit être remis sous la forme de fichier « .jar ». Il doit pouvoir être exécuté avec la commande
suivante :
java –jar labo1-equipeXX.jar –[huff|lzw|opt] –[d|c] <fichier d’entrée> <fichier de sortie>
• XX est le numéro de votre équipe (s’il y a un seul chiffre à votre numéro d’équipe, compléter avec un 0.
Par exemple, l’équipe 1 doit mettre « 01 » comme numéro d’équipe).
• -huff pour utiliser l’algorithme de huffman
• -lzw pour utiliser l’algorithme LZW
• -opt pour utiliser une version optimisée de votre programme de compression
• -c est pour compresser le <fichier d’entrée> qui va créer une version compressée sauvegardée dans <fichier
de sortie>
• -d permettra de décompresser le <fichier d’entrée> dont le résultat sera sauvegardé dans <fichier de sortie>
Un exemple de script python qui sera utilisé pour la correction automatique du laboratoire sera disponible sur le site
Moodle.
Vous devez vous assurez :
• que les consignes mentionnées ci-dessus sont scrupuleusement suivies ;
• qu’il n’y a pas de chemin (path) ou nom de fichier « hardcoder » dans votre programme.
Le non-respect des consignes mène à des pénalités jusqu’à la perte de tous les points de fonctionnement (voir le
barème de correction plus bas).
Programme de démonstration
Un programme de démonstration est disponible sur le site Moodle du cours. Le texte écrit à l’écran par votre
programme ne sera pas pris en compte par le script de correction. Par contre, éviter d’en mettre trop puisque cela
peut affecter le temps d’exécution de votre programme.
Un exemple de script python utilisé pour la correction automatique sera aussi disponible sur le site du cours.
7
Rapport de laboratoire
Votre rapport de laboratoire devra contenir les éléments suivants :
• Description de vos algorithmes en pseudocode
• Ordre asymptotique de vos algorithmes
• Justification de vos choix de conception et d’implémentation (y compris les améliorations à l’algorithme de
base, s’il y a lieu)
Votre rapport doit avoir un maximum de 8 pages. Toutes les pages supplémentaires seront ignorées lors de la
correction.
Barème de correction
Le barème de correction est le suivant :
Votre programme sera testé sur 4 fichiers de test, incluant l’exemple fourni avec le programme de démonstration.
Notez que les fichiers de test peuvent être de très grandes dimensions.
Les points accordés pour la vitesse d’exécution sont relatifs à la classe et la comparaison se fait sur la somme des 4
exemples de test. Pour obtenir ce point, votre programme doit produire une sortie exacte pour tous les exemples de
test. Le temps d’exécution maximale est limité à 2 minutes. Si votre programme dépasse le temps limite, il sera
considéré comme non fonctionnel.
Le point pour le taux de compression pour l’algorithme de Huffman est accordé si votre programme surpasse la
performance de la démo par au moins 400 octets sur tous les exemples de test.
Le point de compression pour l’algorithme optimisé, le taux de compression sera comparé à la classe. Pour ce point,
la contrainte du temps d’exécution de 2 minutes demeure valide.
Notez que des pénalités seront appliquées si les consignes sur la spécification du programme et sur la remise ne sont
pas respectées. Un script d’évaluation sera fourni afin de vous permettre de vous assurer que votre programme
respecte les spécifications. 
