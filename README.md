# TriExterneDossiersPersonels
Mise en place d'un tri externe pour le traitement de larges fichiers

Le tri externe est une classe d'algorithmes de tri qui peut gérer d'énormes quantités de données. Le tri externe est requis lorsque les données triées ne rentrent pas dans la mémoire principale d'un appareil informatique (généralement la RAM) et qu'elles doivent plutôt résider dans la mémoire externe plus lente, généralement un lecteur de disque. Ainsi, les algorithmes de tri externe sont des algorithmes de mémoire externe et donc applicables dans le modèle de calcul de mémoire externe.

Les algorithmes de tri externe se divisent généralement en deux types, le tri par distribution, qui ressemble au tri rapide, et le tri par fusion externe, qui ressemble au tri par fusion. Ce dernier utilise généralement une stratégie hybride tri-fusion. Lors de la phase de tri, des blocs de données suffisamment petits pour tenir dans la mémoire principale sont lus, triés et écrits dans un fichier temporaire. Dans la phase de fusion, les sous-fichiers triés sont combinés en un seul fichier plus grand.

## Informations supplémentaires
Nous considérons maintenant le problème du tri des collections d'enregistrements trop volumineux pour tenir dans la mémoire principale. Étant donné que les enregistrements doivent résider dans une mémoire périphérique ou externe, ces méthodes de tri sont appelées tris externes. Cela contraste avec les tris internes, qui supposent que les enregistrements à trier sont stockés dans la mémoire principale. Le tri de grandes collections d'enregistrements est au cœur de nombreuses applications, telles que le traitement des paies et d'autres bases de données de grandes entreprises. En conséquence, de nombreux algorithmes de tri externes ont été conçus. Il y a des années, les concepteurs d'algorithmes de tri cherchaient à optimiser l'utilisation de configurations matérielles spécifiques, telles que plusieurs lecteurs de bande ou de disque. Aujourd'hui, la majeure partie de l'informatique est effectuée sur des ordinateurs personnels et des stations de travail bas de gamme avec des processeurs relativement puissants, mais seulement un ou au plus deux disques durs. Les techniques présentées ici visent à optimiser le traitement sur un seul lecteur de disque. Cette approche nous permet de couvrir les problèmes les plus importants du tri externe tout en sautant de nombreux détails moins importants dépendant de la machine.

Lorsqu'une collection d'enregistrements est trop volumineuse pour tenir dans la mémoire principale, la seule façon pratique de la trier est de lire certains enregistrements à partir du disque, de les réorganiser, puis de les réécrire sur le disque. Ce processus est répété jusqu'à ce que le fichier soit trié, chaque enregistrement pouvant être lu plusieurs fois. Compte tenu du coût élevé des E/S de disque, il n'est pas surprenant que l'objectif principal d'un algorithme de tri externe soit de minimiser le nombre de fois où les informations doivent être lues ou écrites sur le disque. Une certaine quantité de traitement CPU supplémentaire peut être avantageusement échangée contre un accès disque réduit.

Avant d'aborder les techniques de tri externe, considérons à nouveau le modèle de base pour accéder aux informations à partir du disque. Le fichier à trier est vu par le programmeur comme une série séquentielle de blocs de taille fixe. Supposons (pour simplifier) ​​que chaque bloc contient le même nombre d'enregistrements de données de taille fixe. Selon l'application, un enregistrement peut n'être que de quelques octets (composé de peu ou rien de plus que la clé) ou peut contenir des centaines d'octets avec un champ de clé relativement petit. Les enregistrements sont supposés ne pas franchir les limites des blocs. Ces hypothèses peuvent être assouplies pour les applications de tri à usage spécial, mais ignorer ces complications rend les principes plus clairs.

Rappelez-vous qu'un secteur est l'unité de base des E/S. En d'autres termes, toutes les lectures et écritures sur disque concernent un ou plusieurs secteurs complets. Les tailles de secteur sont généralement une puissance de deux, dans la plage de 512 à 16 Ko, selon le système d'exploitation et la taille et la vitesse du lecteur de disque. La taille de bloc utilisée pour les algorithmes de tri externe doit être égale ou un multiple de la taille de secteur.

### Approches

Si votre système d'exploitation prend en charge la mémoire virtuelle, le tri "externe" le plus simple consiste à lire l'intégralité du fichier dans la mémoire virtuelle et à exécuter une méthode de tri interne telle que Quicksort. Cette approche permet au gestionnaire de mémoire virtuelle d'utiliser son mécanisme de pool de mémoire tampon normal pour contrôler les accès au disque. Malheureusement, ce n'est pas toujours une option viable. Un inconvénient potentiel est que la taille de la mémoire virtuelle est généralement limitée à quelque chose de beaucoup plus petit que l'espace disque disponible. Ainsi, votre fichier d'entrée peut ne pas tenir dans la mémoire virtuelle. La mémoire virtuelle limitée peut être surmontée en adaptant une méthode de tri interne pour utiliser votre propre pool de mémoire tampon.

Un problème plus général lié à l'adaptation d'un algorithme de tri interne à un tri externe est qu'il n'est probablement pas aussi efficace que la conception d'un nouvel algorithme dans le but spécifique de minimiser les E/S de disque. Considérez la simple adaptation de Quicksort pour utiliser un pool de mémoire tampon. Quicksort commence par traiter l'ensemble du tableau d'enregistrements, la première étape de partition déplaçant les index vers l'intérieur à partir des deux extrémités. Cela peut être mis en œuvre efficacement à l'aide d'un pool de mémoire tampon. Cependant, l'étape suivante consiste à traiter chacun des sous-réseaux, suivi du traitement des sous-sous-réseaux, et ainsi de suite. Au fur et à mesure que les sous-réseaux deviennent plus petits, le traitement se rapproche rapidement de l'accès aléatoire au lecteur de disque. Même avec une utilisation maximale du pool de mémoire tampon, Quicksort doit toujours lire et écrire chaque enregistrement en moyenne. On peut faire beaucoup mieux. Enfin, même si le gestionnaire de mémoire virtuelle peut donner de bonnes performances en utilisant un Quicksort standard, cela se fera au prix d'une utilisation importante de la mémoire de travail du système, ce qui signifie que le système ne pourra pas utiliser cet espace pour d'autres travaux. De meilleures méthodes peuvent faire gagner du temps tout en utilisant moins de mémoire.

Notre approche du tri externe est dérivée de l'algorithme Mergesort. La forme la plus simple de Mergesort externe effectue une série de passages séquentiels sur les enregistrements, fusionnant des sous-listes de plus en plus grandes à chaque passage. La première passe fusionne les sous-listes de taille 1 en sous-listes de taille 2 ; la deuxième passe fusionne les sous-listes de taille 2 en sous-listes de taille 4 ; etc. Une sous-liste triée est appelée une série. Ainsi, chaque passe fusionne des paires de pistes pour former des pistes plus longues. Chaque passe copie le contenu du fichier dans un autre fichier. Voici une esquisse de l'algorithme.
