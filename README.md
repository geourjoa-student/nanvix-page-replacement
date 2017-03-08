# nanvix-page-replacement
Some implementations of Nanvix page replacement algorithm

Compte rendu du TP:
3 fichiers ont été produits lors de ce TP.

# paging.c

# paging_seconde_chance.c

Implémentation de l'algorithme dit de Not Recently Used.
Cet algorithme combine deux gros avantages: Il est extrèmement facile à implémenter, et est très efficace.

En effet, lorsque le processeur accède à une page du cache, il modifie la valeur de la variable "accessed" de l'objet "pte" associé à la page, en passant celle-ci à 1.

L'algorithme se base sur une idée simple: On essaye de savoir si une page a été accédée récemment. Si tel a été le cas, on la laisse dans le cas. Le cas échéant, c'est que cette page peut être enlevée du cache, de façon aléatoire entre toutes les pages non accédées récemment du cache.

Afin d'implémenter l'algorithme, il suffit qu'à chaque appel de allocf, on prenne une page qui n'a pas été accédée comme élue (et qui va donc être exclue du cache), et qu'on passe toutes les autres valeurs de "accessed" à 0, ce qui simule la notion de "récemment". Il est important d'utiliser une variable "static" comme accesseur, afin d'effectuer une "ronde" et que ce ne soit aps toujours les premières pages trouvées qui soient sorties du cache.

Cet algorithme est extrêment efficace. En effet, bien que la page sortie du cache ne soit pas nécessairement la plus optimale à chaque fois, la quantité de calculs nécessaire pour l'élire est tellement faible qu'on obtient des résultats extrèmements efficaces. Avec la version que nous avons implémenté, nous arrivons à un total 16.100 cycles nécessaires pour réaliser le test (rapide) implémenté dans Nanvix.

# paging_LRU.c

Implémentation de l'algorithme dit de Least Recently Used
Algorithme qui ressemble beaucoup au NRU, avec de plus amples améliorations possibles.

Le principe du LRU est de trouver la page qui est dans le cache "pour rien" (i.e. sans avoir été utilisée) depuis le plus longtemps, et de l'élire afin de l'exclure de ce dernier.

Ici est implémenté une fonction très simple. On se sert encore de l'objet "pte", mis à jour pour chaque page lorsque cette dernière est accédée par le processeur. Ensuite, lorsque la fonction "allocf" est appelée, on met à jour la variable "not_used_for" (qui est une variable ajoutée dans la structure frames) pour chaque page présente dans le cache à l'aide d'un appel à la fonction "ageUp()". Si une une page a été accédée récemment, sa variable "not_used_for" est remise à 0. le cas échéant, elle est incrémentée. Ensuite, on regarde la valeur la plus élevée et cela devient la page élue pour être exclue. A noter que la mise à jour des "not_used_for" n'est pas exhaustive, et que la fonction ageUp() n'est appelée que qu'avec une probabilité 1/X, en effectuant le test "ticks%X" (ticks étant mis à jour à chaque cycle), ce qui consiste en une approximation.

Ici, les performances sont assez faibles, oscillant entre 30.000 et 25.900 cycles pour le test, en fonction de la valeur de X (Optimal pour X>2000). Il a été fait comme choix de séparer ageUp() comme une fonction. nous avons décider pour explications déerreurs potentielles de laisser le code comme tel. Il apparait clairement lors des test que c'était une mauvaise idée: En effet, il aurait été beaucoup plus efficace de tout effectuer dans la fonction "allocf", car cela nous aurait permis de calculer le "oldest" lors des mises à jour de "not_used_for", évitant des tours de boucle inutiles.

Malgré toutes les optimisations possibles de cet algorithme, il apparait très difficile d'atteindre des performances aussi élevées que pour le NRU. On note d'ailleurs très clairement lors de l'implémentation de ces deux algorithmes qu'une bonne approximation (NRU) est parfois plus efficace qu'un algo plus parfait (LRU)

