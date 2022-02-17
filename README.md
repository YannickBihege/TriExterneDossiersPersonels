# TriExterneDossiersPersonels
Mise en place d'un tri externe pour le traitement de larges fichiers

Le tri externe est une classe d'algorithmes de tri qui peut gérer d'énormes quantités de données. Le tri externe est requis lorsque les données triées ne rentrent pas dans la mémoire principale d'un appareil informatique (généralement la RAM) et qu'elles doivent plutôt résider dans la mémoire externe plus lente, généralement un lecteur de disque. Ainsi, les algorithmes de tri externe sont des algorithmes de mémoire externe et donc applicables dans le modèle de calcul de mémoire externe.

Les algorithmes de tri externe se divisent généralement en deux types, le tri par distribution, qui ressemble au tri rapide, et le tri par fusion externe, qui ressemble au tri par fusion. Ce dernier utilise généralement une stratégie hybride tri-fusion. Lors de la phase de tri, des blocs de données suffisamment petits pour tenir dans la mémoire principale sont lus, triés et écrits dans un fichier temporaire. Dans la phase de fusion, les sous-fichiers triés sont combinés en un seul fichier plus grand.
